---
title: Obtenha dados meteorológicos dos parceiros meteorológicos
description: Este artigo descreve como obter dados meteorológicos dos parceiros.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 39d37b1a032a386219a98a409f2eb04a6ccc6eca
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078739"
---
# <a name="get-weather-data-from-weather-partners"></a>Obtenha dados meteorológicos dos parceiros meteorológicos

O Azure FarmBeats ajuda-o a trazer dados meteorológicos do seu(s) fornecedor de dados meteorológicos usando um Connector Framework baseado em estivadores. Utilizando este quadro, os fornecedores de dados meteorológicos implementam um estivador que pode ser integrado com o FarmBeats. Atualmente, os seguintes fornecedores de dados meteorológicos são suportados:

  ![DTN](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)

  [DTN](https://www.dtn.com/dtn-content-integration/)

Os dados meteorológicos podem ser usados para gerar insights accuíveis e construir modelos AI/ML em FarmBeats.

## <a name="before-you-start"></a>Antes de começar

Para obter dados meteorológicos, certifique-se de que instalou FarmBeats. **A integração meteorológica é suportada na versão 1.2.11 ou superior**. Para instalar o Azure FarmBeats, consulte [a Instalação FarmBeats](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Permitir a integração meteorológica com os FarmBeats

Para começar a obter dados meteorológicos no seu hub de dados FarmBeats, siga os passos abaixo:

1. Vá ao seu hub de dados FarmBeats (https://farmbeatswebsite-api.azurewebsites.net/swagger)

2. Navegue para a API /Partner e faça um pedido DEM com a seguinte carga útil de entrada:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Por exemplo, para obter dados meteorológicos da DTN, use a carga útil abaixo. Pode modificar o nome e a descrição de acordo com a sua preferência.

   > [!NOTE]
   > O passo abaixo requer uma chave API, por favor contacte a DTN para obter o mesmo para a sua subscrição DTN.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > Para obter mais informações sobre o objeto Partner, consulte [o Apêndice](get-weather-data-from-weather-partner.md#appendix)

   O passo anterior irá providenciar os recursos para permitir que o Docker possa funcionar no ambiente FarmBeats do cliente.  

   Leva cerca de 10 a 15 minutos para a provisionar os recursos acima.

3. Verifique o estado do objeto /Partner que criou no passo 2. Para isso, faça um pedido GET sobre a API /Partner e verifique o **estado** do objeto do parceiro. Uma vez que o FarmBeats disposi o parceiro com sucesso, o estado está definido para **Ative**.

4. Navegue para /JobType API e faça um pedido GET no mesmo. Verifique os trabalhos meteorológicos que são criados como parte do processo de adição do Parceiro no passo 2. O **campo pipelineName** nos trabalhos meteorológicos será do seguinte formato: "parceiro-name_partner-type_job-nome".

5. Agora, a sua instância FarmBeats tem um parceiro de dados meteorológicos ativos e você pode executar empregos para solicitar dados meteorológicos para uma determinada localização (latitude/longitude) e um intervalo de data. O(s JobType) terá detalhes sobre quais os parâmetros necessários para executar trabalhos climáticos.

   Por exemplo, para a DTN, serão criados os seguintes Tipos de Trabalho:
   
   - get_dtn_daily_observations (Obtenha observações diárias para um local e período de tempo)
   - get_dtn_daily_forecasts (Obtenha previsões diárias para um período de localização e tempo)
   - get_dtn_hourly_observations (Obter observações horárias para um local e período de tempo)
   - get_dtn_hourly_forecasts (Obtenha previsões horárias para um período de localização e tempo)

6. Tome nota do **ID** e dos parâmetros do(s JobType).

7. Navegue para /Jobs API e faça um pedido POST sobre /Jobs com a seguinte carga útil de entrada:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Por exemplo, para executar **get_dtn_daily_observations**, utilize a seguinte carga útil:

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. O passo anterior irá executar os trabalhos meteorológicos, tal como definido no estivador parceiro e ingerir dados meteorológicos em FarmBeats. Pode verificar o estado do trabalho fazendo um pedido GET em /Jobs e procurar o **Estado atual** na resposta. Uma vez concluído, o Estado atual está definido para **Ser bem sucedido**.

## <a name="query-ingested-weather-data"></a>Dados meteorológicos ingeridos de consulta

Após o tempo de trabalhos estarem completos, você pode consultar dados meteorológicos ingeridos para construir modelos ou insights acccionáveis usando As APIs do FarmBeats Datahub REST.

### <a name="query-using-rest-api"></a>Consulta usando REST API

Para consultar os dados meteorológicos utilizando a API do FarmBeats REST, siga os passos abaixo:

1. No seu hub de dados FarmBeats https://yourdatahub.azurewebsites.net/swagger) (, navegue para a API /WeatherDataLocation e faça um pedido GET. A resposta terá o (s) object(s) de WeatherDataLocation criado para a localização (latitude/longitude) que foi especificado como parte da execução de trabalho. Tome nota do **ID** e do **tempoDataModelId** do(s) objeto(s).

2. Faça um GET/{id} em /WeatherDataModel API para o **weatherDataModelId** como indicado no passo 1. O "Weather Data Model" tem todos os metadados e detalhes sobre os dados meteorológicos ingeridos. Por exemplo, **a Weather Measure** dentro do objeto Weather Data **Model** tem detalhes sobre que informações meteorológicas são suportadas e em que tipos e unidades. Por exemplo,

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Tome nota da resposta da chamada GET/{id} para o Modelo de Dados Meteorológicos.

3. Navegue para **a Telemetria** API e faça um pedido DEM com a seguinte carga útil de entrada:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. A resposta que contenha dados meteorológicos disponíveis para o intervalo de tempo especificado será semelhante:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

No exemplo anterior, a resposta tem dados para dois timetamps juntamente com o nome da medida ("Temperatura") e os valores dos dados meteorológicos reportados nos dois picos de tempo. Terá de consultar o Modelo de Dados Meteorológicos associado (conforme descrito no passo 2 acima) para interpretar o tipo e a unidade dos valores relatados.

## <a name="appendix"></a>Anexo

|        Parceiro   |  Detalhes   |
| ------- | -------             |
|     DockerDetails - nome de imagem         |          Nome da imagem do Docker. Por exemplo, docker.io/mydockerimage (imagem em hub.docker.com) OU myazureacr.azurecr.io/mydockerimage (imagem no Registo do Contentor de Azure) e assim por diante. Se não for fornecido nenhum registo, o incumprimento é hub.docker.com      |
|          DockerDetails - imageTag             |         Nome da imagem do estivador. O padrão é "mais recente"     |
|  DockerDetails - credenciais      |  Credenciais para aceder ao estivador privado. Isto será fornecido pelo parceiro ao cliente   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU. Consulte [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) todas as máquinas virtuais Linux disponíveis. Os valores válidos são: "Pequeno", "ExtraLarge", "Grande", "Grande", "A8", "A9", "Médio", 'A5', 'A6', 'A7', 'STANDARD_D1', 'STANDARD_D2', 'STANDARD_D3', 'STANDARD_D4', 'STANDARD_D11', 'STANDARD_D12', 'STANDARD_D13', 'STANDARD_D14', 'A10', 'A11', 'STANDARD_D1_V2', 'STANDARD_D2_V2', 'STANDARD_D3_V2', 'STANDARD_D4_V2', 'STANDARD_D11_V2', 'STANDARD_D12_V2', 'STANDARD_D13_V2', 'STANDARD_D14_V2', 'STANDARD_G1', 'STANDARD_G2', 'STANDARD_G3', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G4 , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", "BASIC_A2", "BASIC_A3", "BASIC_A4", "STANDARD_A1", "STANDARD_A2", "STANDARD_A3", "STANDARD_A4", "STANDARD_A5", "STANDARD_A6", "STANDARD_A7", "STANDARD_A8", "STANDARD_A9", "STANDARD_A9" 'STANDARD_A10', 'STANDARD_A11', 'STANDARD_D15_V2', 'STANDARD_F1', 'STANDARD_F2', 'STANDARD_F4', 'STANDARD_F8', 'STANDARD_F16', 'STANDARD_NV6', 'STANDARD_NV12', 'STANDARD_NV24', 'STANDARD_NC6', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC24r' , "STANDARD_H8", "STANDARD_H8m", "STANDARD_H16", "STANDARD_H16m", "STANDARD_H16mr", "STANDARD_H16r", "STANDARD_A1_V2", "STANDARD_A2_V2", "STANDARD_A4_V2", "STANDARD_A8_V2", "STANDARD_A2m_V2", "STANDARD_A4m_V2", "STANDARD_A8m_V2", "STANDARD_M64ms", "STANDARD_M64ms", "STANDARD_M128s", STANDARD_D2_V3. **O padrão é "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - dedicadoComputerNodes   |  Não. de nós de computador dedicados para piscina de lote. O valor predefinido é 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Agente de nó de lote Azure SKU ID. Atualmente, apenas é suportado apenas o agente de nó de lote "batch.node.ubuntu 18.04".    |
| DockerDetails - partnerCredentials | credenciais para chamar parceiro API em estivador. O parceiro precisa de dar esta informação aos seus clientes com base no mecanismo auth que é suportado por exemplo. Nome de utilizador/palavra-passe ou Chaves API. |
| parceiroType | "Weather" (Outros tipos de parceiros em FarmBeats são "Sensor" e "Imagery")  |
|  name   |   Nome desejado do parceiro no sistema FarmBeats   |
|  descrição |  Descrição   |

## <a name="next-steps"></a>Próximos passos

Agora, já questionou os dados dos sensores da sua instância Azure FarmBeats. Agora, aprendam a [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para as vossas quintas.
