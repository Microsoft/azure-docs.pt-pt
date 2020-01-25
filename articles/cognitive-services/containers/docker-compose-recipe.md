---
title: Utilizar o Docker Compose para implementar vários contentores
titleSuffix: Azure Cognitive Services
description: Aprenda a implantar vários recipientes de Serviços Cognitivos. Este artigo mostra-lhe como orquestrar várias imagens de contentores do Docker usando Docker Compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 54a2aac3db47d60f02a45adae9aaa6077d675a43
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716907"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Utilizar o Docker Compose para implementar vários contentores

Este artigo mostra-lhe como implantar vários contentores dos Serviços Cognitivos Azure. Especificamente, aprenderás a usar o Docker Compose para orquestrar várias imagens de contentores do Docker.

> [Docker Compose](https://docs.docker.com/compose/) é uma ferramenta para definir e executar aplicações Docker multi-contentores. Em Composição, utiliza um ficheiro YAML para configurar os serviços da sua aplicação. Em seguida, cria e inicia todos os serviços a partir da sua configuração executando um único comando.

Pode ser útil orquestrar várias imagens de contentores num único computador hospedeiro. Neste artigo, vamos reunir os recipientes Recognise Text e Form Recogniser.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que devem ser instaladas e executadas localmente:

* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Motor Docker.](https://www.docker.com/products/docker-engine) Confirme que o Docker CLI funciona numa janela de consola.
* Um recurso Azure com o nível de preços correto. Apenas os seguintes níveis de preços funcionam com este recipiente:
  * **Recurso computer Vision** apenas com f0 ou nível de preços Standard.
  * **Formulário Recurso reconhecível** apenas com f0 ou nível de preços Standard.
  * **Recursos dos Serviços Cognitivos** com o nível de preços S0.

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao registro de contêiner

Complete e submeta o formulário de pedido de pedidos de recipientes de discurso de [serviços cognitivos.](https://aka.ms/speechcontainerspreview/) 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Arquivo De composição De Docker

O ficheiro YAML define todos os serviços a serem implantados. Estes serviços baseiam-se numa `DockerFile` ou numa imagem de contentor existente. Neste caso, usaremos duas imagens de pré-visualização. Copie e cole o seguinte ficheiro YAML e guarde-o como *docker-compose.yaml*. Forneça os valores adequados de **apiásio,** **faturação**e **EndpointUri** no ficheiro.

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
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
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Crie os diretórios na máquina hospedeira especificadas sob o nó de **volumes.** Esta abordagem é necessária porque os diretórios devem existir antes de tentar montar uma imagem utilizando encadernações de volume.

## <a name="start-the-configured-docker-compose-services"></a>Inicie os serviços configurados do Docker Compose

Um ficheiro Docker Compose permite a gestão de todas as etapas do ciclo de vida de um serviço definido: serviços de arranque, paragem e reconstrução; visualizar o estado do serviço; e streaming de log. Abra uma interface de linha de comando a partir do diretório do projeto (onde está localizado o ficheiro docker-compose.yaml).

> [!NOTE]
> Para evitar erros, certifique-se de que a máquina hospedeira partilha corretamente as unidades com o Motor Docker. Por exemplo, se a e:\visualização pública for usada como um diretório no ficheiro docker-compose.yaml, partilhe a unidade E com o Docker.

A partir da interface da linha de comando, execute o seguinte comando para iniciar (ou reiniciar) todos os serviços definidos no ficheiro docker-compose.yaml:

```console
docker-compose up
```

A primeira vez que o Docker executa o comando de **composição de estivadores** utilizando esta configuração, puxa as imagens configuradas sob o nó de **serviços** e depois descarrega-as e monta-as:

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
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
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
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

Após o download das imagens, os serviços de imagem são iniciados:

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

Aqui está um exemplo de saída:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>Testar o recipiente de texto reconhecer

Abra um navegador na máquina anfitriã e vá ao **local de acolhimento** utilizando a porta especificada a partir do ficheiro docker-compose.yaml, como http://localhost:5021/swagger/index.html. Pode utilizar a função "Try It" na API para testar o ponto final do Texto Reconhecido.

![Reconhecer recipiente de texto](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>Testar o recipiente de reconhecimento de formulários

Abra um navegador na máquina anfitriã e vá ao **local de acolhimento** utilizando a porta especificada a partir do ficheiro docker-compose.yaml, como http://localhost:5010/swagger/index.html. Pode utilizar a função "Try It" na API para testar o ponto final do Reconhecimento de Formulários.

![Recipiente de reconhecimento de formulário](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Recipientes de Serviços Cognitivos](../cognitive-services-container-support.md)
