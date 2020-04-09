---
title: Configure recipientes - Rosto
titleSuffix: Azure Cognitive Services
description: O ambiente de tempo de execução do recipiente Face é configurado utilizando os argumentos de `docker run` comando. Existem configurações necessárias e opcionais.
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878430"
---
# <a name="configure-face-docker-containers"></a>Configure recipientes De Estivador de Rosto

O ambiente de tempo de execução do recipiente **Face** é configurado utilizando os argumentos de `docker run` comando. Este recipiente tem várias configurações necessárias, juntamente com algumas configurações opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do recipiente são as definições de faturação. 

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)definições [`Eula`](#eula-setting) e as configurações são utilizadas em conjunto, e deve fornecer valores válidos para todos os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas definições de configuração para instantaneamente um recipiente, consulte [a Faturação](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Definição de configuração ApiKey

A `ApiKey` definição especifica a chave de recursos Azure utilizada para rastrear as informações de faturação do recipiente. Deve especificar um valor para o ApiKey e o valor deve ser uma [`Billing`](#billing-configuration-setting) chave válida para o recurso dos _Serviços Cognitivos_ especificado para a definição de configuração.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: **Cognitive Services** Resource Management, under **Keys**

## <a name="applicationinsights-setting"></a>Definição de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso _Serviços Cognitivos_ em Azure usado para medir informações de faturação para o recipiente. Deve especificar um valor para esta configuração, e o valor deve ser um uri de ponto final válido para um recurso _de Serviços Cognitivos_ no Azure. O recipiente reporta o uso a cada 10 a 15 minutos.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: Visão **geral dos serviços cognitivos,** rotulada`Endpoint`

Lembre-se de adicionar o encaminhamento _face_ ao ponto final URI, como mostra o exemplo. 

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | Ponto final de faturação URI. Para obter mais informações sobre a obtenção do URI de faturação, consulte a [recolha de parâmetros necessários](face-how-to-install-containers.md#gathering-required-parameters). Para mais informações e uma lista completa de pontos finais regionais, consulte [nomes de subdomínio personalizado para Serviços Cognitivos](../cognitive-services-custom-subdomains.md). |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Definições de configuração cloudAI

As definições `CloudAI` de configuração na secção fornecem opções específicas do recipiente únicas para o seu recipiente. As seguintes definições e objetos são `CloudAI` suportados para o recipiente Face na secção

| Nome | Tipo de dados | Descrição |
|------|-----------|-------------|
| `Storage` | Objeto | O cenário de armazenamento usado pelo recipiente Face. Para obter mais informações sobre cenários `Storage` de armazenamento e configurações associadas para o objeto, consulte [as definições](#storage-scenario-settings) do cenário de armazenamento |

### <a name="storage-scenario-settings"></a>Configurações do cenário de armazenamento

O contentor Face armazena blob, cache, metadados e dados de fila, dependendo do que está a ser armazenado. Por exemplo, os índices de formação e os resultados para um grupo de pessoas numerosas são armazenados como dados blob. O recipiente Face fornece dois cenários de armazenamento diferentes ao interagir e armazenar este tipo de dados:

* Memória  
  Todos os quatro tipos de dados são armazenados na memória. Não são distribuídos, nem persistentes. Se o recipiente Face for parado ou removido, todos os dados armazenados para esse recipiente são destruídos.  
  Este é o cenário de armazenamento padrão para o recipiente Face.
* Azure  
  O recipiente Face utiliza o Azure Storage e o Azure Cosmos DB para distribuir estes quatro tipos de dados através de armazenamento persistente. Os dados de blob e fila são tratados pelo Armazenamento Azure. Os dados de metadados e cache são tratados pela Azure Cosmos DB. Se o recipiente Face for parado ou removido, todos os dados armazenados para esse contentor permanecem armazenados em Azure Storage e Azure Cosmos DB.  
  Os recursos utilizados pelo cenário de armazenamento do Azure têm os seguintes requisitos adicionais
  * O recurso de armazenamento Azure deve usar o tipo de conta StorageV2
  * O recurso Azure Cosmos DB deve usar a API do Azure Cosmos DB para MongoDB

Os cenários de armazenamento e as configurações de configuração associadas são geridos pelo `Storage` objeto, sob a secção de `CloudAI` configuração. As seguintes definições `Storage` de configuração estão disponíveis no objeto:

| Nome | Tipo de dados | Descrição |
|------|-----------|-------------|
| `StorageScenario` | String | O cenário de armazenamento suportado pelo recipiente. Os seguintes valores estão disponíveis<br/>`Memory`- Valor por defeito. O recipiente utiliza armazenamento não persistente, não distribuído e em memória, para uso temporário de nó único. Se o recipiente for parado ou removido, o armazenamento desse recipiente é destruído.<br/>`Azure`- O contentor utiliza recursos Azure para armazenamento. Se o recipiente for parado ou removido, o armazenamento desse recipiente é permanecido.|
| `ConnectionStringOfAzureStorage` | String | A corda de ligação para o recurso de armazenamento Azure utilizado pelo recipiente.<br/>Esta definição só `Azure` se aplica `StorageScenario` se for especificada para a definição de configuração. |
| `ConnectionStringOfCosmosMongo` | String | A cadeia de ligação MongoDB para o recurso Azure Cosmos DB utilizado pelo recipiente.<br/>Esta definição só `Azure` se aplica `StorageScenario` se for especificada para a definição de configuração. |

Por exemplo, o seguinte comando especifica o cenário de armazenamento Azure e fornece cordas de ligação de amostra para os recursos de Armazenamento Azure e Cosmos DB utilizados para armazenar dados para o recipiente Face.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

O cenário de armazenamento é manuseado separadamente dos suportes de entrada e dos suportes de saída. Pode especificar uma combinação dessas características para um único recipiente. Por exemplo, o seguinte comando define um `D:\Output` suporte de encaixe do Docker à pasta na máquina hospedeira como a montagem de saída, em seguida, instantaneamente um recipiente da imagem do recipiente Face, guardando ficheiros de registo no formato JSON para o suporte de saída. O comando também especifica o cenário de armazenamento Azure e fornece cordas de conexão de amostra para os recursos de Armazenamento Azure e Cosmos DB utilizados para armazenar dados para o recipiente Face.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Cenário eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de procuração http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações do monte

Utilize suportes de ligação para ler e escrever dados de e para o recipiente. Pode especificar um suporte de entrada ou `--mount` montagem de saída especificando a opção no comando de execução do [estivador.](https://docs.docker.com/engine/reference/commandline/run/)

Os recipientes Face não utilizam suportes de entrada ou saída para armazenar dados de treino ou de serviço. 

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador hospedeiro](face-how-to-install-containers.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço Docker e as permissões de localização do suporte do hospedeiro. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os recipientes faciais não usam isto.|
|Opcional| `Output` | String | O alvo do suporte de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui troncos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos de execução de estivadores exemplo 

Os exemplos seguintes utilizam as definições `docker run` de configuração para ilustrar como escrever e utilizar comandos.  Uma vez em funcionamento, o recipiente continua a funcionar até o [parar.](face-how-to-install-containers.md#stop-the-container)

* **Personagem de continuação da linha**: Os comandos `\`do Docker nas seguintes secções usam o corte traseiro, como um personagem de continuação da linha. Substitua ou remova isto com base nos requisitos do sistema operativo do hospedeiro. 
* **Ordem de argumentação**: Não altere a ordem dos argumentos a menos que esteja muito familiarizado com os contentores do Docker.

Substitua {_argument_name_} com os seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave final `Face` do recurso na `Face` página Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor final da faturação está `Face` disponível na página de visão geral do Azure.| Consulte a [recolha de parâmetros necessários](face-how-to-install-containers.md#gathering-required-parameters) para exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` e as opções devem ser especificadas para executar o recipiente; caso contrário, o contentor não vai começar.  Para mais informações, consulte [billing.](face-how-to-install-containers.md#billing)
> O valor ApiKey **Key** é a `Cognitive Services` chave da página de teclas Azure Resource. 

## <a name="face-container-docker-examples"></a>Face container Exemplos de Docker

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
