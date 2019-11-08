---
title: Configurações de contêiner do Docker-LUIS
titleSuffix: Azure Cognitive Services
description: O ambiente de tempo de execução de contêiner do LUIS é configurado usando os argumentos do comando `docker run`. O LUIS tem várias configurações necessárias, juntamente com algumas configurações opcionais.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: a30fcd0ec7e53c78876596baf787639e81c638db
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795015"
---
# <a name="configure-language-understanding-docker-containers"></a>Configurar contêineres do Docker Reconhecimento vocal 

O ambiente de tempo de execução de contêiner **reconhecimento vocal** (Luis) é configurado usando os argumentos de comando `docker run`. O LUIS tem várias configurações necessárias, juntamente com algumas configurações opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As configurações específicas do contêiner são as configurações de [montagem](#mount-settings) de entrada e as configurações de cobrança. 

## <a name="configuration-settings"></a>Definições de configuração

Esse contêiner tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Sim|[ApiKey](#apikey-setting)|Usado para rastrear informações de cobrança.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite que você adicione suporte à telemetria do [insights aplicativo Azure](https://docs.microsoft.com/azure/application-insights) ao seu contêiner.|
|Sim|[Faturação](#billing-setting)|Especifica o URI do ponto de extremidade do recurso de serviço no Azure.|
|Sim|[EULA](#eula-setting)| Indica que você aceitou a licença para o contêiner.|
|Não|[Fluentd](#fluentd-settings)|Grave log e, opcionalmente, dados de métrica para um servidor Fluentd.|
|Não|[Proxy http](#http-proxy-credentials-settings)|Configure um proxy HTTP para fazer solicitações de saída.|
|Não|[Logout](#logging-settings)|Fornece suporte ao log de ASP.NET Core para seu contêiner. |
|Sim|[Monta](#mount-settings)|Ler e gravar dados do computador host para o contêiner e do contêiner de volta para o computador host.|

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting)e [`Eula`](#eula-setting) são usadas juntas, e você deve fornecer valores válidos para todos os três; caso contrário, o contêiner não será iniciado. Para obter mais informações sobre como usar essas definições de configuração para criar uma instância de um contêiner, consulte [cobrança](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Configuração de ApiKey

A configuração de `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança para o contêiner. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso de _Serviços cognitivas_ especificado para a configuração de [`Billing`](#billing-setting) .

Essa configuração pode ser encontrada nos seguintes locais:

* Portal do Azure: gerenciamento de recursos de **Serviços cognitivas** , em **chaves**
* Portal do LUIS: página **de configurações de ponto de extremidade e chaves** . 

Não use a chave inicial ou a chave de criação. 

## <a name="applicationinsights-setting"></a>Configuração de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Configuração de cobrança

A configuração de `Billing` especifica o URI do ponto de extremidade do recurso de _Serviços cognitivas_ no Azure usado para medir as informações de cobrança do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso de _Serviços cognitivas_ no Azure. O contêiner relata o uso de cada 10 a 15 minutos.

Essa configuração pode ser encontrada nos seguintes locais:

* Portal do Azure: visão geral **dos serviços cognitivas** , rotulados `Endpoint`
* Portal do LUIS: página de **configurações de ponto de extremidade e chaves** , como parte do URI do ponto de extremidade.

| Necessário | Nome | Data type | Descrição |
|----------|------|-----------|-------------|
| Sim      | `Billing` | string | URI do ponto de extremidade de cobrança. Para obter mais informações sobre como obter o URI de cobrança, consulte [coletando parâmetros necessários](luis-container-howto.md#gathering-required-parameters). Para obter mais informações e uma lista completa de pontos de extremidade regionais, consulte [nomes de subdomínio personalizados para serviços cognitivas](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Configuração do EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações de credenciais de proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de log
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados de e para o contêiner. Você pode especificar uma montagem de entrada ou de saída especificando a opção de `--mount` no comando de [execução do Docker](https://docs.docker.com/engine/reference/commandline/run/) . 

O contêiner LUIS não usa montagens de entrada ou de saída para armazenar dados de treinamento ou de serviço. 

A sintaxe exata do local de montagem do host varia dependendo do sistema operacional do host. Além disso, o local de montagem do [computador host](luis-container-howto.md#the-host-computer)pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões de local de montagem do host. 

A tabela a seguir descreve as configurações com suporte.

|Necessário| Nome | Data type | Descrição |
|-------|------|-----------|-------------|
|Sim| `Input` | String | O destino da montagem de entrada. O valor padrão é `/input`. Esse é o local dos arquivos de pacote LUIS. <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Não| `Output` | String | O destino da montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de consulta do LUIS e logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos de execução do Docker de exemplo

Os exemplos a seguir usam as definições de configuração para ilustrar como gravar e usar comandos de `docker run`.  Uma vez em execução, o contêiner continua a ser executado até que você o [interrompa](luis-container-howto.md#stop-the-container) .

* Esses exemplos usam o diretório fora da unidade de `C:` para evitar conflitos de permissão no Windows. Se você precisar usar um diretório específico como o diretório de entrada, talvez seja necessário conceder a permissão de serviço do Docker. 
* Não altere a ordem dos argumentos, a menos que você esteja muito familiarizado com contêineres do Docker.
* Se você estiver usando um sistema operacional diferente, use o console/terminal correto, a sintaxe de pasta para montagens e o caractere de continuação de linha para o seu sistema. Esses exemplos pressupõem um console do Windows com um caractere de continuação de linha `^`. Como o contêiner é um sistema operacional Linux, a montagem de destino usa uma sintaxe de pasta em estilo Linux.

Substitua {_argument_name_} pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave do ponto de extremidade do recurso `LUIS` na página chaves do `LUIS` do Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto de extremidade de cobrança está disponível na página Visão geral do `LUIS` do Azure.| Consulte [coletando parâmetros necessários](luis-container-howto.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As opções `Eula`, `Billing`e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado. Para obter mais informações, consulte [cobrança](luis-container-howto.md#billing).
> O valor ApiKey é a **chave** da página chaves e pontos de extremidade no portal do Luis e também está disponível na página chaves de recurso do `Cognitive Services` do Azure. 

### <a name="basic-example"></a>Exemplo básico

O exemplo a seguir tem os menores argumentos possíveis para executar o contêiner:

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

O exemplo a seguir define o argumento ApplicationInsights para enviar telemetria para Application Insights enquanto o contêiner está em execução:

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

O comando a seguir define o nível de log, `Logging:Console:LogLevel`, para configurar o nível de log para [`Information`](https://msdn.microsoft.com). 

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

* Examine [como instalar e executar contêineres](luis-container-howto.md)
* Consulte [solução de problemas](troubleshooting.md) para resolver problemas relacionados à funcionalidade do Luis.
* Usar mais [contêineres de serviços cognitivas](../cognitive-services-container-support.md)
