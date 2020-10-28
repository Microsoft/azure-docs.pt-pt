---
title: Integração de parceiros de meteorologia
description: Este artigo descreve como um fornecedor de dados meteorológicos pode integrar-se com o FarmBeats
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: dd5d05ff6ed2368308f90f61ea0a6f107e43acd7
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740783"
---
# <a name="weather-partner-integration"></a>Integração de parceiros de meteorologia

Este artigo fornece informações sobre o **Connector** componente estivador Azure FarmBeats que os fornecedores de dados meteorológicos podem desenvolver para se integrarem com os FarmBeats, alavancando as suas APIs e enviando dados meteorológicos para farmbeats. Uma vez que os dados estão disponíveis em FarmBeats, pode ser usado para fusão de dados e para construir modelos de machine learning/inteligência artificial.

 > [!NOTE]
 > Para efeitos desta documentação, utilizaremos uma implementação de referência construída utilizando a NOAA a partir de Azure Open Datasets e está disponível em [https://github.com/azurefarmbeats/noaa_docker](https://github.com/azurefarmbeats/noaa_docker) .
 > A imagem de estiva correspondente está disponível em [https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)

Um parceiro meteorológico terá de fornecer uma imagem/programa de estivador (com especificações abaixo mencionadas) e hospedar a imagem do estivador num registo de contentores acessível pelos clientes. O parceiro meteorológico terá de fornecer aos seus clientes as seguintes informações:

- URL de imagem de estivador
- Etiqueta de imagem de estiva
- Chaves/Credenciais para aceder à imagem do estivador
- Chaves/Credenciais API específicas do cliente para aceder aos dados do sistema do parceiro meteorológico
- Detalhes da VM SKU (Os parceiros podem fornecer isto caso o seu estivador tenha requisitos VM específicos, caso contrário os clientes podem escolher entre SKUs VM suportados em Azure)

Utilizando as informações acima do estivador, o cliente registará um parceiro meteorológico na sua instância FarmBeats. Para saber mais sobre como os clientes podem usar o estivador para ingerir dados meteorológicos em FarmBeats, consulte o guia para [obter dados meteorológicos](./get-weather-data-from-weather-partner.md)

## <a name="connector-docker-development"></a>Desenvolvimento do estivador de conector

**REST Integração baseada em API**

As APIs FarmBeats contêm documentação técnica da Swagger. Para obter informações sobre todas as APIs e os respetivos pedidos ou respostas, consulte [FarmBeats Swagger](https://aka.ms/farmbeatsswagger). 

Se você instalou FarmBeats, você pode acessá-lo em `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Note que "-api" está anexado ao nome do seu site FarmBeats.
O ponto final da API será: `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Datahub lib

FarmBeats fornecerá uma lib que pode ser usada pelo parceiro meteorológico. A lib está atualmente disponível como parte da implementação de referência [aqui.](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib) No futuro, o mesmo estará disponível como um SDK para vários idiomas.

### <a name="authentication"></a>Autenticação

**Autenticação com APIs farmbeats**

FarmBeats utiliza a autenticação do portador e as APIs podem ser acedidas fornecendo um token de acesso na secção de cabeçalho do pedido como abaixo:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

O token de acesso pode ser solicitado a partir de uma Função Azure que está em execução na instância FarmBeats do cliente. O URL da Função Azure será fornecido ao programa de estivadores como argumento e o token de acesso pode ser obtido fazendo um pedido GET no URL. A resposta da URL conterá o token de acesso. O Datahub lib fornece funções de ajudante para permitir aos parceiros obter o token de acesso. Mais detalhes [aqui.](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py)

O token de acesso é válido apenas por algumas horas e precisa de ser reenquisitado quando expirado.

**Autenticação com APIs do lado do parceiro**

Para permitir que os clientes autentem com as APIs do lado do parceiro durante a execução do estivador, os clientes precisam fornecer as credenciais durante o registo do Parceiro da seguinte forma:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
O serviço API serializa este dict e armazena-o num [KeyVault.](../../key-vault/general/basic-concepts.md)

[A Azure Data Factory](../../data-factory/introduction.md) é usada para orquestrar trabalhos meteorológicos e gira recursos para executar o código do estivador. Também fornece um mecanismo para empurrar os dados de forma segura para o VM onde o trabalho de estivador executa. As credenciais API, que estão agora armazenadas de forma segura no KeyVault são lidas como cordas seguras do KeyVault e disponibilizadas como propriedades estendidas no diretório de trabalho do contentor de estivadores como activity.js(caminho para o ficheiro é "/mnt/working_dir/activity.json") O código do estivador pode ler as credenciais deste ficheiro durante o período de tempo de funcionamento para aceder às APIs do lado do parceiro em nome do cliente. As credenciais estarão disponíveis no ficheiro da seguinte forma:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
Note que os "partnerCredentials" estarão disponíveis da forma exata que foi fornecida pelo cliente durante o registo do Parceiro

O FarmBeats lib fornece funções de ajudante para permitir aos parceiros ler as credenciais das propriedades da atividade. Mais detalhes [aqui.](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py)

A duração do ficheiro é apenas durante a execução do código do estivador e será apagada após o fim do estivador.

Para mais detalhes sobre como funcionam os oleodutos e atividades da ADF, [https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping](../../data-factory/copy-activity-schema-and-type-mapping.md) consulte.

**HTTP solicitam cabeçalhos**

Aqui estão os cabeçalhos de pedido mais comuns que precisam de ser especificados quando fizer uma chamada da API para FarmBeats.

**Cabeçalho** | **Descrição e exemplo**
--- | ---
Content-Type | O formato de pedido (Tipo de Conteúdo: aplicação/ <format> ). Para as APIs do FarmBeats Datahub, o formato é JSON. Tipo de conteúdo: aplicação/json
Autorização | Especifica o token de acesso necessário para fazer uma chamada da API. Autorização: <Access-Token> portador
Aceitar | O formato de resposta. Para as APIs do FarmBeats Datahub, o formato é JSON. Aceitar: candidatura/json

## <a name="data-format"></a>Formato de dados

JSON é um formato comum de dados independente da linguagem que fornece uma simples representação de texto de estruturas de dados arbitrárias. Para mais informações, consulte [json.org.](http://json.org)

## <a name="docker-specifications"></a>Especificações do estivador

O programa estivador precisa de ter dois componentes: **Bootstrap** e **Jobs** . Pode haver mais do que um Trabalho.

### <a name="bootstrap"></a>Método bootstrap

Este componente deve ser executado quando o cliente iniciar o registo do estivador no FarmBeats. Os argumentos (arg1, arg2) que serão transmitidos a este programa são:

- FarmBeats API Endpoint: FarmBeats API ponto final para pedidos de API: Este é o ponto final para fazer chamadas API para a implantação farmbeats.
- URL de função Azure: Este é o seu próprio ponto final pessoal que lhe dará o seu token de acesso para APIs FarmBeats. Basta chamar um GET neste url, irá buscar-lhe o token de acesso na sua resposta.

A responsabilidade da bootstrap é criar os metadados necessários para que os utilizadores possam executar os seus trabalhos para obter dados meteorológicos. Consulte aqui a [implementação](https://github.com/azurefarmbeats/noaa_docker)de referência. Pode atualizar o bootstrap_manifest.jsno ficheiro de acordo com as suas necessidades e o programa de botas de referência criará os metadados necessários para si.

Os metadados seguintes são criados como parte deste processo. 

 > [!NOTE]
 > **Por favor, note** que se atualizar o bootstrap_manifest.jsno ficheiro como mencionado na implementação de [referência,](https://github.com/azurefarmbeats/noaa_docker)não precisa de criar os metadados abaixo, uma vez que a bootstrap criará o mesmo com base no seu ficheiro manifesto.

- /**WeatherDataModel** : A WeatherDataModel é um modelo de representação de dados meteorológicos e corresponde a diferentes conjuntos de dados fornecidos pela fonte. Por exemplo, um DailyForecastSimpleModel pode fornecer informações de temperatura média, humidade e precipitação uma vez por dia, enquanto um DailyForecastAdvancedModel pode fornecer muito mais informações a granularidade horária. Pode criar qualquer número de WeatherDataModels.
- /**JobType** : A FarmBeats tem um sistema extensível de gestão de emprego. Como fornecedor de dados meteorológicos, terá diferentes conjuntos de dados/APIs (por exemplo, GetDailyForecasts) - pode capacitá-los em FarmBeats como JobType. Uma vez criado um JobType, um cliente pode desencadear Empregos desse tipo para obter dados meteorológicos para a sua localização/fazenda de interesse (ver JobType e Job APIs em [FarmBeats Swagger).](https://aka.ms/farmbeatsswagger)

### <a name="jobs"></a>Tarefas

Este componente será invocado sempre que um utilizador do FarmBeats executa um trabalho do seu /JobType que criou como parte do processo de bootstrap. O comando de execução do estivador para o trabalho é definido como parte do **/JobType** que criou.
- A responsabilidade do trabalho será recolher dados da fonte e empurrá-lo para farmbeats. Os parâmetros necessários para obter os dados devem ser definidos como parte de /JobType no processo de bootstrap.
- Como parte do trabalho, o programa terá de criar uma **/WeatherDataLocation** baseada no /WeatherDataModel que foi criado como parte do processo de bootstrap. A **/WeatherDataLocation** corresponde a uma localização (lat/long) que é fornecida pelo utilizador como parâmetro para o trabalho.

### <a name="details-of-the-objects"></a>Detalhes dos objetos

  WeatherDataModel | Descrição |
  --- | ---
  Nome  | Nome do modelo de dados meteorológicos |
  Descrição  | Forneça uma descrição significativa do modelo. |
  Propriedades  | Propriedades adicionais definidas pelo fornecedor de dados. |
  meteorologia Medidas > Nome  | O nome da medida meteorológica. Por exemplo, humidity_max |
  meteorologia Medidas > DataType  | ou Double ou Enum. Se enum, medidaEnumDefinition é necessário |
  meteorologia Medidas > medidaEnumDefinition  | Só é necessário se o DataType for o Enum. Por exemplo { "NoRain": 0, "Neve": 1, "Drizzle": 2, "Rain": 3 } |
  meteorologia Medidas > Tipo  | tipo de dados de telemetria meteorológica. Por exemplo, "RelativaHumidity". Seguem-se os tipos definidos pelo sistema: AmbientTemperature, NoUnit, CO2, Depth, ElectricConductivity, LeafWetness, Comprimento, LiquidLevel, Nitrato, O2, PH, Fosfato, PointInTime, Potássio, Pressão, RainGauge, RelativaHumidity, Salinidade, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapopiration, PAR. Para adicionar mais, consulte a API /ExtendedType ou na [secção Add Types and Units](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) abaixo
  meteorologia Medidas > Unidade | Unidade de dados de telemetria meteorológica. Seguem-se as unidades definidas pelo sistema: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercúrio, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentagem, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPercentiMeter, Centibar, Deci SiemensPerMeter, KiloPascal, VolumetricIonContent, Litro, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond e InchesPerHour. Para adicionar mais, consulte a API /ExtendedType ou na [secção Add Types and Units](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) abaixo.
  meteorologia Medidas > AgregaçãoType  | Qualquer um de Nenhum, Média, Máximo, Mínimo, StandardDeviation, Soma, Total
  meteorologia Medidas > Profundidade  | A profundidade do sensor em centímetros. Por exemplo, a medição da humidade a 10 cm debaixo do solo.
  meteorologia Medidas > Descrição  | Fornecer uma descrição significativa da medição. |
  **JobType** | **Descrição** |
  Nome  | nome do Trabalho - por exemplo, Get_Daily_Forecast; o trabalho que o cliente vai correr para obter dados meteorológicos|
  pipelineDetails > parâmetros > nome  | nome do parâmetro |
  pipelineDetails > parâmetros > tipo | ou de String, Int, Float, Bool, Array |
  pipelineDetails > parâmetros > isRequired | booleano; verdadeiro, se necessário parâmetro, falso se não; padrão é verdade |
  pipelineDetails > parâmetros > padrãoValue | Valor predefinido do parâmetro |
  pipelineDetails > parâmetros > descrição | Descrição do parâmetro |
  Propriedades  | Propriedades adicionais do fabricante.
  Propriedades > **programaRunCommand** | estivador executar comando - este comando será executado quando o cliente executar a tarefa meteorológica. |
  **MeteorologiaDataLocation** | **Descrição** |
  weatherDataModelId  | ID do correspondente WeatherDataModel que foi criado durante a bootstrap|
  localização  | representa latitude, longitude e elevação |
  Nome | Nome do objeto |
  Descrição | Descrição |
  farmId | **opcional** ID da quinta - fornecido pelo cliente como parte do parâmetro de trabalho |
  Propriedades  | Propriedades adicionais do fabricante.

 Para obter informações sobre cada um dos objetos e suas propriedades, consulte [Swagger.](https://aka.ms/FarmBeatsSwagger)

 > [!NOTE]
 > As APIs devolvem IDs únicos para cada instância criada. Este ID precisa de ser retido pelo Tradutor para a gestão do dispositivo e sincronização de metadados.

**Sincronização de metadados**

O estivador do Conector deve ter a capacidade de enviar atualizações sobre os metadados. Exemplos de cenários de atualização são – Adição de novos parâmetros meteorológicos no conjunto de dados do fornecedor meteorológico, Adição de funcionalidades (por exemplo. Adição de Previsão de 30 dias)

> [!NOTE]
> A eliminação não é suportada para os metadados, por exemplo. modelo de dados meteorológicos.
>
> Para atualizar metadados, é obrigatório ligar /Get/{ID} no modelo de dados meteorológicos, atualizar as propriedades alteradas e, em seguida, fazer um /Put/{ID} para que quaisquer propriedades definidas pelo utilizador não se percam.

## <a name="weather-data-telemetry-specifications"></a>Especificações de Dados Meteorológicos (Telemetria)

Os dados meteorológicos são mapeados para uma mensagem canónica que é empurrada para um Hub de Eventos Azure para processamento. O Azure Event Hubs é um serviço que permite a ingestão de dados em tempo real (telemetria) a partir de dispositivos e aplicações conectados. Para enviar dados meteorológicos para farmbeats, você precisará criar um cliente que envie mensagens para um centro de eventos em FarmBeats. Para saber mais sobre o envio de telemetria, consulte [o Envio de telemetria para um centro de eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Aqui está uma amostra do código Python que envia telemetria como cliente para um centro de eventos especificado.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

O formato de mensagem canónica é o seguinte:

```json
{
   "weatherstations": [
   {
   "id": "id of the WeatherDataLocation",
   "weatherdata": [
   {
     "timestamp": "timestamp of the data. For historical, this is the time for which the observations are sent. For forecast this is the time for which data is forecasted. Format is ISO 8601. Default time-zone is UTC",
     "predictiontimestamp": "timestamp on which the forecast data is predicted i.e time of prediction. Required only for forecast data. Format is ISO 8601. Default timezone is UTC ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Por exemplo, aqui está uma mensagem de telemetria:

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

**Registo de erros**

Uma vez que o trabalho de parceiro será gerido no quadro de emprego existente – os erros são registados da mesma forma que os erros para outros trabalhos pré-existentes em FarmBeats (como GetFarmData, SensorPlacement, etc.). A atividade ADF que funciona dentro dos registos do gasoduto ADF regista tanto stderr como STDOUT. Ambos os ficheiros estão disponíveis na conta de armazenamento "datahublogs-xxx" dentro do grupo de recursos FarmBeats.

O Datahub lib fornece funções de ajudante para permitir o registo como parte de registos globais do Datahub. Mais detalhes [aqui.](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py)

**Opção de resolução de problemas ou suporte**

No caso de o cliente não conseguir receber dados meteorológicos na instância FarmBeats especificada, o parceiro meteorológico deve fornecer suporte e um mecanismo para resolver os mesmos problemas.

## <a name="add-extendedtype"></a>AdicionarType Estendido

O FarmBeats suporta a adição de novos tipos e unidades de medida de sensor. Note que um parceiro meteorológico pode adicionar novas unidades/tipos atualizando o bootstrap_manifest.jsem arquivo na implementação de referência [aqui](https://github.com/azurefarmbeats/noaa_docker)

Para adicionar um novo tipo weatherMeasure, por exemplo "PrecipitationDepth", siga os passos abaixo.

1. Faça um pedido GET em /ExtendedType com o filtro de consulta - chave = WeatherMeasureType
2. Note a identificação do objeto devolvido.
3. Adicione o novo tipo à lista no objeto devolvido e faça um pedido PUT no /ExtendedType{ID} com a seguinte nova lista. A carga útil de entrada deve ser a mesma que a resposta recebida acima e a nova unidade anexada no final da lista de valores.

Para obter mais informações sobre a API /ExtendedType, consulte [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Passos seguintes

Agora tens um estivador connector que se integra com o FarmBeats. Em seguida, você pode ver como obter dados meteorológicos usando o seu estivador em FarmBeats. Consulte [os dados meteorológicos.](get-weather-data-from-weather-partner.md)
