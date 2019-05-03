---
title: Configurar a opção contentor - reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar o contentor do reconhecedor de formulário para analisar dados de formulário e de tabela.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: pafarley
ms.openlocfilehash: 6066e7856ddf8ef757afc2072218c87420a37c10
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027181"
---
# <a name="configure-form-recognizer-containers"></a>Configurar os contentores do reconhecedor de formulário

Contentores do reconhecedor de forma que os clientes criar uma arquitetura de aplicativo que está otimizada para tirar partido das capacidades de cloud robustas e Localidade de borda.

O **formulário reconhecedor** ambiente de tempo de execução de contentores é configurado usando o `docker run` argumentos de comando. Este contentor tem várias definições necessárias, juntamente com algumas configurações opcionais. Várias [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do contentor são as definições de faturas.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> O [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), e [ `Eula` ](#eula-setting) definições são utilizadas em conjunto, e tem de indicar valores válidos para todas as três-los; caso contrário não inicia o contentor. Para obter mais informações sobre como utilizar estas definições de configuração para criar uma instância de um contentor, consulte [faturação](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey definição de configuração

O `ApiKey` definição especifica a chave de recurso do Azure utilizada para controlar informações de faturação para o contentor. Tem de especificar um valor para o ApiKey e o valor tem de ser uma chave válida para o _formulário reconhecedor_ recurso especificado para o [ `Billing` ](#billing-configuration-setting) definição de configuração.

Esta definição pode ser encontrada no seguinte local:

* Portal do Azure: **Formulário do reconhecedor** gestão de recursos, em **chaves**

## <a name="applicationinsights-setting"></a>Definição do Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

O `Billing` definição especifica o URI do ponto final da _reconhecedor de formulário_ recurso no Azure utilizado para informações de faturação para o contentor do medidor. Tem de especificar um valor para esta definição de configuração e o valor tem de ser um URI de ponto de final válido para um _reconhecedor de formulário_ recursos no Azure. O contentor de relatórios de utilização sobre a cada 10 a 15 minutos.

Esta definição pode ser encontrada no seguinte local:

* Portal do Azure: **Formulário do reconhecedor** descrição geral, o nome `Endpoint`

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia | URI do ponto final de faturação<br><br>Exemplo:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>EULA definição

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Definições de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="logging-settings"></a>Definições de registo

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Definições de montagem

Monta o enlace de utilização para ler e escrever dados de e para o contentor. Pode especificar uma montagem de entrada ou saída de montagem, especificando o `--mount` opção da [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando.

Os contentores do reconhecedor de formulário exige uma montagem de entrada e saída. A montagem de entrada pode ser só de leitura e é necessário para acessar os dados que serão utilizados para a preparação e classificação. A montagem de saída tem de ser gravável e será utilizada para armazenar os modelos e dados temporários.

A sintaxe exata da localização de montagem do anfitrião varia consoante o sistema operativo anfitrião. Além disso, o [computador anfitrião](form-recognizer-container-howto.md#the-host-computer)da localização de montagem não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e o anfitrião montar permissões de localização.

|Opcional| Name | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Necessário| `Input` | String | O destino da montagem de entrada. O valor predefinido é `/input`.    <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Necessário| `Output` | String | O destino de montagem de saída. O valor predefinido é `/output`.  <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Executar comandos de docker de exemplo

Os exemplos seguintes utilizam as definições de configuração para ilustrar como escrever e usar `docker run` comandos.  Quando em execução, o contentor continua a ser executada até [parar](form-recognizer-container-howto.md#stop-the-container) -lo.

* **Caracteres de continuação de linha**: Os comandos de Docker nas secções seguintes utilizam a barra invertida, `\`, como um caractere de continuação de linha. Substituir ou remova esta com base nos requisitos de seu sistema de operativo de anfitrião.
* **Ordem de argumento**: Não altere a ordem dos argumentos, a menos que está bastante familiarizada com contentores do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Marcador de posição | Value |
|-------------|-------|
|{BILLING_KEY} | Esta chave é utilizada para iniciar o contentor e está disponível na página de chaves do reconhecedor de formulário do portal do Azure.  |
|{BILLING_ENDPOINT_URI} | O valor do URI de ponto de extremidade faturação está disponível na página de descrição geral do reconhecedor de formulário do portal do Azure.|
|{COMPUTER_VISION_API_KEY}| A chave está disponível na página de chaves de API de visão do computador do portal do Azure.|
|{COMPUTER_VISION_ENDPOINT_URI}|O ponto final de faturação. Se estiver a utilizar um recurso de imagem digitalizada com base na cloud, o valor do URI está disponível na página de descrição geral da API de visão de computador do portal do Azure. Se estiver a utilizar um `cognitive-services-recognize-text` contentor, utilize o URL de ponto final de faturação é passado para o contentor no `docker run` comando.|

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing-configuration-setting).
> O valor de ApiKey é o **chave** na página de chaves do Azure Resource do reconhecedor de formulário.

## <a name="form-recognizer-container-docker-examples"></a>Exemplos de Docker de contentor do reconhecedor de formulário

Os exemplos de Docker seguintes são para o contentor do reconhecedor de formulário.

### <a name="basic-example-for-form-recognizer"></a>Exemplo básico para reconhecedor de formulário

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Exemplo de registo para o reconhecedor de formulário

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>Passos Seguintes

* Revisão [como instalar e executar contentores](form-recognizer-container-howto.md)
