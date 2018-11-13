---
layout: post
title:  "Rails Flash Message com Boostrap"
date:   2018-11-13 20:00:00 -0300
categories: jekyll update
---

## ApplicationHelper
Rails tem como um dos principios o Dont Repeat Yourself que consistem
em reutilizar funcionalidades, claro que quando faz sentido, podemos concentrar alguns helpers que usamos nas nossa views para evitar aquele copy paste.

## Flash Messages com Boostrap
Normalmente uso Boostrap 4 nos meus projetos, o boostrap tem vários compenentes já com um layout definido para usarmos. Um deles é o alerta para informar-mos quando algo foi salvo, foi excluído, quando erramos a senha entre outras coisas.

No rails podemos mandar um alert dentro do nosso controller, esse alert será exibido como padrão na proxíma view redirecionada.

``` ruby
# app/controllers/people_controller.rb
def create
  @person = Person.new(person_params)

  if @person.save
    redirect_to @person, notice: 'Criado com sucesso'
  end
end
```

*app/views/people/show.html.erb*  
`<p id="notice"><%= notice %></p>`  

que vai renderizar  
> Criado com sucesso

Podemos criar um Helper no application helper já identifica o tipo de alerta do Boostrap com o flash message do Rails

``` ruby
module ApplicationHelper
  BOOTSTRAP_ALERTS = { notice: :success, alert: :warning, error: :danger }.freeze

  def flash_message
    messages = ''
    BOOTSTRAP_ALERTS.keys.each do |type|
      if flash[type]
        messages += "<div class='alert alert-#{BOOTSTRAP_ALERTS[type]}' role='alert'>#{flash[type]}</div>"
      end
    end

    messages.html_safe
  end
end
```
Agora podemos substituimos `<p id="notice"><%= notice %></p>` por:  

*app/views/people/show.html.erb*  
`<%= flash_message %>`

Podemos passar o alert que quisermos

``` ruby
flash[:danger] = 'Alteração não permitida'
flash[:error] = 'O usuário não existe'
```




## Referências
[Boostrap 4 alerts](https://getbootstrap.com/docs/4.0/components/alerts/)  
[Api - Ruby on Rails](https://api.rubyonrails.org/v5.1/classes/ActionController/Helpers.html)  
[NandoVieira](https://nandovieira.com.br/exibindo-mensagens-no-rails)
