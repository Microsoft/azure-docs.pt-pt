---
title: O docker compose receitas de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como implementar vários contentores de serviços cognitivos. Este procedimento mostra-lhe como orquestrar a várias imagens de contentor do Docker com o Docker Compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 8afb7e866bc2a5fefe28a71653c4a2a87fdc7a5b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445782"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>Utilizar vários contentores numa rede privada com o Docker Compose

Saiba como implementar vários contentores de serviços cognitivos. Este procedimento mostra-lhe como orquestrar a várias imagens de contentor do Docker com o Docker Compose.

> [Docker Compose](https://docs.docker.com/compose/) é uma ferramenta para definir e executar aplicações de Docker com vários contentores. Com a composição, utilize um ficheiro YAML para configurar os serviços da sua aplicação. Em seguida, com um comando único, pode criar e iniciar todos os serviços da sua configuração.

Quando apropriado, orquestrar várias imagens de contentor num computador anfitrião único pode ser atraente. Neste artigo, vamos retirar o serviço de texto de reconhecer e o serviço do reconhecedor de formulário em conjunto.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que tem de estar instaladas e executadas localmente.

* Utilize uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Motor do docker](https://www.docker.com/products/docker-engine) e validar que a CLI do Docker funciona numa janela de consola.
* Um recurso do Azure com o escalão de preço correto. Nem todos os escalões de preço trabalham com este contentor:
  * **Imagem digitalizada** recurso com preços Standard ou de F0 camadas apenas.
  * **Formar o reconhecedor** recurso com preços Standard ou de F0 camadas apenas.
  * **Os serviços cognitivos** recurso com o S0 escalão de preço.

## <a name="request-access-to-the-container-registry"></a>Pedir acesso ao registo de contentor

Preencha e submeta a [formulário de pedido de contentores de voz dos serviços cognitivos](https://aka.ms/speechcontainerspreview/) para pedir acesso ao contentor. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>O docker compose ficheiro

O ficheiro YAML define todos os serviços a serem implantados. Estes serviços baseiam-se em qualquer uma um `DockerFile` ou uma imagem de contentor existente, neste caso, usaremos duas imagens de pré-visualização. Copie e cole o seguinte ficheiro YAML e guarde-o como *docker-yaml*. Fornecer o adequado _apikey_, _faturação_, e _URI do ponto final_ valores no _docker-Compose_ ficheiro abaixo.

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
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
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
> Criar os diretórios no computador anfitrião que são especificadas sob o `volumes` nó. Isto é necessário que os diretórios têm de existir antes de tentar montar uma imagem com ligações de volume.

## <a name="start-the-configured-docker-compose-services"></a>Iniciar o docker configurado compor serviços

Um docker compose ficheiro permite o gerenciamento de ciclos todas as definidas do serviço de vida; iniciar/parar e reconstruir a serviços, a ver o estado do serviço e a transmissão de registos. Abra uma interface de linha de comandos a partir do diretório do projeto (em que o *docker-yaml* encontra-se de ficheiros).

> [!NOTE]
> Para evitar erros, certifique-se de que a máquina de anfitrião está a partilhar corretamente unidades com o **motor do Docker**. Por exemplo, se *e:\publicpreview* é utilizado como um diretório no *docker yaml* partilhar o *unidade E* com o docker.

A partir da interface de linha de comandos, execute o seguinte comando para iniciar (ou reiniciar) em todos os serviços definidos no *docker-yaml*:

```console
docker-compose up
```

A primeira execução de tempo a `docker-compose up` comando com esta configuração, **Docker** irá fazer com que as imagens configuradas sob o `services` nó – transferir/montagem-los:

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

As imagens são transferidas, em seguida, os serviços de imagem são iniciados.

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

## <a name="verify-the-service-availability"></a>Verificar a disponibilidade do serviço

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Segue-se um exemplo de saída:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>Testar o contentor de texto de reconhecer

Abra um browser no computador anfitrião e navegue para `localhost` com a porta especificada a partir de *yaml de docker*, por exemplo, `http://localhost:5021/swagger/index.html`. Pode usar Try-lo de recursos da API para testar o ponto de extremidade de texto recognize.

![Reconhecer texto Swagger](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>Testar o contentor do reconhecedor de formulário

Abra um browser no computador anfitrião e navegue para `localhost` com a porta especificada a partir de *yaml de docker*, por exemplo, `http://localhost:5010/swagger/index.html`. Pode usar Try-lo de recursos da API para testar o ponto de extremidade do reconhecedor de formulário.

![Formulário reconhecedor Swagger](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Contentores de serviços cognitivos](../cognitive-services-container-support.md)