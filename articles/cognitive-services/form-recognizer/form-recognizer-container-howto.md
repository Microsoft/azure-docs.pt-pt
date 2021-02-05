---
title: Como instalar e executar o recipiente para o Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Este artigo explicará como usar o recipiente Azure Cognitive Services Recogniser para analisar formulários e dados de tabela.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 53334dfdcdb917c754c9bc4205b0918c6d207da8
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584792"
---
# <a name="install-and-run-form-recognizer-containers-retiring"></a>Instalar e executar recipientes de reconhecimento de formulários (aposentação)

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Azure Form Recogniser aplica tecnologia de aprendizagem automática para identificar e extrair pares e tabelas de valor-chave a partir de formas. Associa valores e entradas de tabela com os pares de valor-chave e, em seguida, produz dados estruturados que incluem as relações no ficheiro original. 

Para reduzir a complexidade e integrar facilmente um modelo personalizado de Reconhecimento de Formulários no seu processo de automatização de fluxo de trabalho ou outra aplicação, pode ligar para o modelo utilizando uma API rest simples. Apenas são necessários cinco documentos de formulário, para que possa obter resultados de forma rápida, precisa e adaptada ao seu conteúdo específico. Não é necessária uma intervenção manual pesada ou uma vasta experiência em ciências dos dados. E não requer rotulagem de dados ou anotação de dados.

| Função | Funcionalidades |
|----------|----------|
| Reconhecedor de Formato | <li>Processos ficheiros PDF, PNG e JPG<li>Treina modelos personalizados com um mínimo de cinco formas do mesmo layout <li>Extrai pares de valores-chave e informações de tabela <li>Utiliza a funcionalidade Azure Cognitive Services Computer Vision API Recognise Text para detetar e extrair texto impresso a partir de imagens dentro de formulários<li>Não requer anotação ou rotulagem |

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de utilizar os recipientes do Form Recogniser, deve cumprir os seguintes requisitos:

| Necessário | Objetivo |
|----------|---------|
| Motor do Docker | Precisa do Motor Docker instalado num [computador anfitrião.](#the-host-computer) O Docker oferece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contentores se conectem e enviem dados de faturação para a Azure. <br><br> No Windows, o Docker também deve ser configurado para suportar recipientes Linux.<br><br> |
| Familiaridade com Docker | Você deve ter uma compreensão básica de conceitos docker, tais como registos, repositórios, contentores e imagens de contentores, e conhecimento de `docker` comandos básicos. |
| A CLI do Azure | Instale o [Azure CLI](/cli/azure/install-azure-cli) no seu anfitrião. |
| Recurso API de Visão Computacional | Para processar documentos e imagens digitalizados, precisa de um recurso de Visão De Computador. Pode aceder à funcionalidade "Reconhecer texto" como um recurso Azure (a API REST ou SDK) ou um recipiente *de texto de reconhecimento de serviços cognitivos.* [](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull) As taxas de faturação habituais aplicam-se. <br><br>Passe na chave API e nos pontos finais para o seu recurso de Visão De Computador (nuvem Azure ou recipiente de Serviços Cognitivos). Utilize esta tecla API e o ponto final como **{COMPUTER_VISION_API_KEY}** e **{COMPUTER_VISION_ENDPOINT_URI}**.<br><br> Se utilizar o recipiente *de texto de reconhecimento de serviços cognitivos, certifique-se* de que:<br><br>A tecla de visão de computador para o recipiente Do Reconhecimento de Formulários é a chave especificada no comando visão de computador `docker run` para o recipiente de texto de reconhecimento de *serviços cognitivos.*<br>O seu ponto final de faturação é o ponto final do contentor (por exemplo, `http://localhost:5000` ). Se utilizar o recipiente de Visão De Computador e o Recipiente Do Reconhecimento de Formulários juntos no mesmo hospedeiro, ambos não podem ser iniciados com a porta padrão de *5000*. |
| Recurso de reconhecimento de formulários | Para utilizar estes recipientes, deve ter:<br><br>Um recurso **Azure Form Recogniser** para obter a chave API associada e o ponto final URI. Ambos os valores estão disponíveis nas páginas do portal Azure **Form Recogniser** Overview e Keys, e ambos os valores são necessários para iniciar o recipiente.<br><br>**{FORM_RECOGNIZER_API_KEY}**: Uma das duas teclas de recursos disponíveis na página Keys<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**: O ponto final, conforme fornecido na página 'Vista Geral', |

> [!NOTE]
> O nome do recurso Visão De Computador deve ser uma única palavra, sem hífen `-` ou qualquer outro caracteres especiais. Esta restrição está em vigor para garantir a compatibilidade do recipiente de identificação de formulários e reconhecimento.

## <a name="gathering-required-parameters"></a>Recolha de parâmetros necessários

Existem três parâmetros primários para todos os contentores dos Serviços Cognitivos que são necessários. O contrato de licença de utilizador final (EULA) deve estar presente com um valor de `accept` . Além disso, tanto um URL endpoint como uma chave API são necessários.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Endpoint URI `{COMPUTER_VISION_ENDPOINT_URI}` e `{FORM_RECOGNIZER_ENDPOINT_URI}`

O valor **Endpoint** URI está disponível na página *geral* do portal Azure do recurso correspondente do Serviço Cognitivo. Navegue para a página *overview,* paire sobre o Ponto final e aparecerá um `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ícone. Copiar e usar sempre que necessário.

![Reúna o ponto final uri para utilização posterior](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Chaves `{COMPUTER_VISION_API_KEY}` e `{FORM_RECOGNIZER_API_KEY}`

Esta chave é utilizada para iniciar o contentor e está disponível na página Chaves do portal Azure do recurso serviço cognitivo correspondente. Navegue na página *Chaves* e clique no `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ícone.

![Pegue uma das duas chaves para uso posterior](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Estas chaves de subscrição são usadas para aceder à sua API de Serviço Cognitivo. Não partilhem as chaves. Guarde-os de forma segura, por exemplo, utilizando o Cofre da Chave Azure. Recomendamos também a regeneração regular destas teclas. Apenas uma chave é necessária para fazer uma chamada da API. Ao regenerar a primeira chave, pode utilizar a segunda chave para o acesso continuado ao serviço.

## <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos e recomendações do contentor

Os núcleos e memórias mínimos e recomendados da CPU a atribuir para cada recipiente do Reconhecimento de Formulários são descritos no seguinte quadro:

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Reconhecedor de Formato | 2 núcleo, memória de 4-GB | 4 núcleo, memória de 8 GB |
| Reconhecer Texto | 1 núcleo, memória de 8 GB | 2 núcleos, memória de 8 GB |

* Cada núcleo deve ser pelo menos 2,6 gigahertz (GHz) ou mais rápido.
* O núcleo e a memória correspondem às `--cpus` `--memory` definições e configurações, que são utilizadas como parte do `docker run` comando.

> [!Note]
> Os valores mínimos e recomendados baseiam-se nos limites do Docker e *não* nos recursos da máquina hospedeira.

Necessitará tanto dos recipientes de Reconhecimento de Formulários como de Reconhecimento de Texto, por favor tenha em atenção que o recipiente **De Texto Reconhecido** é detalhado fora deste [artigo.](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Como usar o recipiente

Depois de o recipiente estar no [computador anfitrião,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

1. [Executar o recipiente,](#run-the-container-by-using-the-docker-run-command)com as definições de faturação necessárias. Mais [exemplos](form-recognizer-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.
1. [Consultar o ponto final de previsão do recipiente.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-by-using-the-docker-run-command"></a>Executar o recipiente usando o comando de execução do estivador

Use o comando de execução do [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar o contentor. Consulte a [recolha dos parâmetros necessários](#gathering-required-parameters) para obter os `{COMPUTER_VISION_ENDPOINT_URI}` `{COMPUTER_VISION_API_KEY}` `{FORM_RECOGNIZER_ENDPOINT_URI}` valores e `{FORM_RECOGNIZER_API_KEY}` valores.

[Exemplos](form-recognizer-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

### <a name="form-recognizer"></a>Reconhecedor de Formato

> [!NOTE]
> Os diretórios utilizados `--mount` nestes exemplos são caminhos de diretório do Windows. Se estiver a utilizar o Linux ou o macOS, altere o parâmetro para o seu ambiente. 

```bash
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

Este comando:

* Executa um recipiente de reconhecimento de formulário a partir da imagem do recipiente.
* Atribui 2 núcleos de CPU e 8 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.
* Monta um /input e um volume de saída para o recipiente.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Executar recipientes separados como comandos separados de execução de estivadores

Para a combinação de Reconhecimento de Formulários e Reconhecimento de Texto que está hospedada localmente no mesmo anfitrião, utilize os seguintes dois comandos do Docker CLI:

Executar o primeiro contentor no porto 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Executar o segundo contentor na porta 5001.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Cada recipiente subsequente deve estar numa porta diferente. 

### <a name="run-separate-containers-with-docker-compose"></a>Executar recipientes separados com Docker Compose

Para a combinação de Reconhecimento de Formulários e Reconhecimento de Texto que está hospedada localmente no mesmo anfitrião, consulte o seguinte exemplo Docker Compose YAML file. O Reconhecedor de Texto `{COMPUTER_VISION_API_KEY}` deve ser o mesmo tanto para os recipientes como para os `formrecognizer` `ocr` recipientes. O `{COMPUTER_VISION_ENDPOINT_URI}` recipiente é utilizado apenas no `ocr` recipiente, porque o recipiente utiliza o nome `formrecognizer` e a `ocr` porta. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: "{COMPUTER_VISION_API_KEY}"

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"
```

> [!IMPORTANT]
> O `Eula` , e , , bem como as `Billing` `ApiKey` `FormRecognizer:ComputerVisionApiKey` opções e `FormRecognizer:ComputerVisionEndpointUri` opções , devem ser especificados para executar o recipiente; caso contrário, o recipiente não será iniciado. Para mais informações, consulte [Billing.](#billing)

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

|Contentor|Ponto final|
|--|--|
|reconhecimento de formulários|http://localhost:5000

### <a name="form-recognizer"></a>Reconhecedor de Formato

O recipiente fornece APIs de consulta baseada em websocket, a que acede através da [documentação SDK dos serviços de reconhecimento de formulários.](./index.yml)

Por padrão, o Form Recogniser SDK utiliza os serviços online. Para utilizar o recipiente, é necessário alterar o método de inicialização. Veja os exemplos abaixo.

#### <a name="for-c"></a>Para C #

Alterar a utilização desta chamada de inicialização em nuvem Azure:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
a esta chamada, que utiliza o ponto final do contentor:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Para Python

Alterar a utilização desta chamada de inicialização em nuvem Azure:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

a esta chamada, que utiliza o ponto final do contentor:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Reconhecedor de Formato

O recipiente fornece APIs de ponto final REST, que pode encontrar na página [API do Reconhecimento de Formulários.](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o recipiente com um [suporte](form-recognizer-container-configuration.md#mount-settings) de saída e um registo ativado, o recipiente gera ficheiros de registo que são úteis para resolver problemas que ocorrem durante o arranque ou execução do recipiente.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os recipientes Do Reconhecimento de Formulário enviam informações de faturação para a Azure utilizando um recurso _de Reconhecimento de Formulário_ na sua conta Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [os recipientes Configure](form-recognizer-container-configuration.md).

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para descarregar, instalar e executar recipientes de Reconhecimento de Formulários. Em resumo:

* O Formulário Recogniser fornece um recipiente Linux para o Docker.
* As imagens do contentor são descarregadas do registo privado de contentores em Azure.
* Imagens de contentores correm em Docker.
* Pode utilizar a API REST ou a REST SDK para ligar para as operações no recipiente Form Recogniser, especificando o URI hospedeiro do recipiente.
* Deve especificar as informações de faturação quando instantânear um recipiente.

> [!IMPORTANT]
>  Os recipientes dos Serviços Cognitivos não estão licenciados para funcionar sem estarem ligados ao Azure para a medição. Os clientes precisam de permitir que os contentores comuniquem informações de faturação com o serviço de medição em todos os momentos. Os recipientes de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Reveja os recipientes de configuração](form-recognizer-container-configuration.md) para configurações.
* Utilize mais [recipientes de serviços cognitivos.](../cognitive-services-container-support.md)