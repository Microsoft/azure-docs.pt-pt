---
title: Como instalar e executar contentores - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Como descarregar, instalar e executar recipientes para Visão de Computador neste tutorial walkthrough.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: bc55ab2697d8278bd975f618d17804499ba0128d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982064"
---
# <a name="install-and-run-read-containers-preview"></a>Instalar e executar Recipientes de leitura (pré-visualização)

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Os contentores permitem-lhe executar as APIs de Imagem Digitalizada no seu próprio ambiente. Os contentores são ótimos para requisitos específicos de governação de dados e segurança. Neste artigo, ficará a saber como transferir, instalar e executar um contentor de Imagem Digitalizada.

O recipiente *Ler* permite detetar e extrair *texto impresso* a partir de imagens de vários objetos com diferentes superfícies e fundos, tais como recibos, cartazes e cartões de visita. Além disso, o recipiente *Ler* deteta *texto manuscrito* em imagens e fornece suporte de ficheiros PDF, TIFF e multi-páginas. Para mais informações, consulte a documentação da [API.](concept-recognizing-text.md#read-api)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Deve encontrar os seguintes pré-requisitos antes de utilizar os recipientes:

|Necessário|Objetivo|
|--|--|
|Motor do Docker| Precisa do Motor Docker instalado num [computador anfitrião.](#the-host-computer) O Docker oferece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contentores se conectem e enviem dados de faturação para a Azure. <br><br> **No Windows,** o Docker também deve ser configurado para suportar recipientes Linux.<br><br>|
|Familiaridade com Docker | Você deve ter uma compreensão básica de conceitos docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de `docker` comandos básicos.| 
|Recurso de Visão Computacional |Para utilizar o recipiente, deve ter:<br><br>Um recurso Azure **Computer Vision** e a chave API associada o ponto final URI. Ambos os valores estão disponíveis nas páginas 'Visão Geral' e Chaves para o recurso e são obrigados a iniciar o contentor.<br><br>**{API_KEY}**: Uma das duas teclas de recursos disponíveis na página **Keys**<br><br>**{ENDPOINT_URI}**: O ponto final, conforme fornecido na página **'Vista Geral',**|

## <a name="request-approval-to-run-the-container"></a>Solicitar aprovação para executar o contentor

Preencha e submeta o [formulário de pedido](https://aka.ms/cognitivegate) para solicitar aprovação para executar o contentor. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Suporte avançado de extensão do vetor

O computador **anfitrião** é o computador que gere o contentor do estivador. O anfitrião *deve suportar* [extensões de vetores avançados](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Pode verificar se há suporte AVX2 em anfitriões Linux com o seguinte comando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> O computador anfitrião é *necessário* para suportar o AVX2. O recipiente *não funcionará* corretamente sem suporte AVX2.

### <a name="container-requirements-and-recommendations"></a>Requisitos e recomendações do contentor

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com `docker pull`

As imagens do recipiente para Ler estão disponíveis.

| Contentor | Registo de contentores / Repositório / Nome da imagem |
|-----------|------------|
| Ler 3.0 pré-visualização | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| Ler 3.1 pré-visualização | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |

Utilize o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para descarregar uma imagem de contentor.

### <a name="docker-pull-for-the-read-container"></a>Docker puxa para o recipiente ler

# <a name="version-31-preview"></a>[Versão 3.1-pré-visualização](#tab/version-3-1)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview
```

# <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Como usar o recipiente

Uma vez que o recipiente esteja no [computador anfitrião,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

1. [Executar o recipiente,](#run-the-container-with-docker-run)com as definições de faturação necessárias. Mais [exemplos](computer-vision-resource-container-config.md) do `docker run` comando estão disponíveis. 
1. [Consultar o ponto final de previsão do recipiente.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Executar o recipiente com `docker run`

Use o comando de execução do [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar o contentor. Consulte a [recolha dos parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os `{ENDPOINT_URI}` valores e `{API_KEY}` valores.

[Exemplos](computer-vision-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

# <a name="version-31-preview"></a>[Versão 3.1-pré-visualização](#tab/version-3-1)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa o recipiente Ler a partir da imagem do recipiente.
* Atribui 8 núcleos de CPU e 18 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.

# <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

Este comando:

* Executa o recipiente Ler a partir da imagem do recipiente.
* Atribui 8 núcleos de CPU e 18 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.

---


Mais [exemplos](./computer-vision-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis. 

> [!IMPORTANT]
> As `Eula` `Billing` opções , e `ApiKey` opções devem ser especificadas para executar o recipiente; caso contrário, o recipiente não arranca.  Para mais informações, consulte [Billing.](#billing)

Se necessitar de uma produção mais elevada (por exemplo, ao processar ficheiros de várias páginas), considere a utilização de vários recipientes V3.0 ou V3.1 [num cluster Kubernetes,](deploy-computer-vision-on-premises.md)utilizando [o Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-create) e a [Azure Queue](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction).

Se estiver a utilizar o Azure Storage para armazenar imagens para processamento, pode criar uma [cadeia de ligação](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string) a utilizar ao ligar para o recipiente.

Para encontrar a sua cadeia de ligação:

1. Navegue para **as contas de Armazenamento** no portal Azure e encontre a sua conta.
2. Clique nas **teclas de acesso** na lista de navegação à esquerda.
3. A sua cadeia de ligação ficará localizada abaixo **da cadeia Connection**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

O contentor fornece APIs de ponto final de predição de consulta com base em REST. 

# <a name="version-31-preview"></a>[Versão 3.1-pré-visualização](#tab/version-3-1)

Utilize o anfitrião, `http://localhost:5000`, para APIs de contentor. Pode ver o caminho de Swagger em: `http://localhost:5000/swagger/vision-v3.0-preview-read/swagger.json` .

# <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Utilize o anfitrião, `http://localhost:5000`, para APIs de contentor. Pode ver o caminho de Swagger em: `http://localhost:5000/swagger/vision-v3.1-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Leitura assíncronea


# <a name="version-31-preview"></a>[Versão 3.1-pré-visualização](#tab/version-3-1)

Pode utilizar o `POST /vision/v3.1/read/analyze` e `GET /vision/v3.1/read/operations/{operationId}` operações em conjunto para ler assíncroneamente uma imagem, semelhante à forma como o serviço de Visão De Computador utiliza as operações correspondentes do REST. O método ASYNCHRONOUS POST devolverá um `operationId` que é usado como identificador ao pedido HTTP GET.


A partir da UI swagger, selecione o `asyncBatchAnalyze` para expandi-lo no navegador. Em seguida, **selecione Experimente-o**  >  **Escolha o ficheiro**. Neste exemplo, usaremos a seguinte imagem:

![separadores vs espaços](media/tabs-vs-spaces.png)

Quando o POST assíncrona tiver funcionado com sucesso, devolve um código de estado **HTTP 202.** Como parte da resposta, há um `operation-location` cabeçalho que detém o resultado final para o pedido.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.1/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

O `operation-location` url totalmente qualificado é acedido através de um HTTP GET. Aqui está a resposta JSON da execução do `operation-location` URL a partir da imagem anterior:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:30:14Z",
  "lastUpdatedDateTime": "2020-09-02T10:30:15Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.999
            },
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "appearance": {
              "style": "print",
              "styleConfidence": 0.603
            },
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Pode utilizar o `POST /vision/v3.0/read/analyze` e `GET /vision/v3.0/read/operations/{operationId}` operações em conjunto para ler assíncroneamente uma imagem, semelhante à forma como o serviço de Visão De Computador utiliza as operações correspondentes do REST. O método ASYNCHRONOUS POST devolverá um `operationId` que é usado como identificador ao pedido HTTP GET.

A partir da UI swagger, selecione o `asyncBatchAnalyze` para expandi-lo no navegador. Em seguida, **selecione Experimente-o**  >  **Escolha o ficheiro**. Neste exemplo, usaremos a seguinte imagem:

![separadores vs espaços](media/tabs-vs-spaces.png)

Quando o POST assíncrona tiver funcionado com sucesso, devolve um código de estado **HTTP 202.** Como parte da resposta, há um `operation-location` cabeçalho que detém o resultado final para o pedido.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

O `operation-location` url totalmente qualificado é acedido através de um HTTP GET. Aqui está a resposta JSON da execução do `operation-location` URL a partir da imagem anterior:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:24:49Z",
  "lastUpdatedDateTime": "2020-09-02T10:24:50Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

---

> [!IMPORTANT]
> Se colocar vários recipientes De leitura atrás de um equilibrador de carga, por exemplo, sob Docker Compose ou Kubernetes, deve ter uma cache externa. Como o recipiente de processamento e o recipiente de pedido GET podem não ser os mesmos, uma cache externa armazena os resultados e partilha-os através de contentores. Para obter mais informações sobre as definições de cache, consulte [os recipientes Configure Computer Vision Docker](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config).

### <a name="synchronous-read"></a>Leitura sincronizada

Pode utilizar a seguinte operação para ler sincronizadamente uma imagem. 

# <a name="version-31-preview"></a>[Versão 3.1-pré-visualização](#tab/version-3-1)

`POST /vision/v3.1/read/syncAnalyze` 

# <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

`POST /vision/v3.0/read/SyncAnalyze`

---

Quando a imagem é lida na sua totalidade, então e só então a API devolve uma resposta JSON. A única exceção a isto é se ocorrer um erro. Quando ocorre um erro, o seguinte JSON é devolvido:

```json
{
    "status": "Failed"
}
```

O objeto de resposta JSON tem o mesmo gráfico de objeto que a versão assíncrona. Se é um utilizador JavaScript e deseja segurança do tipo, considere utilizar o TypeScript para lançar a resposta JSON.

Para uma caixa de utilização por exemplo, consulte a caixa de <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">areia TypeScript aqui <span class="docon docon-navigate-external x-hidden-focus"></span> </a> e selecione **Executar** para visualizar a sua facilidade de utilização.

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o recipiente com um [suporte](./computer-vision-resource-container-config.md#mount-settings) de saída e um registo ativado, o recipiente gera ficheiros de registo que são úteis para resolver problemas que ocorrem durante o arranque ou execução do recipiente.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os contentores dos Serviços Cognitivos enviam informações de faturação para a Azure, utilizando o recurso correspondente na sua conta Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [os recipientes Configure](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para descarregar, instalar e executar recipientes de Visão de Computador. Em resumo:

* A Visão Computacional fornece um recipiente Linux para Docker, encapsulando a Leitura.
* As imagens do contentor são descarregadas a partir do registo do contentor "Pré-visualização do contentor" em Azure.
* Imagens de contentores correm em Docker.
* Pode utilizar a API REST ou a SDK para ligar para as operações em recipientes De leitura, especificando o hospedeiro URI do recipiente.
* Deve especificar as informações de faturação ao instantaneamente um recipiente.

> [!IMPORTANT]
> Os recipientes dos Serviços Cognitivos não estão licenciados para funcionar sem estarem ligados ao Azure para a medição. Os clientes precisam de permitir que os contentores comuniquem informações de faturação com o serviço de medição em todos os momentos. Os recipientes de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Rever recipientes de configuração](computer-vision-resource-container-config.md) para configurações de configuração
* Reveja [a visão geral da Visão computacional](overview.md) para saber mais sobre o reconhecimento de texto impresso e manuscrito
* Consulte a [API de Visão De Computador](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para obter mais informações sobre os métodos suportados pelo recipiente.
* Consulte [perguntas frequentes (FAQ)](FAQ.md) para resolver problemas relacionados com a funcionalidade de Visão de Computador.
* Use mais [recipientes de serviços cognitivos](../cognitive-services-container-support.md)
