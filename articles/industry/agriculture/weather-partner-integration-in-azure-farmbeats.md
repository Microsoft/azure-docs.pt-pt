---
title: Integração de parceiros de meteorologia
description: Saiba como um fornecedor de dados meteorológicos pode integrar-se com o FarmBeats.
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93147084"
---
# <a name="weather-partner-integration-with-farmbeats"></a>Integração de parceiros meteorológicos com FarmBeats

Este artigo fornece informações sobre o componente Azure FarmBeats Connector Docker. Como fornecedor de dados meteorológicos, pode utilizar o Connector Docker para se integrar com o FarmBeats. Use as suas APIs para enviar dados meteorológicos para farmbeats. No FarmBeats, os dados podem ser usados para a fusão de dados e para a construção de modelos de aprendizagem automática ou modelos de inteligência artificial.

 > [!NOTE]
 > Neste artigo, utilizamos uma [implementação de referência](https://github.com/azurefarmbeats/noaa_docker) que foi construída utilizando datasets Azure Open e dados meteorológicos da Administração Oceânica e Atmosférica Nacional (NOAA). Também usamos a [imagem correspondente do Docker.](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)

Você deve fornecer uma [imagem ou programa Docker adequado](#docker-specifications) e hospedar a imagem do estivador em um registo de contentor que os clientes podem aceder. Forneça aos seus clientes as seguintes informações:

- URL de imagem de estivador
- Etiqueta de imagem de estiva
- Chaves ou credenciais para aceder à imagem do Docker
- Chaves ou credenciais API específicas do cliente para aceder aos dados do seu sistema
- Detalhes da VM SKU (Forneça estes detalhes se a sua imagem Docker tiver requisitos VM específicos. Caso contrário, os clientes podem escolher entre SKUs VM suportados em Azure.)

Os clientes usam esta informação do Docker para registar um parceiro meteorológico no seu caso FarmBeats. Para obter mais informações sobre como os clientes podem usar o Docker para ingerir dados meteorológicos em FarmBeats, consulte [obter dados dos parceiros meteorológicos.](./get-weather-data-from-weather-partner.md)

## <a name="connector-docker-development"></a>Desenvolvimento do Conector Docker

**REST Integração baseada em API**

As APIs FarmBeats contêm documentação técnica da Swagger. Para obter mais informações sobre as APIs e os respetivos pedidos ou respostas, consulte o [FarmBeats Swagger](https://aka.ms/farmbeatsswagger). 

Se já instalou FarmBeats, aceda ao seu FarmBeats Swagger em `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Note que *-api* está anexado ao nome do seu site FarmBeats. O ponto final da API é `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Datahub lib

FarmBeats fornece uma lib que você pode usar. A lib está atualmente disponível como [parte da implementação de referência.](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib) Mais tarde, estará disponível como SDK para vários idiomas.

### <a name="authentication"></a>Autenticação

**Autenticação com APIs farmbeats**

FarmBeats usa autenticação ao portador. Pode aceder às APIs fornecendo um token de acesso na secção de cabeçalho do pedido. Eis um exemplo:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Pode solicitar o token de acesso a partir de uma aplicação Azure Functions que está a ser executada na instância FarmBeats do cliente. O URL de Funções Azure é fornecido ao programa Docker como argumento. Você pode obter o token de acesso fazendo um `GET` pedido na URL. A resposta da URL contém o token de acesso. 

Utilize as funções de ajudante na lib do Datahub para obter o token de acesso. Para mais informações, consulte a [página do GitHub para obter a imagem do NoAA Docker](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

O token de acesso é válido apenas por algumas horas. Quando expirar, deve pedir de novo.

**Autenticação com APIs do lado do parceiro**

Para autenticar com as APIs do lado do parceiro enquanto o trabalho do Docker está em execução, os clientes precisam fornecer as credenciais durante o registo do parceiro. Eis um exemplo:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
O serviço API serializa este dict e armazena-o num [cofre chave.](../../key-vault/general/basic-concepts.md)

[A Azure Data Factory](../../data-factory/introduction.md) é usada para orquestrar trabalhos meteorológicos. Dá recursos para executar o código Docker. A Data Factory também fornece um mecanismo para empurrar os dados de forma segura para o VM onde funciona o trabalho do Docker. As credenciais da API são então armazenadas de forma segura no cofre da chave. 

As credenciais são lidas como cordas seguras do cofre da chave. São fornecidas como propriedades estendidas no diretório de trabalho do contentor Docker. O seu percurso de ficheiro é */mnt/working_dir/activity.jsem*. 

O código Docker pode ler as credenciais a partir de *activity.js* durante o tempo de execução para aceder a APIs do lado do parceiro para o cliente. No ficheiro JSON, as credenciais parecem este exemplo de código:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
A `partnerCredentials` credencial está disponível na forma como o cliente o forneceu durante o registo do parceiro.

O FarmBeats lib fornece funções de ajudante. Utilize estas funções para ler as credenciais das propriedades da atividade. Para mais informações, consulte a [página do GitHub para obter a imagem do NoAA Docker](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

O ficheiro só é utilizado enquanto o código Docker está em funcionamento. Depois de terminar o código, o ficheiro é apagado.

Para obter mais informações sobre como funcionam os oleodutos e atividades da Data Factory, consulte [o Schema e o mapeamento do tipo de dados.](../../data-factory/copy-activity-schema-and-type-mapping.md)

**HTTP solicitam cabeçalhos**

A tabela seguinte mostra os cabeçalhos de pedido mais comuns que precisa de especificar quando faz uma chamada da API para FarmBeats.

Cabeçalho | Descrição e exemplo
--- | ---
Content-Type | O formato de pedido. Exemplo: `Content-Type: application/<format>` <br/>Para as APIs do FarmBeats Datahub, o formato é JSON. Exemplo: ` Content-Type: application/json`
Autorização | O sinal de acesso que é necessário para fazer uma chamada da API. Exemplo: `Authorization: Bearer <Access-Token>`
Aceitar | O formato de resposta. Para as APIs do FarmBeats Datahub, o formato é JSON. Exemplo: `Accept: application/json`

## <a name="data-format"></a>Formato de dados

JSON é um formato comum de dados independente da linguagem que fornece uma simples representação de texto de estruturas de dados arbitrárias. Para mais informações, consulte [JSON.org.](https://json.org)

## <a name="docker-specifications"></a>Especificações do estivador

O programa Docker precisa de dois componentes: a bota e o trabalho. O programa pode ter mais do que um trabalho.

### <a name="bootstrap"></a>Método bootstrap

O componente bootstrap deve funcionar quando o cliente iniciar o registo do Docker no FarmBeats. Os seguintes argumentos `arg1` `arg2` (e) são passados para o programa:

- **FarmBeats API ponto final**: O ponto final da FarmBeats API para pedidos de API. Este ponto final faz chamadas de API para a implantação farmbeats.
- **URL de Funções Azure:** O seu próprio ponto final. Este URL fornece o seu token de acesso para APIs FarmBeats. Você pode pedir `GET` a este URL para obter o token de acesso.

A bootstrap cria os metadados que os utilizadores precisam para executar os seus trabalhos para obter dados meteorológicos. Para mais informações, consulte a implementação de [referência.](https://github.com/azurefarmbeats/noaa_docker) 

Se personalizar a *bootstrap_manifest.jsno* ficheiro, o programa de botas de referência criará os metadados necessários para si. O programa bootstrap cria os seguintes metadados: 

 > [!NOTE]
 > Se atualizar o *bootstrap_manifest.jsno* ficheiro como a [implementação](https://github.com/azurefarmbeats/noaa_docker) de referência descreve, não precisa de criar os seguintes metadados. O programa bootstrap utilizará o seu ficheiro manifesto para criar os metadados necessários.

- /**WeatherDataModel**: Os metadados WeatherDataModel representam dados meteorológicos. Corresponde aos conjuntos de dados que a fonte fornece. Por exemplo, um DailyForecastSimpleModel pode fornecer informações médias de temperatura, humidade e precipitação uma vez por dia. Em contraste, um DailyForecastAdvancedModel pode fornecer muito mais informações a granularidade horária. Pode criar vários modelos de dados meteorológicos.
- /**JobType**: A FarmBeats tem um sistema extensível de gestão de emprego. Como provedor de dados meteorológicos, terá vários conjuntos de dados e APIs (por exemplo, GetDailyForecasts). Pode ativar estes conjuntos de dados e APIs em FarmBeats utilizando o JobType. Após a criação de um tipo de trabalho, um cliente pode desencadear trabalhos desse tipo para obter dados meteorológicos para a sua localização ou para a sua quinta de interesse. Para obter mais informações, consulte JobType e Job APIs no [FarmBeats Swagger](https://aka.ms/farmbeatsswagger).

### <a name="jobs"></a>Tarefas

O componente Jobs é invocado sempre que um utilizador da FarmBeats executa um trabalho do /JobType que criou como parte do processo de bootstrap. O comando de execução do Docker para o trabalho é definido como parte do /JobType que criou.

O trabalho recolhe dados da fonte e empurra-os para farmbeats. Durante o processo de bootstrap, os parâmetros necessários para obter os dados devem ser definidos como parte de /JobType.

Como parte do trabalho, o programa deve criar uma /WeatherDataLocation baseada no /WeatherDataModel que foi criado durante o processo de bootstrap. A /WeatherDataLocation corresponde a uma localização (coordenadas de latitude e longitude) que o utilizador forneceu como parâmetro para o trabalho.

### <a name="object-details"></a>Detalhes do objeto

WeatherDataModel | Descrição |
--- | ---
Nome  | Nome do modelo de dados meteorológicos. |
Description  | Uma descrição significativa do modelo. |
Propriedades  | Propriedades adicionais definidas pelo fornecedor de dados. |
meteorologia Medidas > Nome  | O nome da medida meteorológica. Por exemplo, humidity_max. |
meteorologia Medidas > DataType  | Ou Double ou Enum. Se enum, medidaEnumDefinition é necessário. |
meteorologia Medidas > medidaEnumDefinition  | Obrigatório apenas se o DataType for enum. Por exemplo, `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` |
meteorologia Medidas > Tipo  | Tipo de dados de telemetria meteorológica. Por exemplo, RelativaHumidity. Os tipos definidos pelo sistema são AmbientTemperature, NoUnit, CO2, Depth, ElectricConductivity, LeafWetness, Comprimento, LiquidLevel, Nitrato, O2, PH, Fosfato, PointInTime, Potássio, Pressão, RainGauge, RelativaHumidity, Salinidade, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapopiration, e PAR. Para adicionar mais tipos, consulte a secção [Add ExtendedType](#add-extendedtype) neste artigo.
meteorologia Medidas > Unidade | Unidade de dados de telemetria meteorológica. As unidades definidas pelo sistema são NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentagem, PartsPerMillion, MicroMole, MicroMolesPerLiter, SiemensPerErPerMole, MilliSiemensPercentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolePerMeterSquaredPerSecond e InchesPerHour. Para adicionar mais unidades, consulte a secção [Add ExtendedType](#add-extendedtype) neste artigo.
meteorologia Medidas > AgregaçãoType  | O tipo de agregação. Os valores possíveis são Nenhum, Médio, Máximo, Mínimo, StandardDeviation, Sum e Total.
meteorologia Medidas > Profundidade  | A profundidade do sensor em centímetros. Por exemplo, a medição da humidade a 10 cm debaixo do solo.
meteorologia Medidas > Descrição  | Uma descrição significativa da medição. 

JobType | Descrição |
--- | ---
Nome  | O nome do trabalho. Por exemplo, Get_Daily_Forecast. O cliente vai gerir este trabalho para obter dados meteorológicos.|
pipelineDetails > parâmetros > nome  | Nome do parâmetro. |
pipelineDetails > parâmetros > tipo | O tipo de parâmetro. Os valores possíveis incluem String, Int, Float, Bool e Array. |
pipelineDetails > parâmetros > isRequired | O valor booleano do parâmetro. O valor é verdadeiro se o parâmetro for necessário. Caso contrário, o valor é falso. O padrão é verdadeiro. |
pipelineDetails > parâmetros > padrãoValue | Valor predefinido do parâmetro. |
pipelineDetails > parâmetros > descrição | Descrição do parâmetro. |
Propriedades  | Propriedades adicionais do fabricante.
Propriedades > programaRunCommand | O Docker dirige o comando. Este comando funciona quando o cliente gere o trabalho meteorológico. |

MeteorologiaDataLocation | Description |
--- | ---
weatherDataModelId  | ID do correspondente WeatherDataModel que foi criado durante o processo de bootstrap.|
localização  | Latitude, longitude e elevação. |
Name | O nome do objeto. |
Description | Descrição da localização dos dados meteorológicos. |
farmId | (Opcional) Identificação da quinta. O cliente fornece esta identificação como parte do parâmetro de trabalho. |
Propriedades  | Propriedades adicionais do fabricante.

Para obter mais informações sobre os objetos e suas propriedades, consulte o [FarmBeats Swagger.](https://aka.ms/FarmBeatsSwagger)

> [!NOTE]
> As APIs devolvem IDs únicos para cada instância que é criada. O tradutor para a gestão de dispositivos e sincronização de metadados precisa de reter este ID.

**Sincronização de metadados**

O componente Connector Docker deve ser capaz de enviar atualizações sobre os metadados. Por exemplo, deve enviar atualizações quando o fornecedor meteorológico adicionar novos parâmetros a um conjunto de dados ou quando é adicionada uma nova funcionalidade, como uma nova previsão de 30 dias.

> [!NOTE]
> A eliminação não é suportada por metadados no modelo de dados meteorológicos.
>
> Para atualizar metadados, tem de recorrer ao `/Get/{ID}` modelo de dados meteorológicos. Atualize as propriedades alteradas e, em seguida, faça um `/Put/{ID}` para reter quaisquer propriedades que o utilizador conjunto.

## <a name="weather-data-telemetry-specifications"></a>Especificações de dados meteorológicos (telemetria)

Os dados meteorológicos estão mapeados para uma mensagem canónica que é empurrada para um centro de eventos Azure para processamento. O Azure Event Hubs é um serviço que permite a ingestão de dados em tempo real (telemetria) a partir de dispositivos e aplicações conectados. 

Para enviar dados meteorológicos para farmbeats, crie um cliente que envie mensagens para um centro de eventos em FarmBeats. Para obter mais informações, consulte [enviar telemetria para um centro de eventos.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

A seguinte amostra O código Python envia telemetria como cliente para um centro de eventos especificado.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Aqui está o formato de mensagem canónica:

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Aqui está um exemplo de uma mensagem de telemetria:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Resolução de problemas e gestão de erros

### <a name="error-logging"></a>Registo de erros

O trabalho de parceiro funciona no quadro de emprego existente. Assim, os erros são registados da mesma forma que erros para outros trabalhos pré-existentes da FarmBeats (como GetFarmData e SensorPlacement). A atividade da Data Factory que funciona dentro dos registos do gasoduto Data Factory e `STDERR` `STDOUT` . Ambos os ficheiros estão disponíveis na `datahublogs-xxx` conta de armazenamento dentro do grupo de recursos FarmBeats.

O Datahub lib fornece funções de ajudante para permitir o registo como parte de registos globais do Datahub. Para mais informações, consulte a [página do GitHub para obter a imagem do NoAA Docker](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

### <a name="troubleshooting-and-support"></a>Resolução de problemas e apoio

Se o cliente não puder receber dados meteorológicos no caso FarmBeats, forneça suporte e um mecanismo para resolver o problema.

## <a name="add-extendedtype"></a>AdicionarType Estendido

O FarmBeats suporta a adição de novos tipos e unidades de medida de sensor. Pode adicionar novas unidades ou tipos atualizando o *bootstrap_manifest.jsficheiro* na [implementação](https://github.com/azurefarmbeats/noaa_docker)de referência .

Siga estes passos para adicionar um novo tipo WeatherMeasure, por exemplo, PrecipitationDepth.

1. Faça um `GET` pedido sobre /ExtendedType utilizando a consulta `filter - key = WeatherMeasureType` .
2. Note a identificação do objeto devolvido.
3. Adicione o novo tipo à lista no objeto devolvido. Faça um `PUT` pedido no /ExtendedType{ID} com a seguinte nova lista. A carga útil de entrada deve ser a mesma que recebeu anteriormente. A nova unidade deve ser anexada no final da lista de valores.

Para obter mais informações sobre a API /ExtendedType, consulte o [FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Passos seguintes

Agora tem um componente connector Docker que se integra com farmbeats. Em seguida, descubra como [obter dados meteorológicos](get-weather-data-from-weather-partner.md) usando a sua imagem docker em FarmBeats. 
