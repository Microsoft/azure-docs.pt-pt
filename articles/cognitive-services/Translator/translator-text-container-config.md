---
title: Configurar contêineres-Tradução de Texto
titleSuffix: Azure Cognitive Services
description: O Tradução de Texto fornece contêineres com uma estrutura de configuração comum, para que você possa configurar e gerenciar facilmente o armazenamento, o registro em log e a telemetria e as configurações de segurança para seus contêineres.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 3dbedcb5e5212091dc8906defa2b1cf5c7868d60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507673"
---
# <a name="configure-translator-text-docker-containers"></a>Configurar contêineres do Docker Tradução de Texto

Tradução de Texto fornece cada contêiner com uma estrutura de configuração comum, para que você possa configurar e gerenciar facilmente o armazenamento, o registro em log e a telemetria e as configurações de segurança para seus contêineres.

## <a name="configuration-settings"></a>Definições de configuração

O contêiner tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite adicionar suporte à telemetria do [insights aplicativo Azure](https://docs.microsoft.com/azure/application-insights) ao seu contêiner.|
|Sim|[EULA](#eula-setting)| Indica que você aceitou a licença para o contêiner.|
|Não|[Fluentd](#fluentd-settings)|Grava o log e, opcionalmente, os dados de métrica para um servidor Fluentd.|
|Não|Proxy HTTP|Configura um proxy HTTP para fazer solicitações de saída.|
|Não|[Logout](#logging-settings)|Fornece suporte ao log de ASP.NET Core para seu contêiner. |
|Não|[Monta](#mount-settings)|Lê e grava dados do computador host para o contêiner e do contêiner de volta para o computador host.|

> [!IMPORTANT]
> A configuração de [`Eula`](#eula-setting) deve ser fornecida com o valor de `accept`; caso contrário, o contêiner não será iniciado.

## <a name="applicationinsights-setting"></a>Configuração de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

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

Os contêineres de Tradução de Texto não usam montagens de entrada ou de saída para armazenar dados de treinamento ou de serviço. 

A sintaxe exata do local de montagem do host varia, dependendo do sistema operacional do host. Além disso, o local de montagem do [computador host](how-to-install-containers.md#the-host-computer)pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões de local de montagem do host. 

|Opcional| Nome | Data type | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os contêineres de Tradução de Texto não usam isso.|
|Opcional| `Output` | String | O destino da montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos de execução do Docker de exemplo 

Os exemplos a seguir usam as definições de configuração para ilustrar como gravar e usar comandos de `docker run`.  Uma vez em execução, o contêiner continua a ser executado até que você o [interrompa](how-to-install-containers.md#stop-the-container) .

* **Caractere de continuação de linha**: os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova isso com base nos requisitos do seu sistema operacional do host. 
* **Ordem do argumento**: não altere a ordem dos argumentos, a menos que você esteja muito familiarizado com contêineres do Docker.

## <a name="translator-text-container-docker-examples"></a>Exemplos de Docker de contêiner Tradução de Texto

Os exemplos do Docker a seguir são para o contêiner de Tradução de Texto.

### <a name="basic-example"></a>Exemplo básico

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept
```

### <a name="logging-example"></a>Exemplo de log

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Passos seguintes

* Examine [como instalar e executar contêineres](how-to-install-containers.md)
* Usar mais [contêineres de serviços cognitivas](../cognitive-services-container-support.md)
