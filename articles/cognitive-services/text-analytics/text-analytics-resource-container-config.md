---
title: Configurar contêineres-Análise de Texto
titleSuffix: Azure Cognitive Services
description: Análise de Texto fornece cada contêiner com uma estrutura de configuração comum, para que você possa configurar e gerenciar facilmente o armazenamento, o registro em log e a telemetria e as configurações de segurança para seus contêineres.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 8a39327275dca43ddb6ce0e46a3e3bb51ec4555b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795296"
---
# <a name="configure-text-analytics-docker-containers"></a>Configurar contêineres do Docker Análise de Texto

Análise de Texto fornece cada contêiner com uma estrutura de configuração comum, para que você possa configurar e gerenciar facilmente o armazenamento, o registro em log e a telemetria e as configurações de segurança para seus contêineres.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)e [`Eula`](#eula-setting) são usadas juntas, e você deve fornecer valores válidos para todos os três; caso contrário, o contêiner não será iniciado. Para obter mais informações sobre como usar essas definições de configuração para criar uma instância de um contêiner, consulte [cobrança](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Configuração de ApiKey

A configuração de `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança para o contêiner. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o _análise de texto_ recurso especificado para a configuração de [`Billing`](#billing-configuration-setting) .

Essa configuração pode ser encontrada no seguinte local:

* Portal do Azure: gerenciamento de recursos **análise de texto** , em **chaves**

## <a name="applicationinsights-setting"></a>Configuração de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A configuração de `Billing` especifica o URI do ponto de extremidade do recurso de _análise de texto_ no Azure usado para medir as informações de cobrança do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso __análise de texto_ no Azure. O contêiner relata o uso de cada 10 a 15 minutos.

Essa configuração pode ser encontrada no seguinte local:

* Portal do Azure: **análise de texto** visão geral, rotulada `Endpoint`

|Necessário| Nome | Data type | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI do ponto de extremidade de cobrança. Para obter mais informações sobre como obter o URI de cobrança, consulte [coletando parâmetros necessários](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters). Para obter mais informações e uma lista completa de pontos de extremidade regionais, consulte [nomes de subdomínio personalizados para serviços cognitivas](../cognitive-services-custom-subdomains.md). |

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

Os contêineres de Análise de Texto não usam montagens de entrada ou de saída para armazenar dados de treinamento ou de serviço. 

A sintaxe exata do local de montagem do host varia dependendo do sistema operacional do host. Além disso, o local de montagem do [computador host](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões de local de montagem do host. 

|Opcional| Nome | Data type | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os contêineres de Análise de Texto não usam isso.|
|Opcional| `Output` | String | O destino da montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos de execução do Docker de exemplo 

Os exemplos a seguir usam as definições de configuração para ilustrar como gravar e usar comandos de `docker run`.  Uma vez em execução, o contêiner continua a ser executado até que você o [interrompa](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) .

* **Caractere de continuação de linha**: os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova isso com base nos requisitos do seu sistema operacional do host. 
* **Ordem do argumento**: não altere a ordem dos argumentos, a menos que você esteja muito familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave do ponto de extremidade do recurso `Text Analytics` disponível na página chaves do `Text Analytics` do Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | O valor do ponto de extremidade de cobrança está disponível na página Visão geral do `Text Analytics` do Azure.| Consulte [coletando parâmetros necessários](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) para obter exemplos explícitos. |

> [!IMPORTANT]
> As opções `Eula`, `Billing`e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para obter mais informações, consulte [cobrança](how-tos/text-analytics-how-to-install-containers.md#billing).
> O valor de ApiKey é a **chave** da página de chaves de recurso do `Text Analytics` do Azure. 

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Extração de Frases-chave](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detectiontablanguage"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Análise de Sentimento](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Passos seguintes

* Examine [como instalar e executar contêineres](how-tos/text-analytics-how-to-install-containers.md)
* Usar mais [contêineres de serviços cognitivas](../cognitive-services-container-support.md)
