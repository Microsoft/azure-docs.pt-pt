---
title: Recipientes de configuração - Face
titleSuffix: Azure Cognitive Services
description: O ambiente de funcionamento do recipiente face é configurado utilizando os argumentos de `docker run` comando. Existem configurações necessárias e opcionais.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2f608843e27b79d02697df8e2a7f2aba6695e10a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "80878430"
---
# <a name="configure-face-docker-containers"></a>Configurar recipientes face Docker

O ambiente de funcionamento do recipiente **face** é configurado utilizando os argumentos de `docker run` comando. Este recipiente tem várias configurações necessárias, juntamente com algumas configurações opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do contentor são as definições de faturação. 

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) definições , e [`Eula`](#eula-setting) configurações são utilizadas em conjunto, e deve fornecer valores válidos para os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas configurações para instantaneaizar um recipiente, consulte [Billing](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Definição de configuração apikey

A `ApiKey` definição especifica a chave de recursos Azure utilizada para rastrear as informações de faturação do recipiente. Tem de especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso _Serviços Cognitivos_ especificado para a definição de [`Billing`](#billing-configuration-setting) configuração.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: **Cognitive Services** Resource Management, em **Chaves**

## <a name="applicationinsights-setting"></a>Configuração de AplicaçõesInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso _Serviços Cognitivos_ no Azure utilizado para medir informações de faturação para o recipiente. Deve especificar um valor para esta configuração, e o valor deve ser um URI de ponto final válido para um recurso _de Serviços Cognitivos_ em Azure. O recipiente relata o uso a cada 10 a 15 minutos.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: Visão geral **dos Serviços Cognitivos,** rotulada `Endpoint`

Lembre-se de adicionar o encaminhamento _face_ ao ponto final URI, como mostrado no exemplo. 

|Necessário| Name | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI de faturação. Para obter mais informações sobre a obtenção do URI de faturação, consulte [a recolha dos parâmetros necessários](face-how-to-install-containers.md#gathering-required-parameters). Para obter mais informações e uma lista completa de pontos finais regionais, consulte [os nomes de subdomínio personalizados para serviços cognitivos.](../cognitive-services-custom-subdomains.md) |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Definições de configuração cloudAI

As definições de configuração na `CloudAI` secção fornecem opções específicas do contentor únicas para o seu recipiente. As seguintes configurações e objetos são suportados para o recipiente Face na `CloudAI` secção

| Name | Tipo de dados | Descrição |
|------|-----------|-------------|
| `Storage` | Objeto | O cenário de armazenamento utilizado pelo recipiente Face. Para obter mais informações sobre cenários de armazenamento e configurações associadas para o `Storage` objeto, consulte [as definições do cenário de Armazenamento](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Definições de cenário de armazenamento

O recipiente Face armazena bolhas, cache, metadados e dados de fila, dependendo do que está a ser armazenado. Por exemplo, os índices de formação e os resultados de um grupo de pessoas grandes são armazenados como dados blob. O recipiente Face fornece dois cenários de armazenamento diferentes ao interagir e armazenar este tipo de dados:

* Memória  
  Todos os quatro tipos de dados são armazenados na memória. Não são distribuídos, nem são persistentes. Se o recipiente Face for parado ou removido, todos os dados armazenados para esse recipiente serão destruídos.  
  Este é o cenário de armazenamento padrão para o recipiente Face.
* Azure  
  O recipiente Face utiliza o Azure Storage e o Azure Cosmos DB para distribuir estes quatro tipos de dados através do armazenamento persistente. Os dados de blob e fila são tratados pelo Azure Storage. Os dados de metadados e cache são tratados pela Azure Cosmos DB. Se o recipiente Face for parado ou removido, todos os dados armazenados para esse contentor permanecem armazenados no Azure Storage e no Azure Cosmos DB.  
  Os recursos utilizados pelo cenário de armazenamento Azure têm os seguintes requisitos adicionais
  * O recurso Azure Storage deve utilizar o tipo de conta StorageV2
  * O recurso DB da Azure Cosmos deve usar a API da Azure Cosmos para a MongoDB

Os cenários de armazenamento e as definições de configuração associadas são geridos pelo `Storage` objeto, sob a `CloudAI` secção de configuração. As seguintes definições de configuração estão disponíveis no `Storage` objeto:

| Name | Tipo de dados | Descrição |
|------|-----------|-------------|
| `StorageScenario` | String | O cenário de armazenamento suportado pelo contentor. Estão disponíveis os seguintes valores<br/>`Memory` - Valor predefinido. O recipiente utiliza armazenamento temporário não persistente, não distribuído e em memória, para uso temporário de nó único. Se o recipiente for parado ou removido, o depósito desse recipiente será destruído.<br/>`Azure` - O contentor utiliza recursos Azure para armazenamento. Se o recipiente for parado ou removido, o armazenamento desse recipiente é persistido.|
| `ConnectionStringOfAzureStorage` | String | O fio de ligação para o recurso de armazenamento Azure utilizado pelo recipiente.<br/>Esta definição só se aplica se `Azure` for especificada para a definição de `StorageScenario` configuração. |
| `ConnectionStringOfCosmosMongo` | String | A cadeia de ligação MongoDB para o recurso DB Azure Cosmos utilizado pelo recipiente.<br/>Esta definição só se aplica se `Azure` for especificada para a definição de `StorageScenario` configuração. |

Por exemplo, o seguinte comando especifica o cenário de armazenamento Azure e fornece cadeias de conexão de amostra para os recursos de Armazenamento Azure e Coss DB utilizados para armazenar dados para o recipiente Face.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

O cenário de armazenamento é manuseado separadamente dos suportes de entrada e dos suportes de saída. Pode especificar uma combinação dessas características para um único recipiente. Por exemplo, o seguinte comando define um suporte de ligação docker à `D:\Output` pasta na máquina hospedeira como suporte de saída, em seguida, instantanea um recipiente a partir da imagem do recipiente Face, guardando ficheiros de registo no formato JSON para o suporte de saída. O comando também especifica o cenário de armazenamento Azure e fornece cadeias de conexão de amostra para os recursos de Armazenamento Azure e Cosmos DB usados para armazenar dados para o recipiente Face.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Definição de Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de procuração http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Utilize suportes de ligação para ler e escrever dados de e para o recipiente. Pode especificar um suporte de entrada ou de saída especificando a `--mount` opção no comando de execução do [estivador.](https://docs.docker.com/engine/reference/commandline/run/)

Os recipientes Face não utilizam suportes de entrada ou saída para armazenar dados de treino ou de serviço. 

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador anfitrião](face-how-to-install-containers.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço Docker e as permissões de localização do anfitrião. 

|Opcional| Name | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os recipientes faciais não utilizam isto.|
|Opcional| `Output` | String | O alvo do suporte de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui registos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exemplo de estivador executar comandos 

Os exemplos a seguir utilizam as definições de configuração para ilustrar como escrever e utilizar `docker run` comandos.  Uma vez em funcionamento, o recipiente continua a funcionar até o [parar.](face-how-to-install-containers.md#stop-the-container)

* **Carácter de continuação da linha**: Os comandos do Docker nas seguintes secções usam o corte `\` traseiro, como um personagem de continuação de linha. Substitua ou remova isto com base nos requisitos do seu sistema operativo anfitrião. 
* **Ordem de argumento**: Não altere a ordem dos argumentos a menos que esteja muito familiarizado com os recipientes docker.

Substitua {_argument_name_} com os seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave de ponta final do `Face` recurso na página Azure `Face` Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto final de faturação está disponível na página Azure `Face` Overview.| Consulte [os parâmetros necessários](face-how-to-install-containers.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As `Eula` `Billing` opções , e `ApiKey` opções devem ser especificadas para executar o recipiente; caso contrário, o recipiente não arranca.  Para mais informações, consulte [Billing.](face-how-to-install-containers.md#billing)
> O valor ApiKey é a **chave** da página de chaves de recursos Azure. `Cognitive Services` 

## <a name="face-container-docker-examples"></a>Exemplos de Docker de contentor de rosto

Os seguintes exemplos do Docker são para o recipiente facial. 

### <a name="basic-example"></a>Exemplo básico 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exemplo de registo 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Passos seguintes

* Rever [Como instalar e executar contentores](face-how-to-install-containers.md)
