---
title: Configurar contêineres-Análise de Texto
titleSuffix: Azure Cognitive Services
description: Análise de texto fornece cada contentor com uma estrutura comum de configuração, para que possa facilmente configurar e gerir as definições de armazenamento, registo e telemetria e segurança para os seus contentores.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: dapine
ms.openlocfilehash: 65d88e6c201f633a260e31544444341e636e9941
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552260"
---
# <a name="configure-text-analytics-docker-containers"></a>Configurar contêineres do Docker Análise de Texto

Análise de texto fornece cada contentor com uma estrutura comum de configuração, para que possa facilmente configurar e gerir as definições de armazenamento, registo e telemetria e segurança para os seus contentores.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> O [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), e [ `Eula` ](#eula-setting) definições são utilizadas em conjunto, e tem de indicar valores válidos para todas as três-los; caso contrário não inicia o contentor. Para obter mais informações sobre como utilizar estas definições de configuração para criar uma instância de um contentor, consulte [faturação](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey definição de configuração

O `ApiKey` definição especifica a chave de recurso do Azure utilizada para controlar informações de faturação para o contentor. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o _análise de texto_ recurso especificado para a [`Billing`](#billing-configuration-setting) definição de configuração.

Essa configuração pode ser encontrada no seguinte local:

* Portal do Azure: **Análise de texto** o gerenciamento de recursos, em **chaves**

## <a name="applicationinsights-setting"></a>Definição do Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` configuração especifica o URI do ponto de extremidade do recurso de _análise de texto_ no Azure usado para medir as informações de cobrança do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso __análise de texto_ no Azure. O contêiner relata o uso de cada 10 a 15 minutos.

Essa configuração pode ser encontrada no seguinte local:

* Portal do Azure: **Análise de texto** Visão geral, rotulada`Endpoint`

|Requerido| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia | O URI do ponto de extremidade de cobrança necessário |

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

Os contêineres de Análise de Texto não usam montagens de entrada ou de saída para armazenar dados de treinamento ou de serviço. 

A sintaxe exata da localização de montagem do anfitrião varia consoante o sistema operativo anfitrião. Além disso, o [computador anfitrião](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)da localização de montagem não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do docker e o anfitrião montar permissões de localização. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | Cadeia | Os contêineres de Análise de Texto não usam isso.|
|Opcional| `Output` | Cadeia | O destino de montagem de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Executar comandos de docker de exemplo 

Os exemplos seguintes utilizam as definições de configuração para ilustrar como escrever e usar `docker run` comandos.  Quando em execução, o contentor continua a ser executada até [parar](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) -lo.

* **Caractere de continuação de linha**: Os comandos do Docker nas seções a seguir usam a barra `\`invertida,, como um caractere de continuação de linha. Substituir ou remova esta com base nos requisitos de seu sistema de operativo de anfitrião. 
* **Ordem do argumento**: Não altere a ordem dos argumentos, a menos que está bastante familiarizada com contentores do docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou de exemplo |
|-------------|-------|---|
|{API_KEY} | A chave do ponto de `Text Analytics` extremidade do recurso disponível na `Text Analytics` página chaves do Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
|{ENDPOINT_URI} | O valor do ponto de extremidade de cobrança está `Text Analytics` disponível na página Visão geral do Azure.|`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](how-tos/text-analytics-how-to-install-containers.md#billing).
> O valor de ApiKey é a **chave** da página `Text Analytics` de chaves de recurso do Azure. 

## <a name="key-phrase-extraction-container-docker-examples"></a>Exemplos de encaixe de contêiner de extração de frases-chave

Os exemplos do Docker a seguir são para o contêiner de extração de frases-chave. 

### <a name="basic-example"></a>Exemplo básico 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exemplo de log 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="language-detection-container-docker-examples"></a>Exemplos de Docker de contêiner de detecção de idioma

Os exemplos do Docker a seguir são para o contêiner detecção de idioma. 

### <a name="basic-example"></a>Exemplo básico

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exemplo de log

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} Logging:Console:LogLevel:Default=Information
  ```
 
## <a name="sentiment-analysis-container-docker-examples"></a>Exemplos de Docker de contêiner de análise de opiniões

Os exemplos do Docker a seguir são para o contêiner de análise de sentimentos. 

### <a name="basic-example"></a>Exemplo básico

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exemplo de log

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Passos Seguintes

* Revisão [como instalar e executar contentores](how-tos/text-analytics-how-to-install-containers.md)
* Usar mais [contêineres de serviços cognitivas](../cognitive-services-container-support.md)
