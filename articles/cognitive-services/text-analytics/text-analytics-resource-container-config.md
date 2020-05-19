---
title: Configure recipientes - Análise de Texto
titleSuffix: Azure Cognitive Services
description: O Text Analytics fornece a cada recipiente uma estrutura de configuração comum, para que possa configurar e gerir facilmente o armazenamento, a extração e a telemetria e as definições de segurança dos seus contentores.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f6a1bc652125990a7daf3414895f34b95c544912
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590559"
---
# <a name="configure-text-analytics-docker-containers"></a>Configure Recipientes de estivador de análise de texto

O Text Analytics fornece a cada recipiente uma estrutura de configuração comum, para que possa configurar e gerir facilmente o armazenamento, a extração e a telemetria e as definições de segurança dos seus contentores. Vários comandos de execução de [estivadores](how-tos/text-analytics-how-to-install-containers.md#run-the-container-with-docker-run) também estão disponíveis.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As definições e as [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) [`Eula`](#eula-setting) configurações são utilizadas em conjunto, e você deve fornecer valores válidos para todos os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas definições de configuração para instantaneamente um recipiente, consulte [a Faturação](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Definição de configuração ApiKey

A `ApiKey` definição especifica a chave de recursos Azure utilizada para rastrear as informações de faturação do recipiente. Deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso _Text Analytics_ especificado para a definição de [`Billing`](#billing-configuration-setting) configuração.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: Gestão de recursos de Análise de **Texto,** em **Keys**

## <a name="applicationinsights-setting"></a>Definição de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso _Text Analytics_ no Azure utilizado para medir as informações de faturação do recipiente. Deve especificar um valor para esta configuração, e o valor deve ser um uri de ponto final válido para um recurso __Text Analytics_ no Azure. O recipiente reporta o uso a cada 10 a 15 minutos.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: Visão geral **do Text Analytics,** rotulada`Endpoint`

|Necessário| Name | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | Ponto final de faturação URI. Para obter mais informações sobre a obtenção do URI de faturação, consulte a [recolha de parâmetros necessários](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters). Para mais informações e uma lista completa de pontos finais regionais, consulte [nomes de subdomínio personalizado para Serviços Cognitivos](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Cenário eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de procuração http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações do monte

Utilize suportes de ligação para ler e escrever dados de e para o recipiente. Pode especificar um suporte de entrada ou montagem de saída especificando a `--mount` opção no comando de execução do [estivador.](https://docs.docker.com/engine/reference/commandline/run/)

Os recipientes Text Analytics não utilizam suportes de entrada ou saída para armazenar dados de formação ou de serviço. 

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador hospedeiro](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço do estivador e as permissões de localização do suporte do hospedeiro. 

|Opcional| Name | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os recipientes de Análise de Texto não usam isto.|
|Opcional| `Output` | String | O alvo do suporte de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui troncos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="next-steps"></a>Passos seguintes

* Rever [Como instalar e executar contentores](how-tos/text-analytics-how-to-install-containers.md)
* Use mais [recipientes de serviços cognitivos](../cognitive-services-container-support.md)
