---
title: Implementar uma imagem de simulação de dispositivo personalizada - Azure / Microsoft Docs
description: Neste guia de como fazer, aprende a implementar uma imagem personalizada do Docker da solução de Simulação do Dispositivo para o Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "61448416"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Implemente uma imagem personalizada do estivador de simulação de dispositivo

Pode modificar a solução de Simulação do Dispositivo para adicionar funcionalidades personalizadas. Por exemplo, a [telemetria Serialize utilizando](iot-accelerators-device-simulation-protobuf.md) o artigo de Buffers protocolar mostra-lhe como adicionar um dispositivo personalizado à solução que utiliza buffers de protocolo (Protobuf) para enviar telemetria. Depois de ter testado as suas alterações localmente, o próximo passo é implementar as alterações na sua instância de Simulação de Dispositivo no Azure. Para completar esta tarefa, precisa de criar e implementar uma imagem Docker que contenha o seu serviço modificado.

Os passos deste guia mostram-lhe como:

1. Preparar um ambiente de desenvolvimento
1. Gerar uma nova imagem do Docker
1. Configure simulação de dispositivo para usar a sua nova imagem docker
1. Executar uma simulação usando a nova imagem

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como guiar, você precisa:

* Uma instância de [simulação de dispositivo](quickstart-device-simulation-deploy.md) implantada.
* O Docker. Descarregue a [Edição Comunitária do Docker](https://www.docker.com/products/docker-engine#/download) para a sua plataforma.
* Uma [conta Docker Hub](https://hub.docker.com/) onde podes fazer o upload das tuas imagens do Docker. Na sua conta Docker Hub, crie um repositório público chamado **simulação de dispositivo.**
* Uma solução de [simulação de dispositivo](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) modificada e testada na sua máquina local. Por exemplo, pode modificar a solução para [serializar a telemetria utilizando tampões de protocolo](iot-accelerators-device-simulation-protobuf.md).
* Uma concha que pode correr SSH. Se instalar o Git For Windows, pode utilizar a casca de **bata** que faz parte da instalação. Também pode utilizar o [seu Azure Cloud Shell](https://shell.azure.com/).

As instruções deste artigo pressupõem que está a usar o Windows. Se estiver a utilizar outro sistema operativo, poderá ter de ajustar alguns dos caminhos e comandos de ficheiros para se adequar ao seu ambiente.

## <a name="create-a-new-docker-image"></a>Criar uma nova imagem do Docker

Para implementar as suas próprias alterações no serviço de Simulação de Dispositivos, é necessário editar os scripts de construção e implementação em **scripts\docker** pasta para carregar os contentores para a sua conta de estivador

### <a name="modify-the-docker-scripts"></a>Modifique os scripts do estivador

Modifique a **construção do Docker.cmd,** **publique.cmd**e **execute scripts.cmd** nas **pastas scripts\docker** com as informações do repositório do Docker Hub. Estes passos pressupõem que criou um repositório público chamado **simulação de dispositivo:**

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Atualize o ficheiro **docker-compose.yml** da seguinte forma:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Configurar a solução para incluir quaisquer novos ficheiros

Se adicionou novos ficheiros de modelos de dispositivos, tem de os incluir explicitamente na solução. Adicione uma entrada nos **serviços/serviços.csproj** para cada ficheiro adicional a incluir. Por exemplo, se tiver concluído a [telemetria Serialize utilizando tampões](iot-accelerators-device-simulation-protobuf.md) de protocolo como fazê-lo, adicione as seguintes entradas:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Gere novas imagens do Docker e empurre para o Docker Hub

Publique a nova imagem do Docker no Docker Hub utilizando a sua conta de estivador:

1. Abra um pedido de comando e navegue para a sua cópia local do repositório de simulação do dispositivo.

1. Navegue para a pasta **do estivador:**

    ```cmd
    cd scripts\docker
    ```

1. Executar o seguinte comando para construir a imagem docker:

    ```cmd
    build.cmd
    ```

1. Executar o seguinte comando para publicar a imagem do Docker no seu repositório Docker Hub. Inscreva-se no Docker com as suas credenciais docker hub:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Atualizar o serviço

Para atualizar o recipiente de Simulação de Dispositivo para utilizar a sua imagem personalizada, complete os seguintes passos:

* Utilize o SSH para ligar à máquina virtual que hospeda a sua instância de Simulação do Dispositivo. Utilize o endereço IP e a palavra-passe que fez na secção anterior:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Navegue para o **diretório /app:**

    ```sh
    cd /app
    ```

* Editar o ficheiro **docker-compose.yml:**

    ```sh
    sudo nano docker-compose.yml
    ```

    Modifique a **imagem** para apontar a imagem **de simulação de dispositivo** personalizada que carregou para o seu repositório Docker Hub:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Guarde as alterações.

* Executar o seguinte comando para reiniciar os microserviços:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Executar a sua Simulação

Agora pode executar uma simulação utilizando a sua solução de Simulação de Dispositivo personalizada:

1. Lance o seu UI web de simulação de dispositivo a partir de Aceleradores de [Solução IoT microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Utilize a UI web para configurar e executar uma simulação. Se tiver concluído previamente [a telemetria Serialize utilizando tampões de protocolo,](iot-accelerators-device-simulation-protobuf.md)pode utilizar o seu modelo de dispositivo personalizado.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implementar uma imagem personalizada de Simulação de Dispositivos, talvez queira aprender a [utilizar um hub IoT existente com o acelerador de solução de simulação de dispositivos.](iot-accelerators-device-simulation-choose-hub.md)