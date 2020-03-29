---
title: Configure recipientes - Análise de Texto
titleSuffix: Azure Cognitive Services
description: O Text Analytics fornece a cada recipiente uma estrutura de configuração comum, para que possa configurar e gerir facilmente o armazenamento, a extração e a telemetria e as definições de segurança dos seus contentores.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795296"
---
# <a name="configure-text-analytics-docker-containers"></a>Configure Recipientes de estivador de análise de texto

O Text Analytics fornece a cada recipiente uma estrutura de configuração comum, para que possa configurar e gerir facilmente o armazenamento, a extração e a telemetria e as definições de segurança dos seus contentores.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)definições [`Eula`](#eula-setting) e as configurações são utilizadas em conjunto, e deve fornecer valores válidos para todos os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas definições de configuração para instantaneamente um recipiente, consulte [a Faturação](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Definição de configuração ApiKey

A `ApiKey` definição especifica a chave de recursos Azure utilizada para rastrear as informações de faturação do recipiente. Deve especificar um valor para o ApiKey e o valor deve ser uma [`Billing`](#billing-configuration-setting) chave válida para o recurso Text _Analytics_ especificado para a definição de configuração.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: Gestão de recursos de Análise de **Texto,** em **Keys**

## <a name="applicationinsights-setting"></a>Definição de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso _Text Analytics_ no Azure utilizado para medir as informações de faturação do recipiente. Deve especificar um valor para esta configuração, e o valor deve ser um uri de ponto final válido para um recurso __Text Analytics_ no Azure. O recipiente reporta o uso a cada 10 a 15 minutos.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: Visão geral **do Text Analytics,** rotulada`Endpoint`

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia | Ponto final de faturação URI. Para obter mais informações sobre a obtenção do URI de faturação, consulte a [recolha de parâmetros necessários](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters). Para mais informações e uma lista completa de pontos finais regionais, consulte [nomes de subdomínio personalizado para Serviços Cognitivos](../cognitive-services-custom-subdomains.md). |

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

Os recipientes Text Analytics não utilizam suportes de entrada ou saída para armazenar dados de formação ou de serviço. 

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador hospedeiro](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço do estivador e as permissões de localização do suporte do hospedeiro. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | Cadeia | Os recipientes de Análise de Texto não usam isto.|
|Opcional| `Output` | Cadeia | O alvo do suporte de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui troncos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos de execução de estivadores exemplo 

Os exemplos seguintes utilizam as definições `docker run` de configuração para ilustrar como escrever e utilizar comandos.  Uma vez em funcionamento, o recipiente continua a funcionar até o [parar.](how-tos/text-analytics-how-to-install-containers.md#stop-the-container)

* **Carácter de continuação da linha**: Os comandos do `\`estivador nas seguintes secções utilizam a barra traseira, como um carácter de continuação da linha. Substitua ou remova isto com base nos requisitos do sistema operativo do hospedeiro. 
* **Ordem de argumentação**: Não altere a ordem dos argumentos a menos que esteja muito familiarizado com os recipientes de estivadores.

Substitua {_argument_name_} com os seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave final `Text Analytics` do recurso disponível `Text Analytics` na página Azure Keys. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | O valor final da faturação está `Text Analytics` disponível na página de visão geral do Azure.| Consulte a [recolha de parâmetros necessários](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) para exemplos explícitos. |

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` e as opções devem ser especificadas para executar o recipiente; caso contrário, o contentor não vai começar.  Para mais informações, consulte [billing.](how-tos/text-analytics-how-to-install-containers.md#billing)
> O valor ApiKey **Key** é a `Text Analytics` chave da página de teclas Azure Resource. 

#### <a name="key-phrase-extraction"></a>[Extração de Expressões-Chave](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detection"></a>[Deteção de Idiomas](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Passos seguintes

* Rever [Como instalar e executar contentores](how-tos/text-analytics-how-to-install-containers.md)
* Use mais [recipientes de serviços cognitivos](../cognitive-services-container-support.md)
