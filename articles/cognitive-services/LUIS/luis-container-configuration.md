---
title: Configurações do contentor do Estivador - LUIS
titleSuffix: Azure Cognitive Services
description: O ambiente de tempo de execução do recipiente LUIS é configurado utilizando os argumentos de `docker run` comando. O LUIS tem várias definições necessárias, juntamente com algumas definições opcionais.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 48a9856c58a815eabcc0b105efcd548e66ddd552
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874216"
---
# <a name="configure-language-understanding-docker-containers"></a>Configure recipientes de compreensão de linguagem 

O ambiente de tempo de execução do `docker run` recipiente **de compreensão linguística** (LUIS) é configurado utilizando os argumentos de comando. O LUIS tem várias definições necessárias, juntamente com algumas definições opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do recipiente são as [definições](#mount-settings) de suporte de entrada e as definições de faturação. 

## <a name="configuration-settings"></a>Definições de configuração

Este recipiente tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Sim|[ApiKey](#apikey-setting)|Usado para rastrear informações de faturação.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite-lhe adicionar suporte de telemetria [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) ao seu recipiente.|
|Sim|[Faturação](#billing-setting)|Especifica o ponto final URI do recurso de serviço no Azure.|
|Sim|[Eula](#eula-setting)| Indica que aceitou a licença para o contentor.|
|Não|[Fluente](#fluentd-settings)|Escreva registo e, opcionalmente, dados métricos para um servidor Fluente.|
|Não|[Http Proxy](#http-proxy-credentials-settings)|Configure um proxy HTTP para fazer pedidos de saída.|
|Não|[Registo](#logging-settings)|Fornece ASP.NET suporte de exploração madeireira Core para o seu recipiente. |
|Sim|[Montes](#mount-settings)|Leia e escreva dados do computador de acolhimento para o contentor e do contentor de volta para o computador anfitrião.|

> [!IMPORTANT]
> As [`ApiKey`](#apikey-setting) [`Billing`](#billing-setting)definições [`Eula`](#eula-setting) e as configurações são utilizadas em conjunto, e deve fornecer valores válidos para todos os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas definições de configuração para instantaneamente um recipiente, consulte [a Faturação](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Definição ApiKey

A `ApiKey` definição especifica a chave de recursos Azure utilizada para rastrear as informações de faturação do recipiente. Deve especificar um valor para o ApiKey e o valor deve ser uma [`Billing`](#billing-setting) chave válida para o recurso dos _Serviços Cognitivos_ especificado para a definição de configuração.

Esta definição pode ser encontrada nos seguintes locais:

* Portal Azure: **Cognitive Services** Resource Management, under **Keys**
* Portal LUIS: Página de definições de **teclas e ponto final.** 

Não utilize a tecla de arranque nem a chave de autor. 

## <a name="applicationinsights-setting"></a>Definição de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso _Serviços Cognitivos_ em Azure usado para medir informações de faturação para o recipiente. Deve especificar um valor para esta configuração, e o valor deve ser um uri de ponto final válido para um recurso _de Serviços Cognitivos_ no Azure. O recipiente reporta o uso a cada 10 a 15 minutos.

Esta definição pode ser encontrada nos seguintes locais:

* Portal Azure: Visão **geral dos serviços cognitivos,** rotulada`Endpoint`
* Portal LUIS: Página de definições de **chaves e ponto final,** como parte do ponto final URI.

| Necessário | Nome | Tipo de dados | Descrição |
|----------|------|-----------|-------------|
| Sim      | `Billing` | string | Ponto final de faturação URI. Para obter mais informações sobre a obtenção do URI de faturação, consulte a [recolha de parâmetros necessários](luis-container-howto.md#gathering-required-parameters). Para mais informações e uma lista completa de pontos finais regionais, consulte [nomes de subdomínio personalizado para Serviços Cognitivos](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Cenário eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de procuração HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações do monte

Utilize suportes de ligação para ler e escrever dados de e para o recipiente. Pode especificar um suporte de entrada ou `--mount` montagem de saída especificando a opção no comando de execução do [estivador.](https://docs.docker.com/engine/reference/commandline/run/) 

O recipiente LUIS não utiliza suportes de entrada ou saída para armazenar dados de formação ou de serviço. 

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador hospedeiro](luis-container-howto.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço do estivador e as permissões de localização do suporte do hospedeiro. 

A tabela seguinte descreve as definições suportadas.

|Necessário| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Sim| `Input` | String | O alvo do suporte de entrada. O valor predefinido é `/input`. Esta é a localização dos ficheiros pacote LUIS. <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Não| `Output` | String | O alvo do suporte de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui registos de consulta LUIS e registos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos de execução de estivadores exemplo

Os exemplos seguintes utilizam as definições `docker run` de configuração para ilustrar como escrever e utilizar comandos.  Uma vez em funcionamento, o recipiente continua a funcionar até o [parar.](luis-container-howto.md#stop-the-container)

* Estes exemplos usam o `C:` diretório fora da unidade para evitar quaisquer conflitos de permissões no Windows. Se precisar de usar um diretório específico como diretório de entrada, poderá ter de conceder a permissão de serviço do estivador. 
* Não altere a ordem dos argumentos a menos que esteja muito familiarizado com os recipientes de estivadores.
* Se estiver a utilizar um sistema operativo diferente, utilize a consola/terminal correto, a sintaxe de pasta para os suportes e o carácter de continuação da linha para o seu sistema. Estes exemplos assumem uma consola `^`Windows com um carácter de continuação de linha . Como o recipiente é um sistema operativo Linux, o suporte-alvo usa uma sintaxe de pasta estilo Linux.

Substitua {_argument_name_} com os seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave final `LUIS` do recurso na `LUIS` página Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor final da faturação está `LUIS` disponível na página de visão geral do Azure.| Consulte a [recolha de parâmetros necessários](luis-container-howto.md#gathering-required-parameters) para exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` e as opções devem ser especificadas para executar o recipiente; caso contrário, o contentor não vai começar. Para mais informações, consulte [billing.](luis-container-howto.md#billing)
> O valor ApiKey é a **chave** da página Keys e Endpoints no `Cognitive Services` portal LUIS e também está disponível na página de chaves de recursos Azure. 

### <a name="basic-example"></a>Exemplo básico

O exemplo que se segue tem o menor número de argumentos possíveis para executar o contentor:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Exemplo de ApplicationInsights

O exemplo que se segue define o argumento ApplicationInsights para enviar telemetria para A Aplicação Insights enquanto o recipiente está em execução:

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

O comando seguinte define o `Logging:Console:LogLevel`nível de exploração, para configurar o nível de exploração madeireira para [`Information`](https://msdn.microsoft.com). 

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
