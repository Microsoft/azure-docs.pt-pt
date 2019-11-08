---
title: Como configurar um contêiner para o reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Saiba como configurar o contêiner do reconhecedor de formulário para analisar dados de formulário e tabela.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: a0b0d0d95e1ffd50faba19f1665ea5dae737b124
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796140"
---
# <a name="configure-form-recognizer-containers"></a>Configurar contêineres do reconhecedor de formulário

Usando contêineres do reconhecedor do Azure Form, você pode criar uma arquitetura de aplicativo otimizada para aproveitar os recursos robustos de nuvem e a localidade de borda.

Você configura o ambiente de tempo de execução do contêiner do reconhecedor de formulário usando os argumentos do comando `docker run`. Esse contêiner tem várias configurações necessárias e algumas configurações opcionais. Para alguns exemplos, consulte a seção ["exemplo de comandos de execução do Docker"](#example-docker-run-commands) . As configurações específicas do contêiner são as configurações de cobrança.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)e [`Eula`](#eula-setting) são usadas juntas. Você deve fornecer valores válidos para todas as três configurações; caso contrário, o contêiner não será iniciado. Para obter mais informações sobre como usar essas definições de configuração para criar uma instância de um contêiner, consulte [cobrança](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Configuração de ApiKey

A configuração de `ApiKey` especifica a chave de recurso do Azure que é usada para rastrear informações de cobrança para o contêiner. O valor de ApiKey deve ser uma chave válida para o recurso _reconhecedor de formulário_ especificado para `Billing` na seção "configuração de cobrança".

Você pode encontrar essa configuração na portal do Azure, em **formulário, gerenciamento de recursos do reconhecedor**, em **chaves**.

## <a name="applicationinsights-setting"></a>Configuração de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A configuração `Billing` especifica o URI do ponto de extremidade do recurso _reconhecedor de formulário_ no Azure que é usado para medir as informações de cobrança do contêiner. O valor dessa definição de configuração deve ser um URI de ponto de extremidade válido para um recurso de _reconhecimento de formulário_ no Azure. O contêiner relata o uso de cada 10 a 15 minutos.

Você pode encontrar essa configuração na portal do Azure, na **visão geral do reconhecedor de formulário**, em **ponto de extremidade**.

|Necessário| Nome | Data type | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI do ponto de extremidade de cobrança. Para obter mais informações sobre como obter o URI de cobrança, consulte [coletando parâmetros necessários](form-recognizer-container-howto.md#gathering-required-parameters). Para obter mais informações e uma lista completa de pontos de extremidade regionais, consulte [nomes de subdomínio personalizados para serviços cognitivas](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Configuração do EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações de credenciais de proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de log

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados de e para o contêiner. Você pode especificar uma montagem de entrada ou uma montagem de saída especificando a opção `--mount` no [comando`docker run`](https://docs.docker.com/engine/reference/commandline/run/).

O contêiner do reconhecedor de formulário requer uma montagem de entrada e uma montagem de saída. A montagem de entrada pode ser somente leitura e é necessária para o acesso aos dados que são usados para treinamento e pontuação. A montagem de saída deve ser gravável e você a usa para armazenar os modelos e os dados temporários.

A sintaxe exata do local de montagem do host varia dependendo do sistema operacional do host. Além disso, o local de montagem do [computador host](form-recognizer-container-howto.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões de conta de serviço do Docker e as permissões de local de montagem do host.

|Opcional| Nome | Data type | Descrição |
|-------|------|-----------|-------------|
|Necessário| `Input` | String | O destino da montagem de entrada. O valor padrão é `/input`.    <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Necessário| `Output` | String | O destino da montagem de saída. O valor padrão é `/output`.  <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos de execução do Docker de exemplo

Os exemplos a seguir usam as definições de configuração para ilustrar como gravar e usar comandos de `docker run`. Quando estiver em execução, o contêiner continuará a ser executado até que você o [interrompa](form-recognizer-container-howto.md#stop-the-container).

* **Caractere de continuação de linha**: os comandos do Docker nas seções a seguir usam uma barra invertida (\\) como um caractere de continuação de linha. Substitua ou remova esse caractere, dependendo dos requisitos do seu sistema operacional do host.
* **Ordem do argumento**: não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.

Substitua {_argument_name_} na tabela a seguir com seus próprios valores:

| Marcador de posição | Valor |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | A chave usada para iniciar o contêiner. Ele está disponível na página chaves do reconhecedor do portal do Azure Form. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | O valor do URI do ponto de extremidade de cobrança está disponível na página Visão geral do reconhecedor do portal do Azure Form.|
| **{COMPUTER_VISION_API_KEY}** | A chave está disponível na página portal do Azure API da Pesquisa Visual Computacional chaves.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | O ponto de extremidade de cobrança. Se você estiver usando um recurso de Pesquisa Visual Computacional baseado em nuvem, o valor do URI estará disponível na página Visão geral do API da Pesquisa Visual Computacional de portal do Azure. Se você estiver usando um contêiner *cognitiva-Services-Recognize-Text* , use a URL do ponto de extremidade de cobrança que é passada para o contêiner no comando `docker run`. |

Consulte [coletando parâmetros necessários](form-recognizer-container-howto.md#gathering-required-parameters) para obter detalhes sobre como obter esses valores.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Para executar o contêiner, especifique as opções `Eula`, `Billing`e `ApiKey`; caso contrário, o contêiner não será iniciado. Para obter mais informações, consulte [cobrança](#billing-configuration-setting).

## <a name="form-recognizer-container-docker-examples"></a>Exemplos do Docker de contêiner do reconhecedor de formulário

Os exemplos do Docker a seguir são para o contêiner do reconhecedor de formulário.

### <a name="basic-example-for-form-recognizer"></a>Exemplo básico para o reconhecedor de formulário

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Exemplo de log para reconhecedor de formulário

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Passos seguintes

* Examine [instalar e executar contêineres](form-recognizer-container-howto.md).
