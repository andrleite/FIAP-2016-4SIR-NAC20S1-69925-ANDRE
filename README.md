# FIAP-2016-4SIR-NAC20S1-69925-ANDRE

**Nac de Sistemas Distribuídos**

  A Idéia dessa Nac como parte do projeto do TCC, é subir aplicações Java em containers Docker. Para isso utilizei o conceito de uber-jar onde todas as dependências do meu app estão embarcadas em meu pacote Jar.

  Nesse exemplo subi uma aplicação Spring que acessa dados do MongoDB via REST.

  Utilizei o micro framework do Spring, _"Spring Boot"_ , pois permite criar aplicações java standalone que são executadas através do comando java -jar, por exemplo, e nesse caso o arquivo jar gerado  irá conter um servidor embarcado como Tomcat, o que é bom para containers.

  Para o build utilizei Gradle.

##### Resumindo utilizei:
    - Editor de Texto ou IDE.
    - JDK 1.8 ou mais novo
    - mongodb
    - Gradle 2.3+
    - Docker (linux) ou Docker-machine (Mac)

Na Raiz do projeto, tenho o arquivo build.gradle onde passo as depêndencias da aplicação e o o arquivo de configuração do docker para gerar a imagem. Informo também o repositório do docke hub para pulicar a imagem no caso _group = 'andrleite'_ .

Para o mongodb basta subir um container com imagem padrão de mongodb.

<code>docker run -P -d --name mongodb mongo</code>

Subi o o mongo com opção -P expondo suas portas através de portas randômicas passando a imagem mongo.

Em **src/main/docker** criei o Dockerfile para imagem de docker

>  FROM anapsix/alpine-java:jre8
VOLUME /tmp
ADD accessing-mongodb-data-rest-0.1.0.jar app.jar
RUN sh -c 'touch /app.jar'
ENTRYPOINT ["java","-Dspring.data.mongodb.uri=mongodb://mongodb/person","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]

Para aplicação utilizei a imagem padrão alpine-java pois é uma imagem bem leve com jre 8 pois é bem leve. Adicionei o jar em app.jar . Para subir a aplicação utilizei o comando no ENTRYPOINT, passando o nome do container de mongo onde o app vai se conectar.

#### build

<code>build buildDocker</code>

Subindo applicação
<code>docker run -p 8080:8080 -d --name restapp --link mongodb:mongodb andrleite/fiap-2016-4sir-nac20s1-69925-andre</code>

Exemplos de uso da aplicação:

**Para Mac o ip é do Docker-machine ao invés de localhost**

> $ curl http://localhost:8080
{
  "_links" : {
    "people" : {
      "href" : "http://localhost:8080/people{?page,size,sort}",
      "templated" : true
    }
  }
}
<br />

> $ curl http://localhost:8080/people
{
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people{?page,size,sort}",
      "templated" : true
    },
    "search" : {
      "href" : "http://localhost:8080/people/search"
    }
  },
  "page" : {
    "size" : 20,
    "totalElements" : 0,
    "totalPages" : 0,
    "number" : 0
  }
}
<br />


> $ curl -i -X POST -H "Content-Type:application/json" -d '{  "firstName" : "Frodo",  "lastName" : "Baggins" }' http://localhost:8080/people
HTTP/1.1 201 Created
Server: Apache-Coyote/1.1
Location: http://localhost:8080/people/53149b8e3004990b1af9f229
Content-Length: 0
Date: Mon, 03 Mar 2014 15:08:46 GMT

<br />

> $ curl http://localhost:8080/people
{
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people{?page,size,sort}",
      "templated" : true
    },
    "search" : {
      "href" : "http://localhost:8080/people/search"
    }
  },
  "_embedded" : {
    "persons" : [ {
      "firstName" : "Frodo",
      "lastName" : "Baggins",
      "_links" : {
        "self" : {
          "href" : "http://localhost:8080/people/53149b8e3004990b1af9f229"
        }
      }
    } ]
  },
  "page" : {
    "size" : 20,
    "totalElements" : 1,
    "totalPages" : 1,
    "number" : 0
  }
}

<br />

> $ curl http://localhost:8080/people/53149b8e3004990b1af9f229
{
  "firstName" : "Frodo",
  "lastName" : "Baggins",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people/53149b8e3004990b1af9f229"
    }
  }
}
