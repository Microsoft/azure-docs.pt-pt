---
title: Configure recipientes - Visão Computacional
titleSuffix: Azure Cognitive Services
description: Este artigo mostra-lhe como configurar as definições necessárias e opcionais para reconhecer recipientes de texto em Visão Computacional.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 3be302019c712c13bd29d7ed3781151a1648e847
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879314"
---
# <a name="configure-computer-vision-docker-containers"></a>Configure recipientes de Computer Vision Docker

Configura o ambiente de tempo de execução `docker run` do recipiente Computer Vision utilizando os argumentos de comando. Este recipiente tem várias configurações necessárias, juntamente com algumas configurações opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do recipiente são as definições de faturação. 

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)definições [`Eula`](#eula-setting) e as configurações são utilizadas em conjunto, e deve fornecer valores válidos para todos os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas definições de configuração para instantaneamente um recipiente, consulte [a Faturação](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Definição de configuração ApiKey

A `ApiKey` definição especifica `Cognitive Services` a chave de recursos Azure utilizada para rastrear as informações de faturação do recipiente. Deve especificar um valor para o ApiKey e o valor deve ser uma [`Billing`](#billing-configuration-setting) chave válida para o recurso dos _Serviços Cognitivos_ especificado para a definição de configuração.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: **Cognitive Services** Resource Management, under **Keys**

## <a name="applicationinsights-setting"></a>Definição de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso _Serviços Cognitivos_ em Azure usado para medir informações de faturação para o recipiente. Deve especificar um valor para esta configuração, e o valor deve ser um uri de ponto final válido para um recurso _de Serviços Cognitivos_ no Azure. O recipiente reporta o uso a cada 10 a 15 minutos.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: Visão **geral dos serviços cognitivos,** rotulada`Endpoint`

Lembre-se `vision/v1.0` de adicionar o encaminhamento ao ponto final URI, como mostrado na tabela seguinte. 

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | Ponto final de faturação URI<br><br>Exemplo:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Cenário eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de procuração HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações do monte

Utilize suportes de ligação para ler e escrever dados de e para o recipiente. Pode especificar um suporte de entrada ou `--mount` montagem de saída especificando a opção no comando de execução do [estivador.](https://docs.docker.com/engine/reference/commandline/run/)

Os recipientes Computer Vision não utilizam suportes de entrada ou saída para armazenar dados de treino ou de serviço. 

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador hospedeiro](computer-vision-how-to-install-containers.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço Docker e as permissões de localização do suporte do hospedeiro. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os recipientes Computer Vision não usam isto.|
|Opcional| `Output` | String | O alvo do suporte de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui troncos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos de execução de estivadores exemplo

Os exemplos seguintes utilizam as definições `docker run` de configuração para ilustrar como escrever e utilizar comandos.  Uma vez em funcionamento, o recipiente continua a funcionar até o [parar.](computer-vision-how-to-install-containers.md#stop-the-container)

* **Personagem de continuação da linha**: Os comandos `\`do Docker nas seguintes secções usam o corte traseiro, como um personagem de continuação da linha. Substitua ou remova isto com base nos requisitos do sistema operativo do hospedeiro. 
* **Ordem de argumentação**: Não altere a ordem dos argumentos a menos que esteja muito familiarizado com os contentores do Docker.

Substitua {_argument_name_} com os seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave final `Computer Vision` do recurso na `Computer Vision` página Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor final da faturação está `Computer Vision` disponível na página de visão geral do Azure.| Consulte a [recolha de parâmetros necessários](computer-vision-how-to-install-containers.md#gathering-required-parameters) para exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` e as opções devem ser especificadas para executar o recipiente; caso contrário, o contentor não vai começar.  Para mais informações, consulte [billing.](computer-vision-how-to-install-containers.md#billing)
> O valor ApiKey **Key** é a `Cognitive Services` chave da página de teclas Azure Resource.

## <a name="container-docker-examples"></a>Exemplos de Container Docker

Os seguintes exemplos do Docker são para o recipiente Read.

### <a name="basic-example"></a>Exemplo básico

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exemplo de registo 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Passos seguintes

* Reveja [Como instalar e executar contentores](computer-vision-how-to-install-containers.md).
