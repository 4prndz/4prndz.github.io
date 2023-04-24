---
title: CRUD no Laravel 10
date: 2023-04-23 07:22:00 +/-0300
categories: [PHP, Laravel]
tags: [crud, cheatsheet] 
---
Apenas uma Nota para quando precisar criar um CRUD, rapidamente! :)

## Configuration

Para criar um Controller com os métodos do CRUD, basta rodar o seguinte comando no terminal:

`$ php artisan make:controller ObjetosController --resource` 

Irá gerar um Controller chamado ***ObjetosController*** com todos os métodos necessários para implementar o CRUD de um modelo.

Em seguida, é necessário criar um Model para representar o objeto em questão:

`$ php artisan make:model ObjectModel`

Este comando irá gerar um Model chamado ObjectModel, que irá representar uma tabela.

---

## Route

Finalmente, é preciso criar todas as rotas do CRUD. Isso pode ser feito adicionando a seguinte linha ao arquivo `routes/web.php`:

```php
Route::resource('object', ObjetosController::class); // Route::{verbo}({URI}, [ObjetosController::class, {metodo}])->name({nome_da_rota})
```

Essa linha equivale a uma série de outras rotas que seriam necessárias para implementar o CRUD manualmente. Cada célula na tabela abaixo corresponde a um método que será implementado pelo Controller.

| Verbo HTTP | URI | Método | Nome da Rota | Explicação |
| --- | --- | --- | --- | --- |
| GET | /objetos | index | objetos.index | Retorna uma listagem dos objetos |
| GET | /objetos/create | create | objetos.create | Retorna um formulário de criação |
| POST | /objetos | store | objetos.store | Cria um novo objeto no DB |
| GET | /objetos/{objeto} | show | objetos.show | Retorna um único objeto |
| GET | /objetos/{objeto}/edit | edit | objetos.edit | Retorna um fomulário de edição |
| PUT/PATCH | /objetos/{objeto} | update | objetos.update | Atualiza um objeto do DB |
| DELETE | /objetos/{objeto} | destroy | objetos.destroy | Remove um objeto do DB |

---

## Controller

**`ObjetosController.php`** é responsável por definir os métodos para manipulação dos objetos no banco de dados.

Primeiro importe o Model, para ser utilizado no Controller:

```php
use App\Models\ObjetosModel;
```

Dentro da Classe `ObjetosController`, teremos os métodos:

### Index

Responsável por retornar a view que lista todos os objetos do banco de dados. Ele utiliza o método **`all`** do Model para obter todos os objetos e passa-os para a view.

```php
public function index()
{
    $objetos = ObjetosModel::all();
    return view('objetos', ['objetos' => $objetos]);
}
```

### Create

Retorna a view que contém o formulário de criação de um novo objeto.

```php
    public function create()
    {
        return view('objetos_create');
    }
```

### Store

Responsável por salvar um novo objeto no banco de dados. Ele utiliza o método **`create`** do Modelpara criar um novo objeto com os dados fornecidos pelo formulário. Em seguida, ele verifica se a operação foi realizada com sucesso e redireciona o usuário para a página anterior com uma mensagem de sucesso ou de erro.

```php
public function store(Request $request)
{
    $created = ObjetosModel::create($request->except('_token'));

    if ($created) {
        return redirect()->back()->with('success', 'Criado com Sucesso');
    }
    return redirect()->back()->with('fail', 'Erro: Falha ao Criar');
}
```

### Show

Responsável por exibir os detalhes de um objeto específico. Ele recebe o objeto como um parâmetro e o passa para a view.

```php
public function show(ObjetoModel $objeto)
{
    return view('objetos_show', ['objeto' => $objeto]); 
}
```

### Edit

Retorna a view que contém o formulário de edição de um objeto específico. Ele recebe o objeto como um parâmetro e o passa para a view.

```php
public function edit(ObjetoModel $objeto)
{
    return view('objetos_edit', ['objeto' => $objeto]); // Formulário de Edição de um Objeto
}
```

### Update

Responsável por atualizar um objeto específico no banco de dados. Ele utiliza o método **`update`** do Model para atualizar os dados do objeto com os dados fornecidos pelo formulário. Em seguida, ele verifica se a operação foi realizada com sucesso e redireciona o usuário para a página anterior com uma mensagem de sucesso ou de erro.

```php
public function update(Request $request, string $id)
{
    $updated = ObjetoModel::where('id', $id)->update($request->except('_token', '_method'));

    if ($updated) {
        return redirect()->back()->with('success', 'Atualizar com Sucesso');
    }

    return redirect()->back()->with('fail
```

### Destroy

Reponsável por deletar um objeto específico no banco de dados. Utiliza o método `**delete**` do Model no objeto com id passado. E depois retorna para a rota **`objetos.index`**.

```php
public function destroy(string $id)
{
	$deleted = ObjetoModel::where('id', $id)->delete(); 
	return redirect()->route('objetos'); 
}
```

---

## Views

`resources/views/objetos.blade.php`

Visualização principal da página de listagem de objetos. Ele exibe uma tabela com informações dos objetos e botões de ***create***, ***edit*** e ***delete*** para os objetos.

```php
@extends('layout')

@section('content')
  ...
  <a href="{{ '{' }}{ route('objetos.create') }}" class="btn btn-info">Create</a>
  <table class="table table-dark table-striped">
    <thead>
      <tr>
        <th>Coluna</th>
        ...
        <th>Editar</th>
        <th>Remover</th>
      </tr>
    </thead>
    <tbody>
      @foreach ($objetos as $objeto)
        <tr>
          <td>
            <a href="{{ '{' }}{ route('objetos.show', ['objeto' => $objeto->id]) }}" class="p-2 rounded text-decoration-none bg-white text-black">{{ '{' }}{ $objeto->nome }}</a>
          </td>
          ...
          <td>
            <a href="{{ '{' }}{ route('objetos.edit', ['objeto' => $objeto->id]) }}" class="btn btn-success">Editar</a>
          </td>
          <td>
            <form action="{{ '{' }}{ route('objetos.destroy', ['objeto' => $objeto->id]) }}" method="POST" style="display:inline">
              @csrf
              @method('DELETE')
              <button type="submit" class="btn btn-danger">Excluir</button>
            </form>
          </td>
        </tr>
      @endforeach
    </tbody>
  </table>
  ...
@endsection
```

`resources/views/objetos_create.blade.php`

Visualização da página de criação de objetos. Contém um formulário onde o usuário pode inserir informações sobre o novo objeto. O formulário envia as informações inseridas para a rota de ***create***.

```php
@extends('layout')

@section('content')
  // Mostra se houver alguma flash message 
  @if (session()->has('success'))
    <span class="alert alert-success">{{ '{' }}{ session()->get('success') }}</span>
  @elseif(session()->has('fail'))
    <span class="alert alert-danger">{{ '{' }}{ session()->get('fail') }}</span>
  @endif

  <form action="{{ '{' }}{ route('objetos.store', ['objeto' => $objeto->id]) }}" method="POST">
    @csrf
    <input type="text" class="form-control" name="nome" value="{{ '{' }}{ $objeto->nome }}">
    // Outros campos do formulário 
    <button type="submit" class="btn btn-primary">Enviar</button>
  </form>
@endsection
```

`resources/views/objetos_show.blade.php`

Visualização da página de exibição de um objeto específico. Ele exibe as informações do objeto em um formato mais detalhado do que a tabela na página de listagem de objetos.

```php
@extends('layout')

@section('content')
  <h1>{{ '{' }}{ $objeto->nome }}</h1>

  @foreach ($objetos as $objeto)
    <ul>
      <li>Nome: {{ '{' }}{ $objeto->nome }}</li>
      // Outros campos do objeto
    </ul>
  @endforeach

  <form action="{{ '{' }}{ route('objetos.destroy', ['objeto' => $objeto->id]) }}" method="POST">
    @csrf
    @method('DELETE')
    <button type="submit" class="btn btn-danger">Delete</button>
  </form>
@endsection
```

`resources/views/objetos_edit.blade.php`

Visualização da página de edição de um objeto. Ele contém um formulário que permite ao usuário editar as informações do objeto existente. O formulário envia as informações atualizadas para a rota de atualização do objeto.

```php
@extends('layout')

@section('content')
  // Mostra se houver alguma flash message
  @if (session()->has('success'))
    <span class="alert alert-success">{{ '{' }}{ session()->get('success') }}</span>
  @elseif(session()->has('fail'))
    <span class="alert alert-danger">{{ '{'}}{ session()->get('fail') }}</span>
  @endif

  <form action="{{ '{' }}{ route('objetos.update', ['objeto' => $objeto->id]) }}" method="POST">
    @csrf
    @method('PUT')
    <input type="text" class="form-control" name="nome" value="{{ '{' }}{ $objeto->nome }}">
    // ...
    <button type="submit" class="btn btn-primary">Enviar</button>
  </form>
@endsection
```
---
Por enquanto, pretendo adicionar mais coisas com o tempo, e deixar mais explicativo! :P