---
title: Coloque o módulo GPU no seu dispositivo GPU Azure Stack Edge Pro| Microsoft Docs
description: Descreve como ativar a computação e tornar o seu dispositivo Azure Stack Edge Pro pronto através da UI local.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4142542c9f17ae464a996df310c50d73d8711d8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438204"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-pro-gpu-device"></a>Implementar um módulo IoT ativado por GPO em dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Este artigo descreve como implementar um módulo IoT Edge ativado por GPU no seu dispositivo GPU Azure Stack Edge Pro. 

Neste artigo, vai aprender a:
  - Prepare o Azure Stack Edge Pro para executar um módulo GPU.
  - Descarregue e instale o código de amostra de um repositório de Git.
  - Construa a solução e gere um manifesto de implantação.
  - Implemente a solução para o dispositivo Azure Stack Edge Pro.
  - Monitorize a saída do módulo.


## <a name="about-sample-module"></a>Sobre o módulo de amostra

O módulo de amostra gpu neste artigo inclui pyTorch e tensorFlow código de amostra de benchmarking para CPU contra GPU.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

- Tem acesso a um dispositivo GPU ativado por 1 nó Azure Stack Edge Pro. Este dispositivo está ativado com um recurso no Azure. Ver [Ativar o dispositivo](azure-stack-edge-gpu-deploy-activate.md).
- Configuraste o cálculo deste dispositivo. Siga os passos em [Tutorial: Conigure compute no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md).
- Registo de Contentores Azure (ACR). Vá à lâmina das **teclas Access** e tome nota do servidor de login ACR, nome de utilizador e senha. Para mais informações, aceda ao [Quickstart: Crie um registo de contentores privados utilizando o portal Azure](../container-registry/container-registry-get-started-portal.md#create-a-container-registry).
- Os seguintes recursos de desenvolvimento num cliente Windows:
    - [Azure CLI 2.0 ou mais tarde](https://aka.ms/installazurecliwindows)
    - [Docker CE.](https://store.docker.com/editions/community/docker-ce-desktop-windows) Poderá ter de criar uma conta para descarregar e instalar o software.
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Extensão Azure IoT Edge para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).    
    - [Extensão python para código de estúdio visual](https://marketplace.visualstudio.com/items?itemName=ms-python.python)    
    - [Python 3](https://www.python.org/)    
    - Pip para instalar pacotes Python (tipicamente incluídos com a sua instalação Python)

## <a name="get-the-sample-code"></a>Obter o código de exemplo

1. Aceda aos [padrões de arestas inteligentes Azure em amostras Azure](https://github.com/azure-samples/azure-intelligent-edge-patterns). Clone ou descarregue o ficheiro postal para código. 

    ![Descarregue o ficheiro zip](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    Extrair os ficheiros do fecho. Também pode clonar as amostras.

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>Construir e implantar módulo

1. Abra a pasta **GpuReferenceModules** no Código do Estúdio Visual.

    ![GPUReferenceSadulos abertos em código VS](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. Abra a *deployment.template.js* e identifique os parâmetros que refere para o registo do contentor. No seguinte ficheiro, são utilizados CONTAINER_REGISTRY_USERNAME, CONTAINER_REGISTRY_PASSWORD e CONTAINER_REGISTRY_NAME.

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. Criar um novo ficheiro. Preencha os valores dos parâmetros de registo do seu contentor (utilize os identificados no passo anterior) da seguinte forma: 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    Abaixo é apresentada uma *amostra.env:*
    
    ![Criar e guardar ficheiro .env](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. Guarde o ficheiro como *.env* na pasta **SampleSolution.**

5. Para iniciar seducker, insira o seguinte comando no terminal integrado do Código do Estúdio Visual. 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    Aceda à secção de chaves de **acesso** do seu registo de contentores no portal Azure. Copie e use o nome do registo, palavra-passe e servidor de login.

    ![Chaves de acesso no registo do seu contentor](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    Depois de fornecidas as credenciais, o sinal é bem sucedido.

    ![Sinal de sucesso](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. Empurre a sua imagem para o registo do seu contentor Azure. No VS Code Explorer, selecione e clique com odeployment.template.jsno ficheiro e, **em** seguida, selecione **a solução Build and Push IoT Edge**. 

    ![Construa e empurre a solução IoT Edge](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    Se a extensão Python e Python não forem instaladas, estas serão instaladas quando construir e empurrar a solução. No entanto, isto resultaria em tempos de construção mais longos. 

    Uma vez concluído este passo, vê o módulo no registo do seu contentor.

    ![Módulo no registo de contentores](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. Para criar um manifesto de implantação, clique com odeployment.template.jse, **em** seguida, selecione **Generate IoT Edge Deployment Manifest**. 

    ![Gerar manifesto de implantação IoT Edge](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    A notificação informa-o sobre o caminho pelo qual o manifesto de implantação foi gerado. O manifesto é o `deployment.amd64.json` ficheiro gerado na pasta **config.** 

8. Selecione a **deployment.amd64.jsno** ficheiro na pasta **config** e, em seguida, escolha **Criar Implementação para Dispositivo Único**. Não utilize a **deployment.template.jsno** ficheiro. 

    ![Criar implementação para dispositivo único](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    Na janela **saída,** deve ver uma mensagem que a implementação conseguiu.

    ![Implementação conseguiu na saída](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>Monitorize o módulo  

1. Na paleta de comandos do VS Code, execute o **Hub IoT do Azure: Selecionar Hub IoT**.

2. Escolha a subscrição e o hub IoT que contém o dispositivo do IoT Edge que pretende configurar. Neste caso, selecione a subscrição utilizada para implementar o dispositivo Azure Stack Edge Pro e selecione o dispositivo IoT Edge criado para o seu dispositivo Azure Stack Edge Pro. Isto ocorre quando configura o cálculo através do portal Azure nos passos anteriores.

3. No explorador de código VS, expanda a secção Azure IoT Hub. Em **Dispositivos**, deve ver o dispositivo IoT Edge correspondente ao seu dispositivo Azure Stack Edge Pro. 

    1. Selecione este dispositivo, clique à direita e selecione **Start Monitoring Built-in Event Endpoint**.
  
        ![Iniciar a monitorização](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Vá a **Dispositivos > Módulos** e deverá ver o seu **módulo GPU** a funcionar.

        ![Módulo no Hub IoT](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. O terminal vs Code também deve mostrar os eventos IoT Hub como a saída de monitorização do seu dispositivo Azure Stack Edge Pro.

        ![Produção de monitorização](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Pode ver que o tempo necessário para executar o mesmo conjunto de operações (5000 iterações de transformação de forma) por GPU é muito menor do que é para a CPU.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como configurar a [GPU para utilizar um módulo.](azure-stack-edge-j-series-configure-gpu-modules.md)
