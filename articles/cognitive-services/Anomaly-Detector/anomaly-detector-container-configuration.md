---
title: Configurar contentores - detetor de anomalias
titleSuffix: Azure Cognitive Services
description: O ambiente de tempo de execução de contentores detetor de anomalias é configurado usando o `docker run` argumentos de comando. Este contentor tem várias definições necessárias, juntamente com algumas configurações opcionais.
services: cognitive-services
author: IEvangelist
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: e6b5bcefb2a8df136d37dad062fbca651c312dc4
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275510"
---
# <a name="configure-anomaly-detector-containers"></a>Configurar contentores detetor de anomalias

O **detetor de anomalias** ambiente de tempo de execução de contentores é configurado usando o `docker run` argumentos de comando. Este contentor tem várias definições necessárias, juntamente com algumas configurações opcionais. Várias [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do contentor são as definições de faturas. 

# <a name="configuration-settings"></a>Definições de configuração

Este contentor tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Sim|[ApiKey](#apikey-configuration-setting)|Utilizado para controlar informações de faturação.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite que adicione [do Azure Application Insights](https://docs.microsoft.com/azure/application-insights) suporte de telemetria ao seu contentor.|
|Sim|[Billing](#billing-configuration-setting)|Especifica o ponto final do URI do recurso de serviço no Azure.|
|Sim|[EULA](#eula-setting)| Indica que aceite a licença para o contentor.|
|Não|[Fluentd](#fluentd-settings)|Escrever o registo e, opcionalmente, dados de métrica para um servidor de Fluentd.|
|Não|[Proxy de HTTP](#http-proxy-credentials-settings)|Configure um proxy HTTP para fazer pedidos de saída.|
|Não|[Logging](#logging-settings)|Fornece suporte de registo do ASP.NET Core para o seu contentor. |
|Não|[Mounts](#mount-settings)|Ler e escrever dados do computador anfitrião para o contentor e do contentor para o computador anfitrião.|

> [!IMPORTANT]
> O [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), e [ `Eula` ](#eula-setting) definições são utilizadas em conjunto, e tem de indicar valores válidos para todas as três-los; caso contrário não inicia o contentor. Para obter mais informações sobre como utilizar estas definições de configuração para criar uma instância de um contentor, consulte [faturação](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey definição de configuração

O `ApiKey` definição especifica a chave de recurso do Azure utilizada para controlar informações de faturação para o contentor. Tem de especificar um valor para o ApiKey e o valor tem de ser uma chave válida para o _detetor de anomalias_ recurso especificado para o [ `Billing` ](#billing-configuration-setting) definição de configuração.

Esta definição pode ser encontrada no seguinte local:

* Portal do Azure: **Detetor de anomalias** gestão de recursos, em **chaves**

## <a name="applicationinsights-setting"></a>Definição do Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

O `Billing` definição especifica o URI do ponto final da _detetor de anomalias_ recurso no Azure utilizado para informações de faturação para o contentor do medidor. Tem de especificar um valor para esta definição de configuração e o valor tem de ser um URI de ponto de final válido para um _detetor de anomalias_ recursos no Azure.

Esta definição pode ser encontrada no seguinte local:

* Portal do Azure: **Detetor de anomalias** descrição geral, o nome `Endpoint`

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia | URI do ponto final de faturação<br><br>Exemplo:<br>`Billing=https://westus2.api.cognitive.microsoft.com` |

## <a name="eula-setting"></a>EULA definição

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Definições de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de proxy de HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Definições de montagem

Monta o enlace de utilização para ler e escrever dados de e para o contentor. Pode especificar uma montagem de entrada ou saída de montagem, especificando o `--mount` opção da [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando.

Os contentores de anomalias detetor não usam a entrada ou saída monta para armazenar dados de serviço ou de treinamento. 

A sintaxe exata da localização de montagem do anfitrião varia consoante o sistema operativo anfitrião. Além disso, o [computador anfitrião](anomaly-detector-container-howto.md#the-host-computer)da localização de montagem não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e o anfitrião montar permissões de localização. 

|Opcional| Name | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Contentores de detetor de anomalias não utilize esta opção.|
|Opcional| `Output` | String | O destino de montagem de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui registos de contentor. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Executar comandos de docker de exemplo 

Os exemplos seguintes utilizam as definições de configuração para ilustrar como escrever e usar `docker run` comandos.  Quando em execução, o contentor continua a ser executada até [parar](anomaly-detector-container-howto.md#stop-the-container) -lo.

* **Caracteres de continuação de linha**: Os comandos de Docker nas secções seguintes utilizam a barra invertida, `\`, como um caractere de continuação de linha de um shell de bash. Substituir ou remova esta com base nos requisitos de seu sistema de operativo de anfitrião. Por exemplo, o caráter de continuação de linha para o windows é um sinal de interpolação `^`. Substitua a barra invertida o sinal de interpolação. 
* **Ordem de argumento**: Não altere a ordem dos argumentos, a menos que está bastante familiarizada com contentores do Docker.

Substitua o valor entre parênteses Retos, `{}`, com seus próprios valores:

| Marcador de posição | Valor | Formato ou de exemplo |
|-------------|-------|---|
|{BILLING_KEY} | A chave de ponto final do recurso detetor de anomalias. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | O valor de ponto final faturação incluindo região.|`https://westus2.api.cognitive.microsoft.com`|

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](anomaly-detector-container-howto.md#billing).
> O valor de ApiKey é o **chave** na página de chaves do recurso de detetor de anomalias do Azure. 

## <a name="anomaly-detector-container-docker-examples"></a>Exemplos de Docker de contentor de detetor de anomalias

Os exemplos de Docker seguintes são para o contentor de detetor de anomalias. 

### <a name="basic-example"></a>Exemplo básico 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Exemplo de Registro em log com argumentos da linha de comandos

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```
