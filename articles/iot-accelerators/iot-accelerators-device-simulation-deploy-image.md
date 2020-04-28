---
title: Implemente uma imagem de simulação de dispositivo personalizada - Azure/ Microsoft Docs
description: Neste guia de como guiar, aprende a implementar uma imagem personalizada do Docker da solução de Simulação de Dispositivos para o Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "61448416"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Implementar uma imagem personalizada de estivador de simulação de dispositivo

Pode modificar a solução de Simulação de Dispositivo para adicionar funcionalidades personalizadas. Por exemplo, a [telemetria Serialize utilizando](iot-accelerators-device-simulation-protobuf.md) o artigo protocolar buffers mostra-lhe como adicionar um dispositivo personalizado à solução que utiliza buffers protocolar (Protobuf) para enviar telemetria. Depois de ter testado as suas alterações localmente, o próximo passo é implementar as suas alterações na sua instância de Simulação de Dispositivos em Azure. Para completar esta tarefa, precisa de criar e implementar uma imagem do Docker que contenha o seu serviço modificado.

Os passos neste como-guia mostram-lhe como:

1. Preparar um ambiente de desenvolvimento
1. Gerar uma nova imagem do Docker
1. Configure a simulação do dispositivo para usar a sua nova imagem do Docker
1. Executar uma simulação usando a nova imagem

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como guiar, você precisa:

* Uma instância de [simulação](quickstart-device-simulation-deploy.md) de dispositivo implantado.
* Docker. Descarregue a [Edição Comunitária do Docker](https://www.docker.com/products/docker-engine#/download) para a sua plataforma.
* Uma [conta do Docker Hub](https://hub.docker.com/) onde podes carregar as tuas imagens do Docker. Na sua conta Do Docker Hub, crie um repositório público chamado **simulação de dispositivos.**
* Uma solução modificada e testada de simulação de [dispositivo solução](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) na sua máquina local. Por exemplo, pode modificar a solução para serializar a [telemetria utilizando tampões protocolares](iot-accelerators-device-simulation-protobuf.md).
* Uma concha que pode executar SSH. Se instalar o Git Para windows, pode utilizar a concha de **batida** que faz parte da instalação. Também pode utilizar a sua [Concha de Nuvem Azure.](https://shell.azure.com/)

As instruções deste artigo assumem que está a usar o Windows. Se estiver a utilizar outro sistema operativo, poderá ter de ajustar alguns dos caminhos e comandos de ficheiros para se adequar ao seu ambiente.

## <a name="create-a-new-docker-image"></a>Criar uma nova imagem do Docker

Para implementar as suas próprias alterações no serviço de Simulação de Dispositivos, precisa de editar os scripts de construção e implementação em **scripts\docker** folder para carregar os recipientes para a sua conta de docker-hub

### <a name="modify-the-docker-scripts"></a>Modificar os scripts de estivador

Modifique a **construção Do Docker.cmd,** **publique.cmd,** e execute scripts **de dispôs** nos **scripts\docker** pasta com a sua informação de repositório Docker Hub. Estes passos assumem que criou um repositório público chamado **simulação de dispositivos:**

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Atualize o ficheiro **docker-compose.yml** da seguinte forma:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Configure a solução para incluir quaisquer novos ficheiros

Se adicionou novos ficheiros de modelos de dispositivos, tem de os incluir explicitamente na solução. Adicione uma entrada nos **serviços/serviços.csproj** para cada ficheiro adicional a incluir. Por exemplo, se tiver concluído a [telemetria Serialize utilizando](iot-accelerators-device-simulation-protobuf.md) o Protocolo Buffers how-to, adicione as seguintes entradas:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Gere novas imagens do Docker e empurre para o Docker Hub

Publique a nova imagem do Docker no Docker Hub usando a sua conta de docker-hub:

1. Abra um pedido de comando e navegue para a sua cópia local do repositório de simulação do dispositivo.

1. Navegue para a pasta do **estivador:**

    ```cmd
    cd scripts\docker
    ```

1. Executar o seguinte comando para construir a imagem do Docker:

    ```cmd
    build.cmd
    ```

1. Execute o seguinte comando para publicar a imagem do Docker no seu repositório Docker Hub. Inscreva-se no Docker com as suas credenciais do Docker Hub:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Atualizar o serviço

Para atualizar o recipiente de simulação de dispositivo para utilizar a sua imagem personalizada, complete os seguintes passos:

* Utilize o SSH para se ligar à máquina virtual que acolhe a sua instância de Simulação de Dispositivos. Utilize o endereço IP e a palavra-passe de que tomou nota na secção anterior:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Navegue para o diretório **/app:**

    ```sh
    cd /app
    ```

* Editar o ficheiro **docker-compose.yml:**

    ```sh
    sudo nano docker-compose.yml
    ```

    Modifique a **imagem** para indicar a imagem **de simulação de dispositivo** personalizada que carregou para o seu repositório Docker Hub:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Guarde as alterações.

* Executar o seguinte comando para reiniciar os microserviços:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Executar a sua Simulação

Agora pode executar uma simulação utilizando a sua solução de simulação de dispositivo personalizada:

1. Lance o uI web de simulação de dispositivo a partir de aceleradores de [soluções Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Utilize a UI web para configurar e executar uma simulação. Se já tiver concluído a [telemetria Serialize utilizando buffers protocolar,](iot-accelerators-device-simulation-protobuf.md)pode utilizar o seu modelo de dispositivo personalizado.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implementar uma imagem de Simulação de Dispositivo personalizada, talvez queira aprender a [utilizar um hub IoT existente com o acelerador de solução](iot-accelerators-device-simulation-choose-hub.md)de simulação de dispositivo .