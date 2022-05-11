# comandos-uteis

* `pipenv install`, cria ambiente virtual com os packages de pipfile
* `pipenv shell`, ativa ambiente virtual
* `python manage.py runserver` para correr app
* `python manage.py shell` lança consola python para correr comandos


Passos para manipular models.y e a Base de dados:
1. modificar `models.py`
1. `python manage.py makemigrations` cria comandos sql para modificar base de dados de acordo com o `models.py` 
1. `python manage.py migrate` executa comandos sql para modificar base de dados 
1. só depois podemos manipular a base de dados no `admin`, inserindo elementos
    1. tens que tens permissões de superuser: `py manage.py createsuperuser` 
1. na consola, tambem é possivel manipular a base de dados. para tal, lançar `python manage.py shell`, que lança a consola python. PAssps:
    1. importar models: `>> import nomeAplicacao.models`
    1. obter todos os objetos duma tabela: `>> Celula.objects.all()`


## Carregando e mostrando imagens com o <a name="ImageField"></a> campo ImageField


* [How to manage static files](https://docs.djangoproject.com/en/4.0/howto/static-files/)
* [How to upload and display images](https://studygyaan.com/django/how-to-upload-and-display-image-in-django)

Passos para ter um campo para carregar corretamente uma imagem para uma pasta que queiramos:

#### 0. Instalar o pillow no ambiente virtual ativado

```bash
pipenv shell
python -m pip install Pillow
```

#### 1. Primeiro devemos dar instruções para criar uma pasta (MEDIA) onde guardar as imagens. Colocar em settings.py:

```Python
# settings.py

import os
MEDIA_ROOT = os.path.join(BASE_DIR, "media")
MEDIA_URL = "/media/"
```

#### 2. no app/urls.py   (funciona no config/urls.py ?!): 

```Python
# config/urls.py

from django.conf import settings
from django.conf.urls.static import static

urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```


#### 3. Classe com atributo image e argumento upload_to

Depois podemos utilizar na definição do atributo da classe. Podemos especificar  no `upload_to` a pasta, dentro da pasta MEDIA, onde queremos guardar as imagens. Por exemplo, em baixo queremos guardar uma imagem duma resposta dum determinado utilizador. Usamos o seu id no nome da pasta por forma a que as imagens fiquem organizadas por utilizador. Usa-se uma função auxiliar para determinar o caminho:


```Python
# views.py

def resolution_path(instance, filename):
    return f'users/{instance.id}/'
    
class Answer(models.Model):
    name = models.CharField(max_length=40)
    fotografia = models.ImageField(upload_to=resolution_path)
```

Exemplo de outra classe mais complexa:

```Python
# views.py

def resolution_path(instance, filename):
    return f'users/{instance.resolution.patient.id}/resolutions/{instance.resolution.id}'
    
    
class Answer(models.Model):
    question = models.ForeignKey('Question',
                                 on_delete=models.CASCADE)
    resolution = models.ForeignKey('Resolution',on_delete=models.CASCADE)
    submitted_answer = models.ImageField(upload_to=resolution_path)
```

#### 4. No HTML, formulário incluir sempre `enctype="multipart/form-data"`

```html
  <form method = "post" enctype="multipart/form-data"> 
```


No html deve-se inserir no especificador o campo `url` da imagem
```html
 <img src="{{pessoa.fotografia.url}}" alt="retrato" width="250" height="250">
```





# notas


class AuthorForm(ModelForm):
    class Meta:
        model = Cuidador
        fields = ('name', 'escolaridade', 'birth_date')
        widgets = {
            'name': Textarea(attrs={'cols': 80, 'rows': 20}),
        }
        
        
        
 selected="True"
