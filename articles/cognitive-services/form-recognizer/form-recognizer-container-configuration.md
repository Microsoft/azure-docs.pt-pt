---
title: Como configurar um recipiente para reconhecimento de formulários
titleSuffix: Azure Cognitive Services
description: Aprenda a configurar o recipiente 'Reconhecimento de Formulários' para analisar os dados de forma e de tabela.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: bc48c0ba23e73adec312adfeeb1fcd57dba6ceec
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879161"
---
# <a name="configure-form-recognizer-containers"></a>Configure Recipientes de reconhecimento de formulário

Ao utilizar recipientes Azure Form Recogniser, pode construir uma arquitetura de aplicação otimizada para tirar partido das capacidades de nuvem robustas e da localidade de borda.

Configura o ambiente de tempo de execução `docker run` do recipiente 'Reconhecimento de Formulários', utilizando os argumentos de comando. Este recipiente tem várias configurações necessárias e algumas configurações opcionais. Para alguns exemplos, consulte a secção ["Exemplo de comandos de estivador".](#example-docker-run-commands) As definições específicas do recipiente são as definições de faturação.

> [!IMPORTANT]
> Os recipientes 'Reconhecimento de Formulários' utilizam atualmente a versão 1.0 da API do Reconhecimento de Formulários. Pode aceder à versão mais recente da API utilizando o serviço gerido.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)definições [`Eula`](#eula-setting) e as definições são utilizadas em conjunto. Deve fornecer valores válidos para as três definições; caso contrário, o seu contentor não vai arrancar. Para obter mais informações sobre a utilização destas definições de configuração para instantaneamente um recipiente, consulte [a Faturação](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Definição de configuração ApiKey

A `ApiKey` definição especifica a chave de recursos Azure que é usada para rastrear informações de faturação para o recipiente. O valor para o ApiKey deve ser uma chave válida para `Billing` o recurso Reconhecimento de _Formulários_ especificado na secção "Configuração de Configuração de Faturação".

Pode encontrar esta definição no portal Azure, na **Gestão**de Recursos do Reconhecimento de Formulários, em **Keys**.

## <a name="applicationinsights-setting"></a>Definição de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso _'Reconhecimento_ de Formulários' no Azure que é usado para medir informações de faturação para o recipiente. O valor para esta configuração deve ser um uri de ponto final válido para um recurso _de reconhecimento_ de formulário no Azure. O recipiente reporta o uso a cada 10 a 15 minutos.

Pode encontrar esta definição no portal Azure, na visão geral do Reconhecimento de **Formulários,** em **Endpoint**.

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | Ponto final de faturação URI. Para obter mais informações sobre a obtenção do URI de faturação, consulte a [recolha de parâmetros necessários](form-recognizer-container-howto.md#gathering-required-parameters). Para mais informações e uma lista completa de pontos finais regionais, consulte [nomes de subdomínio personalizado para Serviços Cognitivos](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Cenário eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de procuração HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Configurações do monte

Utilize suportes de ligação para ler e escrever dados de e para o recipiente. Pode especificar um suporte de entrada ou um `--mount` suporte de saída especificando a opção no [ `docker run` comando](https://docs.docker.com/engine/reference/commandline/run/).

O recipiente 'Reconhecimento de Formulários' requer um suporte de entrada e um suporte de saída. O suporte de entrada pode ser apenas de leitura, e é necessário para o acesso aos dados que são usados para treinar e marcar. O suporte de saída tem de ser repreensível e usa-o para armazenar os modelos e os dados temporários.

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador hospedeiro](form-recognizer-container-howto.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões da conta de serviço Docker e as permissões de localização do hospedeiro.

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Necessário| `Input` | String | O alvo do suporte de entrada. O valor predefinido é `/input`.    <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Necessário| `Output` | String | O alvo do suporte de saída. O valor predefinido é `/output`.  <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos de execução de estivadores exemplo

Os exemplos seguintes utilizam as definições `docker run` de configuração para ilustrar como escrever e utilizar comandos. Quando está a funcionar, o contentor continua a funcionar até o [parares.](form-recognizer-container-howto.md#stop-the-container)

* **Personagem de continuação da linha**: Os comandos\\do Docker nas seguintes secções usam um corte traseiro ( ) como um personagem de continuação da linha. Substitua ou remova este personagem, dependendo dos requisitos do sistema operativo do anfitrião.
* **Ordem de argumentação**: Não altere a ordem dos argumentos a menos que esteja familiarizado com os contentores do Docker.

Substitua {_argument_name_} na tabela seguinte com os seus próprios valores:

| Marcador de posição | Valor |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | A chave que é usada para ligar o contentor. Está disponível na página do portal Azure Form Recogniser Keys. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | O valor final da faturação URI está disponível na página de visão geral do portal Azure Form Recogniser.|
| **{COMPUTER_VISION_API_KEY}** | A chave está disponível na página Do portal Azure Computer Vision API Keys.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | O ponto final da faturação. Se estiver a utilizar um recurso computer vision baseado na nuvem, o valor URI está disponível na página de visão geral da Visão Computacional do Portal Azure. Se estiver a usar um recipiente de texto de *reconhecimento cognitivo-serviços,* utilize o URL `docker run` final de faturação que é passado para o recipiente no comando. |

Consulte [a recolha de parâmetros necessários](form-recognizer-container-howto.md#gathering-required-parameters) para obter detalhes sobre como obter estes valores.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Para executar o recipiente, especifique as `Eula`opções e `Billing` `ApiKey` as opções; caso contrário, o contentor não vai começar. Para mais informações, consulte [billing.](#billing-configuration-setting)

## <a name="form-recognizer-container-docker-examples"></a>Formulário recipiente reconhecível Exemplos Docker

Os seguintes exemplos do Docker são para o recipiente 'Reconhecimento de Formulários'.

### <a name="basic-example-for-form-recognizer"></a>Exemplo básico para reconhecimento de formulários

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

### <a name="logging-example-for-form-recognizer"></a>Exemplo de registo para reconhecimento de formulário

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
