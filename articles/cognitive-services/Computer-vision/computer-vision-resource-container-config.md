---
title: Configurar contêineres-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Este artigo mostra como definir as configurações obrigatórias e opcionais para contêineres de Reconhecimento de Texto no Pesquisa Visual Computacional.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: ddbee3695c2a7ef7cb63c48cccacbd2d53a8c1a9
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718986"
---
# <a name="configure-computer-vision-docker-containers"></a>Configurar contêineres do Docker Pesquisa Visual Computacional

Você configura o ambiente de tempo de execução do contêiner de Pesquisa Visual Computacional usando os argumentos do comando `docker run`. Esse contêiner tem várias configurações necessárias, juntamente com algumas configurações opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)e [`Eula`](#eula-setting) são usadas juntas, e você deve fornecer valores válidos para todos os três; caso contrário, o contêiner não será iniciado. Para obter mais informações sobre como usar essas definições de configuração para criar uma instância de um contêiner, consulte [cobrança](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Configuração de ApiKey

A configuração de `ApiKey` especifica a chave de recurso de `Cognitive Services` do Azure usada para rastrear informações de cobrança do contêiner. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso de _Serviços cognitivas_ especificado para a configuração de [`Billing`](#billing-configuration-setting) .

Essa configuração pode ser encontrada no seguinte local:

* Portal do Azure: gerenciamento de recursos de **Serviços cognitivas** , em **chaves**

## <a name="applicationinsights-setting"></a>Configuração de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A configuração de `Billing` especifica o URI do ponto de extremidade do recurso de _Serviços cognitivas_ no Azure usado para medir as informações de cobrança do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso de _Serviços cognitivas_ no Azure. O contêiner relata o uso de cada 10 a 15 minutos.

Essa configuração pode ser encontrada no seguinte local:

* Portal do Azure: visão geral **dos serviços cognitivas** , rotulados `Endpoint`

Lembre-se de adicionar o roteamento de `vision/v1.0` para o URI do ponto de extremidade, conforme mostrado na tabela a seguir. 

|Necessário| Nome | Data type | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI do ponto de extremidade de cobrança<br><br>Exemplo:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Configuração do EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações de credenciais de proxy HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de log
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados de e para o contêiner. Você pode especificar uma montagem de entrada ou de saída especificando a opção de `--mount` no comando de [execução do Docker](https://docs.docker.com/engine/reference/commandline/run/) .

Os contêineres de Pesquisa Visual Computacional não usam montagens de entrada ou de saída para armazenar dados de treinamento ou de serviço. 

A sintaxe exata do local de montagem do host varia dependendo do sistema operacional do host. Além disso, o local de montagem do [computador host](computer-vision-how-to-install-containers.md#the-host-computer)pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões de local de montagem do host. 

|Opcional| Nome | Data type | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os contêineres de Pesquisa Visual Computacional não usam isso.|
|Opcional| `Output` | String | O destino da montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos de execução do Docker de exemplo

Os exemplos a seguir usam as definições de configuração para ilustrar como gravar e usar comandos de `docker run`.  Uma vez em execução, o contêiner continua a ser executado até que você o [interrompa](computer-vision-how-to-install-containers.md#stop-the-container) .

* **Caractere de continuação de linha**: os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova isso com base nos requisitos do seu sistema operacional do host. 
* **Ordem do argumento**: não altere a ordem dos argumentos, a menos que você esteja muito familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave do ponto de extremidade do recurso `Computer Vision` na página chaves do `Computer Vision` do Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto de extremidade de cobrança está disponível na página Visão geral do `Computer Vision` do Azure.| Consulte [coletando parâmetros necessários](computer-vision-how-to-install-containers.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As opções `Eula`, `Billing`e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para obter mais informações, consulte [cobrança](computer-vision-how-to-install-containers.md#billing).
> O valor de ApiKey é a **chave** da página de chaves de recurso do `Cognitive Services` do Azure.

## <a name="container-docker-examples"></a>Exemplos de Docker de contêiner

Os exemplos do Docker a seguir são para o contêiner de leitura.

### <a name="basic-example"></a>Exemplo básico

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exemplo de log 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Passos seguintes

* Examine [como instalar e executar contêineres](computer-vision-how-to-install-containers.md).
