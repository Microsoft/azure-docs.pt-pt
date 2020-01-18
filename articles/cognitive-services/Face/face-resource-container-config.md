---
title: Configurar contêineres-face
titleSuffix: Azure Cognitive Services
description: O ambiente de tempo de execução de contêiner facial é configurado usando os argumentos de comando `docker run`. Há configurações obrigatórias e opcionais.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 3c78c9eb85c3a8be236be5c3a24bd877db204b6c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76167968"
---
# <a name="configure-face-docker-containers"></a>Configurar contêineres do Docker facial

O ambiente de tempo de execução de contêiner **facial** é configurado usando os argumentos de comando `docker run`. Esse contêiner tem várias configurações necessárias, juntamente com algumas configurações opcionais. Várias [exemplos](#example-docker-run-commands) do comando estão disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> O [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), e [ `Eula` ](#eula-setting) definições são utilizadas em conjunto, e tem de indicar valores válidos para todas as três-los; caso contrário não inicia o contentor. Para obter mais informações sobre como utilizar estas definições de configuração para criar uma instância de um contentor, consulte [faturação](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey definição de configuração

O `ApiKey` definição especifica a chave de recurso do Azure utilizada para controlar informações de faturação para o contentor. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso de _Serviços cognitivas_ especificado para a configuração de [`Billing`](#billing-configuration-setting) .

Essa configuração pode ser encontrada no seguinte local:

* Portal do Azure: gerenciamento de recursos de **Serviços cognitivas** , em **chaves**

## <a name="applicationinsights-setting"></a>Definição do Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A configuração de `Billing` especifica o URI do ponto de extremidade do recurso de _Serviços cognitivas_ no Azure usado para medir as informações de cobrança do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso de _Serviços cognitivas_ no Azure. O contêiner relata o uso de cada 10 a 15 minutos.

Essa configuração pode ser encontrada no seguinte local:

* Portal do Azure: visão geral **dos serviços cognitivas** , rotulados `Endpoint`

Lembre-se de adicionar o roteamento _facial_ ao URI do ponto de extremidade, conforme mostrado no exemplo. 

|Obrigatório| Nome | Data type | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia | URI do ponto de extremidade de cobrança. Para obter mais informações sobre como obter o URI de cobrança, consulte [coletando parâmetros necessários](face-how-to-install-containers.md#gathering-required-parameters). Para obter mais informações e uma lista completa de pontos de extremidade regionais, consulte [nomes de subdomínio personalizados para serviços cognitivas](../cognitive-services-custom-subdomains.md). |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Definições de configuração de CloudAI

As definições de configuração no `CloudAI` secção fornecer opções de contentor específicas exclusivas para o seu contentor. As seguintes definições e os objetos são suportados para o contentor de Face no `CloudAI` secção

| Nome | Data type | Descrição |
|------|-----------|-------------|
| `Storage` | Objeto | O cenário de armazenamento utilizado pelo contentor de rostos. Para obter mais informações sobre cenários de armazenamento e as definições associadas para o `Storage` objeto, consulte [as definições de cenário de armazenamento](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Definições de cenário de armazenamento

O contentor de Face armazena BLOBs, cache, metadados e dados de fila, consoante o que está a ser armazenado. Por exemplo, os índices de treinamento e resultados de um grupo de pessoas grandes são armazenados como dados de Blobs. O contentor de Face fornece dois cenários de armazenamento diferente ao interagir com e armazenar esses tipos de dados:

* Memória  
  Todos os quatro tipos de dados são armazenados na memória. Eles não estão a ser distribuídos, nem são persistentes. Se o contentor de Face está parado ou removido, todos os dados no armazenamento para esse contentor for destruída.  
  Este é o cenário de armazenamento padrão para o contentor de rostos.
* Azure  
  O contentor de rostos utiliza o armazenamento do Azure e o Azure Cosmos DB para distribuir estes quatro tipos de dados entre armazenamento persistente. Dados de BLOBs e filas são processados pelo armazenamento do Azure. Os metadados e os dados de cache são tratados pelo Azure Cosmos DB. Se o contentor de Face está parado ou removido, todos os dados no armazenamento para esse contentor permanece armazenado no armazenamento do Azure e o Azure Cosmos DB.  
  Os recursos utilizados pelo cenário de armazenamento do Azure têm os seguintes requisitos adicionais
  * O recurso de armazenamento do Azure tem de utilizar o tipo de conta StorageV2
  * O recurso de Azure Cosmos DB deve usar a API do Azure Cosmos DB para MongoDB

Os cenários de armazenamento e as definições de configuração associados são geridas pelos `Storage` objeto no `CloudAI` seção de configuração. As seguintes definições de configuração estão disponíveis no `Storage` objeto:

| Nome | Data type | Descrição |
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

## <a name="http-proxy-credentials-settings"></a>Configurações de credenciais de proxy http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Definições de montagem

Monta o enlace de utilização para ler e escrever dados de e para o contentor. Pode especificar uma montagem de entrada ou saída de montagem, especificando o `--mount` opção da [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando.

Os contêineres de face não usam montagens de entrada ou de saída para armazenar dados de treinamento ou de serviço. 

A sintaxe exata da localização de montagem do anfitrião varia consoante o sistema operativo anfitrião. Além disso, o local de montagem do [computador host](face-how-to-install-containers.md#the-host-computer)pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões de local de montagem do host. 

|Opcional| Nome | Data type | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | Cadeia | Os contêineres de face não usam isso.|
|Opcional| `Output` | Cadeia | O destino de montagem de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Executar comandos de docker de exemplo 

Os exemplos seguintes utilizam as definições de configuração para ilustrar como escrever e usar `docker run` comandos.  Quando em execução, o contentor continua a ser executada até [parar](face-how-to-install-containers.md#stop-the-container) -lo.

* **Caractere de continuação de linha**: os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substituir ou remova esta com base nos requisitos de seu sistema de operativo de anfitrião. 
* **Ordem do argumento**: não altere a ordem dos argumentos, a menos que você esteja muito familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou de exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave do ponto de extremidade do recurso `Face` na página chaves do `Face` do Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto de extremidade de cobrança está disponível na página Visão geral do `Face` do Azure.| Consulte [coletando parâmetros necessários](face-how-to-install-containers.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](face-how-to-install-containers.md#billing).
> O valor de ApiKey é a **chave** da página de chaves de recurso do `Cognitive Services` do Azure. 

## <a name="face-container-docker-examples"></a>Exemplos do Docker de contêiner facial

Os exemplos do Docker a seguir são para o contêiner de face. 

### <a name="basic-example"></a>Exemplo básico 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exemplo de log 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Passos seguintes

* Revisão [como instalar e executar contentores](face-how-to-install-containers.md)
