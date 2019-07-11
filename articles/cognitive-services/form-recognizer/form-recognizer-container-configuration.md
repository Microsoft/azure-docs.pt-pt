---
title: Como configurar um contentor para o reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Saiba como configurar o contentor do reconhecedor de formulário para analisar dados de formulário e de tabela.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 7752b09dd1bf20d796b19d03e62426b098486c39
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718451"
---
# <a name="configure-form-recognizer-containers"></a>Configurar os contentores do reconhecedor de formulário

Ao utilizar contentores do reconhecedor de formulário do Azure, pode criar uma arquitetura de aplicativo que está otimizada para tirar partido das capacidades de cloud robustas e Localidade de borda.

Configurar o ambiente de tempo de execução de contentores do reconhecedor de formulário com o `docker run` argumentos de comando. Este contentor tem vários necessário as definições e algumas definições opcionais. Para obter alguns exemplos, consulte a ["Execute comandos docker de exemplo"](#example-docker-run-commands) secção. As definições específicas do contentor são as definições de faturas.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> O [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), e [ `Eula` ](#eula-setting) definições são utilizadas em conjunto. Tem de fornecer valores válidos para todos os três definições; caso contrário, não inicia o contentor. Para obter mais informações sobre como utilizar estas definições de configuração para criar uma instância de um contentor, consulte [faturação](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey definição de configuração

O `ApiKey` definição especifica a chave de recurso do Azure que é utilizada para controlar informações de faturação para o contentor. O valor para o ApiKey tem de ser uma chave válida para o _formulário reconhecedor_ recurso especificado para `Billing` na secção "Definição de configuração de faturação".

Pode encontrar esta definição no portal do Azure, além **gestão de recursos do reconhecedor de formulário**, em **chaves**.

## <a name="applicationinsights-setting"></a>Definição do Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

O `Billing` definição especifica o URI do ponto final da _reconhecedor de formulário_ recursos do Azure, que é utilizado para informações de faturação para o contentor do medidor. O valor para esta definição de configuração tem de ser um URI de ponto de final válido para um _reconhecedor de formulário_ recursos no Azure. O contentor de relatórios de utilização sobre a cada 10 a 15 minutos.

Pode encontrar esta definição no portal do Azure, além **descrição geral do formulário reconhecedor**, em **Endpoint**.

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia | URI do ponto final de faturação<br><br>Exemplo:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>EULA definição

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Definições de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Definições de montagem

Monta o enlace de utilização para ler e escrever dados de e para o contentor. Pode especificar uma montagem de entrada ou de uma montagem de saída especificando o `--mount` opção da [ `docker run` comando](https://docs.docker.com/engine/reference/commandline/run/).

O contentor do reconhecedor de formulário exige uma montagem de entrada e um monte de saída. A montagem de entrada pode ser só de leitura, e ela é necessária para acesso aos dados que são utilizados para preparação e classificação. A montagem de saída tem de ser gravável e utilizá-lo para armazenar os modelos e dados temporários.

A sintaxe exata da localização de montagem do anfitrião varia consoante o sistema operativo anfitrião. Além disso, a localização de montagem do [computador anfitrião](form-recognizer-container-howto.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões de conta de serviço do Docker e as permissões de localização de montagem do anfitrião.

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Necessário| `Input` | Cadeia | O destino da montagem de entrada. O valor predefinido é `/input`.    <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Necessário| `Output` | Cadeia | O destino de montagem de saída. O valor predefinido é `/output`.  <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Executar comandos de docker de exemplo

Os exemplos seguintes utilizam as definições de configuração para ilustrar como escrever e usar `docker run` comandos. Quando ele é executado, o contentor continua a ser executada até [pará-la](form-recognizer-container-howto.md#stop-the-container).

* **Caracteres de continuação de linha**: Os comandos de Docker nas secções seguintes utilizam uma barra invertida (\\) como um caractere de continuação de linha. Substituir ou remova este caráter, dependendo das necessidades do seu sistema de operativo de anfitrião.
* **Ordem de argumento**: Não altere a ordem dos argumentos, a menos que está familiarizado com contentores do Docker.

Substitua {_argument_name_} na tabela a seguir pelos seus próprios valores:

| Marcador de posição | Value |
|-------------|-------|
|{BILLING_KEY} | A chave que é utilizada para iniciar o contentor. Está disponível no portal do Azure, página chaves do reconhecedor de formulário.  |
|{BILLING_ENDPOINT_URI} | O valor do URI de ponto de extremidade faturação está disponível no portal do Azure, página de descrição geral do reconhecedor de formulário.|
|{COMPUTER_VISION_API_KEY}| A chave está disponível no portal do Azure, página chaves de API de visão do computador.|
|{COMPUTER_VISION_ENDPOINT_URI}|O ponto final de faturação. Se estiver a utilizar um recurso de imagem digitalizada com base na cloud, o valor do URI está disponível no portal do Azure, página de descrição geral da API de visão de computador. Se estiver a utilizar um *cognitive services-reconhecer-texto* contentor, utilize o URL de ponto final de faturação que é passado para o contentor no `docker run` comando.|

> [!IMPORTANT]
> Para executar o contentor, especifique a `Eula`, `Billing`, e `ApiKey` opções; caso contrário, não inicia o contentor. Para obter mais informações, consulte [faturação](#billing-configuration-setting).

> [!NOTE] 
> O valor de ApiKey é o **chave** na página de chaves do Azure Resource do reconhecedor de formulário.

## <a name="form-recognizer-container-docker-examples"></a>Exemplos de Docker de contentor do reconhecedor de formulário

Os exemplos de Docker seguintes são para o contentor do reconhecedor de formulário.

### <a name="basic-example-for-form-recognizer"></a>Exemplo básico para reconhecedor de formulário

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Exemplo de registo para o reconhecedor de formulário

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>Passos Seguintes

* Revisão [instalação e execução de contentores](form-recognizer-container-howto.md).
