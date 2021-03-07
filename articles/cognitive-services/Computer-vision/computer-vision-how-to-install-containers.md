---
title: Instalar recipientes de OCR Docker da Visão de Computador
titleSuffix: Azure Cognitive Services
description: Utilize os recipientes Read OCR Docker da Visão de Computador para extrair texto de imagens e documentos, no local.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: no local, OCR, Docker, contentor
ms.openlocfilehash: 1c9e681e3c02cb65b2a54070cc778051a0e7ac53
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432558"
---
# <a name="install-read-ocr-docker-containers-preview"></a>Instalar Ler recipientes OCR Docker (Pré-visualização) 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Os contentores permitem-lhe executar as APIs de Imagem Digitalizada no seu próprio ambiente. Os contentores são ótimos para requisitos específicos de governação de dados e segurança. Neste artigo você vai aprender a descarregar, instalar e executar recipientes de Visão de Computador.

O recipiente *Read* OCR permite extrair texto impresso e manuscrito de imagens e documentos com suporte para formatos de ficheiro JPEG, PNG, BMP, PDF e TIFF. Para mais informações, consulte a documentação da [API de leitura.](concept-recognizing-text.md#read-api)

## <a name="read-32-preview-container"></a>Ler 3.2 pré-visualização do recipiente

> [!NOTE]
> O recipiente de pré-visualização Read 3.0 foi precotado. 

O recipiente OCR de pré-visualização Leia 3.2 fornece:
* Novos modelos para maior precisão.
* Suporte para várias línguas dentro do mesmo documento.
* Apoio a um total de 73 línguas. Consulte a lista completa de [idiomas apoiados pelo OCR](./language-support.md#optical-character-recognition-ocr).
* Uma única operação para documentos e imagens.
* Suporte para documentos e imagens maiores.
* Pontuações de confiança.
* Suporte para documentos com texto impresso e manuscrito.
* Capacidade de extrair texto de apenas páginas selecionadas num documento.
* Escolha a ordem de saída da linha de texto do padrão para uma ordem de leitura mais natural apenas para línguas latinas.
* Classificação da linha de texto como estilo manuscrito ou não apenas para línguas latinas.

Se estiver a utilizar os recipientes Read 2.0 hoje, consulte o [guia de migração](read-container-migration-guide.md) para saber mais sobre as mudanças nas novas versões.

## <a name="prerequisites"></a>Pré-requisitos

Deve encontrar os seguintes pré-requisitos antes de utilizar os recipientes:

|Necessário|Objetivo|
|--|--|
|Motor do Docker| Precisa do Motor Docker instalado num [computador anfitrião.](#the-host-computer) O Docker oferece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contentores se conectem e enviem dados de faturação para a Azure. <br><br> **No Windows,** o Docker também deve ser configurado para suportar recipientes Linux.<br><br>|
|Familiaridade com Docker | Você deve ter uma compreensão básica de conceitos docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de `docker` comandos básicos.| 
|Recurso de Visão Computacional |Para utilizar o recipiente, deve ter:<br><br>Um recurso Azure **Computer Vision** e a chave API associada o ponto final URI. Ambos os valores estão disponíveis nas páginas 'Visão Geral' e Chaves para o recurso e são obrigados a iniciar o contentor.<br><br>**{API_KEY}**: Uma das duas teclas de recursos disponíveis na página **Keys**<br><br>**{ENDPOINT_URI}**: O ponto final, conforme fornecido na página **'Vista Geral',**|

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="request-approval-to-run-the-container"></a>Solicitar aprovação para executar o contentor

Preencha e submeta o [formulário de pedido](https://aka.ms/csgate) para solicitar aprovação para executar o contentor. 

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
| Ler 2.0 pré-visualização | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Ler 3.2 pré-visualização | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2` |

Utilize o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para descarregar uma imagem de contentor.

### <a name="docker-pull-for-the-read-container"></a>Docker puxa para o recipiente ler

# <a name="version-32-preview"></a>[Versão 3.2-pré-visualização](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2
```

# <a name="version-20-preview"></a>[Versão 2.0-pré-visualização](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
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

# <a name="version-32-preview"></a>[Versão 3.2-pré-visualização](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa o recipiente Ler a partir da imagem do recipiente.
* Atribui 8 núcleos de CPU e 18 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.

# <a name="version-20-preview"></a>[Versão 2.0-pré-visualização](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa o recipiente Ler a partir da imagem do recipiente.
* Atribui 8 núcleos de CPU e 16 gigabytes (GB) de memória.
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o recipiente.
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.

---


Mais [exemplos](./computer-vision-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis. 

> [!IMPORTANT]
> As `Eula` `Billing` opções , e `ApiKey` opções devem ser especificadas para executar o recipiente; caso contrário, o recipiente não arranca.  Para mais informações, consulte [Billing.](#billing)

Se necessitar de uma produção mais elevada (por exemplo, ao processar ficheiros de várias páginas), considere colocar vários contentores [num cluster Kubernetes,](deploy-computer-vision-on-premises.md)utilizando [o Azure Storage](../../storage/common/storage-account-create.md) e a [Azure Queue](../../storage/queues/storage-queues-introduction.md).

Se estiver a utilizar o Azure Storage para armazenar imagens para processamento, pode criar uma [cadeia de ligação](../../storage/common/storage-configure-connection-string.md) a utilizar ao ligar para o recipiente.

Para encontrar a sua cadeia de ligação:

1. Navegue para **as contas de Armazenamento** no portal Azure e encontre a sua conta.
2. Clique nas **teclas de acesso** na lista de navegação à esquerda.
3. A sua cadeia de ligação ficará localizada abaixo **da cadeia Connection**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

O contentor fornece APIs de ponto final de predição de consulta com base em REST. 

# <a name="version-32-preview"></a>[Versão 3.2-pré-visualização](#tab/version-3-2)

Utilize o anfitrião, `http://localhost:5000`, para APIs de contentor. Pode ver o caminho de Swagger em: `http://localhost:5000/swagger/vision-v3.2-preview-read/swagger.json` .

# <a name="version-20-preview"></a>[Versão 2.0-pré-visualização](#tab/version-2)

Utilize o anfitrião, `http://localhost:5000`, para APIs de contentor. Pode ver o caminho de Swagger em: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Leitura assíncronea


# <a name="version-32-preview"></a>[Versão 3.2-pré-visualização](#tab/version-3-2)

Pode utilizar o `POST /vision/v3.2/read/analyze` e `GET /vision/v3.2/read/operations/{operationId}` operações em conjunto para ler assíncroneamente uma imagem, semelhante à forma como o serviço de Visão De Computador utiliza as operações correspondentes do REST. O método ASYNCHRONOUS POST devolverá um `operationId` que é usado como identificador ao pedido HTTP GET.


A partir da UI swagger, selecione o `Analyze` para expandi-lo no navegador. Em seguida, **selecione Experimente-o**  >  **Escolha o ficheiro**. Neste exemplo, usaremos a seguinte imagem:

![separadores vs espaços](media/tabs-vs-spaces.png)

Quando o POST assíncrona tiver funcionado com sucesso, devolve um código de estado **HTTP 202.** Como parte da resposta, há um `operation-location` cabeçalho que detém o resultado final para o pedido.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

O `operation-location` url totalmente qualificado é acedido através de um HTTP GET. Aqui está a resposta JSON da execução do `operation-location` URL a partir da imagem anterior:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          },
          {
            "boundingBox": [
              286,
              171,
              415,
              165,
              417,
              197,
              287,
              201
            ],
            "text": "paces",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.746
              }
            },
            "words": [
              {
                "boundingBox": [
                  286,
                  179,
                  404,
                  166,
                  405,
                  198,
                  290,
                  201
                ],
                "text": "paces",
                "confidence": 0.938
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[Versão 2.0-pré-visualização](#tab/version-2)

Pode utilizar o `POST /vision/v2.0/read/core/asyncBatchAnalyze` e `GET /vision/v2.0/read/operations/{operationId}` operações em conjunto para ler assíncroneamente uma imagem, semelhante à forma como o serviço de Visão De Computador utiliza as operações correspondentes do REST. O método ASYNCHRONOUS POST devolverá um `operationId` que é usado como identificador ao pedido HTTP GET.

A partir da UI swagger, selecione o `asyncBatchAnalyze` para expandi-lo no navegador. Em seguida, **selecione Experimente-o**  >  **Escolha o ficheiro**. Neste exemplo, usaremos a seguinte imagem:

![separadores vs espaços](media/tabs-vs-spaces.png)

Quando o POST assíncrona tiver funcionado com sucesso, devolve um código de estado **HTTP 202.** Como parte da resposta, há um `operation-location` cabeçalho que detém o resultado final para o pedido.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

O `operation-location` url totalmente qualificado é acedido através de um HTTP GET. Aqui está a resposta JSON da execução do `operation-location` URL a partir da imagem anterior:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

---

> [!IMPORTANT]
> Se colocar vários recipientes De leitura atrás de um equilibrador de carga, por exemplo, sob Docker Compose ou Kubernetes, deve ter uma cache externa. Como o recipiente de processamento e o recipiente de pedido GET podem não ser os mesmos, uma cache externa armazena os resultados e partilha-os através de contentores. Para obter mais informações sobre as definições de cache, consulte [os recipientes Configure Computer Vision Docker](./computer-vision-resource-container-config.md).

### <a name="synchronous-read"></a>Leitura sincronizada

Pode utilizar a seguinte operação para ler sincronizadamente uma imagem. 

# <a name="version-32-preview"></a>[Versão 3.2-pré-visualização](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[Versão 2.0-pré-visualização](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

Quando a imagem é lida na sua totalidade, então e só então a API devolve uma resposta JSON. A única exceção a isto é se ocorrer um erro. Quando ocorre um erro, o seguinte JSON é devolvido:

```json
{
    "status": "Failed"
}
```

O objeto de resposta JSON tem o mesmo gráfico de objeto que a versão assíncrona. Se é um utilizador JavaScript e deseja segurança do tipo, considere utilizar o TypeScript para lançar a resposta JSON.

Para uma caixa de utilização por exemplo, consulte a caixa de <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">areia TypeScript aqui</a> e selecione **Executar** para visualizar a sua facilidade de utilização.

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o recipiente com um [suporte](./computer-vision-resource-container-config.md#mount-settings) de saída e um registo ativado, o recipiente gera ficheiros de registo que são úteis para resolver problemas que ocorrem durante o arranque ou execução do recipiente.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os contentores dos Serviços Cognitivos enviam informações de faturação para a Azure, utilizando o recurso correspondente na sua conta Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [os recipientes Configure](./computer-vision-resource-container-config.md). 

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
* Consulte a [API de Visão De Computador](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) para obter mais informações sobre os métodos suportados pelo recipiente.
* Consulte [perguntas frequentes (FAQ)](FAQ.md) para resolver problemas relacionados com a funcionalidade de Visão de Computador.
* Use mais [recipientes de serviços cognitivos](../cognitive-services-container-support.md)
