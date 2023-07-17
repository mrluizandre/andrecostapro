---
layout: post
title:  "Como configurar um projeto em Ruby on Rails pra português brasileiro"
date:   2018-09-11 20:31:00 -0300
categories: rails portugues
---
## Introdução
Neste tutorial vou demonstrar como alterar alguns aspectos no Rails pra trabalhar em Português do Brasil. Muitas vezes iniciamos um projeto e no decorrer do desenvolvimento temos que ir mudando esses itens pouco a pouco pra ter o sistema rodando da forma que queremos, pensando nisso e em não precisar ficar catando configurações aqui e ali na internet resolvi reunir em um único lugar as que sempre uso. Sugestões são bem vindas :D

## Mudar a interface para português do Brasil
Dentro da pasta `config/locales` crie o arquivo `pt-BR.yml`. Baixe o arquivo [nesse link](https://gist.github.com/mrluizandre/c6eb0cfa07312c13830ddbf1dcac5c9d).

Em seguida edite o arquivo `config/application.rb` e insira a seguinte linha:
```ruby
config.i18n.default_locale = :"pt-BR"
```

O conteúdo do arquivo ficará assim:
```ruby
require_relative 'boot'require 'rails/all'
Bundler.require(*Rails.groups)module Teste
  class Application < Rails::Application
    config.load_defaults 5.2    config.i18n.default_locale = :"pt-BR" #linha inserida  end
end
```

Esse comando localiza o Rails para o português brasileiro, assim ele passa a usar as traduções que adicionamos com o arquivo acima, que contém datas, horário, erros de validações, números por extenso e até textos de botões formulário do CRUD.

Esssa configuração também localiza gems que têm os arquivo de locale disponível, não exite em conferir alguns exemplos no último item do turorial.

### Modelos e atributos

O arquivo `config/locales/pt-BR.yml`, que inserimos no projeto logo acima, é onde também podemos traduzir os models e atributos que criamos em nossos projetos. Tomemos como exemplo um model Post com os atributos title e body. Insira logo abaixo da linha `activerecord:`:

```yml
pt-BR:
  activerecord:
    models:
      post: Postagem
    attributes:
      post:
        title: Título
        body: Corpo    #Código já existente abaixo
    errors:
      messages:
```

Essa tradução será usada, por exemplo, nas labels de formulários e o nome do model no botão salve respectivo.

## Pluralização para português
Esse item não é consenso entre todos os desenvolvedores. Tem-se como boa prática sempre programar utilizando termos em inglês, isso torna o seu código, digamos, mais internacionalizável, pois se outro programador vier a trabalhar no seu projeto, os termos em inglês facilitam o entendimento do projeto, principalmente se a pessoa não falar português.

Mas como o Rails é uma ferramenta bastante flexível é possível ajustar a pluralização de termos. Essa pluralização é usada em várias partes do sistema, como em generators e métodos.

O arquivo que faz essa configuração é o `config/initializers/inflections.rb`, ele é responsável por dizer ao Rails como pluralizar/singularizar as palavras. Altere-o para o seguinte conteúdo:

```ruby
# encoding: utf-8ActiveSupport::Inflector.inflections do |inflect|
  inflect.clear  inflect.plural(/$/,  's')
  inflect.plural(/(s)$/i,  '\1')
  inflect.plural(/^(paí)s$/i, '\1ses')
  inflect.plural(/(z|r)$/i, '\1es')
  inflect.plural(/al$/i,  'ais')
  inflect.plural(/el$/i,  'eis')
  inflect.plural(/ol$/i,  'ois')
  inflect.plural(/ul$/i,  'uis')
  inflect.plural(/([^aeou])il$/i,  '\1is')
  inflect.plural(/m$/i,   'ns')
  inflect.plural(/^(japon|escoc|ingl|dinamarqu|fregu|portugu)ês$/i,  '\1eses')
  inflect.plural(/^(|g)ás$/i,  '\1ases')
  inflect.plural(/ão$/i,  'ões')
  inflect.plural(/^(irm|m)ão$/i,  '\1ãos')
  inflect.plural(/^(alem|c|p)ão$/i,  '\1ães')  # Sem acentos...
  inflect.plural(/ao$/i,  'oes')
  inflect.plural(/^(irm|m)ao$/i,  '\1aos')
  inflect.plural(/^(alem|c|p)ao$/i,  '\1aes')  inflect.singular(/([^ê])s$/i, '\1')
  inflect.singular(/^(á|gá|paí)s$/i, '\1s')
  inflect.singular(/(r|z)es$/i, '\1')
  inflect.singular(/([^p])ais$/i, '\1al')
  inflect.singular(/eis$/i, 'el')
  inflect.singular(/ois$/i, 'ol')
  inflect.singular(/uis$/i, 'ul')
  inflect.singular(/(r|t|f|v)is$/i, '\1il')
  inflect.singular(/ns$/i, 'm')
  inflect.singular(/sses$/i, 'sse')
  inflect.singular(/^(.*[^s]s)es$/i, '\1')
  inflect.singular(/ães$/i, 'ão')
  inflect.singular(/aes$/i, 'ao')
  inflect.singular(/ãos$/i, 'ão')
  inflect.singular(/aos$/i, 'ao')
  inflect.singular(/ões$/i, 'ão')
  inflect.singular(/oes$/i, 'ao')
  inflect.singular(/(japon|escoc|ingl|dinamarqu|fregu|portugu)eses$/i, '\1ês')
  inflect.singular(/^(g|)ases$/i,  '\1ás')  # Incontáveis
  inflect.uncountable %w( tórax tênis ônibus lápis fênix )  # Irregulares
  inflect.irregular "país", "países"
end
```

Porém vale lembrar que nem mesmo assim a pluralização pode não sair da forma esperada. Antes de fazer cada operação você pode testar utilizando o console. Abra o Rails console com `rails console` ou pelo atalho `rails c`. Em seguida faça o teste com a palavra a ser testada executando: `"teste".pluralize`. Caso o resultado não seja o esperado insira no inflections.rb o código correspondente:

```ruby
inflect.irregular "teste", "teste"
```

## Rotas em português
Mesmo que você siga as boas práticas e nomeie seus models em inglês não quer dizer que você não possa mudar/traduzir suas rotas pra apresentar URLs mais amigáveis aos seus usuários. Mesmo tendo um model `User`, seu endereço pode ser `www.meusite.com.br/usuarios`. Para isso, basta alterar o arquivo `config/routes.rb`. e adicionar o seguinte:

```ruby
resources :users, path: 'usuarios'
```

Rotas de edição e criação, `new` e `edit` também podem ser modicadas, exemplo:

```ruby
resources :users, path: 'usuarios', path_names: { new: "novo", edit: "editar" }
```

Para alterar vários models de uma vez só, você pode usar:

```ruby
scope path_names: { new: "novo", edit: "editar" } do
  # seus resources
end
```

Vale lembrar que as variáveis `_path` não são alteradas, continuam com `new` e `edit`. Por exemplo: `new_user_path` e `edit_user_path(@user)`.

## Fuso horário
Quase sempre as aplicações que desenvolvemos dependem de alguma forma do horário. Por padrão data e hora são guardadas no banco no formato UTC. Partindo disso podemos configurar de duas formas.

Caso deseje que horário continue sendo guardado no formato UTC no banco, mas converta para o horário de Brasília na hora de exibição, insira o código abaixo no arquivo `config/application.rb`:

```ruby
config.time_zone = 'Brasilia'
```

Fazendo dessa forma é uma boa ideia caso você precise suportar vários idiomas na mesma aplicação.

Outra abordagem é guardar o hora no banco já no horário de Brasília, insira o código abaixo no mesmo arquivo:

```ruby
config.time_zone = "Brasilia"
config.active_record.default_timezone = :local
```

## Extras
No decorrer do projeto sempre adicionamos várias gems pra facilitar nosso trabalho, e é na variedade de gems que o Rails brilha. Elas também não escapam da tradução. Abaixo alguns exemplos.

### Devise
O Devise é umas das mais famosas gems para se fazer autênticação no Rails. Ao setar o locale pra pt-BR, como fizemos no primeiro item do tutorial, procura automaticamente o arquivo `config/locales/devise.pt-BR.yml`, você pode encontrá-lo [aqui](https://gist.github.com/mrluizandre/59238461bdcf77ecf5c18c22251a7831).

### Simple Form
O Simple Form é uma poderosa ferramenete que faz, adivinhe…, facilita o desenvolvimento de formulários, de forma simples. Ele procura o arquivo `config/locales/simple-form.pt-BR.yml`, este você encontra [aqui](https://gist.github.com/mrluizandre/f6fc0f48431f78e584bc7d2624522f9f).

Com informações de: [Reposta do do user7261 no Stack Overflow](https://pt.stackoverflow.com/a/19513).
