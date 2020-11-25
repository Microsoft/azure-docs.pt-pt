---
title: Configure Ler recipientes OCR - Visão computacional
titleSuffix: Azure Cognitive Services
description: Este artigo mostra-lhe como configurar as definições necessárias e opcionais para os recipientes OCR de leitura em Visão de Computador.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 0539f37fe15f68d8bfd47bf426333f9d5c67c37d
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96006882"
---
# <a name="configure-read-ocr-docker-containers"></a>Configure Ler recipientes OCR Docker

Configurar o ambiente de funcionamento do recipiente OCR da Visão de Computador, utilizando os argumentos de `docker run` comando. Este recipiente tem várias configurações necessárias, juntamente com algumas configurações opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do contentor são as definições de faturação. 

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) definições , e [`Eula`](#eula-setting) configurações são utilizadas em conjunto, e deve fornecer valores válidos para os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas configurações para instantaneaizar um recipiente, consulte [Billing](computer-vision-how-to-install-containers.md).

O contentor também tem as seguintes definições de configuração específicas do contentor:

|Necessário|Definição|Objetivo|
|--|--|--|
|Não|ReadEngineConfig:ResultadoExpirationPeriod| apenas contentores v2.0. Prazo de expiração do resultado em horas. O padrão é de 48 horas. A definição especifica quando o sistema deve limpar os resultados do reconhecimento. Por exemplo, se `resultExpirationPeriod=1` , o sistema limpar o resultado do reconhecimento 1 hora após o processo. Se `resultExpirationPeriod=0` , o sistema limpar o resultado do reconhecimento após a recuperação do resultado.|
|Não|Cache:Redis| apenas contentores v2.0. Permite o armazenamento redis para armazenar resultados. É *necessária* uma cache se vários recipientes de leitura forem colocados atrás de um equilibrador de carga.|
|Não|Fila:RabbitMQ|apenas contentores v2.0. Permite o RabbitMQ para o envio de tarefas. A regulação é útil quando vários recipientes de leitura são colocados atrás de um equilibrador de carga.|
|Não|Fila:Azure:QueueVisibilityTimeoutInMilliseconds | apenas contentores v3.x. O tempo para uma mensagem ser invisível quando outro trabalhador está a processá-la. |
|Não|Armazenamento::D ocumentStore::MongoDB|apenas contentores v2.0. Permite o MongoDB para armazenamento permanente de resultados. |
|Não|Armazenamento:ObjectStore:AzureBlob:ConnectionString| apenas contentores v3.x. Cadeia de ligação de armazenamento de bolhas Azure. |

## <a name="apikey-configuration-setting"></a>Definição de configuração apikey

A `ApiKey` definição especifica a chave de recursos Azure `Cognitive Services` utilizada para rastrear as informações de faturação do recipiente. Tem de especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso _Serviços Cognitivos_ especificado para a definição de [`Billing`](#billing-configuration-setting) configuração.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: **Cognitive Services** Resource Management, em **Chaves**

## <a name="applicationinsights-setting"></a>Configuração de AplicaçõesInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso _Serviços Cognitivos_ no Azure utilizado para medir informações de faturação para o recipiente. Deve especificar um valor para esta configuração, e o valor deve ser um URI de ponto final válido para um recurso _de Serviços Cognitivos_ em Azure. O recipiente relata o uso a cada 10 a 15 minutos.

Esta definição pode ser encontrada no seguinte local:

* Portal Azure: Visão geral **dos Serviços Cognitivos,** rotulada `Endpoint`

Lembre-se de adicionar o `vision/v1.0` encaminhamento ao ponto final URI, como indicado na tabela seguinte. 

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI de ponto final de faturação<br><br>Exemplo:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Definição de Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de procuração HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Utilize suportes de ligação para ler e escrever dados de e para o recipiente. Pode especificar um suporte de entrada ou de saída especificando a `--mount` opção no comando de execução do [estivador.](https://docs.docker.com/engine/reference/commandline/run/)

Os recipientes de Visão de Computador não utilizam suportes de entrada ou saída para armazenar dados de treino ou de serviço. 

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador anfitrião](computer-vision-how-to-install-containers.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço Docker e as permissões de localização do anfitrião. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os recipientes de Visão de Computador não utilizam isto.|
|Opcional| `Output` | String | O alvo do suporte de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui registos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exemplo de estivador executar comandos

Os exemplos a seguir utilizam as definições de configuração para ilustrar como escrever e utilizar `docker run` comandos.  Uma vez em funcionamento, o recipiente continua a funcionar até o [parar.](computer-vision-how-to-install-containers.md#stop-the-container)

* **Carácter de continuação da linha**: Os comandos do Docker nas seguintes secções usam o corte `\` traseiro, como um personagem de continuação de linha. Substitua ou remova isto com base nos requisitos do seu sistema operativo anfitrião. 
* **Ordem de argumento**: Não altere a ordem dos argumentos a menos que esteja muito familiarizado com os recipientes docker.

Substitua {_argument_name_} com os seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave de ponta final do `Computer Vision` recurso na página Azure `Computer Vision` Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto final de faturação está disponível na página Azure `Computer Vision` Overview.| Consulte [os parâmetros necessários](computer-vision-how-to-install-containers.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As `Eula` `Billing` opções , e `ApiKey` opções devem ser especificadas para executar o recipiente; caso contrário, o recipiente não arranca.  Para mais informações, consulte [Billing.](computer-vision-how-to-install-containers.md#billing)
> O valor ApiKey é a **chave** da página de chaves de recursos Azure. `Cognitive Services`

## <a name="container-docker-examples"></a>Exemplos de estivador de contentores

Os seguintes exemplos do Docker são para o recipiente Ler.


# <a name="version-32-preview"></a>[Versão 3.2-pré-visualização](#tab/version-3-2)

### <a name="basic-example"></a>Exemplo básico

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Exemplo de registo 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-20-preview"></a>[Versão 2.0-pré-visualização](#tab/version-2)

### <a name="basic-example"></a>Exemplo básico

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Exemplo de registo 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Passos seguintes

* Rever [Como instalar e executar recipientes](computer-vision-how-to-install-containers.md).
