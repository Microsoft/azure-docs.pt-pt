---
title: Alterar e reimplantar um microserviço - Azure [ Microsoft Docs
description: Este tutorial mostra-lhe como mudar e reimplantar um microserviço em Monitorização Remota
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 1552c54afe2195d58a032e9cc7bfa5aa70c844b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447646"
---
# <a name="customize-and-redeploy-a-microservice"></a>Personalizar e reimplementar um microsserviço

Este tutorial mostra-lhe como editar um dos [microserviços](https://azure.com/microservices) na solução de Monitorização Remota, construir uma imagem do seu microserviço, implementar a imagem para o seu centro de estivadores e depois usá-la na solução de Monitorização Remota. Para introduzir este conceito, o tutorial usa um cenário básico onde você chama uma API microserviço e muda a mensagem de estado de "Alive and Well" para "New Edits Made Here!"

A solução de monitorização remota utiliza microserviços que são construídos utilizando imagens de estivadores que são retiradas de um centro de estivadores. 

Neste tutorial, ficará a saber como:

>[!div class="checklist"]
> * Editar e construir um microserviço na solução de Monitorização Remota
> * Construa uma imagem de estivador
> * Empurre uma imagem de estivador para o seu centro de estivadores
> * Puxe a nova imagem do estivador
> * Visualizar as alterações 

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, precisa de:

>[!div class="checklist"]
> * [Implementar o acelerador de solução de monitorização remota localmente](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Uma conta docker](https://hub.docker.com/)
> * [Carteiro](https://www.getpostman.com/) - Necessário para ver a resposta da API

## <a name="call-the-api-and-view-response-status"></a>Ligue para a API e veja o estado de resposta

Nesta parte, você chama o gestor de hub padrão IoT microservice API. A API devolve uma mensagem de estado que muda mais tarde, personalizando o microserviço.

1. Certifique-se de que a solução de monitorização remota está a funcionar localmente na sua máquina.
2. Localize onde descarregou o Carteiro e abra-o.
3. No Carteiro, insira o `http://localhost:8080/iothubmanager/v1/status`seguinte no GET: .
4. Veja o retorno e deve ver, "Status": "OK:Alive and Well".

    ![Mensagem viva e bem carteiro](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Alterar o estado e construir a imagem

Agora mude a mensagem de estado do microserviço do Iot Hub Manager para "New Edits Made Here!" e, em seguida, reconstruir a imagem do estivador com este novo estatuto. Se tiver problemas aqui, consulte a nossa secção de [resolução](#Troubleshoot) de problemas.

1. Certifique-se de que o seu terminal está aberto e mude para o diretório onde clonou a solução de Monitorização Remota. 
1. Mude o seu diretório para "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services".
1. Abra StatusService.cs em qualquer editor de texto ou IDE que você goste. 
1. Localize o seguinte código:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    e mudá-lo para o código abaixo e guardá-lo.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Volte ao seu terminal mas agora mude para o seguinte diretório: "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker".
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

![Imagem de estivador bem sucedida](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Etiquetar e enviar a imagem
Antes de poder empurrar a sua nova imagem para um centro de estivadores, o Docker espera que as suas imagens sejam marcadas. Se tiver problemas aqui, consulte a nossa secção de [resolução](#Troubleshoot) de problemas.

1. Localize o ID de imagem da imagem do estivador que criou digitando:

    ```cmd/sh
    docker images
    ```

2. Para marcar a sua imagem com o tipo de "teste"

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Para empurrar a sua imagem recém-marcada para o seu centro de estivadores, escreva

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Abra o seu navegador de internet e vá ao seu centro de [estivadores](https://hub.docker.com/) e inscreva-se.
5. Agora devias ver a tua nova imagem de estiva no teu centro de estivadores.
![Imagem de estivador no centro de estivadores](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Atualize a sua solução de monitorização remota
Agora precisa atualizar o seu docker-compose.yml local para tirar a sua nova imagem de estivador do seu centro de estivadores. Se tiver problemas aqui, consulte a nossa secção de [resolução](#Troubleshoot) de problemas.

1. Volte ao terminal e mude para o seguinte diretório: "azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local".
2. Abra docker-compose.yml em qualquer editor de texto ou IDE que você gosta.
3. Localize o seguinte código:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    e mudá-la para parecer a imagem abaixo e salvá-la.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Ver o novo estado de resposta
Termine reimplantando uma instância local da solução de Monitorização Remota e visualizando a nova resposta de estado no Carteiro.

1. Volte ao seu terminal e mude para o seguinte diretório: "azure-iot-pcs-remote-monitoring-dotnet/scripts/local".
2. Inicie a sua instância local da solução de monitorização remota digitando o seguinte comando no terminal:

    ```cmd/sh
    docker-compose up
    ```

3. Localize onde descarregou o Carteiro e abra-o.
4. No Carteiro, insira o seguinte `http://localhost:8080/iothubmanager/v1/status`pedido no GET: . Deve agora ver, "Status": "OK: Novas Editits Made Here!".

![Nova mensagem de carteiro made here](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="troubleshoot"></a><a name="Troubleshoot"></a>Resolução de problemas

Se estiver com problemas, tente remover as imagens e contentores da máquina local.

1. Para remover todos os recipientes, primeiro terá de parar todos os recipientes de funcionamento. Abra o seu terminal e escreva

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Para remover todas as imagens, abra o seu terminal e escreva 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Pode verificar se existem recipientes na máquina digitando

    ```cmd/sh
    docker ps -aq 
    ```

    Se removeu com sucesso todos os contentores, nada deve aparecer.

4. Pode verificar se existem imagens na máquina digitando

    ```cmd/sh
    docker images
    ```

    Se removeu com sucesso todos os contentores, nada deve aparecer.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, viu como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Editar e construir um microserviço na solução de Monitorização Remota
> * Construa uma imagem de estivador
> * Empurre uma imagem de estivador para o seu centro de estivadores
> * Puxe a nova imagem do estivador
> * Visualizar as alterações 

A próxima coisa a tentar é [personalizar o microserviço simulador de dispositivo na solução](iot-accelerators-microservices-example.md) de Monitorização Remota

Para obter mais informações sobre a solução de Monitorização Remota, consulte:

* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

