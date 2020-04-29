---
title: Como instalar e executar recipiente para reconhecimento de formulários
titleSuffix: Azure Cognitive Services
description: Este artigo explicará como usar o recipiente de reconhecimento de formulários de formulário de serviços cognitivos Azure para analisar os dados de forma e tabela.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8cfa9114c5a5e57882cb84b604c1cf71be9acc52
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80878345"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>Instalar e executar recipientes de reconhecimento de formulário (Pré-visualização)

O Azure Form Recogniser aplica tecnologia de machine learning para identificar e extrair pares e tabelas de valor-chave a partir de formulários. Associa valores e entradas de tabela com os pares de valor-chave e, em seguida, produz dados estruturados que incluem as relações no ficheiro original. 

Para reduzir a complexidade e integrar facilmente um modelo personalizado de Reconhecimento de Formulários no seu processo de automatização de fluxode trabalho ou outra aplicação, pode chamar o modelo utilizando uma Simples API REST. São necessários apenas cinco documentos de formulário (ou um formulário vazio e dois formulários preenchidos), para que possa obter resultados rapidamente, com precisão e adaptados ao seu conteúdo específico. Não é necessária nenhuma intervenção manual pesada ou uma vasta experiência em ciência de dados. E não requer rotulagem de dados ou anotação de dados.

> [!IMPORTANT]
> Os recipientes 'Reconhecimento de Formulários' utilizam atualmente a versão 1.0 da API do Reconhecimento de Formulários. Pode aceder à versão mais recente da API utilizando o serviço gerido.

| Função | Funcionalidades |
|----------|----------|
| Reconhecedor de Formato | <li>Processos ficheiros PDF, PNG e JPG<li>Treina modelos personalizados com um mínimo de cinco formas do mesmo layout <li>Extrai pares de valor-chave e informações de tabela <li>Utiliza a funcionalidade de texto de reconhecimento da Visão Computacional Azure Cognitive Services API para detetar e extrair texto impresso a partir de imagens dentro de formulários<li>Não requer anotação ou rotulagem |

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de utilizar os recipientes 'Reconhecimento de Formulários', deve cumprir os seguintes pré-requisitos:

| Necessário | Objetivo |
|----------|---------|
| Motor do Docker | Precisa do Motor Docker instalado num [computador de acolhimento.](#the-host-computer) O Docker oferece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os recipientes se conectem e enviem dados de faturação para o Azure. <br><br> No Windows, o Docker também deve ser configurado para suportar os recipientes Linux.<br><br> |
| Familiaridade com Docker | Você deve ter uma compreensão básica dos conceitos Docker, tais como registos, repositórios, contentores e imagens de contentores, e conhecimento de comandos básicos. `docker` |
| A CLI do Azure | Instale o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) no seu anfitrião. |
| Recurso API de Visão Computacional | Para processar documentos e imagens digitalizados, precisa de um recurso computer vision. Pode aceder à funcionalidade Recognise Text como um recurso Azure (o REST API ou SDK) ou um recipiente de texto de *reconhecimento cognitivo-serviços* [container](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull). Aplicam-se as taxas habituais de faturação. <br><br>Passe tanto na tecla API como nos pontos finais para o seu recurso Computer Vision (nuvem Azure ou recipiente de Serviços Cognitivos). Utilize esta tecla API e o ponto final como **{COMPUTER_VISION_API_KEY}** e **{COMPUTER_VISION_ENDPOINT_URI}**.<br><br> Se utilizar o recipiente de texto de *reconhecimento cognitivo-serviços, certifique-se* de que:<br><br>A chave Computer Vision para o recipiente 'Reconhecimento de `docker run` Formulários' é a chave especificada no comando computer vision para o recipiente de *texto de reconhecimento cognitivo-serviços.*<br>O seu ponto final de faturação é o `http://localhost:5000`ponto final do contentor (por exemplo, ). Se utilizar o recipiente Computer Vision e o recipiente 'Reconhecimento de Formulários' juntos no mesmo hospedeiro, ambos não podem ser iniciados com a porta padrão de *5000*. |
| Recurso reconhecedor de formulários | Para utilizar estes recipientes, deve ter:<br><br>Um recurso Azure **Form Recogniser** para obter a chave API associada e o ponto final URI. Ambos os valores estão disponíveis nas páginas de Visão **Geral** e Teclas do portal Azure, e ambos os valores são necessários para iniciar o recipiente.<br><br>**{FORM_RECOGNIZER_API_KEY}**: Uma das duas teclas de recursos disponíveis na página Keys<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**: O ponto final fornecido na página 'Visão Geral' |

> [!NOTE]
> O nome do recurso Computer Vision deve ser `-` uma única palavra, sem um hífen ou qualquer outro caracteres especiais. Esta restrição está em vigor para garantir a compatibilidade do recipiente de texto de reconhecimento de formulários e reconhecer o texto.

## <a name="gathering-required-parameters"></a>Recolha de parâmetros necessários

Existem três parâmetros primários para todos os recipientes dos Serviços Cognitivos que são necessários. O acordo de licença de utilizador final (EULA) deve estar presente com um valor de `accept`. Além disso, são necessários tanto um URL endpoint como uma chave API.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Endpoint `{COMPUTER_VISION_ENDPOINT_URI}` URI e`{FORM_RECOGNIZER_ENDPOINT_URI}`

O valor **Endpoint** URI está disponível na página de *visão geral* do portal Azure do correspondente recurso do Serviço Cognitivo. Navegue para a página *de visão geral,* paire sobre o Ponto Final, e aparecerá um `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ícone. Copiar e usar sempre que necessário.

![Junte o ponto final uri para uso posterior](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Chaves `{COMPUTER_VISION_API_KEY}` e`{FORM_RECOGNIZER_API_KEY}`

Esta chave é usada para iniciar o recipiente, e está disponível na página chaves do portal Azure do recurso correspondente do Serviço Cognitivo. Navegue na página *Keys* e `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> clique no ícone.

![Obtenha uma das duas chaves para uso posterior](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Estas chaves de subscrição são usadas para aceder à sua API do Serviço Cognitivo. Não partilhe as chaves. Guarde-os de forma segura, por exemplo, utilizando o Cofre de Chaves Azure. Recomendamos também a regeneração regular destas teclas. Só uma chave é necessária para fazer uma chamada da API. Ao regenerar a primeira tecla, pode utilizar a segunda tecla para o acesso continuado ao serviço.

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao registo de contentores

Primeiro deve completar e submeter o formulário de pedido de acesso aos recipientes de reconhecimento de formulários de [serviços cognitivos](https://aka.ms/FormRecognizerContainerRequestAccess) para solicitar o acesso ao recipiente. Ao fazê-lo também o inscreve para a Visão Computacional. Não precisa de se inscrever separadamente para o formulário de pedido de visão computacional. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos e recomendações de contentores

Os núcleos e memória cpu mínimos e recomendados para alocar para cada recipiente de reconhecimento de formulário são descritos na tabela seguinte:

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Reconhecedor de Formato | 2 núcleo, 4-GB de memória | 4 núcleos, 8-GB de memória |
| Reconhecer texto | 1 núcleo, 8-GB de memória | 2 núcleos, 8-GB de memória |

* Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.
* O núcleo e `--cpus` a `--memory` memória correspondem às definições e definições, que são usadas como parte do `docker run` comando.

> [!Note]
> Os valores mínimos e recomendados baseiam-se nos limites do Docker e *não* nos recursos da máquina hospedeira.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>Obtenha as imagens do recipiente com o comando de puxar estivador

As imagens do recipiente para as ofertas de Texto **reconhecidos** e **reconhecem** os formulários estão disponíveis no seguinte registo de contentores:

| Contentor | Nome de imagem totalmente qualificado |
|-----------|------------|
| Reconhecedor de Formato | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| Reconhecer texto | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Necessitará de ambos os recipientes, por favor note que o recipiente **De Texto Reconhecível** é [detalhado fora deste artigo.](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker puxar para o recipiente de reconhecimento de formulário

#### <a name="form-recognizer"></a>Reconhecedor de Formato

Para obter o recipiente 'Reconhecimento de Formulários', utilize o seguinte comando:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>Docker puxar para o recipiente de texto reconhecer

#### <a name="recognize-text"></a>Reconhecer texto

Para obter o recipiente Reconhecer Texto, utilize o seguinte comando:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>Como utilizar o recipiente

Depois de o recipiente estar no [computador de acolhimento,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

1. [Executar o recipiente,](#run-the-container-by-using-the-docker-run-command)com as definições de faturação necessárias. Mais [exemplos](form-recognizer-container-configuration.md#example-docker-run-commands) `docker run` do comando estão disponíveis.
1. [Consulta do ponto final da previsão do recipiente](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Executar o recipiente usando o comando de execução de estivador

Use o comando de execução de [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar o recipiente. Consulte a recolha de [parâmetros necessários](#gathering-required-parameters) `{COMPUTER_VISION_ENDPOINT_URI}`para `{COMPUTER_VISION_API_KEY}` `{FORM_RECOGNIZER_ENDPOINT_URI}` obter `{FORM_RECOGNIZER_API_KEY}` os valores e os valores necessários.

[Exemplos](form-recognizer-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

### <a name="form-recognizer"></a>Reconhecedor de Formato

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
* Remove automaticamente o recipiente após a sua saída. A imagem do recipiente ainda está disponível no computador hospedeiro.
* Monta uma entrada e um volume /saída para o recipiente.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Executar recipientes separados como comandos separados de estivador

Para a combinação de reconhecimento de formulários e reconhecimento de texto que é hospedado localmente no mesmo hospedeiro, use os seguintes dois comandos ClI de docker:

Gereno primeiro recipiente no porto 5000. 

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

Corra o segundo contentor no porto 5001.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Cada recipiente subsequente deve estar numa porta diferente. 

### <a name="run-separate-containers-with-docker-compose"></a>Executar recipientes separados com Docker Compose

Para a combinação de reconhecimento de formulários e reconhecimento de texto que é hospedado localmente no mesmo anfitrião, consulte o seguinte exemplo Docker Compose YAML ficheiro. O Reconhecimento `{COMPUTER_VISION_API_KEY}` de Texto deve ser `formrecognizer` `ocr` o mesmo tanto para os recipientes como para os recipientes. O `{COMPUTER_VISION_ENDPOINT_URI}` `ocr` recipiente é utilizado apenas `formrecognizer` no `ocr` recipiente, porque o recipiente utiliza o nome e a porta. 

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
> O `Eula` `Billing`, `ApiKey`e, bem `FormRecognizer:ComputerVisionApiKey` como `FormRecognizer:ComputerVisionEndpointUri` as e as opções, devem ser especificados para executar o recipiente; caso contrário, o contentor não vai começar. Para mais informações, consulte [billing.](#billing)

## <a name="query-the-containers-prediction-endpoint"></a>Consulta do ponto final da previsão do recipiente

|Contentor|Ponto Final|
|--|--|
|reconhecimento de forma|http://localhost:5000

### <a name="form-recognizer"></a>Reconhecedor de Formato

O recipiente fornece APIs de ponto final baseado em websocket, a que acede através da [documentação SDK](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)dos serviços de reconhecimento de formulários .

Por padrão, o SDK do Reconhecimento de Formulários utiliza os serviços online. Para utilizar o recipiente, é necessário alterar o método de inicialização. Veja os exemplos abaixo.

#### <a name="for-c"></a>Para C #

Mude de utilização desta chamada de inicialização azure-cloud:

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

Mude de utilização desta chamada de inicialização azure-cloud:

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

O recipiente fornece APIs de ponto final REST, que pode encontrar na página [API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) do Reconhecimento de Formulários.


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o recipiente com um [suporte](form-recognizer-container-configuration.md#mount-settings) de saída e uma exploração de madeira ativada, o recipiente gera ficheiros de registo que são úteis para resolver problemas que ocorrem durante o arranque ou funcionamento do recipiente.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os recipientes 'Reconhecimento de Formulários' enviam informações de faturação para o Azure utilizando um recurso _'Reconhecimento_ de Formulários' na sua conta Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para mais informações sobre estas opções, consulte [os recipientes Configur.](form-recognizer-container-configuration.md)

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para descarregar, instalar e executar recipientes de Reconhecimento de Formulários. Em resumo:

* O Reconhecimento de Formulários fornece um recipiente Linux para o Docker.
* As imagens do contentor são descarregadas do registo privado de contentores em Azure.
* Imagens de contentores correm em Docker.
* Pode utilizar a API REST ou o REST SDK para ligar para operações no recipiente 'Reconhecimento de Formulários', especificando o uri hospedeiro do recipiente.
* Deve especificar as informações de faturação quando instantaneamente um recipiente.

> [!IMPORTANT]
>  Os recipientes dos Serviços Cognitivos não estão licenciados para funcionar sem serem ligados ao Azure para medição. Os clientes precisam de permitir que os contentores comuniquem sempre informações de faturação com o serviço de medição. Os recipientes dos Serviços Cognitivos não enviam dados dos clientes (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* Reveja os recipientes de [configuração](form-recognizer-container-configuration.md) para configurações de configuração.
* Utilize mais recipientes de [serviços cognitivos.](../cognitive-services-container-support.md)
