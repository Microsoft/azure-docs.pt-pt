---
title: Quickstart - Utilize a chave simétrica para forrar um dispositivo ao Azure IoT Hub utilizando Node.js
description: Neste arranque rápido, utilizará o Azure IoT SDK para Node.js com o Serviço de Provisionamento de Dispositivos (DPS) para providenciar um dispositivo chave simétrico a um hub IoT
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: 78005ba46952bcf05b19c7627feecb1ec30ac651
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92429376"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-nodejs"></a>Quickstart: Disposição de um dispositivo chave simétrico utilizando Node.js

Neste arranque rápido, você vai aprender a providenciar uma máquina de desenvolvimento Windows como um dispositivo para um hub IoT usando Node.js. Este dispositivo utilizará uma chave simétrica e uma inscrição individual para autenticar com uma instância do Serviço de Provisionamento de Dispositivos (DPS) para ser atribuído a um hub IoT. O código de amostra do [Azure IoT SDK para Node.js](https://github.com/Azure/azure-iot-sdk-node.git) será utilizado para o fornecimento do dispositivo. 

Embora este artigo demonstre o provisionamento com uma inscrição individual, você também pode usar grupos de inscrição. Há algumas diferenças na utilização de grupos de inscrição. Por exemplo, deve utilizar uma chave de dispositivo derivada com um ID de registo único para o dispositivo. [Os dispositivos de fornecimento com teclas simétricas](how-to-legacy-device-symm-key.md) fornecem um exemplo de grupo de inscrição. Para obter mais informações sobre os grupos de inscrição, consulte [as inscrições do Grupo para o Atestado chave simétrica](concepts-symmetric-key-attestation.md#group-enrollments).

Se não estiver familiarizado com o processo de provisão automática, reveja a visão geral do [provisionamento.](about-iot-dps.md#provisioning-process) 

Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar este início rápido. Este guia de início rápido exige que já tenha criado a instância do Serviço de Aprovisionamento de Dispositivos.

Este artigo é orientado para uma estação de trabalho baseada no Windows. No entanto, pode efetuar os procedimentos no Linux. Para um exemplo linux, consulte [Provisão para multitenência](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

- Familiarizado com [conceitos de provisionamento.](about-iot-dps.md#provisioning-process)
- Conclusão do Serviço de Provisionamento de [Dispositivos IoT Hub com o portal Azure](./quick-setup-auto-provision.md).
- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Node.js v4.0+](https://nodejs.org).
- [Git.](https://git-scm.com/download/)


## <a name="create-a-device-enrollment"></a>Criar uma inscrição de dispositivo

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione o botão **Todos os recursos** no menu esquerdo e abra a sua instância de Serviço de Provisionamento de Dispositivos (DPS).

2. Selecione o **separador Descodusagens** de Gestão e, em seguida, selecione o botão **de inscrição individual Adicionar** no topo. 

3. No painel **'Adicionar Inscrição',** introduza as seguintes informações e prima o botão **Guardar.**

   - **Mecanismo:** selecione **Chave Simétrica** como o *Mecanismo* de atestado de identidades.

   - **Teclas de geração automática**: Verifique esta caixa.

   - **ID de Registo**: introduza um ID de registo para identificar a inscrição. Utilize apenas carateres alfanuméricos em minúsculas e travessões ("-"). Por exemplo, **symm-key-nodejs-device-01**.

   - **ID do Dispositivo do Hub IoT:** introduza o identificador de um dispositivo. Por exemplo, **nodejs-device-01**.

     ![Adicionar inscrição individual para o atestado de chave simétrica no portal](./media/quick-create-device-symmetric-key-node/create-individual-enrollment-node.png)

4. Uma vez guardada a sua inscrição, a **Chave Primária** e a **Chave Secundária** serão geradas e adicionadas à inscrição. A inscrição do seu dispositivo chave simétrico aparece como **symm-key-nodejs-device-01** sob a coluna *de Identificação de Registo* no separador *Matrículas Individuais.* 

5. Abra a inscrição e copie o valor da **Chave Primária** gerada. Utilizará este valor chave e o **ID de registo** mais tarde quando adicionar variáveis ambientais para utilização com o código de amostra de provisionamento do dispositivo.



## <a name="prepare-the-nodejs-environment"></a>Prepare o ambiente Node.js 

1. Abra um ambiente de linha de comando Git CMD ou Git Bash. Clone o [Azure IoT SDK para Node.js](https://github.com/Azure/azure-iot-sdk-node.git) repositório GitHub usando o seguinte comando:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```


<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Preparar o código de provisionamento do dispositivo

Nesta secção, irá adicionar as seguintes quatro variáveis ambientais que serão utilizadas como parâmetros para o dispositivo que fornece o código de amostra para o fornecimento do seu dispositivo chave simétrica. 

* `PROVISIONING_HOST`
* `PROVISIONING_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PROVISIONING_SYMMETRIC_KEY`

O código de provisionamento entrará em contacto com a instância DPS com base nestas variáveis para autenticar o seu dispositivo. O dispositivo será então atribuído a um hub IoT já ligado à instância DPS com base na configuração de inscrição individual. Uma vez previsto, o código de amostra enviará alguma telemetria de teste para o hub IoT.

1. No [portal Azure](https://portal.azure.com), no menu serviço de fornecimento de dispositivos, selecione **Overview** e copie o seu _Ponto final_ de serviço e _ID Scope_. Utilizará estes valores para as `PROVISIONING_HOST` variáveis e `PROVISIONING_IDSCOPE` ambientais.

    ![Informações de serviço](./media/quick-create-device-symmetric-key-node/extract-dps-endpoints.png)

2. Abra um pedido de comando para executar comandos Node.js e navegue para o seguinte *diretório de provisionamento/dispositivo/amostras.*

    ```cmd
    cd azure-iot-sdk-node/provisioning/device/samples
    ```

3. Na pasta *de provisionamento/dispositivo/amostras,* abra *register_symkey.js* e reveja o código. 

    Note que o código de amostra define uma carga personalizada...

    ```nodejs
    provisioningClient.setProvisioningPayload({a: 'b'});
    ```

    Este código não é necessário com este arranque rápido. Este código é um exemplo de definição de uma carga útil personalizada se quiser utilizar uma função de atribuição personalizada para atribuir o seu dispositivo a um Hub IoT. Para obter mais informações, consulte [Tutorial: Utilize políticas de atribuição personalizadas.](tutorial-custom-allocation-policies.md)

    O `provisioningClient.register()` método tenta o registo do seu dispositivo.

    Não serão necessárias alterações ao código de amostra para registar o seu dispositivo.

4. No seu pedido de comando, adicione as variáveis ambientais para o hospedeiro de provisionamento, ID Scope, ID de registo e chave simétrica primária que copiou da inscrição individual na secção anterior.  

    Os seguintes comandos são exemplos para mostrar sintaxe de comando. Certifique-se de que utiliza os valores corretos.

    ```console
    set PROVISIONING_HOST=test-dps-docs.azure-devices-provisioning.net
    ```

    ```console
    set PROVISIONING_IDSCOPE=0ne00000A0A
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-nodejs-device-01
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```


4. Construa e execute o código de amostra utilizando os seguintes comandos.

    ```console
    npm install
    ```

    ```console
    node register_symkey.js
    ```

5. A saída esperada deve ser semelhante à seguinte que mostra o hub IoT ligado ao que o dispositivo foi atribuído com base nas definições individuais de inscrição. Uma cadeia "Hello World" é enviada para o centro como uma mensagem de teste:

    ```output
    D:\Docs\test\azure-iot-sdk-node\provisioning\device\samples>node register_symkey.js
    registration succeeded
    assigned hub=docs-test-iot-hub.azure-devices.net
    deviceId=nodejs-device-01
    payload=undefined
    Client connected
    send status: MessageEnqueued    
    ```
    
6. No portal Azure, navegue até ao hub IoT ligado ao seu serviço de fornecimento e abra a lâmina dos **dispositivos IoT.** Depois de ter previsto com sucesso o dispositivo chave simétrico no hub, o ID do dispositivo é mostrado com *status* conforme **ativado**. Pode ser necessário premir o botão **Refresh** na parte superior se já tiver aberto a lâmina antes de executar o código de amostra do dispositivo. 

    ![O dispositivo é registado no hub IoT](./media/quick-create-device-symmetric-key-node/hub-registration-node.png) 

> [!NOTE]
> Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, consulte [Compreender e utilizar gémeos do dispositivo no IoT Hub.](../iot-hub/iot-hub-devguide-device-twins.md)
>


## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender continuar a trabalhar e explorar a amostra do cliente do dispositivo, não limpe os recursos criados neste quickstart. Se não pretender continuar, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido.

1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu serviço de Provisionamento de Dispositivos. Abrir **As Inscrições** para o seu serviço e, em seguida, selecionar o separador **Inscrições Individuais.** Selecione a caixa de verificação ao lado do *ID de registo* do dispositivo que inscreveu neste arranque rápido e prima o botão **Eliminar** na parte superior do painel. 
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu hub IoT. Abra **os dispositivos IoT** para o seu hub, selecione a caixa de verificação ao lado do *ID* do dispositivo que registou neste arranque rápido e, em seguida, pressione o botão **Eliminar** na parte superior do painel.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, adquirou um dispositivo de chave simétrica baseado no Windows para o seu hub IoT utilizando o Serviço de Provisionamento de Dispositivos IoT Hub. Para saber como providenciar dispositivos de certificado X.509 utilizando Node.js, continue com o arranque rápido abaixo para dispositivos X.509. 

> [!div class="nextstepaction"]
> [Azure quickstart - Provisão dispositivos X.509 usando DPS e Node.js](quick-create-simulated-device-x509-node.md)
