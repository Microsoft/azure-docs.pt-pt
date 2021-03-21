---
title: Configurações do contentor do estivador - LUIS
titleSuffix: Azure Cognitive Services
description: O ambiente de funcionamento do recipiente LUIS é configurado utilizando os argumentos de `docker run` comando. O LUIS tem várias configurações necessárias, juntamente com algumas configurações opcionais.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a4f2b07edc6c290fa030621a4dc400ab50890bba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001198"
---
# <a name="configure-language-understanding-docker-containers"></a>Configurar recipientes de estiva de linguagem 

O ambiente de tempo de funcionamento do recipiente **de compreensão** linguística (LUIS) é configurado utilizando os argumentos de `docker run` comando. O LUIS tem várias configurações necessárias, juntamente com algumas configurações opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do contentor são as [definições de montagem de](#mount-settings) entrada e as definições de faturação. 

## <a name="configuration-settings"></a>Definições de configuração

Este recipiente tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Yes|[ApiKey](#apikey-setting)|Usado para rastrear informação de faturação.|
|No|[ApplicationInsights](#applicationinsights-setting)|Permite-lhe adicionar suporte de telemetria [Azure Application Insights](/azure/application-insights) ao seu recipiente.|
|Yes|[Faturação](#billing-setting)|Especifica o ponto final URI do recurso de serviço no Azure.|
|Yes|[Eula](#eula-setting)| Indica que aceitou a licença para o contentor.|
|No|[Fluente](#fluentd-settings)|Escreva log e, opcionalmente, dados métricos para um servidor Fluentd.|
|No|[Http Proxy](#http-proxy-credentials-settings)|Configure um representante HTTP para fazer pedidos de saída.|
|No|[Registo](#logging-settings)|Fornece ASP.NET suporte de registo de registo do núcleo para o seu recipiente. |
|Yes|[Montes](#mount-settings)|Leia e escreva dados do computador anfitrião para o recipiente e do recipiente de volta ao computador anfitrião.|

> [!IMPORTANT]
> As [`ApiKey`](#apikey-setting) [`Billing`](#billing-setting) definições , e [`Eula`](#eula-setting) configurações são utilizadas em conjunto, e deve fornecer valores válidos para os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas configurações para instantaneaizar um recipiente, consulte [Billing](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Definição de ApiKey

A `ApiKey` definição especifica a chave de recursos Azure utilizada para rastrear as informações de faturação do recipiente. Tem de especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso _Serviços Cognitivos_ especificado para a definição de [`Billing`](#billing-setting) configuração.

Esta definição pode ser encontrada nos seguintes locais:

* Portal Azure: **Cognitive Services** Resource Management, em **Chaves**
* Portal LUIS: **Página de definições de chaves e ponto final.** 

Não utilize a chave de arranque nem a chave de autoria. 

## <a name="applicationinsights-setting"></a>Configuração de AplicaçõesInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Definição de faturação

A `Billing` definição especifica o ponto final URI do recurso _Serviços Cognitivos_ no Azure utilizado para medir informações de faturação para o recipiente. Deve especificar um valor para esta configuração, e o valor deve ser um URI de ponto final válido para um recurso _de Serviços Cognitivos_ em Azure. O recipiente relata o uso a cada 10 a 15 minutos.

Esta definição pode ser encontrada nos seguintes locais:

* Portal Azure: Visão geral **dos Serviços Cognitivos,** rotulada `Endpoint`
* Portal LUIS: **Página de definições de chaves e ponto final,** como parte do ponto final URI.

| Necessário | Name | Tipo de dados | Descrição |
|----------|------|-----------|-------------|
| Sim      | `Billing` | string | URI de faturação. Para obter mais informações sobre a obtenção do URI de faturação, consulte [a recolha dos parâmetros necessários](luis-container-howto.md#gathering-required-parameters). Para obter mais informações e uma lista completa de pontos finais regionais, consulte [os nomes de subdomínio personalizados para serviços cognitivos.](../cognitive-services-custom-subdomains.md) |

## <a name="eula-setting"></a>Definição de Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de procuração HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Utilize suportes de ligação para ler e escrever dados de e para o recipiente. Pode especificar um suporte de entrada ou de saída especificando a `--mount` opção no comando de execução do [estivador.](https://docs.docker.com/engine/reference/commandline/run/) 

O recipiente LUIS não utiliza suportes de entrada ou saída para armazenar dados de treino ou serviço. 

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador anfitrião](luis-container-howto.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço do estivador e as permissões de localização do anfitrião. 

A tabela seguinte descreve as definições suportadas.

|Necessário| Name | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Sim| `Input` | String | O alvo do suporte de entrada. O valor predefinido é `/input`. Esta é a localização dos ficheiros do pacote LUIS. <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Não| `Output` | String | O alvo do suporte de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui registos de consulta LUIS e troncos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exemplo de estivador executar comandos

Os exemplos a seguir utilizam as definições de configuração para ilustrar como escrever e utilizar `docker run` comandos.  Uma vez em funcionamento, o recipiente continua a funcionar até o [parar.](luis-container-howto.md#stop-the-container)

* Estes exemplos utilizam o diretório fora da `C:` unidade para evitar quaisquer conflitos de permissão no Windows. Se precisar de utilizar um diretório específico como diretório de entrada, poderá ter de conceder a permissão de serviço do estivador. 
* Não altere a ordem dos argumentos a menos que esteja muito familiarizado com os recipientes de estivadores.
* Se estiver a utilizar um sistema operativo diferente, utilize a sintaxe correta da consola/terminal, a sintaxe da pasta para os suportes e o carácter de continuação da linha para o seu sistema. Estes exemplos assumem uma consola Windows com um carácter de continuação de `^` linha. Como o recipiente é um sistema operativo Linux, o suporte alvo utiliza uma sintaxe de pasta ao estilo Linux.

Substitua {_argument_name_} com os seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave de ponta final do `LUIS` recurso na página Azure `LUIS` Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto final de faturação está disponível na página Azure `LUIS` Overview.| Consulte [os parâmetros necessários](luis-container-howto.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As `Eula` `Billing` opções , e `ApiKey` opções devem ser especificadas para executar o recipiente; caso contrário, o recipiente não arranca. Para mais informações, consulte [Billing.](luis-container-howto.md#billing)
> O valor ApiKey é a **chave** da página Chaves e Endpoints no portal LUIS e também está disponível na página de `Cognitive Services` teclas de recursos Azure. 

### <a name="basic-example"></a>Exemplo básico

O exemplo a seguir tem o menor número possível de argumentos para executar o contentor:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Exemplo de AplicaçõesInsights

O exemplo a seguir define o argumento ApplicationInsights para enviar telemetria para Insights de Aplicação enquanto o recipiente está em funcionamento:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Exemplo de registo 

O comando que se segue define o nível de registo, `Logging:Console:LogLevel` para configurar o nível de registo para [`Information`](https://msdn.microsoft.com) . 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Passos seguintes

* Rever [Como instalar e executar contentores](luis-container-howto.md)
* Consulte a [Resolução de Problemas](troubleshooting.md) para resolver problemas relacionados com a funcionalidade LUIS.
* Use mais [recipientes de serviços cognitivos](../cognitive-services-container-support.md)