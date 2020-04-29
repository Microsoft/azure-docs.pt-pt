---
title: Obtenha dados meteorológicos dos parceiros meteorológicos
description: Este artigo descreve como obter dados meteorológicos de parceiros.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266165"
---
# <a name="get-weather-data-from-weather-partners"></a>Obtenha dados meteorológicos dos parceiros meteorológicos

O Azure FarmBeats ajuda-o a trazer dados meteorológicos do seu(s) fornecedor de dados meteorológicos usando uma Estrutura de Conector baseada em estivadores. Utilizando este quadro, os fornecedores de dados meteorológicos implementam um estivador que pode ser integrado com farmBeats. Atualmente, os seguintes fornecedores de dados meteorológicos são apoiados:

[Dados da NOAA dos Conjuntos de Dados Abertos do Azure](https://azure.microsoft.com/services/open-datasets/)

Os dados meteorológicos podem ser usados para gerar insights atuais e construir modelos De IA/ML em FarmBeats.

## <a name="before-you-start"></a>Antes de começar

Para obter dados meteorológicos, certifique-se de que instalou FarmBeats. **A integração meteorológica é suportada na versão 1.2.11 ou superior.** Para instalar o Azure FarmBeats, consulte [instalar FarmBeats](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Permitir a integração meteorológica com farmBeats

Para começar a obter dados meteorológicos no seu centro de dados FarmBeats, siga os passos abaixo:

1. Vá ao seu hub de dados farmBeats swagger (https://yourdatahub.azurewebsites.net/swagger)

2. Navegue para /Partner API e faça um pedido de POST com a seguinte carga útil de entrada:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
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

   Por exemplo, para obter dados meteorológicos da NOAA por Azure Open Datasets, use a carga útil abaixo. Pode modificar o nome e a descrição de acordo com a sua preferência.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > Para mais informações sobre o objeto parceiro, consulte [o Apêndice](get-weather-data-from-weather-partner.md#appendix)

   O passo anterior fornecerá os recursos para permitir que o Docker corra no ambiente FarmBeats do cliente.  

   Leva cerca de 10 a 15 minutos para fornecer os recursos acima.

3. Verifique o estado do objeto /Parceiro que criou no passo 2. Para tal, faça um pedido get na API /Partner e verifique o **estado** do objeto parceiro. Uma vez que a FarmBeats forprovisiona o parceiro com sucesso, o estado é definido para **Ative**.

4. Navegue para /JobType API e faça um pedido GET no mesmo. Verifique os trabalhos climáticos que são criados como parte do processo de adição do Parceiro no passo 2. O **pipelineName** no campo meteorológico será do seguinte formato: "parceiro-name_partner-type_job-nome".

5. Agora, a sua instância FarmBeats tem um parceiro de dados meteorológicos ativos e pode executar trabalhos para solicitar dados meteorológicos para uma determinada localização (latitude/longitude) e um intervalo de data. O JobType(s) terá detalhes sobre quais os parâmetros necessários para executar trabalhos climáticos.

   Por exemplo, para os dados da NOAA a partir de Conjuntos de Dados Abertos Do Azure, serão criados seguintes JobType(s):

   - get_weather_data (Obter dados meteorológicos isd/históricos)
   - get_weather_forecast_data (Obter GFS/dados meteorológicos previstos)

6. Tome nota do **ID** e dos parâmetros do JobType(s).

7. Navegue para /Jobs API e faça um pedido post on/Jobs com a seguinte carga útil de entrada:

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

   Por exemplo, para executar **get_weather_data,** utilize a seguinte carga útil:

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. O passo anterior executará os trabalhos climáticos definidos no parceiro docker e ingerirá dados meteorológicos em FarmBeats. Pode verificar o estado do trabalho fazendo um pedido get em /Jobs e procurar o **Estado atual** na resposta. Uma vez concluído, o atual Estado está definido para **ser bem sucedido**.

## <a name="query-ingested-weather-data"></a>Consulta de dados meteorológicos ingeridos

Depois de os trabalhos meteorológicos estarem completos, pode consultar dados meteorológicos ingeridos para construir modelos ou insights atuais. Existem duas formas de aceder e consultar dados meteorológicos da FarmBeats:

- API e
- Insights da Série Time (TSI).

### <a name="query-using-rest-api"></a>Consulta usando API REST

Para consultar os dados meteorológicos utilizando a FarmBeats REST API, siga os passos abaixo:

1. No seu farmBeats Datahttps://yourdatahub.azurewebsites.net/swagger)hub swagger ( , navegue para /WeatherDataLocation API e faça um pedido GET. A resposta terá o objeto(s) da WeatherDataLocation criado para a localização (latitude/longitude) que foi especificado como parte da execução do trabalho. Tome nota do **ID** e do **tempoDataModelId** do ou do objeto.

2. Faça um GET/{id} em /WeatherDataModel API para o **tempoDataModelId,** conforme notado no passo 1. O "Weather Data Model" tem todos os metadados e detalhes sobre os dados meteorológicos ingeridos. Por exemplo, **a Medida meteorológica** dentro do objeto do Modelo de Dados **meteorológicos** tem detalhes sobre que informações meteorológicas são suportadas e em que tipos e unidades. Por exemplo,

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

   Tome nota da resposta da chamada GET/{id} para o Modelo de Dados meteorológicos.

3. Navegue para a **Telemetria** API e faça um pedido de POST com a seguinte carga útil de entrada:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. A resposta que contém dados meteorológicos disponíveis para o intervalo de tempo especificado será assim:

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

No exemplo anterior, a resposta tem dados para dois selos temporais, juntamente com o nome de medida ("Temperatura") e valores dos dados meteorológicos reportados nos dois selos temporais. Terá de se referir ao Modelo de Dados meteorológicos associado (conforme descrito no passo 2 acima) para interpretar o tipo e a unidade dos valores reportados.

### <a name="query-using-azure-time-series-insights-tsi"></a>Consulta utilizando Insights da Série De Tempo Azure (TSI)

FarmBeats usa [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) para ingerir, armazenar, consultar e visualizar dados na escala IoT -- dados altamente contextuacionais e otimizados para séries temporais.

Os dados meteorológicos são recebidos num EventHub e depois empurrados para um ambiente TSI dentro do grupo de recursos FarmBeats. Os dados podem então ser consultados diretamente a partir da TSI. Para mais informações, consulte [a documentação da TSI.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)

Siga os passos para visualizar dados sobre a TSI:

1. Vá ao **portal** > Azure**FarmBeats DataHub grupo** de recursos > selecione Time Series **Insights** ambiente (tsi-xxxx) > Políticas de acesso a **dados**. Adicione o utilizador com acesso ao Leitor ou colaborador.

2. Vá à página **de visão geral** do ambiente Time Series **Insights** (tsi-xxxx) e selecione o URL do Time Series **Insights Explorer**. Agora pode visualizar os dados meteorológicos ingeridos.

Além de armazenar, consultar e visualizar dados meteorológicos, a TSI também permite a integração num dashboard Power BI. Para mais informações, consulte [Visualizar os dados da Time Series Insights no Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi).

## <a name="appendix"></a>Anexo

|        Parceiro   |  Detalhes   |
| ------- | -------             |
|     DockerDetails - nome de imagem         |          Nome da imagem do Docker. Por exemplo, docker.io/azurefarmbeats/farmbeats-noaa (imagem em hub.docker.com) ou myazureacr.azurecr.io/mydockerimage (imagem no Registo de Contentores Azure) e assim por diante. Se não for fornecido nenhum registo, o predefinido é hub.docker.com      |
|          DockerDetails - imageTag             |         Nome da etiqueta da imagem do estivador. Padrão é "mais recente"     |
|  DockerDetails - credenciais      |  Credenciais para aceder ao estivador privado. Isto será fornecido pelo parceiro ao cliente   |
|  DockerDetails - azureBatchVMDetails - loteVMSKU     |    Lote Azure VM SKU. Veja [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) todas as máquinas virtuais linux disponíveis. Os valores válidos são: "Pequeno", 'ExtraLarge', 'Large', 'A8', 'A9', 'Medium', 'A5', 'A6', 'A7', 'STANDARD_D1', 'STANDARD_D2', 'STANDARD_D3', 'STANDARD_D4', 'STANDARD_D11', 'STANDARD_D12', 'STANDARD_D13', 'STANDARD_D14', 'A10', 'STANDARD_D1_V2', 'STANDARD_D2_V2', 'STANDARD_D3_V2', 'STANDARD_D4_V2', 'STANDARD_D11_V2', 'STANDARD_D12_V2', 'STANDARD_D13_V2', 'STANDARD_D14_V2', 'STANDARD_G1', 'STANDARD_G2', 'STANDARD_G3', 'STANDARD_G4', 'STANDARD_G4', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G4', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3', 'STANDARD_G3' , 'STANDARD_G5', 'STANDARD_D5_V2', 'BASIC_A1', 'BASIC_A2', 'BASIC_A3', 'BASIC_A4', 'STANDARD_A1', 'STANDARD_A2', 'STANDARD_A3', 'STANDARD_A4', 'STANDARD_A5', 'STANDARD_A6', 'STANDARD_A7', 'STANDARD_A8', 'STANDARD_A9', 'STANDARD_A10', 'STANDARD_A11', 'STANDARD_D15_V2', 'STANDARD_F1', 'STANDARD_F2', 'STANDARD_F4', 'STANDARD_F8', 'STANDARD_F16', 'STANDARD_NV6', 'STANDARD_NV12', 'STANDARD_NV24', 'STANDARD_NC6', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC24r STANDARD_NC24', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC24', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC12' " STANDARD_H8", 'STANDARD_H8m', 'STANDARD_H16', 'STANDARD_H16m', 'STANDARD_H16mr', 'STANDARD_H16r', 'STANDARD_A1_V2', 'STANDARD_A2_V2', 'STANDARD_A4_V2', 'STANDARD_A8_V2', 'STANDARD_A2m_V2', 'STANDARD_A4m_V2', 'STANDARD_A8m_V2', 'STANDARD_M64ms', 'STANDARD_M128s', 'STANDARD_D2_V3'. **Padrão é "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - dedicadoSComputerNodes   |  Não. de nós de computador dedicados para piscina de lote. O valor padrão é 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Agente do nó de lote azure SKU ID. Atualmente, apenas o agente do nó de lote "batch.node.ubuntu 18.04" é suportado.    |
| DockerDetails - partnerCredenciais | credenciais para chamar parceiro API em estivador. O parceiro precisa de dar esta informação aos seus clientes com base no mecanismo de auth que é suportado, por exemplo. Nome de utilizador/palavra-passe ou Teclas API. |
| partnerType | "Weather" (Outros tipos de parceiros em FarmBeats são "Sensor" e "Imagery")  |
|  nome   |   Nome desejado do parceiro no sistema FarmBeats   |
|  descrição |  Descrição   |

## <a name="next-steps"></a>Passos seguintes

Agora questionou os dados do sensor da sua instância Azure FarmBeats. Agora, aprendam a [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para as vossas quintas.
