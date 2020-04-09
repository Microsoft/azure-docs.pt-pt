---
title: Como configurar um recipiente para API do Detetor de Anomalias
titleSuffix: Azure Cognitive Services
description: O ambiente de funcionação do recipiente API do detetor de anomalias é configurado utilizando os argumentos de `docker run` comando. Este recipiente tem várias configurações necessárias, juntamente com algumas configurações opcionais.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 569499002c5e047d7030575342790e9a074b9404
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875185"
---
# <a name="configure-anomaly-detector-containers"></a>Configurar contentores do Detetor de Anomalias

O ambiente de funcionação do recipiente `docker run` do detetor de **anomalias** é configurado utilizando os argumentos de comando. Este recipiente tem várias configurações necessárias, juntamente com algumas configurações opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do recipiente são as definições de faturação. 

## <a name="configuration-settings"></a>Definições de configuração

Este recipiente tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Sim|[ApiKey](#apikey-configuration-setting)|Usado para rastrear informações de faturação.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite-lhe adicionar suporte de telemetria [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) ao seu recipiente.|
|Sim|[Faturação](#billing-configuration-setting)|Especifica o ponto final URI do recurso de serviço no Azure.|
|Sim|[Eula](#eula-setting)| Indica que aceitou a licença para o contentor.|
|Não|[Fluente](#fluentd-settings)|Escreva registo e, opcionalmente, dados métricos para um servidor Fluente.|
|Não|[Http Proxy](#http-proxy-credentials-settings)|Configure um proxy HTTP para fazer pedidos de saída.|
|Não|[Registo](#logging-settings)|Fornece ASP.NET suporte de exploração madeireira Core para o seu recipiente. |
|Não|[Montes](#mount-settings)|Leia e escreva dados do computador de acolhimento para o contentor e do contentor de volta para o computador anfitrião.|

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)definições [`Eula`](#eula-setting) e as configurações são utilizadas em conjunto, e deve fornecer valores válidos para todos os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas definições de configuração para instantaneamente um recipiente, consulte [a Faturação](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Definição de configuração ApiKey

A `ApiKey` definição especifica a chave de recursos Azure utilizada para rastrear as informações de faturação do recipiente. Deve especificar um valor para o ApiKey e o valor deve ser uma [`Billing`](#billing-configuration-setting) chave válida para o recurso do Detetor de _Anomalias_ especificado para a definição de configuração.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: Gestão de Recursos do Detetor de **Anomalias,** em **Keys**

## <a name="applicationinsights-setting"></a>Definição de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso Do Detetor de _Anomalias_ em Azure utilizado para medir informações de faturação para o recipiente. Deve especificar um valor para esta configuração, e o valor deve ser um uri de ponto final válido para um recurso do Detetor de _Anomalias_ no Azure.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: Visão geral **do Detetor de Anomalias,** rotulada`Endpoint`

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | Ponto final de faturação URI. Para obter mais informações sobre a obtenção do URI de faturação, consulte a [recolha de parâmetros necessários](anomaly-detector-container-howto.md#gathering-required-parameters). Para mais informações e uma lista completa de pontos finais regionais, consulte [nomes de subdomínio personalizado para Serviços Cognitivos](../cognitive-services-custom-subdomains.md). |

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

Os recipientes do Detetor de Anomalias não utilizam suportes de entrada ou saída para armazenar dados de treino ou de serviço. 

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador hospedeiro](anomaly-detector-container-howto.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço Docker e as permissões de localização do suporte do hospedeiro. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os recipientes do Detetor de Anomalias não usam isto.|
|Opcional| `Output` | String | O alvo do suporte de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui troncos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos de execução de estivadores exemplo 

Os exemplos seguintes utilizam as definições `docker run` de configuração para ilustrar como escrever e utilizar comandos.  Uma vez em funcionamento, o recipiente continua a funcionar até o [parar.](anomaly-detector-container-howto.md#stop-the-container)

* **Personagem de continuação da linha**: Os comandos `\`do Docker nas seguintes secções usam o corte traseiro, como um personagem de continuação de linha para uma concha de batida. Substitua ou remova isto com base nos requisitos do sistema operativo do hospedeiro. Por exemplo, o carácter de continuação `^`da linha para janelas é um cuidado, . Substitua o corte traseiro pelo cuidado. 
* **Ordem de argumentação**: Não altere a ordem dos argumentos a menos que esteja muito familiarizado com os contentores do Docker.

Substitua o valor `{}`nos suportes, com os seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave final `Anomaly Detector` do recurso na `Anomaly Detector` página Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor final da faturação está `Anomaly Detector` disponível na página de visão geral do Azure.| Consulte a [recolha de parâmetros necessários](anomaly-detector-container-howto.md#gathering-required-parameters) para exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` e as opções devem ser especificadas para executar o recipiente; caso contrário, o contentor não vai começar.  Para mais informações, consulte [billing.](anomaly-detector-container-howto.md#billing)
> O valor ApiKey é a **chave** da página de teclas de recurso do detetor de anomalias Azure. 

## <a name="anomaly-detector-container-docker-examples"></a>Dados do contentor do detetor de anomalias Exemplos do Docker

Os seguintes exemplos do Docker são para o contentor do Detetor de Anomalias. 

### <a name="basic-example"></a>Exemplo básico 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Exemplo de registo com argumentos de linha de comando

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Passos seguintes

* [Implante um recipiente de detetor de anomalias para instâncias de contentores de Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Saiba mais sobre o serviço API do Detetor de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
