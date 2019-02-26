---
title: Configurar contentores
titlesuffix: Face - Azure Cognitive Services
description: Definições de configuração para contentores.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: diberry
ms.openlocfilehash: 4215b008af21a3473a1d2dcef5f73a1b19133215
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821564"
---
# <a name="configure-face-docker-containers"></a>Configurar os contentores do Docker de rostos

O **Face** ambiente de tempo de execução de contentores é configurado usando o `docker run` argumentos de comando. Este contentor tem várias definições necessárias, juntamente com algumas configurações opcionais. Várias [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do contentor são as definições de faturas. 

Definições de contentor são [hierárquica](#hierarchical-settings) e podem ser definidas com [variáveis de ambiente](#environment-variable-settings) ou docker [argumentos da linha de comandos](#command-line-argument-settings).

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> O [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), e [ `Eula` ](#eula-setting) definições são utilizadas em conjunto, e tem de indicar valores válidos para todas as três-los; caso contrário não inicia o contentor. Para obter mais informações sobre como utilizar estas definições de configuração para criar uma instância de um contentor, consulte [faturação](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey definição de configuração

O `ApiKey` definição especifica a chave de recurso do Azure utilizada para controlar informações de faturação para o contentor. Tem de especificar um valor para o ApiKey e o valor tem de ser uma chave válida para o _Face_ recurso especificado para o [ `Billing` ](#billing-configuration-setting) definição de configuração.

Esta definição pode ser encontrada no seguinte local:

* Portal do Azure: **Da face** gestão de recursos, em **chaves**

## <a name="applicationinsights-setting"></a>Definição do Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

O `Billing` definição especifica o URI do ponto final da _Face_ recurso no Azure utilizado para informações de faturação para o contentor do medidor. Tem de especificar um valor para esta definição de configuração e o valor tem de ser um URI de ponto de final válido para um _Face_ recursos no Azure. O contentor de relatórios de utilização sobre a cada 10 a 15 minutos.

Esta definição pode ser encontrada no seguinte local:

* Portal do Azure: **Da face** descrição geral, o nome `Endpoint`

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia | URI do ponto final de faturação<br><br>Exemplo:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Definições de configuração de CloudAI

As definições de configuração no `CloudAI` secção fornecer opções de contentor específicas exclusivas para o seu contentor. As seguintes definições e os objetos são suportados para o contentor de Face no `CloudAI` secção

| Nome | Tipo de dados | Descrição |
|------|-----------|-------------|
| `Storage` | Object | O cenário de armazenamento utilizado pelo contentor de rostos. Para obter mais informações sobre cenários de armazenamento e as definições associadas para o `Storage` objeto, consulte [as definições de cenário de armazenamento](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Definições de cenário de armazenamento

O contentor de Face armazena BLOBs, cache, metadados e dados de fila, consoante o que está a ser armazenado. Por exemplo, os índices de treinamento e resultados de um grupo de pessoas grandes são armazenados como dados de Blobs. O contentor de Face fornece dois cenários de armazenamento diferente ao interagir com e armazenar esses tipos de dados:

* Memória  
  Todos os quatro tipos de dados são armazenados na memória. Eles não estão a ser distribuídos, nem são persistentes. Se o contentor de Face está parado ou removido, todos os dados no armazenamento para esse contentor for destruída.  
  Este é o cenário de armazenamento padrão para o contentor de rostos.
* Azure  
  O contentor de rostos utiliza o armazenamento do Azure e o Azure Cosmos DB para distribuir estes quatro tipos de dados entre armazenamento persistente. Dados de BLOBs e filas são processados pelo armazenamento do Azure. Dados de metadados e o cache são processados pelo Azure Cosmos DB. Se o contentor de Face está parado ou removido, todos os dados no armazenamento para esse contentor permanece armazenado no armazenamento do Azure e o Azure Cosmos DB.  
  Os recursos utilizados pelo cenário de armazenamento do Azure têm os seguintes requisitos adicionais
  * O recurso de armazenamento do Azure tem de utilizar o tipo de conta StorageV2
  * O recurso do Azure Cosmos DB tem de utilizar o Azure Cosmos DB API para MongoDB

Os cenários de armazenamento e as definições de configuração associados são geridas pelos `Storage` objeto no `CloudAI` seção de configuração. As seguintes definições de configuração estão disponíveis no `Storage` objeto:

| Nome | Tipo de dados | Descrição |
|------|-----------|-------------|
| `StorageScenario` | Cadeia | O cenário de armazenamento suportado pelo contentor. Os seguintes valores estão disponíveis<br/>`Memory` -Valor de predefinido. Contentor utiliza o armazenamento não persistentes, e distribuída e dentro da memória, para utilização temporária, de nó único. Se o contentor está parado ou removido, o armazenamento para esse contentor for destruído.<br/>`Azure` -Container utiliza recursos do Azure para o armazenamento. Se o contentor está parado ou removido, o armazenamento para esse contêiner é mantido.|
| `ConnectionStringOfAzureStorage` | Cadeia | A cadeia de ligação para o recurso de armazenamento do Azure utilizado pelo contentor.<br/>Esta definição aplica-se apenas se `Azure` é especificado para o `StorageScenario` definição de configuração. |
| `ConnectionStringOfCosmosMongo` | Cadeia | A cadeia de ligação do MongoDB para o recurso do Azure Cosmos DB utilizada pelo contentor.<br/>Esta definição aplica-se apenas se `Azure` é especificado para o `StorageScenario` definição de configuração. |

Por exemplo, o seguinte comando Especifica o cenário de armazenamento do Azure e fornece as cadeias de ligação de exemplo para os recursos de armazenamento do Azure e o Cosmos DB utilizados para armazenar os dados para o contentor de rostos.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

O cenário de armazenamento é processado separadamente da monta de entrada e saída monta. Pode especificar uma combinação desses recursos para um único contentor. Por exemplo, o comando seguinte define uma montagem de enlace de Docker para o `D:\Output` pasta na máquina host como a montagem de saída, em seguida, cria uma instância de um contentor a partir da imagem de contentor Face, guardar ficheiros de registo no formato JSON para a montagem de saída. O comando também especifica o cenário de armazenamento do Azure e fornece as cadeias de ligação de exemplo para os recursos de armazenamento do Azure e o Cosmos DB utilizados para armazenar os dados para o contentor de rostos.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>EULA definição

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Definições de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de proxy de HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Definições de montagem

Monta o enlace de utilização para ler e escrever dados de e para o contentor. Pode especificar uma montagem de entrada ou saída de montagem, especificando o `--mount` opção da [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando.

Os contentores de rostos não usam a entrada ou saída monta para armazenar dados de serviço ou de treinamento. 

A sintaxe exata da localização de montagem do anfitrião varia consoante o sistema operativo anfitrião. Além disso, o [computador anfitrião](face-how-to-install-containers.md#the-host-computer)da localização de montagem não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e o anfitrião montar permissões de localização. 

|Opcional| Name | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Contentores de rostos não utilize esta opção.|
|Opcional| `Output` | String | O destino de montagem de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui registos de contentor. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Definições de hierárquicas

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]

## <a name="example-docker-run-commands"></a>Executar comandos de docker de exemplo 

Os exemplos seguintes utilizam as definições de configuração para ilustrar como escrever e usar `docker run` comandos.  Quando em execução, o contentor continua a ser executada até [parar](face-how-to-install-containers.md#stop-the-container) -lo.

* **Caracteres de continuação de linha**: Os comandos de Docker nas secções seguintes utilizam a barra invertida, `\`, como um caractere de continuação de linha. Substituir ou remova esta com base nos requisitos de seu sistema de operativo de anfitrião. 
* **Ordem de argumento**: Não altere a ordem dos argumentos, a menos que está bastante familiarizada com contentores do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou de exemplo |
|-------------|-------|---|
|{BILLING_KEY} | A chave de ponto final do recurso de rostos. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | O valor de ponto final faturação incluindo região.|`https://westcentralus.api.cognitive.microsoft.com/face/v1.0`|

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](face-how-to-install-containers.md#billing).
> O valor de ApiKey é o **chave** na página chaves do Azure Face recursos. 

## <a name="face-container-docker-examples"></a>Exemplos do contentor Docker de rostos

Os exemplos de Docker seguintes são para o contentor de rostos. 

### <a name="basic-example"></a>Exemplo básico 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Exemplo de Registro em log com argumentos da linha de comandos

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-environment-variable"></a>Exemplo de registo com a variável de ambiente

  ```
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY}
  ```

## <a name="next-steps"></a>Passos Seguintes

* Revisão [como instalar e executar contentores](face-how-to-install-containers.md)
