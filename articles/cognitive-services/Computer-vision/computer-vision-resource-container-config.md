---
title: Configurar contêineres-Pesquisa Visual Computacional
titlesuffix: Azure Cognitive Services
description: Defina várias configurações para contêineres de Reconhecimento de Texto no Pesquisa Visual Computacional.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 90358d54077a0c320e8d3186e806b8a61d951c82
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321346"
---
# <a name="configure-recognize-text-docker-containers"></a>Configurar contêineres do Docker Reconhecimento de Texto

O  ambiente de tempo de execução de contêiner reconhecimento de texto `docker run` é configurado usando os argumentos de comando. Esse contêiner tem várias configurações necessárias, juntamente com algumas configurações opcionais. Várias [exemplos](#example-docker-run-commands) do comando estão disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> O [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), e [ `Eula` ](#eula-setting) definições são utilizadas em conjunto, e tem de indicar valores válidos para todas as três-los; caso contrário não inicia o contentor. Para obter mais informações sobre como utilizar estas definições de configuração para criar uma instância de um contentor, consulte [faturação](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>ApiKey definição de configuração

A `ApiKey` configuração especifica a chave `Cognitive Services` de recurso do Azure usada para rastrear informações de cobrança para o contêiner. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso de _Serviços cognitivas_ especificado [`Billing`](#billing-configuration-setting) para a definição de configuração.

Essa configuração pode ser encontrada no seguinte local:

* Portal do Azure: **Serviços cognitivas** Gerenciamento de recursos, em **chaves**

## <a name="applicationinsights-setting"></a>Definição do Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` configuração especifica o URI do ponto de extremidade do recurso de _Serviços cognitivas_ no Azure usado para medir as informações de cobrança do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso de _Serviços cognitivas_ no Azure. O contêiner relata o uso de cada 10 a 15 minutos.

Essa configuração pode ser encontrada no seguinte local:

* Portal do Azure: **Serviços cognitivas** Visão geral, rotulada`Endpoint`

Lembre-se de `vision/v1.0` adicionar o roteamento ao URI do ponto de extremidade, conforme mostrado na tabela a seguir. 

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia | URI do ponto final de faturação<br><br>Exemplo:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

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

Os contêineres de Pesquisa Visual Computacional não usam montagens de entrada ou de saída para armazenar dados de treinamento ou de serviço. 

A sintaxe exata da localização de montagem do anfitrião varia consoante o sistema operativo anfitrião. Além disso, o local de montagem do [computador host](computer-vision-how-to-install-containers.md#the-host-computer)pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões de local de montagem do host. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | Cadeia | Os contêineres de Pesquisa Visual Computacional não usam isso.|
|Opcional| `Output` | Cadeia | O destino de montagem de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Executar comandos de docker de exemplo 

Os exemplos seguintes utilizam as definições de configuração para ilustrar como escrever e usar `docker run` comandos.  Quando em execução, o contentor continua a ser executada até [parar](computer-vision-how-to-install-containers.md#stop-the-container) -lo.

* **Caractere de continuação de linha**: Os comandos do Docker nas seções a seguir usam a barra `\`invertida,, como um caractere de continuação de linha. Substituir ou remova esta com base nos requisitos de seu sistema de operativo de anfitrião. 
* **Ordem do argumento**: Não altere a ordem dos argumentos, a menos que você esteja muito familiarizado com contêineres do Docker.

Lembre-se de `vision/v1.0` adicionar o roteamento ao URI do ponto de extremidade, conforme mostrado na tabela a seguir. 

Substitua {_argument_name_} pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou de exemplo |
|-------------|-------|---|
|{API_KEY} | A chave do ponto de extremidade do recurso de serviços cognitivas. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | O valor do ponto de extremidade de cobrança, incluindo a região.|`https://westcentralus.api.cognitive.microsoft.com/vision/v1.0`|

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](computer-vision-how-to-install-containers.md#billing).
> O valor de ApiKey é a **chave** da página `Cognitive Services` de chaves de recurso do Azure. 

## <a name="recognize-text-container-docker-examples"></a>Exemplos de encaixe de contêiner de texto de reconhecimento

Os exemplos do Docker a seguir são para o contêiner reconhecer texto. 

### <a name="basic-example"></a>Exemplo básico 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exemplo de log 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Passos Seguintes

* Revisão [como instalar e executar contentores](computer-vision-how-to-install-containers.md)
