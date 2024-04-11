# Laboratorio AWS Serverless: 

### Crear un Rest API(CRUD) con AWS Lambda(Python), API Gateway y DynamoDB

 1. **Objetivos**
 2. **Tarea 1: crear una tabla de DynamoDB**
 3. **Tarea 2: crear funciones de Lambda**
 4. **Tarea 3: crear y probar una API de API Gateway**
 5. **Tarea 4: implementar la API**
 6. **Tarea 5: probar la API**
 7. **Conclusiones**


###   **1. Objetivos**
Al final de este laboratorio, podrá hacer lo siguiente:

- Crear una tabla de DynamoDB
- Crear funciones Lambda para leer y escribir datos de una table DynamoDB
- Crear y probar la API de API Gateway
- Implementar y probar la API

------------

###   **2. Tarea 1: crear una tabla de DynamoDB**
- **Table name** (Nombre de la tabla): ingrese `movies`
- **Partition key** (Clave de partición): ingrese `title` y luego seleccione **String** (Cadena)
- **Sort key** (Clave de ordenación) (opcional): dejar en blanco
- **Table settings** (Configuración de la tabla): seleccione **Default settings** (Configuración predeterminada)


###   **3. Tarea 2: crear funciones de Lambda**
##### TAREA 2.1: CREAR UNA FUNCIÓN DE LAMBDA MOVIE
- **Create function** (Crear función): seleccione  **Author from scratch**(Crear desde cero)
- **Function name**(Nombre de función): ingrese `create_movie`
- **Runtime**(Tiempo de ejecución): seleccione **Python 3.9**
- **Architecture** (Arquitectura): seleccione  **x86_64**

**Permisos:**
- Expanda **Change default execution role** (Cambiar el rol de ejecución predeterminado)
- **Execution role** (Rol de ejecución):  seleccione **Use an existing role** (Utilizar un rol existente)
- **Existing role** (Rol existente): seleccione `LabLambdaExecutionRole` 
> **Nota**: este rol otorga a la función de Lambda y a otras funciones de Lambda los permisos que necesitan para interactuar con DynamoDB.

Copie y pegue el siguiente código para reemplazar el código existente en el archivo **lambda_function.py**.


    import boto3
    
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('movies')
    
    def lambda_handler(event, context):
       year = event['year'] if event['year'] else '0'
       title = event['title'] if event['title'] else ''
       actors = event['actors'] if event['actors'] else ''
       
       response = table.put_item(
            Item={
                'year': year,
                'title': title,
                'info': {
                    'actors': actors
                }
            }
        )
       
       return {
            "statusCode": 200,
            "headers": {
                "Content-Type": "application/json"
            },
            "body": response
        }
    
Implemente y pruebe la función de Lambda a través de las siguientes opciones:

- **Test event action** (Acción de evento de prueba): seleccione **Create new event** (Crear evento nuevo)
- **Event name** (Nombre de evento): ingrese `CreateMovieTest`
- **Event sharing settings** (Configuración para compartir eventos): seleccione  **Private** (Privado)
- **Template **(Plantilla)** (Opcional)**: ingrese `hello-world`
- **Event JSON**: copie y pegue el siguiente código:


        {
          "year": 1972,
          "title": "The Godfather",
          "actors": ["Marlon Brando", "Al Pacino", "James Caan"]
        }
    
Para crear dos elementos más en la tabla **movies** cambie el código anterior **JSON Code** pegando los suientes codigos y ejecute la prueba otra vez:
- **Segundo Elemento**

        {
          "year": 1972,
          "title": "Deliverance",
          "actors": ["Jon Voight", "Burt Reynolds", "Ned Beatty"]
        }


- **Tercer Elemento**

        {
          "year": 1994,
          "title": "The Shawshank Redemption",
          "actors": ["Tim Robbins", "Morgan Freeman", "Bob Gunton"]
        }

> **Nota**: si la prueba se ejecuta correctamente, verá una **Respuesta** con un **HTTPStatusCode** igual a 200 y los **Elementos** creados en la tabla **movies** de DynamoDB.

------------


##### TAREA 2.2: CREAR UNA FUNCIÓN DE LAMBDA GET MOVIE (OBTENER MOVIE)
- **Create function** (Crear función): seleccione  **Author from scratch** (Crear desde cero)
- **Function name** (Nombre de función): ingrese `get_movie`
- **Runtime** (Tiempo de ejecución): seleccione **Python 3.9**
- **Architecture** (Arquitectura): seleccione  **x86_64**

Permisos:
- Expanda  **Change default execution role** (Cambiar rol de ejecución predeterminado)
- **Execution role** (Rol de ejecución): seleccione  **Use an existing role** (Utilizar un rol existente)
- **Existing role** (Rol existente): seleccione `LabLambdaExecutionRole `

Copie y pegue el siguiente código para reemplazar el código existente en el archivo **lambda_function.py**.


    import boto3
    import json
    
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('movies')
        
    def lambda_handler(event, context):
        title = event['title']
        response = table.get_item(
           Key={'title': title}
        )
        return {
            'statusCode': 200,
            'headers': {
                'Content-Type': 'application/json'
            },
            "body": response
        }
    
Implemente y pruebe la función de Lambda a través de las siguientes opciones:
- **Test event action** (Acción de evento de prueba): seleccione Create new event (Crear evento nuevo)
- **Event name** (Nombre de evento): ingrese `GetMovieTest`
- **Event sharing settings**(Configuración para compartir eventos): seleccione  **Private **(Privado)
- **Template** (Plantilla) (**opcional**): ingrese `hello-world`
- **Event JSON**: copie y pegue el siguiente código


      {
          "title": "The Shawshank Redemption"
      }
> Nota: si la prueba se ejecuta correctamente, verá una **Respuesta** con un **HTTPStatusCode** igual a 200 y un Elemento con los datos relacionados al **año** y al **título** de la tabla movie que utilizó.
