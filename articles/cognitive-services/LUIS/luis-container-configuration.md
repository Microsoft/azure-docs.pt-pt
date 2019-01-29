﻿---
title: Definições de contentor do docker
titleSuffix: Language Understanding - Azure Cognitive Services
description: O ambiente de tempo de execução de contentores do LUIS é configurado usando o `docker run` argumentos de comando. LUIS tem várias definições necessárias, juntamente com algumas configurações opcionais.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: 31d6725b6e02bbc583ad80f235360574941a97d3
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468340"
---
# <a name="configure-language-understanding-docker-containers"></a>Configurar os contentores do docker de compreensão de idiomas 

O ambiente de tempo de execução do contentor de compreensão de idiomas (LUIS) é configurado usando o `docker run` argumentos de comando. LUIS tem várias definições necessárias, juntamente com algumas configurações opcionais. Várias [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do contentor são a entrada [definições de montagem](#mount-settings) e as definições de faturas. 

Definições de contentor são [hierárquica](#hierarchical-settings) e podem ser definidas com [variáveis de ambiente](#environment-variable-settings) ou docker [argumentos da linha de comandos](#command-line-argument-settings).

## <a name="configuration-settings"></a>Definições de configuração

Este contentor tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Sim|[ApiKey](#apikey-setting)|Utilizado para controlar informações de faturação.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite que adicione [do Azure Application Insights](https://docs.microsoft.com/azure/application-insights) suporte de telemetria ao seu contentor.|
|Sim|[Billing](#billing-setting)|Especifica o ponto final do URI do recurso de serviço no Azure.|
|Sim|[EULA](#eula-setting)| Indica que aceite a licença para o contentor.|
|Não|[Fluentd](#fluentd-settings)|Escrever o registo e, opcionalmente, dados de métrica para um servidor de Fluentd.|
|Não|[Logging](#logging-settings)|Fornece suporte de registo do ASP.NET Core para o seu contentor. |
|Não|[Logging](#logging-settings)|Fornece suporte de registo do ASP.NET Core para o seu contentor. |
|Sim|[Mounts](#mount-settings)|Ler e escrever dados do computador anfitrião para o contentor e do contentor para o computador anfitrião.|

> [!IMPORTANT]
> O [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), e [ `Eula` ](#eula-setting) definições são utilizadas em conjunto, e tem de indicar valores válidos para todas as três-los; caso contrário não inicia o contentor. Para obter mais informações sobre como utilizar estas definições de configuração para criar uma instância de um contentor, consulte [faturação](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>ApiKey definição

O `ApiKey` definição especifica a chave de recurso do Azure utilizada para controlar informações de faturação para o contentor. Tem de especificar um valor para o ApiKey e o valor tem de ser uma chave válida para o _compreensão de idiomas_ recurso especificado para o [ `Billing` ](#billing-setting) definição de configuração.

Esta definição pode ser encontrada nos seguintes locais:

* Portal do Azure: **Compreensão de idiomas** gestão de recursos, em **chaves**
* Portal de LUIS: **As chaves e definições de ponto final** página. 

Não utilize a chave de arranque ou a chave de criação. 

## <a name="applicationinsights-setting"></a>Definição do Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Definição de faturação

O `Billing` definição especifica o URI do ponto final da _compreensão de idiomas_ recurso no Azure utilizado para informações de faturação para o contentor do medidor. Tem de especificar um valor para esta definição de configuração e o valor tem de ser um URI de ponto de final válido para um _compreensão de idiomas_ recursos no Azure.

Esta definição pode ser encontrada nos seguintes locais:

* Portal do Azure: **Compreensão de idiomas** descrição geral, o nome `Endpoint`
* Portal de LUIS: **As chaves e definições de ponto final** página, como parte do ponto final do URI.

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia | URI do ponto final de faturação<br><br>Exemplo:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

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

O contentor de LUIS não usa a entrada ou saída monta para armazenar dados de serviço ou de treinamento. 

A sintaxe exata da localização de montagem do anfitrião varia consoante o sistema operativo anfitrião. Além disso, o [computador anfitrião](luis-container-howto.md#the-host-computer)da localização de montagem não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do docker e o anfitrião montar permissões de localização. 

A tabela seguinte descreve as definições suportadas.

|Necessário| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Sim| `Input` | Cadeia | O destino da montagem de entrada. O valor predefinido é `/input`. Esta é a localização dos ficheiros de pacote do LUIS. <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Não| `Output` | Cadeia | O destino de montagem de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui registos de consulta do LUIS e registos de contentor. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Definições de hierárquicas

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]


## <a name="example-docker-run-commands"></a>Executar comandos de docker de exemplo

Os exemplos seguintes utilizam as definições de configuração para ilustrar como escrever e usar `docker run` comandos.  Quando em execução, o contentor continua a ser executada até [parar](luis-container-howto.md#stop-the-container) -lo.


* **Caracteres de continuação de linha**: Os comandos de docker nas secções seguintes utilizam a barra invertida, `\`, como um caractere de continuação de linha. Substituir ou remova esta com base nos requisitos de seu sistema de operativo de anfitrião. 
* **Ordem de argumento**: Não altere a ordem dos argumentos, a menos que está bastante familiarizada com contentores do docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou de exemplo |
|-------------|-------|---|
|{ENDPOINT_KEY} | A chave de ponto final da aplicação LUIS treinada. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | O valor de ponto final de faturação está disponível na página de descrição geral de compreensão de idioma do portal do Azure.|https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](luis-container-howto.md#billing).
> O valor de ApiKey é o **chave** também está disponível na página de chaves do recurso de compreensão de linguagem do Azure e as chaves e os pontos finais de página no portal do LUIS. 

### <a name="basic-example"></a>Exemplo básico

O exemplo seguinte tem os argumentos de menor número possíveis de executar o contentor:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> O comando anterior utiliza o diretório a `c:` unidade para evitar conflitos de permissão no Windows. Se precisar de utilizar um diretório específico como a entrada de diretório, poderá ter de conceder o docker permissão de serviço. O comando docker anterior utiliza a barra invertida, `\`, como um caractere de continuação de linha. Substituir ou remover isso com base no seu [computador anfitrião](luis-container-howto.md#the-host-computer) requisitos do sistema operacional. Não altere a ordem dos argumentos, a menos que está bastante familiarizada com contentores do docker.


### <a name="applicationinsights-example"></a>Exemplo do Application Insights

O exemplo seguinte define o argumento do Application Insights para enviar telemetria para o Application Insights, enquanto o contentor está em execução:

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example-with-command-line-arguments"></a>Exemplo de Registro em log com argumentos da linha de comandos

O comando a seguir define o nível de registo `Logging:Console:LogLevel`, para configurar o nível de registo para [ `Information` ](https://msdn.microsoft.com). 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel=Information
```

### <a name="logging-example-with-environment-variable"></a>Exemplo de registo com a variável de ambiente

A utilização de comandos seguinte uma variável de ambiente, chamado `Logging:Console:LogLevel` para configurar o nível de registo para [ `Information` ](https://msdn.microsoft.com). 

```bash
SET Logging:Console:LogLevel=Information
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={APPLICATION_ID} \
```

## <a name="next-steps"></a>Passos Seguintes

* Revisão [como instalar e executar contentores](luis-container-howto.md)
* Consulte a [perguntas mais frequentes (FAQ) do sobre](luis-resources-faq.md) para resolver problemas relacionados com a funcionalidade de LUIS.
* Utilizar mais [contentores de serviços cognitivos](../cognitive-services-container-support.md)
