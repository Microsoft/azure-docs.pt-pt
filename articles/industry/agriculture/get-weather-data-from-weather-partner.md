---
title: Obtenha dados meteorológicos dos parceiros meteorológicos
description: Este artigo descreve como obter dados meteorológicos dos parceiros.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 2705e3d724530e879dd02346392f17fda274913a
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675323"
---
# <a name="get-weather-data-from-weather-partners"></a>Obtenha dados meteorológicos dos parceiros meteorológicos

O Azure FarmBeats ajuda-o a trazer dados meteorológicos dos seus fornecedores de dados meteorológicos utilizando uma estrutura de conector baseada em Docker. Utilizando este quadro, os fornecedores de dados meteorológicos implementam um Docker que pode ser integrado com o FarmBeats. Atualmente, o seguinte fornecedor de dados meteorológicos é suportado.

  ![Parceiros FarmBeats](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

Os dados meteorológicos podem ser usados para gerar insights accuíveis e construir modelos de IA ou ML em FarmBeats.

## <a name="before-you-start"></a>Antes de começar

Para obter dados meteorológicos, certifique-se de que [instalou FarmBeats](./install-azure-farmbeats.md). A integração meteorológica é suportada nas versões 1.2.11 ou superior. 

## <a name="enable-weather-integration-with-farmbeats"></a>Permitir a integração meteorológica com os FarmBeats

Para começar a obter dados meteorológicos no seu Datahub FarmBeats:

1. Vá ao seu FarmBeats Datahub `https://farmbeatswebsite-api.azurewebsites.net/swagger` Swagger.

2. Vá à API /Partner e, em seguida, faça um pedido DEM. Utilize a seguinte carga útil de entrada:

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

   Por exemplo, para obter dados meteorológicos da DTN, utilize a seguinte carga útil. Pode modificar o nome e a descrição de acordo com a sua preferência.

   > [!NOTE]
   > O passo seguinte requer uma chave API. Para obter uma chave para a sua subscrição DTN, contacte a DTN.

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
   > Para obter mais informações sobre o objeto do parceiro, consulte o [apêndice](get-weather-data-from-weather-partner.md#appendix) neste artigo.

   O passo anterior irá providenciar os recursos para permitir que Docker corra no ambiente FarmBeats do cliente.  

   Leva cerca de 10 a 15 minutos para a provisionar os recursos.

3. Verifique o estado do objeto /Parceiro que criou no passo anterior. Para verificar o estado, faça um pedido GET na API /Partner e verifique o estado do objeto do parceiro. Após a FarmBeats prever o parceiro com sucesso, o estado está definido para **Ative** .

4. Na API /JobType, faça um pedido GET. Verifique os trabalhos meteorológicos que criou anteriormente, no processo de adição de parceiros. Nos trabalhos meteorológicos, o **campo pipelineName** tem o seguinte formato: **nome name_partner-type_job parceiro** .

      Agora, a sua instância FarmBeats tem um parceiro de dados meteorológicos ativos. Você pode executar trabalhos para solicitar dados meteorológicos para uma determinada localização (latitude e longitude) e um intervalo de datas. Os tipos de emprego terão detalhes sobre quais os parâmetros necessários para executar trabalhos climáticos.

      Por exemplo, para a DTN, serão criados os seguintes tipos de emprego:
   
      - **get_dtn_daily_observations** : Obtenha observações diárias para um local e período de tempo.
      - **get_dtn_daily_forecasts** : Obtenha previsões diárias para um período de localização e tempo.
      - **get_dtn_hourly_observations:** Obtenha observações de hora a hora para um local e período de tempo.
      - **get_dtn_hourly_forecasts:** Obtenha previsões horárias para um período de localização e tempo.

6. Tome nota do ID e dos parâmetros dos tipos de trabalho.

7. Vá à API /Jobs e faça um pedido de CORREIO sobre /Jobs. Utilize a seguinte carga útil de entrada:

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

   Por exemplo, para executar **get_dtn_daily_observations** , utilize a seguinte carga útil:

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

8. O passo anterior executa os trabalhos meteorológicos como definido no parceiro Docker e ingere dados meteorológicos em FarmBeats. Pode verificar o estado do trabalho fazendo um pedido GET em /Jobs. Na resposta, procure o **Estado atual.** Quando terminar, **o Estado atual** está definido para Ser bem **sucedido** .

## <a name="query-ingested-weather-data"></a>Dados meteorológicos ingeridos de consulta

Após o tempo de trabalhos estarem completos, você pode consultar dados meteorológicos ingeridos para construir modelos ou insights acccionáveis usando As APIs do FarmBeats Datahub REST.

Para consultar os dados meteorológicos utilizando uma API de REST FarmBeats:

1. No seu FarmBeats Datahub [Swagger,](https://yourdatahub.azurewebsites.net/swagger)vá à API /WeatherDataLocation e faça um pedido GET. A resposta inclui os objetos /WeatherDataLocation criados para a localização (latitude e longitude) que o trabalho de execução especificou. Tome nota do **ID** e do **tempoDataModelId** dos objetos.

2. Faça um pedido GET/{id} sobre a API /WeatherDataModel para o **weatherDataModelId** como fez anteriormente. O modelo de dados meteorológicos mostra todos os metadados e detalhes sobre os dados meteorológicos ingeridos. Por exemplo, no objeto do modelo de dados meteorológicos, a medida meteorológica detalha quais as informações meteorológicas suportadas e em que tipos e unidades. Por exemplo:

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

   Tome nota da resposta da chamada GET/{id} para o modelo de dados meteorológicos.

3. Vá à API de Telemetria e faça um pedido de CORREIO. Utilize a seguinte carga útil de entrada:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    A resposta mostra os dados meteorológicos disponíveis para o intervalo de tempo especificado:

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

No exemplo anterior, a resposta mostra dados relativos a dois carimbos temporais. Também mostra o nome da medida (temperatura) e os valores dos dados meteorológicos reportados nos dois selos temporais. Consulte o modelo de dados meteorológicos associados para interpretar o tipo e a unidade dos valores reportados.

## <a name="troubleshoot-job-failures"></a>Falhas no trabalho de resolução de problemas

Para resolver problemas de trabalho, [verifique os registos de trabalho.](troubleshoot-azure-farmbeats.md#weather-data-job-failures)


## <a name="appendix"></a>Apêndice

|        Parceiro   |  Detalhes   |
| ------- | -------             |
|     DockerDetails - nome de imagem         |          Nome da imagem do Docker. Por exemplo, docker.io/mydockerimage (imagem em hub.docker.com) ou myazureacr.azurecr.io/mydockerimage (imagem no Registo do Contentor de Azure) e assim por diante. Se não for fornecido nenhum registo, o padrão é hub.docker.com.      |
|          DockerDetails - imageTag             |         Nome da imagem do Docker. O padrão é "o mais recente".     |
|  DockerDetails - credenciais      |  Credenciais para aceder ao estivador privado. O parceiro fornece as credenciais.   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU. Para mais informações, consulte [todas as máquinas virtuais Linux disponíveis.](../../virtual-machines/sizes.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) <BR> <BR> Valores válidos são 'Pequeno', 'ExtraLarge', 'Grande', 'A8', 'A9', 'Médio', 'A5', 'A6', 'A7', 'STANDARD_D1', 'STANDARD_D2', 'STANDARD_D3', 'STANDARD_D4', 'STANDARD_D11', 'STANDARD_D12', 'STANDARD_D13', 'STANDARD_D14', 'A10', 'A11', 'STANDARD_D1_V2', 'STANDARD_D2_V2', 'STANDARD_D3_V2', 'STANDARD_D4_V2', 'STANDARD_D11_V2', 'STANDARD_D12_V2', 'STANDARD_D13_V2', 'STANDARD_D14_V2', 'STANDARD_G1', 'STANDARD_G2', 'STANDARD_G3', 'STANDARD_G4' , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", "BASIC_A2", "BASIC_A3", "BASIC_A4", "STANDARD_A1", "STANDARD_A2", "STANDARD_A3", "STANDARD_A4", "STANDARD_A5", "STANDARD_A6", "STANDARD_A7", "STANDARD_A8", "STANDARD_A9", "STANDARD_A9" 'STANDARD_A10', 'STANDARD_A11', 'STANDARD_D15_V2', 'STANDARD_F1', 'STANDARD_F2', 'STANDARD_F4', 'STANDARD_F8', 'STANDARD_F16', 'STANDARD_NV6', 'STANDARD_NV12', 'STANDARD_NV24', 'STANDARD_NC6', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC24r' , "STANDARD_H8", 'STANDARD_H8m', 'STANDARD_H16', 'STANDARD_H16m', 'STANDARD_H16mr', 'STANDARD_H16r', 'STANDARD_A1_V2', 'STANDARD_A2_V2', 'STANDARD_A4_V2', 'STANDARD_A8_V2', 'STANDARD_A2m_V2', 'STANDARD_A4m_V2', 'STANDARD_A8m_V2', 'STANDARD_M64ms', 'STANDARD_M128s', e 'STANDARD_D2_V3'. *O padrão é "STANDARD_D2_V2".*  |
|    DockerDetails - azureBatchVMDetails - dedicadoComputerNodes   |  Número de nós de computador dedicados por piscina de lote. O valor predefinido é 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Agente de nó de nó de Azure Batch SKU ID. Atualmente, apenas o agente de nó de lote "batch.node.ubuntu 18.04" está suportado.    |
| DockerDetails - partnerCredentials | Credenciais para chamar o parceiro API em Docker. O parceiro fornece esta informação com base no mecanismo de autorização suportado; por exemplo, nome de utilizador e senha, ou teclas API. |
| parceiroType | "Tempo". Outros tipos de parceiros em FarmBeats são "Sensor" e "Imagery".  |
|  name   |   Nome desejado do parceiro no sistema FarmBeats.   |
|  descrição |  Descrição.   |

## <a name="next-steps"></a>Passos seguintes

Agora que já questionou os dados dos sensores a partir do seu exemplo Azure FarmBeats, aprenda a [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para as suas quintas.