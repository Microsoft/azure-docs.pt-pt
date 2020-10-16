---
title: Como configurar um recipiente para o Reconhecedor de Formulários
titleSuffix: Azure Cognitive Services
description: Saiba como configurar o recipiente Do Reconhecimento de Formulários para analisar os dados de formulário e tabela.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: 324b70fc810acc4faba4f488f821049f7eb0875e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86538008"
---
# <a name="configure-form-recognizer-containers"></a>Configurar recipientes de reconhecimento de formulários

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Ao utilizar recipientes Azure Form Recogniser, pode construir uma arquitetura de aplicações otimizada para tirar partido das capacidades de nuvem robustas e da localidade de borda.

Configura o ambiente de tempo de funcionamento do recipiente Do Reconhecimento de Formulários utilizando os argumentos de `docker run` comando. Este recipiente tem várias configurações necessárias e algumas configurações opcionais. Para alguns exemplos, consulte a secção ["Comandos de execução de exemplo" do "Exemplo de estivador".](#example-docker-run-commands) As definições específicas do contentor são as definições de faturação.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) definições , e [`Eula`](#eula-setting) configurações são usadas em conjunto. Deve fornecer valores válidos para as três configurações; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas configurações para instantaneaizar um recipiente, consulte [Billing](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Definição de configuração apikey

A `ApiKey` definição especifica a chave de recursos Azure que é usada para rastrear informações de faturação para o recipiente. O valor para o ApiKey deve ser uma chave válida para o recurso _'Reconhecimento_ de Formulário' especificado na secção "Configuração de configuração de `Billing` faturação".

Pode encontrar esta definição no portal Azure, na **Gestão de Recursos do Reconhecimento de Formulários,** em **Teclas**.

## <a name="applicationinsights-setting"></a>Configuração de AplicaçõesInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso _Form Recogniser_ no Azure que é usado para medir informações de faturação para o recipiente. O valor desta definição de configuração deve ser um URI de ponto final válido para um recurso _de Reconhecimento de Formulário_ no Azure. O recipiente relata o uso a cada 10 a 15 minutos.

Pode encontrar esta definição no portal Azure, na **Visão Geral do Reconhecimento de Formulários**, no **ponto final**.

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI de faturação. Para obter mais informações sobre a obtenção do URI de faturação, consulte [a recolha dos parâmetros necessários](form-recognizer-container-howto.md#gathering-required-parameters). Para obter mais informações e uma lista completa de pontos finais regionais, consulte [os nomes de subdomínio personalizados para serviços cognitivos.](../cognitive-services-custom-subdomains.md) |

## <a name="eula-setting"></a>Definição de Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de procuração HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Configurações de montagem

Utilize suportes de ligação para ler e escrever dados de e para o recipiente. Pode especificar um suporte de entrada ou um suporte de saída especificando a `--mount` opção no [ `docker run` comando](https://docs.docker.com/engine/reference/commandline/run/).

O recipiente Do Reconhecimento de Formulários requer um suporte de entrada e um suporte de saída. O suporte de entrada pode ser apenas lido, e é necessário para o acesso aos dados que são usados para treinar e pontuar. O suporte de saída tem de ser writable, e você usá-lo para armazenar os modelos e dados temporários.

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização de montagem do [computador anfitrião](form-recognizer-container-howto.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões da conta de serviço Docker e as permissões de localização do anfitrião.

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Obrigatório| `Input` | Cadeia | O alvo do suporte de entrada. O valor predefinido é `/input`.    <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Necessário| `Output` | Cadeia | O alvo do suporte de saída. O valor predefinido é `/output`.  <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exemplo de estivador executar comandos

Os exemplos a seguir utilizam as definições de configuração para ilustrar como escrever e utilizar `docker run` comandos. Quando está a funcionar, o contentor continua a funcionar até [o parar.](form-recognizer-container-howto.md#stop-the-container)

* **Carácter de continuação da linha**: Os comandos do Docker nas seguintes secções utilizam um corte traseiro \\ () como um carácter de continuação de linha. Substitua ou remova este carácter, dependendo dos requisitos do sistema operativo do anfitrião.
* **Ordem de argumento:** Não altere a ordem dos argumentos a menos que esteja familiarizado com os recipientes do Docker.

Substitua {_argument_name_} na tabela seguinte pelos seus próprios valores:

| Marcador de posição | Valor |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | A chave que é usada para ligar o contentor. Está disponível na página 'Teclas do reconhecimento do formulário' do portal Azure. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | O valor URI do ponto final de faturação está disponível na página de Visão Geral do Portal Azure.|
| **{COMPUTER_VISION_API_KEY}** | A chave está disponível na página Azure portal Computer Vision API Keys.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | O ponto final da faturação. Se estiver a utilizar um recurso de Visão Computacional baseado na nuvem, o valor URI está disponível na página de Visão Geral do portal Azure Computer Vision. Se estiver a utilizar um recipiente *de texto cognitivo-reconhecimento,* use o URL do ponto final de faturação que é passado para o recipiente no `docker run` comando. |

Consulte [os parâmetros necessários](form-recognizer-container-howto.md#gathering-required-parameters) para obter detalhes sobre como obter estes valores.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Para executar o recipiente, especifique o `Eula` `Billing` , e as `ApiKey` opções; caso contrário, o recipiente não arranca. Para mais informações, consulte [Billing.](#billing-configuration-setting)

## <a name="form-recognizer-container-docker-examples"></a>Exemplos de Docker de recipiente de reconhecimento de formulário

Os seguintes exemplos do Docker são para o recipiente Do Reconhecimento de Formulários.

### <a name="basic-example-for-form-recognizer"></a>Exemplo básico para o Reconhecimento de Formulários

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

### <a name="logging-example-for-form-recognizer"></a>Exemplo de registo para Reconhecimento de Formulários

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

* Rever [Instalar e executar recipientes](form-recognizer-container-howto.md).
