---
# You don't need to edit this file, it's empty on purpose.
# Edit theme's home layout instead if you wanna make some changes
# See: https://jekyllrb.com/docs/themes/#overriding-theme-defaults
#layout é a pasta layouts
layout: default
---

<header class="masthead">
	
  <img src="img/danilo.jpg" class="me">

  <h1 class="masthead-title">
    <a href="{{ site.baseurl }}/">{{ site.title }}</a>
  </h1>
  <nav class="masthead-nav">
	<!-- Lê todas as páginas .md da mesma raiz deste arquivo -->
  	{% for page in site.pages %}
  		{% if page.title %}
  			<a href="{{ page.url }}"> {{page.title}}</a>
  		{% endif %}
  	{% endfor%}
  </nav>

	<ul>
	  {% if site.github_username %}
		<li>
			<a href="http://github.com/{{ site.github_username }}">GitHub</a><br/>
		</li>
	  {% endif %}

	  {% if site.twitter_username %}
		<li>
			<a href="http://twitter.com/{{ site.twitter_username }}">Twitter</a><br/>
		</li>
	  {% endif %}
	 
	  {% if site.linkedin_username %}
		<li>
			<a href="http://linkedin.com/in/{{ site.linkedin_username }}">Linkedin</a>
		</li>
	  {% endif %}
	</ul>

</header>

<div class="content list">
  	<!-- Lê todas as páginas .markdown da pasta _posts -->
  {% if site.posts.size == 0 %}
    <h2>No post found</h2>
  {% else %}
    {% for post in site.posts %}
      <div class="list-item">
        <h2 class="list-post-title">
          <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
        </h2>
        <div class="list-post-date">
          <time>{{ post.date | date_to_string }}</time>
        </div>
      </div>
    {% endfor %}
  {% endif %}
</div>
