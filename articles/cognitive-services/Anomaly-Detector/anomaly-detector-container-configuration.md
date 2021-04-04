---
title: Como configurar um recipiente para a API do Detetor de Anomalias
titleSuffix: Azure Cognitive Services
description: O ambiente de funcionamento do contentor API do detetor de anomalias está configurado utilizando os argumentos de `docker run` comando. Este recipiente tem várias configurações necessárias, juntamente com algumas configurações opcionais.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mbullwin
ms.openlocfilehash: c175a52259e9cfe5b4d03ce0279bbe24d16a48ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "94363719"
---
# <a name="configure-anomaly-detector-containers"></a>Configurar contentores do Detetor de Anomalias

O ambiente de funcionamento **do detetor de anomalias** é configurado utilizando os argumentos de `docker run` comando. Este recipiente tem várias configurações necessárias, juntamente com algumas configurações opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do contentor são as definições de faturação. 

## <a name="configuration-settings"></a>Definições de configuração

Este recipiente tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Usado para rastrear informação de faturação.|
|No|[ApplicationInsights](#applicationinsights-setting)|Permite-lhe adicionar suporte de telemetria [Azure Application Insights](/azure/application-insights) ao seu recipiente.|
|Yes|[Faturação](#billing-configuration-setting)|Especifica o ponto final URI do recurso de serviço no Azure.|
|Yes|[Eula](#eula-setting)| Indica que aceitou a licença para o contentor.|
|No|[Fluente](#fluentd-settings)|Escreva log e, opcionalmente, dados métricos para um servidor Fluentd.|
|No|[Http Proxy](#http-proxy-credentials-settings)|Configure um representante HTTP para fazer pedidos de saída.|
|No|[Registo](#logging-settings)|Fornece ASP.NET suporte de registo de registo do núcleo para o seu recipiente. |
|No|[Montes](#mount-settings)|Leia e escreva dados do computador anfitrião para o recipiente e do recipiente de volta ao computador anfitrião.|

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) definições , e [`Eula`](#eula-setting) configurações são utilizadas em conjunto, e deve fornecer valores válidos para os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas configurações para instantaneaizar um recipiente, consulte [Billing](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Definição de configuração apikey

A `ApiKey` definição especifica a chave de recursos Azure utilizada para rastrear as informações de faturação do recipiente. Tem de especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso _de detetor de anomalias_ especificado para a definição de [`Billing`](#billing-configuration-setting) configuração.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: **Gestão de** Recursos do Detetor de Anomalias, em **Chaves**

## <a name="applicationinsights-setting"></a>Configuração de AplicaçõesInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso de detetor de _anomalias_ no Azure utilizado para medir informações de faturação para o recipiente. Tem de especificar um valor para esta definição de configuração, e o valor deve ser um URI de ponto final válido para um recurso _de detetor de anomalias_ no Azure.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: **Visão** geral do Detetor de Anomalias, etiquetada `Endpoint`

|Necessário| Name | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI de faturação. Para obter mais informações sobre a obtenção do URI de faturação, consulte [a recolha dos parâmetros necessários](anomaly-detector-container-howto.md#gathering-required-parameters). Para obter mais informações e uma lista completa de pontos finais regionais, consulte [os nomes de subdomínio personalizados para serviços cognitivos.](../cognitive-services-custom-subdomains.md) |

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

Os recipientes do Detetor de Anomalias não utilizam suportes de entrada ou saída para armazenar dados de treino ou de serviço. 

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador anfitrião](anomaly-detector-container-howto.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço Docker e as permissões de localização do anfitrião. 

|Opcional| Name | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os recipientes do Detetor de Anomalias não usam isto.|
|Opcional| `Output` | String | O alvo do suporte de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui registos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exemplo de estivador executar comandos 

Os exemplos a seguir utilizam as definições de configuração para ilustrar como escrever e utilizar `docker run` comandos.  Uma vez em funcionamento, o recipiente continua a funcionar até o [parar.](anomaly-detector-container-howto.md#stop-the-container)

* **Carácter de continuação da linha**: Os comandos do Docker nas seguintes secções usam o corte traseiro, `\` como um personagem de continuação de linha para uma casca de bash. Substitua ou remova isto com base nos requisitos do seu sistema operativo anfitrião. Por exemplo, o carácter de continuação da linha para janelas é um cuidado, `^` . Substitua o corte traseiro pelo cuidado. 
* **Ordem de argumento**: Não altere a ordem dos argumentos a menos que esteja muito familiarizado com os recipientes docker.

Substitua o valor nos parênteses, `{}` com os seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave de ponta final do `Anomaly Detector` recurso na página Azure `Anomaly Detector` Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto final de faturação está disponível na página Azure `Anomaly Detector` Overview.| Consulte [os parâmetros necessários](anomaly-detector-container-howto.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As `Eula` `Billing` opções , e `ApiKey` opções devem ser especificadas para executar o recipiente; caso contrário, o recipiente não arranca.  Para mais informações, consulte [Billing.](anomaly-detector-container-howto.md#billing)
> O valor ApiKey é a **chave** da página de teclas de detetor de anomalias Azure. 

## <a name="anomaly-detector-container-docker-examples"></a>Exemplos de estivadores de detetor de anomalias

Os seguintes exemplos do Docker são para o detetor de anomalias. 

### <a name="basic-example"></a>Exemplo básico 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Registar exemplo com argumentos de linha de comando

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Passos seguintes

* [Implantar um detetor de anomalias em instâncias de contentores de Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Saiba mais sobre o serviço API do Detetor de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)