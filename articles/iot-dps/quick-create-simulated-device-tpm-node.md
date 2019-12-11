---
title: Início rápido-provisionar dispositivo TPM simulado para o Hub IoT do Azure usando node. js
description: Início rápido – criar e provisionar um dispositivo TPM simulado usando o SDK do dispositivo node. js para o serviço de provisionamento de dispositivos do Hub IoT do Azure (DPS). Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: b5c5aa59c520560ad8424994d78e3c68bb0dc57a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976643"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Início rápido: criar e provisionar um dispositivo TPM simulado usando o SDK do dispositivo node. js para o serviço de provisionamento de dispositivos no Hub IoT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Estes passos mostram como criar um dispositivo simulado no seu computador de desenvolvimento que executa o SO Windows, executa o simulador Windows TPM como o [Módulo de Segurança de Hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) do dispositivo e utiliza o código de exemplo para ligar esse dispositivo com o Serviço Aprovisionamento de Dispositivos e o seu hub IoT. 

Se não estiver familiarizado com o processo de aprovisionamento automático, reveja também [Conceitos de aprovisionamento automático](concepts-auto-provisioning.md). Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:
- [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
- [Inscrições Individuais](concepts-service.md#individual-enrollment): utilizadas para inscrever um dispositivo individual.

Este artigo irá demonstrar as inscrições individuais.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Certifique-se de que tem o [Node.js v4.0 ou superior](https://nodejs.org) instalado no seu computador.

1. Verifique se `git` está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 


## <a name="simulate-a-tpm-device"></a>Simular um dispositivo TPM

1. Abra uma linha de comandos ou o Git Bash. Clone o repositório de GitHub `azure-utpm-c`:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git --recursive
    ```

1. Navegue para a pasta raiz de GitHub e execute o simulador de [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview). O simulador escuta através de um socket nas portas 2321 e 2322. Não feche esta janela de comando; Você precisa manter este simulador em execução até o final deste guia de início rápido: 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. Crie uma nova pasta vazia chamada **registerdevice**. Na pasta **registerdevice**, crie um ficheiro package.json com o seguinte comando na sua linha de comandos. Certifique-se de que responde a todas as perguntas colocadas por `npm` ou aceite as predefinições se as considerar adequadas para o seu caso:
   
    ```cmd/sh
    npm init
    ```

1. Instale os seguintes pacotes precursores:

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > Existem alguns problemas conhecidos na instalação dos pacotes acima indicados. Para os resolver estes problemas, execute `npm install --global --production windows-build-tools` com uma linha de comandos no modo **Executar como administrador**, execute `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140` depois de substituir o caminho pela versão instalada e, em seguida, volte a executar os comandos de instalação acima indicados.
    >

1. Instale os seguintes pacotes que contêm os componentes utilizados durante o registo:

   - um cliente de segurança que funcione com TPM: `azure-iot-security-tpm`
   - um transporte para o dispositivo se ligar ao Serviço de Aprovisionamento de Dispositivos: `azure-iot-provisioning-device-http` ou `azure-iot-provisioning-device-amqp`
   - um cliente para utilizar o cliente de segurança e o transporte: `azure-iot-provisioning-device`

     Depois de o dispositivo ser registado, pode utilizar os pacotes do Cliente de Dispositivo de Hub IoT habituais para ligar o dispositivo com as credenciais fornecidas durante o registo. Irá precisar dos seguintes elementos:

   - o cliente de dispositivo: `azure-iot-device`
   - um transporte: `azure-iot-device-amqp`, `azure-iot-device-mqtt` ou `azure-iot-device-http`
   - o cliente de segurança que já instalou: `azure-iot-security-tpm`

     > [!NOTE]
     > Os exemplos abaixo utilizam os transportes `azure-iot-provisioning-device-http` e `azure-iot-device-mqtt`.
     > 

     Pode instalar todos estes pacotes em simultâneo com a execução do seguinte comando na sua linha de comandos na pasta **registerdevice**:

       ```cmd/sh
       npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
       ```

1. Com um editor de texto, crie um novo ficheiro **ExtractDevice.js** na pasta **registerdevice**.

1. Adicione as seguintes instruções `require` no início do ficheiro **ExtractDevice.js**:
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. Adicione a seguinte função para implementar o método:
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. Guarde e feche o ficheiro **ExtractDevice.js**. Execute o exemplo:

    ```cmd/sh
    node ExtractDevice.js
    ```

1. A janela saída exibe a **_chave de endosso_** e a **_ID de registro_** necessária para o registro do dispositivo. Anote estes valores. 


## <a name="create-a-device-entry"></a>Criar um entrada de dispositivo

1. Entre no portal do Azure, selecione o botão **todos os recursos** no menu esquerdo e abra o serviço de provisionamento de dispositivos.

1. No menu serviço de provisionamento de dispositivos, selecione **gerenciar registros**. Selecione a guia registros **individuais** e selecione o botão **adicionar registro individual** na parte superior. 

1. No painel **adicionar registro** , insira as seguintes informações:
   - Selecione **TPM** como o *Mecanismo* de atestado de identidades.
   - Insira a *ID de registro* e a *chave de endosso* do seu dispositivo TPM a partir dos valores que você anotou anteriormente.
   - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
   - Opcionalmente, pode fornecer as seguintes informações:
       - Insira uma *ID de dispositivo*exclusiva. Certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. Se você optar por não fornecer um, a ID de registro será usada para identificar o dispositivo.
       - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
   - Depois de concluído, pressione o botão **salvar** . 

     ![Introduza as informações de inscrição de dispositivos no painel do portal](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Após a instalação bem-sucedida, o *ID de Registo* do seu dispositivo aparece na lista, no separador *Inscrições Individuais*. 


## <a name="register-the-device"></a>Registar o dispositivo

1. Na portal do Azure, selecione a folha **visão geral** para o serviço de provisionamento de dispositivos e anote os valores do **_ponto de extremidade do dispositivo global_** e do **_escopo da ID_** .

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. Com um editor de texto, crie um novo ficheiro **RegisterDevice.js** na pasta **registerdevice**.

1. Adicione as seguintes instruções `require` no início do ficheiro **RegisterDevice.js**:
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > O **SDK de IoT do Azure para Node.js** suporta protocolos adicionais, como _AMQP_, _AMQP WS_ e _MQTT WS_.  Para obter mais exemplos, veja [SDK do Serviço de Aprovisionamento de Dispositivos para exemplos de Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples).
    > 

1. Adicione as variáveis **globalDeviceEndpoint** e **idScope**, e utilize-as para criar uma instância de **ProvisioningDeviceClient**. Substitua **{globalDeviceEndpoint}** e **{idScope}** pelos valores de **_Ponto Final do Dispositivo Global_** e **_Âmbito do ID_** do **Passo 1**:
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. Adicione a seguinte função para implementar o método no dispositivo:
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. Guarde e feche o ficheiro **RegisterDevice.js**. Execute o exemplo:

    ```cmd/sh
    node RegisterDevice.js
    ```

1. Repare nas mensagens que simulam o arranque e a ligação do dispositivo ao Serviço Aprovisionamento de Dispositivos para obter as informações do seu hub IoT. No provisionamento bem-sucedido do dispositivo simulado para o Hub IoT vinculado ao seu serviço de provisionamento, a ID do dispositivo aparece na folha **dispositivos IOT** do Hub. 

    ![O dispositivo é registado no hub IoT](./media/quick-create-simulated-device/hub-registration.png) 

    Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, veja [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT)


## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando e explorando o exemplo de cliente do dispositivo, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, use as etapas a seguir para excluir todos os recursos criados por este guia de início rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. Feche a janela do simulador TPM no seu computador.
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o serviço de provisionamento de dispositivos. Abra a folha **gerenciar registros** para seu serviço e, em seguida, selecione a guia registros **individuais** . Marque a caixa de seleção ao lado da *ID de registro* do dispositivo registrado neste guia de início rápido e pressione o botão **excluir** na parte superior do painel. 
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o Hub IOT. Abra a folha **dispositivos IOT** para o Hub, marque a caixa de seleção ao lado da *ID do dispositivo* que você registrou neste guia de início rápido e pressione o botão **excluir** na parte superior do painel.


## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um dispositivo simulado do TPM em seu computador e o provisionou no Hub IoT usando o serviço de provisionamento de dispositivos do Hub IoT. Para saber como registrar seu dispositivo TPM programaticamente, continue no início rápido para o registro programático de um dispositivo TPM. 

> [!div class="nextstepaction"]
> [Guia de início rápido do Azure-registrar dispositivo TPM no serviço de provisionamento de dispositivos no Hub IoT do Azure](quick-enroll-device-tpm-node.md)
