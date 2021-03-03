---
title: Tutorial - Modificar os módulos de análise de vídeo ao vivo Azure IoT Edge
description: Este tutorial mostra-lhe como modificar e construir os módulos de gateway de vídeo ao vivo que o modelo de aplicação de deteção de objetos e movimentos usa.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: cbff2e774fae1f71624aa3cdbd83bfb21e3a9586
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721468"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>Tutorial: Modificar e construir os módulos de gateway de vídeo ao vivo

Este tutorial mostra-lhe como modificar o código do módulo IoT Edge para os módulos de análise de vídeo ao vivo (LVA).

Os tutoriais anteriores usam imagens pré-construídas dos módulos.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste tutorial, você precisa:


> [!div class="checklist"]
> * [Node.js](https://nodejs.org/en/download/) v10 ou mais tarde
> * [Código de estúdio visual](https://code.visualstudio.com/Download) com extensão [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin) instalada
> * [Motor estivador](https://www.docker.com/products/docker-desktop)
> * Um [Registo de Contentores Azure](../../container-registry/index.yml) para hospedar as suas versões dos módulos.
> * Uma conta [da Azure Media Services.](../../media-services/index.yml) Se tiver completado os tutoriais anteriores, poderá reutilizar o que criou anteriormente.

## <a name="clone-the-repository"></a>Clonar o repositório

Se ainda não clonou o repositório, utilize o seguinte comando para cloná-lo para um local adequado na sua máquina local:

```cmd
git clone https://github.com/Azure/live-video-analytics
```

Abra a pasta local de repositório *de vídeo-análise ao vivo* com código VS.

## <a name="edit-the-deploymentamd64json-file"></a>Editar o deployment.amd64.jsno ficheiro

1. Se ainda não o fez, crie uma pasta chamada *ref-apps/lva-edge-iot-central-gateway/armazenamento* na cópia local do repositório **Lva-Gateway.** Esta pasta é ignorada pela Git de modo a evitar que verifique acidentalmente qualquer informação confidencial.
1. Copie o ficheiro *deployment.amd64.jsligado* da pasta *de configuração* para a pasta *de armazenamento.*
1. Em Código VS, abra o *armazenamento/deployment.amd64.jsem* arquivo.
1. Edite a `registryCredentials` secção para adicionar as suas credenciais de Registo de Contentores Azure.
1. Edite a secção do `LvaEdgeGatewayModule` módulo para adicionar o nome da sua imagem e o nome da sua conta AMS no `env:amsAccountName:value` .
1. Edite a secção do `lvaYolov3` módulo e adicione o nome da sua imagem.
1. Edite a secção do `lvaEdge` módulo e adicione o nome da sua imagem.
1. Consulte a [aplicação De análise de vídeo no Azure IoT Central](tutorial-video-analytics-create-app-yolo-v3.md) para obter mais informações sobre como completar a configuração.

## <a name="build-the-code"></a>Compilar o código

1. Antes de tentar construir o código pela primeira vez, utilize o terminal de código VS para executar o `npm install` comando. Este comando instala as embalagens necessárias e executa os scripts de configuração.

    > [!TIP]
    > Se puxar uma versão mais recente do repositório GitHub, elimine a pasta *node_modules* e volte a `npm install` funcionar.

1. Edite o *ficheiro ./setup/imageConfig.jspara* atualizar a imagem com base no nome do registo do seu contentor:

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. Utilize o terminal de código VS para executar o `docker login [your server].azurecr.io` comando. Utilize as mesmas credenciais que forneceu no manifesto de implantação dos módulos.

1. Utilize o terminal de código VS para executar o comando **de patch de versão npm.** Este script de construção implanta as imagens no registo do seu contentor. A saída na janela do terminal vs Código mostra-lhe se a construção for bem sucedida.

1. A versão da imagem **LvaEdgeGatewayModule** incrementa cada vez que a construção termina. Tem de utilizar esta versão no ficheiro manifesto de implantação.

## <a name="clean-up-resources"></a>Limpar os recursos

Se terminou a aplicação, pode remover todos os recursos que criou da seguinte forma:

1. Na aplicação IoT Central, navegue para a **página de aplicação** da sua candidatura na secção **Administração.** Em seguida, selecione **Eliminar**.
1. No portal Azure, elimine o grupo de recursos **Lva-rg.**

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre a análise de vídeo - modelo de aplicação de deteção de objetos e movimentos e os módulos LVA IoT Edge, o próximo passo sugerido é saber mais sobre:

> [!div class="nextstepaction"]
> [Criar soluções para o comércio com o Azure IoT Central](overview-iot-central-retail.md)