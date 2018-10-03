---
layout: post
title:  "Helper Methods Customizados"
date:   2018-10-03 13:14:00 -0300
categories: jekyll update
---

## O que é um Helper? 
Helpers no rails são módulos usados com a finalidade de extrair complexidade lógica das views.

## Movendo a lógica da view
Quando estamos desenvolvendo alguma funcionalidade e nos deparamos com algo alguma lógica entre apresentar ou não algo para o usuário. Podemos fazer como é feito abaixo, porém em alguma outra tela vamos precisa fazer o mesmo, verificar se o usuário tem email


``` ruby
  <% if @customer && @customer.email.present? %>
    <%= @user.email %>
  <% end %>
```

A solução é ser reutilizável, assim criaremos nosso helper.

``` ruby
module CustomersHelper
  def customer_email(customer)
    customer.email if customer.try(:email)
  end
end
```

Agora podemos usar em nossa view nosso Helper recém criado.

``` ruby
<%= user_email(@user) %>
```

## Dicas
Não use variáveis de instância nos Helpers, isso vai tornar o helper difício de manter
se você ficar mudando o estado de uma helper.

#### Carregamento dos Helpers
Nas versões dos Rails inferiores a 4 os helpers só eram carregados quando o nome do Helper dava match com o nome do controllers. Exemplo: um controller **CustomersController** iria carregar o **CustomersHelpers** e ia ficar disponível nas views da pasta **_/views/customers/*_** . Isto não funciona mais assim, cada controller carrega todos os helpers, se você quer voltar com o comportamento anterior é possível usar a *config* abaixo.

```
config.action_controller.include_all_helpers = false
```