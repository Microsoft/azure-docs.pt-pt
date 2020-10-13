---
title: Alterar e reafectar um microserviço - Azure / Microsoft Docs
description: Este tutorial mostra-lhe como alterar e redistribuir um micro serviço em Monitorização Remota
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ff3e12721628e244f247e174af101e71ea91191
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998327"
---
# <a name="customize-and-redeploy-a-microservice"></a>Personalizar e reimplementar um microsserviço

Este tutorial mostra-lhe como editar um dos [microserviços](https://azure.com/microservices) na solução de Monitorização Remota, construir uma imagem do seu microserviço, implementar a imagem no seu hub de estivador e depois usá-la na solução de Monitorização Remota. Para introduzir este conceito, o tutorial usa um cenário básico onde se chama API de microserviço e altera a mensagem de estado de "Alive and Well" para "New Edits Made Here!"

A solução de Monitorização Remota utiliza microserviços que são construídos usando imagens de estivadores que são retiradas de um estivador. 

Neste tutorial, ficará a saber como:

>[!div class="checklist"]
> * Editar e construir um microserviço na solução de Monitorização Remota
> * Construir uma imagem de estivador
> * Empurre uma imagem de estiva para o seu centro de estivadores
> * Puxe a nova imagem do estivador
> * Visualizar as mudanças 

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar este tutorial, precisa:

>[!div class="checklist"]
> * [Implementar o acelerador de solução de monitorização remota localmente](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Uma conta Docker](https://hub.docker.com/)
> * [Carteiro](https://www.getpostman.com/) - Necessário para ver a resposta da API

## <a name="call-the-api-and-view-response-status"></a>Ligue para a API e veja o estado de resposta

Nesta parte, você chama o gestor de ioT padrão microservice API. A API devolve uma mensagem de estado que muda mais tarde, personalizando o microserviço.

1. Certifique-se de que a solução de Monitorização Remota está a funcionar localmente na sua máquina.
2. Localize onde descarregou o Carteiro e abra-o.
3. No Carteiro, insira o seguinte no GET: `http://localhost:8080/iothubmanager/v1/status` .
4. Veja o retorno e deverá ver, "Status": "OK:Alive and Well".

    ![Mensagem viva e bem carteiro](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Alterar o estado e construir a imagem

Agora mude a mensagem de estado do microserviço Iot Hub Manager para "Novas Edições Feitas Aqui!" e, em seguida, reconstruir a imagem do estivador com este novo estatuto. Se tiver problemas aqui, consulte a nossa secção [de resolução de problemas.](#Troubleshoot)

1. Certifique-se de que o seu terminal está aberto e mude para o diretório onde clonou a solução de Monitorização Remota. 
1. Altere o seu diretório para "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services".
1. Abra StatusService.cs em qualquer editor de texto ou IDE que você gosta. 
1. Localize o seguinte código:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    e alterá-lo para o código abaixo e guardá-lo.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Volte para o seu terminal mas agora mude para o seguinte diretório: "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker".
6. Para construir a sua nova imagem de estivador, escreva

    ```sh
    sh build
    ```
    
    ou no Windows:
    
    ```cmd
    ./build.cmd
    ```

7. Para verificar se a sua nova imagem foi criada com sucesso, escreva

    ```cmd/sh
    docker images 
    ```

O repositório deve ser "azureiotpcs/iothub-manager-dotnet".

![Imagem bem sucedida do estivador](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Etiquetar e enviar a imagem
Antes de poder empurrar a sua nova imagem de estivador para um estivador, o Docker espera que as suas imagens sejam marcadas. Se tiver problemas aqui, consulte a nossa secção [de resolução de problemas.](#Troubleshoot)

1. Localize o ID de imagem da imagem do estivador que criou digitando:

    ```cmd/sh
    docker images
    ```

2. Para marcar a sua imagem com o tipo "teste"

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Para empurrar a sua imagem recém-marcada para o seu eixo de estivador, escreva

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Abra o seu navegador de internet e vá ao seu [estivador](https://hub.docker.com/) e inscreva-se.
5. Devias agora ver a tua nova imagem de estivador no teu estivador.
![Imagem de estivador no centro do estivador](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Atualize a sua solução de monitorização remota
Agora tens de atualizar o teu estivador local.yml para tirar a tua nova imagem do estivador do teu estivador. Se tiver problemas aqui, consulte a nossa secção [de resolução de problemas.](#Troubleshoot)

1. Volte para o terminal e mude para o seguinte diretório: "azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local".
2. Abra o docker-compose.yml em qualquer editor de texto ou IDE que você gosta.
3. Localize o seguinte código:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    e mudá-la para parecer a imagem abaixo e salvá-la.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Ver o novo estado de resposta
Termine reenviando uma instância local da solução de monitorização remota e visualizando a nova resposta de estado no Carteiro.

1. Volte para o seu terminal e mude para o seguinte diretório: "azure-iot-pcs-remote-monitoring-dotnet/scripts/local".
2. Inicie a sua instância local da solução de Monitorização Remota digitando o seguinte comando no terminal:

    ```cmd/sh
    docker-compose up
    ```

3. Localize onde descarregou o Carteiro e abra-o.
4. No Carteiro, insira o seguinte pedido no GET: `http://localhost:8080/iothubmanager/v1/status` . Agora deve ver, "Status": "OK: Novas Edições Feitas Aqui!".

![Novas edições feitas aqui mensagem carteiro](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="troubleshoot"></a><a name="Troubleshoot"></a>Resolução de problemas

Se estiver a ter problemas, tente remover as imagens e os contentores do estivador na máquina local.

1. Para remover todos os recipientes, primeiro terá de parar todos os recipientes de funcionamento. Abra o seu terminal e tipo

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Para remover todas as imagens, abra o seu terminal e tipo 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Pode verificar se há algum recipiente na máquina digitando

    ```cmd/sh
    docker ps -aq 
    ```

    Se tiver removido todos os recipientes com sucesso, nada deve aparecer.

4. Pode verificar se há alguma imagem na máquina digitando

    ```cmd/sh
    docker images
    ```

    Se tiver removido todos os recipientes com sucesso, nada deve aparecer.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, viu como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Editar e construir um microserviço na solução de Monitorização Remota
> * Construir uma imagem de estivador
> * Empurre uma imagem de estiva para o seu centro de estivadores
> * Puxe a nova imagem do estivador
> * Visualizar as mudanças 

A próxima coisa a tentar é [personalizar o microserviço do simulador de dispositivo na solução de Monitorização Remota](iot-accelerators-microservices-example.md)

Para obter mais informações sobre a solução de Monitorização Remota, consulte:

* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

