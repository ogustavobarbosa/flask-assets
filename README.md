# FLASK: widget de paginação

### Incorporando widget de paginação, no Flask.

> listagem de item por página `views.py` 
```
@main.route('/', methods=['GET', 'POST'])
def index():
    ...
    page = request.args.get('page', 1, type=int)

    pagination = Product \
                  .query.order_by(Product.timestamp.desc()) \
                  .paginate(
                            page,
                            per_page=20,
                            error_out=False
                            )
    products = pagination.items
    return render_template('index.html', products=products, pagination=pagination)
```

> O valor de retorno de [paginate( )](https://flask-sqlalchemy.palletsprojects.com/en/2.x/api/?highlight=paginate#flask_sqlalchemy.BaseQuery.paginate) é um objeto da classe [Pagination](https://flask-sqlalchemy.palletsprojects.com/en/2.x/api/?highlight=paginate#flask_sqlalchemy.Pagination), uma classe definida por FlaskSQLAlchemy. Este objeto contém várias propriedades que são úteis para gerar páginas
links em um modelo, então ele é passado para o modelo como um argumento, para a `_macros.hmtl`.

```
{% macro pagination_widget(pagination, endpoint) %}

  <ul class="pagination">
      <li {% if not pagination.has_prev %} class="disabled"{% endif %}>
         <a href="{% if pagination.has_prev %}{{ url_for(endpoint,
         page = pagination.page - 1, **kwargs) }}{% else %}#{% endif %}">
         &laquo;
         </a>
      </li>
      {% for p in pagination.iter_pages() %}
         {% if p %}
            {% if p == pagination.page %}
               <li class="active">
               <a href="{{ url_for(endpoint, page = p, **kwargs) }}">{{ p }}</a>
               </li>
            {% else %}
               <li>
               <a href="{{ url_for(endpoint, page = p, **kwargs) }}">{{ p }}</a>
               </li>
            {% endif %}
         {% else %}
             <li class="disabled"><a href="#">&hellip;</a></li>
         {% endif %}
      {% endfor %}
      <li {% if not pagination.has_next %} class="disabled"{% endif %}>
          <a href="{% if pagination.has_next %}{{ url_for(endpoint,
          page = pagination.page + 1, **kwargs) }}{% else %}#{% endif %}">
          &raquo;
          </a>
      </li>
  </ul>
  
{% endmacro %}
```
>Agora podemos incluir a macro *hmtl* de paginação para a listagem de produtos em `.../index.html`

```
{% import "_macros.html" as macros %}
...
{% include '_posts.html' %}
<div class="pagination">
 {{ macros.pagination_widget(pagination, '.index') }}
</div>
{% endif %}
```
  
  
  
