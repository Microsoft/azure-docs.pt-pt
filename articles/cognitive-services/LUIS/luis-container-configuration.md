---
title: Definições de contentor do docker
titleSuffix: Language Understanding - Azure Cognitive Services
description: O ambiente de tempo de execução de contentores do LUIS é configurado usando o `docker run` argumentos de comando. LUIS tem várias definições necessárias, juntamente com algumas configurações opcionais.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: dapine
ms.openlocfilehash: 7858d94b6e2a9ef07da9121cb84ffaf6adaa24d3
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360532"
---
# <a name="configure-language-understanding-docker-containers"></a>Configurar contêineres do Docker Reconhecimento vocal 

O ambiente de tempo de execução de contêiner **reconhecimento vocal** (Luis) `docker run` é configurado usando os argumentos de comando. LUIS tem várias definições necessárias, juntamente com algumas configurações opcionais. Várias [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do contentor são a entrada [definições de montagem](#mount-settings) e as definições de faturas. 

## <a name="configuration-settings"></a>Definições de configuração

Este contentor tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Sim|[ApiKey](#apikey-setting)|Utilizado para controlar informações de faturação.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite que adicione [do Azure Application Insights](https://docs.microsoft.com/azure/application-insights) suporte de telemetria ao seu contentor.|
|Sim|[Billing](#billing-setting)|Especifica o URI do ponto de extremidade do recurso de serviço no Azure.|
|Sim|[EULA](#eula-setting)| Indica que aceite a licença para o contentor.|
|Não|[Fluentd](#fluentd-settings)|Escrever o registo e, opcionalmente, dados de métrica para um servidor de Fluentd.|
|Não|[Proxy http](#http-proxy-credentials-settings)|Configure um proxy HTTP para fazer solicitações de saída.|
|Não|[Logging](#logging-settings)|Fornece suporte de registo do ASP.NET Core para o seu contentor. |
|Sim|[Mounts](#mount-settings)|Ler e escrever dados do computador anfitrião para o contentor e do contentor para o computador anfitrião.|

> [!IMPORTANT]
> O [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), e [ `Eula` ](#eula-setting) definições são utilizadas em conjunto, e tem de indicar valores válidos para todas as três-los; caso contrário não inicia o contentor. Para obter mais informações sobre como utilizar estas definições de configuração para criar uma instância de um contentor, consulte [faturação](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>ApiKey definição

O `ApiKey` definição especifica a chave de recurso do Azure utilizada para controlar informações de faturação para o contentor. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso de _Serviços cognitivas_ especificado [`Billing`](#billing-setting) para a definição de configuração.

Essa configuração pode ser encontrada nos seguintes locais:

* Portal do Azure: **Serviços cognitivas** Gerenciamento de recursos, em **chaves**
* Portal do LUIS: Página **configurações de ponto de extremidade e chaves** . 

Não utilize a chave de arranque ou a chave de criação. 

## <a name="applicationinsights-setting"></a>Definição do Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Definição de faturação

A `Billing` configuração especifica o URI do ponto de extremidade do recurso de _Serviços cognitivas_ no Azure usado para medir as informações de cobrança do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso de _Serviços cognitivas_ no Azure. O contêiner relata o uso de cada 10 a 15 minutos.

Essa configuração pode ser encontrada nos seguintes locais:

* Portal do Azure: **Serviços cognitivas** Visão geral, rotulada`Endpoint`
* Portal do LUIS: Página **configurações de ponto de extremidade e chaves** , como parte do URI do ponto de extremidade.

Lembre-se de `luis/v2.0` incluir o roteamento na URL, conforme mostrado na tabela a seguir:


|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia | URI do ponto final de faturação<br><br>Exemplo:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>EULA definição

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Definições de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações de credenciais de proxy http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Definições de montagem

Monta o enlace de utilização para ler e escrever dados de e para o contentor. Pode especificar uma montagem de entrada ou saída de montagem, especificando o `--mount` opção da [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando. 

O contentor de LUIS não usa a entrada ou saída monta para armazenar dados de serviço ou de treinamento. 

A sintaxe exata da localização de montagem do anfitrião varia consoante o sistema operativo anfitrião. Além disso, o [computador anfitrião](luis-container-howto.md#the-host-computer)da localização de montagem não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do docker e o anfitrião montar permissões de localização. 

A tabela seguinte descreve as definições suportadas.

|Necessário| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Sim| `Input` | Cadeia | O destino da montagem de entrada. O valor predefinido é `/input`. Esta é a localização dos ficheiros de pacote do LUIS. <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Não| `Output` | Cadeia | O destino de montagem de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui registos de consulta do LUIS e registos de contentor. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Executar comandos de docker de exemplo

Os exemplos seguintes utilizam as definições de configuração para ilustrar como escrever e usar `docker run` comandos.  Quando em execução, o contentor continua a ser executada até [parar](luis-container-howto.md#stop-the-container) -lo.

* Esses exemplos usam o diretório fora da `c:` unidade para evitar conflitos de permissão no Windows. Se precisar de utilizar um diretório específico como a entrada de diretório, poderá ter de conceder o docker permissão de serviço. 
* Não altere a ordem dos argumentos, a menos que está bastante familiarizada com contentores do docker.
* Se você estiver usando um sistema operacional diferente, use o console/terminal correto, a sintaxe de pasta para montagens e o caractere de continuação de linha para o seu sistema. Esses exemplos pressupõem um console do Windows com um caractere `^`de continuação de linha. Como o contêiner é um sistema operacional Linux, a montagem de destino usa uma sintaxe de pasta em estilo Linux.

Lembre-se de `luis/v2.0` incluir o roteamento na URL, conforme mostrado na tabela a seguir.

Substitua {_argument_name_} pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou de exemplo |
|-------------|-------|---|
|{API_KEY} | A chave de ponto final da aplicação LUIS treinada. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URL} | O valor do ponto de extremidade de cobrança está `Cognitive Services` disponível na página Visão geral do Azure. |https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](luis-container-howto.md#billing).
> O valor ApiKey é a **chave** da página chaves e pontos de extremidade no portal do Luis e também está disponível na página chaves de `Cognitive Services` recurso do Azure. 

### <a name="basic-example"></a>Exemplo básico

O exemplo seguinte tem os argumentos de menor número possíveis de executar o contentor:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Exemplo do Application Insights

O exemplo seguinte define o argumento do Application Insights para enviar telemetria para o Application Insights, enquanto o contentor está em execução:

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

### <a name="logging-example"></a>Exemplo de log 

O comando a seguir define o nível de registo `Logging:Console:LogLevel`, para configurar o nível de registo para [ `Information` ](https://msdn.microsoft.com). 

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

## <a name="next-steps"></a>Passos Seguintes

* Revisão [como instalar e executar contentores](luis-container-howto.md)
* Consulte [solução de problemas](troubleshooting.md) para resolver problemas relacionados à funcionalidade do Luis.
* Usar mais [contêineres de serviços cognitivas](../cognitive-services-container-support.md)
