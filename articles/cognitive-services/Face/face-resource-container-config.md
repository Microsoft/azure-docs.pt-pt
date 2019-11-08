---
title: Configurar contêineres-API de detecção facial
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
ms.openlocfilehash: 78fd2aa977062d2f0d6b981140f3db5b263e4651
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795037"
---
# <a name="configure-face-docker-containers"></a>Configurar contêineres do Docker facial

O ambiente de tempo de execução de contêiner **facial** é configurado usando os argumentos de comando `docker run`. Esse contêiner tem várias configurações necessárias, juntamente com algumas configurações opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)e [`Eula`](#eula-setting) são usadas juntas, e você deve fornecer valores válidos para todos os três; caso contrário, o contêiner não será iniciado. Para obter mais informações sobre como usar essas definições de configuração para criar uma instância de um contêiner, consulte [cobrança](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Configuração de ApiKey

A configuração de `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança para o contêiner. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso de _Serviços cognitivas_ especificado para a configuração de [`Billing`](#billing-configuration-setting) .

Essa configuração pode ser encontrada no seguinte local:

* Portal do Azure: gerenciamento de recursos de **Serviços cognitivas** , em **chaves**

## <a name="applicationinsights-setting"></a>Configuração de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A configuração de `Billing` especifica o URI do ponto de extremidade do recurso de _Serviços cognitivas_ no Azure usado para medir as informações de cobrança do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso de _Serviços cognitivas_ no Azure. O contêiner relata o uso de cada 10 a 15 minutos.

Essa configuração pode ser encontrada no seguinte local:

* Portal do Azure: visão geral **dos serviços cognitivas** , rotulados `Endpoint`

Lembre-se de adicionar o roteamento _facial_ ao URI do ponto de extremidade, conforme mostrado no exemplo. 

|Necessário| Nome | Data type | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI do ponto de extremidade de cobrança. Para obter mais informações sobre como obter o URI de cobrança, consulte [coletando parâmetros necessários](face-how-to-install-containers.md#gathering-required-parameters). Para obter mais informações e uma lista completa de pontos de extremidade regionais, consulte [nomes de subdomínio personalizados para serviços cognitivas](../cognitive-services-custom-subdomains.md). |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Definições de configuração do CloudAI

As definições de configuração na seção `CloudAI` fornecem opções específicas de contêiner exclusivas ao seu contêiner. As configurações e os objetos a seguir têm suporte para o contêiner de face na seção `CloudAI`

| Nome | Data type | Descrição |
|------|-----------|-------------|
| `Storage` | Object | O cenário de armazenamento usado pelo contêiner de face. Para obter mais informações sobre cenários de armazenamento e configurações associadas para o objeto `Storage`, consulte [configurações de cenário de armazenamento](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Configurações de cenário de armazenamento

O contêiner facial armazena dados de BLOB, cache, metadados e fila, dependendo do que está sendo armazenado. Por exemplo, os índices de treinamento e os resultados de um grupo de pessoas grandes são armazenados como dados de BLOB. O contêiner facial fornece dois cenários de armazenamento diferentes ao interagir com e armazenar esses tipos de dados:

* Memória  
  Todos os quatro tipos de dados são armazenados na memória. Eles não são distribuídos, nem são persistentes. Se o contêiner de face for interrompido ou removido, todos os dados no armazenamento desse contêiner serão destruídos.  
  Esse é o cenário de armazenamento padrão para o contêiner de face.
* Azure  
  O contêiner facial usa o armazenamento do Azure e Azure Cosmos DB para distribuir esses quatro tipos de dados entre o armazenamento persistente. Os dados de BLOB e de fila são tratados pelo armazenamento do Azure. Os metadados e os dados de cache são tratados pelo Azure Cosmos DB. Se o contêiner de face for interrompido ou removido, todos os dados no armazenamento desse contêiner permanecerão armazenados no armazenamento do Azure e Azure Cosmos DB.  
  Os recursos usados pelo cenário de armazenamento do Azure têm os seguintes requisitos adicionais
  * O recurso de armazenamento do Azure deve usar o tipo de conta StorageV2
  * O recurso de Azure Cosmos DB deve usar a API do Azure Cosmos DB para MongoDB

Os cenários de armazenamento e as definições de configuração associadas são gerenciados pelo objeto `Storage`, na seção `CloudAI` configuração. As definições de configuração a seguir estão disponíveis no objeto `Storage`:

| Nome | Data type | Descrição |
|------|-----------|-------------|
| `StorageScenario` | String | O cenário de armazenamento com suporte no contêiner. Os seguintes valores estão disponíveis<br/>`Memory`-valor padrão. O contêiner usa armazenamento não persistente, não distribuído e na memória, para uso temporário de nó único. Se o contêiner for interrompido ou removido, o armazenamento desse contêiner será destruído.<br/>o `Azure`-container usa recursos do Azure para armazenamento. Se o contêiner for interrompido ou removido, o armazenamento desse contêiner será persistido.|
| `ConnectionStringOfAzureStorage` | String | A cadeia de conexão para o recurso de armazenamento do Azure usado pelo contêiner.<br/>Essa configuração se aplica somente se `Azure` for especificado para a definição de configuração de `StorageScenario`. |
| `ConnectionStringOfCosmosMongo` | String | A cadeia de conexão do MongoDB para o recurso de Azure Cosmos DB usado pelo contêiner.<br/>Essa configuração se aplica somente se `Azure` for especificado para a definição de configuração de `StorageScenario`. |

Por exemplo, o comando a seguir especifica o cenário de armazenamento do Azure e fornece exemplos de cadeias de conexão para o armazenamento do Azure e Cosmos DB recursos usados para armazenar dados para o contêiner de face.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

O cenário de armazenamento é tratado separadamente de montagens de entrada e montagens de saída. Você pode especificar uma combinação desses recursos para um único contêiner. Por exemplo, o comando a seguir define uma montagem de ligação do Docker para a pasta `D:\Output` no computador host como a montagem de saída e, em seguida, cria uma instância de um contêiner a partir da imagem de contêiner facial, salvando os arquivos de log no formato JSON na montagem de saída. O comando também especifica o cenário de armazenamento do Azure e fornece exemplos de cadeias de conexão para o armazenamento do Azure e Cosmos DB recursos usados para armazenar dados para o contêiner facial.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Configuração do EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações de credenciais de proxy http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de log
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados de e para o contêiner. Você pode especificar uma montagem de entrada ou de saída especificando a opção de `--mount` no comando de [execução do Docker](https://docs.docker.com/engine/reference/commandline/run/) .

Os contêineres de face não usam montagens de entrada ou de saída para armazenar dados de treinamento ou de serviço. 

A sintaxe exata do local de montagem do host varia dependendo do sistema operacional do host. Além disso, o local de montagem do [computador host](face-how-to-install-containers.md#the-host-computer)pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões de local de montagem do host. 

|Opcional| Nome | Data type | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os contêineres de face não usam isso.|
|Opcional| `Output` | String | O destino da montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos de execução do Docker de exemplo 

Os exemplos a seguir usam as definições de configuração para ilustrar como gravar e usar comandos de `docker run`.  Uma vez em execução, o contêiner continua a ser executado até que você o [interrompa](face-how-to-install-containers.md#stop-the-container) .

* **Caractere de continuação de linha**: os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova isso com base nos requisitos do seu sistema operacional do host. 
* **Ordem do argumento**: não altere a ordem dos argumentos, a menos que você esteja muito familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave do ponto de extremidade do recurso `Face` na página chaves do `Face` do Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto de extremidade de cobrança está disponível na página Visão geral do `Face` do Azure.| Consulte [coletando parâmetros necessários](face-how-to-install-containers.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As opções `Eula`, `Billing`e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para obter mais informações, consulte [cobrança](face-how-to-install-containers.md#billing).
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

* Examine [como instalar e executar contêineres](face-how-to-install-containers.md)
