# Criando uma API com .NET integrada ao MongoDB.

Requisitos:
<li>VS code;
<li>.NET instalado pelo SDK + runtime(linux users);
<li>Conta no MongoDB;
<li>Postman;


Checar versão do .NET:

``` dotnet --version ````


Iniciando o projeto no repositorio destino

``` dotnet new api -n <nome da api>```

Ir até a pasta do projeto:

```cd <nomeDoProjeto>``` 

Abrir o VS code:

```code .```

Deletar a classe e controller weather que vem por padrão

Com a extensão C# no VS code é possível criar classes fácilmente clicando com botão direito do mouse e escolhendo C# new class. Remover algumas configurações do program.cs que não serão utilizadas, como  o autenticador, por exemplo. Feito isso, partir para o MongoDB e, no cluster, clicar em Database Access e criar um novo usuário de database.

Ir no network access e configurar IP de acesso escolhendo "allow access from anywhere" - lembrando que é perigoso pois o cluster ficará aberto, mas como se está usando para fins didáticos está ok. O MongoDB inclusive envia um email alertando tal prática. Voltar em cluster, clicar em connect e clicar em connect your application. Feito isso, escolher o driver, nesse caso, C# e a versão mais recente. O Mongo vai gerar a connection string, só copiar e colar em algum notepad file por enquanto.


Para usar essa conexão, é preciso criar uma connection strings no appsettings.json, portanto foi adicionar:

``` "ConnectionString": "<add link gerado aqui>", "NomeBanco": "<NomeBancoAqui>" ``` 


Criar uma pasta para Data, criar outra pasta nela -> Collections e dentro desta uma classe chamada Infectado.cs. Nesta classe foram criados objetos dataNascimento, sexo, latitude e longitude. Sendo esses dois ultimos para ser utilizada o GeoJson2DGeographicCoordinates e possívelmente criar um índice de infectados por região, localização. Isso é possível através do MongoDB driver, que é um pacote criado pela MongoDB.

Para instalar o MongoDB driver, ir para paste do projeto no terminar e executar o comando:

``` dotnet add package MongoDB.Driver```


Após instalar o package, o ideal é acessar o <a href="https://www.docs.mongodb.com/drivers">site</a> e consultar as funcionalidades que, inclusive, incluem outras linguagens além de C#. Com a instalação do package concluída, só importá-la e o VS code vai reconhecer a collections no banco.


Seguindo o conceito do context do entity framework, criar uma classe chamada MongoDB.cs. Injetar uma interface IConfiguration'(classe dotnet), que vai colocar as variaveis ambientes, configs do appsettings.json, o que for relacionado a configuracao será adicionado à classe pela interface. no código, tais funções:


```
var settings = MongoClientSettings.FromUrl(new MongoUrl(Configuration[ConnectionString<adicionar:+exemplo:mongo>]));
var cliente = new MongoClient(Settings);
DB = client.GetDatabase(configuration["NomeBanco"]);
MapClasses();

```
 

DB = public IMongoDatabase DB {get;}

Irá buscar a string da connection com o MongoDB. Com base na connections, ele vai configurar, instanciando o objeto MongoClient e pelo cliente, dar um getdabase no banco de dados (coronavirus).Com entity framework, existem duas formas de mapear as entidades, as classes para o banco:

1- Usando data notations -> [nome]
2- Fluent Map, escrevendo o código diretamente, como foi feito. Utilizou-se i.AutoMap(), i.SetIgnoreExtraElements(true);

Criar uma model para retorno, poderia ser feito pela 'infectado' mas por questões de boas práticas, é melhor criar uma dto.Portanto, criar pasta Models e class InfectadoDto.cs. Ao criar uma DTO, é possível retornar uma consulta com várias informações de uma vez só.

Latitude e Longitude separadas para que não mostrar que se está usando classe do .net para localização.

Agora criar uma controller, para tal, ir na pasta Controller e criar a classe InfectadoController - por questões de convenção - a classe controller tem que ter 'controller' no final do file. Usou-se anotações, collections, para deixar claro que a classe é um controller. A controller geralmente é o primeiro step da route, route essa para chegar ao endpoint. Lembrar de adicionar o "AddSingleton" ao startup.cs para que o Mongo seja started. 

OBS: Poderia ser utilizado o AddSCoped, o singleton foi utilizado para criar uma instancia unica e mante-la em memória podendo ser requisitada. O singleton normalmente não é recomendando. Entretanto, foi usado neste examplo por conta dos maps.

Para finalizar, injetar dependência no controller para não precisar ficar instanciando tudo na mão e desacoplar o código.
 
 ``` "ConnectionString": "inserirlinkdocluster" ```
