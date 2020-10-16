---
title: Recipientes de configuração - Análise de Texto
titleSuffix: Azure Cognitive Services
description: O Text Analytics fornece a cada recipiente uma estrutura de configuração comum, para que possa configurar e gerir facilmente o armazenamento, registo e telemetria e as definições de segurança para os seus contentores.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83590559"
---
# <a name="configure-text-analytics-docker-containers"></a>Configurar os recipientes de estiva de texto Analytics

O Text Analytics fornece a cada recipiente uma estrutura de configuração comum, para que possa configurar e gerir facilmente o armazenamento, registo e telemetria e as definições de segurança para os seus contentores. Vários [comandos de execução de estivadores](how-tos/text-analytics-how-to-install-containers.md#run-the-container-with-docker-run) de exemplo também estão disponíveis.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) definições , e [`Eula`](#eula-setting) configurações são utilizadas em conjunto, e deve fornecer valores válidos para os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas configurações para instantaneaizar um recipiente, consulte [Billing](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Definição de configuração apikey

A `ApiKey` definição especifica a chave de recursos Azure utilizada para rastrear as informações de faturação do recipiente. Tem de especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso _Text Analytics_ especificado para a definição de [`Billing`](#billing-configuration-setting) configuração.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: Gestão de recursos **de Text Analytics,** em **Chaves**

## <a name="applicationinsights-setting"></a>Configuração de AplicaçõesInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso _Text Analytics_ no Azure utilizado para medir as informações de faturação do recipiente. Tem de especificar um valor para esta definição de configuração, e o valor deve ser um URI de ponto final válido para um recurso __Text Analytics_ em Azure. O recipiente relata o uso a cada 10 a 15 minutos.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: Visão geral da Análise de **Texto,** rotulada `Endpoint`

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI de faturação. Para obter mais informações sobre a obtenção do URI de faturação, consulte [a recolha dos parâmetros necessários](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters). Para obter mais informações e uma lista completa de pontos finais regionais, consulte [os nomes de subdomínio personalizados para serviços cognitivos.](../cognitive-services-custom-subdomains.md) |

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

Os recipientes Text Analytics não utilizam suportes de entrada ou saída para armazenar dados de treino ou de serviço. 

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador anfitrião](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço do estivador e as permissões de localização do anfitrião. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | Cadeia | Os recipientes de análise de texto não utilizam isto.|
|Opcional| `Output` | Cadeia | O alvo do suporte de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui registos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="next-steps"></a>Passos seguintes

* Rever [Como instalar e executar contentores](how-tos/text-analytics-how-to-install-containers.md)
* Use mais [recipientes de serviços cognitivos](../cognitive-services-container-support.md)
