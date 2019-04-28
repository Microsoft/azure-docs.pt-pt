---
title: Implementar uma imagem de simulação de dispositivo personalizada - Azure | Documentos da Microsoft
description: Este guia de procedimentos, irá aprender a implementar uma imagem personalizada do Docker da solução de simulação do dispositivo no Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61448416"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Implementar uma imagem personalizada do docker de simulação do dispositivo

É possível modificar a solução de simulação do dispositivo para adicionar recursos personalizados. Por exemplo, o [serializar telemetria com Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) artigo mostra-lhe como adicionar um dispositivo personalizado para a solução que utiliza Protocol Buffers (Protobuf) para enviar telemetria. Depois de o testar as suas alterações localmente, a próxima etapa é implantar as alterações à sua instância de simulação do dispositivo no Azure. Para concluir essa tarefa, terá de criar e implementar uma imagem do Docker que contém o serviço modificado.

Os passos neste procedimentos-to-guia mostram como para:

1. Preparar um ambiente de desenvolvimento
1. Gerar uma nova imagem de Docker
1. Configurar a simulação do dispositivo para utilizar a nova imagem do Docker
1. Executar uma simulação usando a nova imagem

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste guia de procedimentos, terá de:

* Um implementado [simulação do dispositivo](quickstart-device-simulation-deploy.md) instância.
* Docker. Transfira o [Docker Community Edition](https://www.docker.com/products/docker-engine#/download) para a sua plataforma.
* R [conta do Docker Hub](https://hub.docker.com/) onde pode carregar as imagens do Docker. Na sua conta do Docker Hub, criar um repositório público chamado **simulação do dispositivo**.
* A modificação e testado [solução de simulação do dispositivo](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) no seu computador local. Por exemplo, pode modificar a solução para [serializar telemetria com Protocol Buffers](iot-accelerators-device-simulation-protobuf.md).
* Um shell que pode ser executadas SSH. Se instalar o Git para Windows, pode utilizar o **bash** shell que faz parte da instalação de th. Também pode utilizar o seu [Azure Cloud Shell](https://shell.azure.com/).

As instruções neste artigo partem do princípio de que está a utilizar o Windows. Se estiver a utilizar outro sistema operacional, poderá ter de ajustar alguns dos caminhos de ficheiro e comandos de acordo com seu ambiente.

## <a name="create-a-new-docker-image"></a>Criar uma nova imagem de Docker

Para implementar as suas próprias alterações no serviço de simulação do dispositivo, tem de editar os scripts de compilação e implementação na **scripts\docker** pasta para carregar os contentores à sua conta do docker hub

### <a name="modify-the-docker-scripts"></a>Modificar os scripts de docker

Modificar o Docker **build.cmd**, **publish.cmd**, e **run.cmd** scripts no **scripts\docker** pasta com o seu Hub do Docker informações do repositório. Estes passos partem do princípio de que criou um repositório público chamado **simulação do dispositivo**:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Atualização do **docker-Compose** ficheiros da seguinte forma:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Configurar a solução para incluir todos os novos ficheiros

Se tiver adicionado quaisquer novos ficheiros de modelo do dispositivo, terá de explicitamente incluídos na solução. Adicionar uma entrada para o **services/services.csproj** para cada ficheiro adicional incluir. Por exemplo, se concluiu a [serializar telemetria com Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) procedimentos, adicione as seguintes entradas:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Gerar novas imagens do Docker e enviar por push para o Docker Hub

Publicar a nova imagem do Docker para o Docker Hub com a sua conta do docker hub:

1. Abra uma linha de comandos e navegue para a sua cópia local do repositório de simulação do dispositivo.

1. Navegue para o **docker** pasta:

    ```cmd
    cd scripts\docker
    ```

1. Execute o seguinte comando para criar a imagem do Docker:

    ```cmd
    build.cmd
    ```

1. Execute o seguinte comando para publicar a imagem do Docker para o seu repositório do Docker Hub. Inicie sessão no Docker com as suas credenciais do Docker Hub:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Atualize o serviço

Para atualizar o contentor de simulação do dispositivo ao utilizar a sua imagem personalizada, conclua os seguintes passos:

* Utilize SSH para ligar à máquina virtual que aloja a instância de simulação do dispositivo. Utilize o endereço IP e a palavra-passe que anotou na secção anterior:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Navegue para o **/app** diretório:

    ```sh
    cd /app
    ```

* Editar a **docker-Compose** ficheiro:

    ```sh
    sudo nano docker-compose.yml
    ```

    Modificar a **imagem** para apontar o personalizado **simulação do dispositivo** imagem carregada para o seu repositório de Hub do Docker:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Guarde as alterações.

* Execute o seguinte comando para reiniciar os microsserviços:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Executar a simulação

Agora, pode executar uma simulação usando sua solução de simulação de dispositivo personalizada:

1. Lançamento da interface do Usuário da web a simulação do dispositivo [Aceleradores de soluções do Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Utilize a IU da web para configurar e executar uma simulação. Se concluiu anteriormente [serializar telemetria com Protocol Buffers](iot-accelerators-device-simulation-protobuf.md), pode usar o modelo do dispositivo personalizada.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como implementar uma imagem de simulação de dispositivo personalizada, pode querer saber como [utilizar um hub IoT com o solution accelerator de simulação do dispositivo](iot-accelerators-device-simulation-choose-hub.md).