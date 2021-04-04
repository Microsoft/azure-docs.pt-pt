---
title: Utilizar o Docker Compose para implementar vários contentores
titleSuffix: Azure Cognitive Services
description: Saiba como implantar vários recipientes de Serviços Cognitivos. Este artigo mostra-lhe como orquestrar várias imagens de contentores Docker usando Docker Compose.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: aahi
ms.openlocfilehash: cedcf8a3fcd656c4af0ca7493c598791d35d20d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "95996135"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Utilizar o Docker Compose para implementar vários contentores

Este artigo mostra-lhe como implantar vários contentores dos Serviços Cognitivos Azure. Especificamente, você vai aprender a usar Docker Compose para orquestrar várias imagens de contentores Docker.

> [Docker Compose](https://docs.docker.com/compose/) é uma ferramenta para definir e executar aplicações Docker multi-contentores. Em Compose, utiliza um ficheiro YAML para configurar os serviços da sua aplicação. Em seguida, cria e inicia todos os serviços a partir da sua configuração executando um único comando.

Pode ser útil orquestrar várias imagens de contentores num único computador anfitrião. Neste artigo, vamos reunir os recipientes de Leitura e Reconhecimento de Formulários.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que devem ser instaladas e executadas localmente:

* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services) antes de começar.
* [Motor Docker.](https://www.docker.com/products/docker-engine) Confirme que o Docker CLI funciona numa janela da consola.
* Um recurso Azure com o nível de preços correto. Apenas os seguintes níveis de preços funcionam com este recipiente:
  * **Recurso de Visão De Computador** apenas com nível de preços F0 ou Standard.
  * **Formulário Recurso Recogniser** apenas com nível de preços F0 ou Standard.
  * **Recurso de Serviços Cognitivos** com o nível de preços S0.
* Se estiver a utilizar um recipiente de pré-visualização fechado, terá de preencher o [formulário de pedido on-line](https://aka.ms/csgate/) para o utilizar.

## <a name="docker-compose-file"></a>Arquivo De Composição Docker

O ficheiro YAML define todos os serviços a serem implementados. Estes serviços dependem de uma `DockerFile` imagem de contentor ou de um recipiente existente. Neste caso, usaremos duas imagens de pré-visualização. Copie e cole o seguinte ficheiro YAML, e guarde-o como *docker-compose.yaml*. Forneça os **valores adequados de apikey,** **faturação** e **EndpointUri** no ficheiro.

```yaml
version: '3.7'
services:
  forms:
    image: "mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Crie os diretórios na máquina hospedeira que são especificados no nó **de volumes.** Esta abordagem é necessária porque os diretórios devem existir antes de tentar montar uma imagem utilizando encadernações de volume.

## <a name="start-the-configured-docker-compose-services"></a>Inicie os serviços de composição do Docker Configurado

Um ficheiro Docker Compose permite a gestão de todas as etapas do ciclo de vida de um serviço definido: serviços de partida, paragem e reconstrução; visualização do estado de serviço; e streaming de registos. Abra uma interface de linha de comando a partir do diretório do projeto (onde está localizado o ficheiro docker-compose.yaml).

> [!NOTE]
> Para evitar erros, certifique-se de que a máquina hospedeira partilha corretamente as unidades com o Motor Docker. Por exemplo, se *e:\publicpreview* for usado como um diretório no ficheiro *docker-compose.yaml,* partilhe a unidade **E** com Docker.

A partir da interface de linha de comando, execute o seguinte comando para iniciar (ou reiniciar) todos os serviços definidos no ficheiro *docker-compose.yaml:*

```console
docker-compose up
```

A primeira vez que Docker executa o comando **de estiva-composição** através desta configuração, puxa as imagens configuradas sob o nó de **serviços** e, em seguida, descarrega e monta:

```console
Pulling forms (mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout:)...
latest: Pulling from azure-cognitive-services/form-recognizer/layout
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview:)...
latest: Pulling from /azure-cognitive-services/vision/read:3.1-preview
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Depois de as imagens serem descarregadas, os serviços de imagem são iniciados:

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>Verifique a disponibilidade do serviço

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Aqui está uma saída de exemplo:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout          latest
4be104c126c5        mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview         latest
```

### <a name="test-containers"></a>Recipientes de ensaio

Abra um browser na máquina hospedeira e vá para o **local,** utilizando a porta especificada a partir do ficheiro *docker-compose.yaml,* como http://localhost:5021/swagger/index.html . Por exemplo, pode utilizar a função **Try It** na API para testar o ponto final do Reconhecimento de Formulários. Ambas as páginas de swagger de contentores devem estar disponíveis e testáveis.

![Recipiente de reconhecimento de formulários](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Contentores de Serviços Cognitivos](../cognitive-services-container-support.md)
