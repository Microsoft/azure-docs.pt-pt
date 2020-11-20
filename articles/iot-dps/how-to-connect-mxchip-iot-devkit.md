---
title: Como utilizar o Serviço de Provisionamento de Dispositivos Azure IoT Hub para registar o MXChip IoT DevKit com IoT Hub ! Microsoft Docs
description: Como utilizar o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS) para registar o MXChip IoT DevKit com ioT Hub.
author: wesmc7777
ms.author: wesmc
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: d6b6649d03da319171b24baa24983972bf270679
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954550"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Utilize o Serviço de Provisionamento de Dispositivos Azure IoT Hub para registar o MXChip IoT DevKit com ioT Hub

Este artigo descreve como usar o Serviço de Provisionamento de Dispositivos Azure IoT Hub para [o fornecimento](about-iot-dps.md#provisioning-process) do MXChip IoT DevKit a um Hub IoT Azure. Neste tutorial, vai aprender a:

* Configure o ponto final global do serviço de provisionamento de dispositivos num dispositivo.
* Utilize um dispositivo secreto único (UDS) para gerar um certificado X.509.
* Inscreva um dispositivo individual.
* Verifique se o dispositivo está registado.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é um tabuleiro compatível com Arduino com periféricos e sensores ricos. Você pode desenvolver para ele usando [a bancada de trabalho do dispositivo Azure IoT](https://aka.ms/iot-workbench) ou o pacote de extensão [Azure IoT Tools](https://aka.ms/azure-iot-tools) em Visual Studio Code. O DevKit vem com um catálogo de [projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) em crescimento para orientar as suas soluções de protótipo Internet of Things (IoT) que tiram partido dos serviços Azure.

## <a name="before-you-begin"></a>Before you begin

Para completar os passos neste tutorial, primeiro faça as seguintes tarefas:

* Configure o Wi-Fi do seu DevKit e prepare o seu ambiente de desenvolvimento seguindo os passos da secção "Prepare o ambiente de desenvolvimento" no [Connect IoT DevKit AZ3166 para Azure IoT Hub na nuvem.](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment)
* Upgrade para o firmware mais recente (1.3.0 ou mais tarde) com o tutorial [de firmware Update DevKit.](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)
* Criar e ligar um Hub IoT com uma instância de serviço de provisionamento de dispositivos seguindo os passos na [Configuração do Serviço de Provisionamento de Dispositivos IoT Hub com o portal Azure](./quick-setup-auto-provision.md).

## <a name="open-sample-project"></a>Projeto de amostra aberta

1. Certifique-se de que o seu IoT DevKit não está **ligado** ao computador. Inicie primeiro o Código VS e, em seguida, ligue o DevKit ao seu computador.

1. Clique `F1` para abrir a paleta de comando, escreva e selecione **Azure IoT Device Workbench: Open Examples...**. Em seguida, selecione **IoT DevKit** como placa.

1. Na página IoT Workbench Exemplos, encontre **o Registo do Dispositivo com DPS** e clique em Abrir **Amostra**. Em seguida, seleciona o caminho predefinido para descarregar o código de amostra.
    ![Amostra aberta](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Guarde um segredo de dispositivo único no armazenamento de segurança do dispositivo

O provisionamento automático pode ser configurado num dispositivo baseado no mecanismo de [atestação](concepts-service.md#attestation-mechanism)do dispositivo . O MXChip IoT DevKit utiliza o motor de [composição](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) de identidade do dispositivo do [Grupo de Computação Fidedigna](https://trustedcomputinggroup.org). Um **exclusivo segredo de dispositivo** (UDS) guardado num chip de segurança STSAFE [(STSAFE-A100)](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)no DevKit é utilizado para gerar o certificado [X.509](concepts-x509-attestation.md)único do dispositivo . O certificado é usado posteriormente para o processo de inscrição no serviço de Provisionamento de Dispositivos, e durante o registo no tempo de execução.

Um UDS típico é uma cadeia de 64 caracteres, como visto na seguinte amostra:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Para guardar uma UDS no DevKit:

1. Em Código VS, clique na barra de estado para selecionar a porta COM para o DevKit.
  ![Selecione porta COM](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. No DevKit, mantenha premido **o botão A**, pressione e solte o botão **de reset** e, em seguida, desbloqueie **o botão A**. O seu DevKit entra no modo de configuração.

1. Clique `F1` para abrir a paleta de comando, digite e selecione **Azure IoT Device Workbench: Configurações do dispositivo de configuração... > Config Unique Device String (UDS)**.
  ![Configure UDS](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Note a cadeia UDS gerada. Vai precisar dele para gerar o certificado X.509. Em seguida, prima `Enter` .
  ![UDS de cópia](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Confirme a partir da notificação que a UDS foi configurada com sucesso no STSAFE.
  ![Configurar o sucesso da UDS](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Em alternativa, pode configurar o UDS através da porta de série utilizando utilitários como o Putty. Siga [o modo de configuração utilizar](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) para o fazer.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Atualizar o ponto final global do dispositivo e o âmbito de id

No código do dispositivo, é necessário especificar o [dispositivo que a provisiona](./concepts-service.md#device-provisioning-endpoint) o ponto final e o âmbito de identificação para garantir o isolamento do inquilino.

1. No portal Azure, selecione o painel de **visão geral** do seu serviço de provisionamento de dispositivos e anote os valores **do ponto final** do dispositivo Global e do **ID Scope.**
  ![Serviço de Fornecimento de Dispositivos Global Endpoint e ID Scope](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Open **DevKitDPS.ino**. Encontre e substitua `[Global Device Endpoint]` e `[ID Scope]` pelos valores que acabou de anotado.
  ![Ponto final do serviço de fornecimento de dispositivos](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Preencha a `registrationId` variável no código. Apenas é permitida a combinação alfanumérica, minúscula e hífen com um máximo de 128 caracteres. Também anotado o valor.
  ![ID de inscrição](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Clique, `F1` escreva e selecione **Azure IoT Device Workbench: Upload Device Code**. Começa a compilar e a enviar o código para o DevKit.
  ![Upload de dispositivos](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Gerar certificado X.509

O [mecanismo de atestado](./concepts-service.md#attestation-mechanism) utilizado por esta amostra é o certificado X.509. É preciso usar uma utilidade para o gerar.

1. No Código VS, `F1` clique, escreva e selecione **Abrir Novo Terminal** para abrir a janela do terminal.

1. Corra `dps_cert_gen.exe` em `tool` pasta.

1. Especifique a localização do ficheiro binário compilado como `..\.build\DevKitDPS` . Em seguida, cole a **UDS** e **o registrationId** que acabou de anotado. 
  ![Gerar X.509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. Um `.pem` certificado X.509 gera na mesma pasta.
  ![Ficheiro X.509](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Criar uma entrada de inscrição de dispositivos

1. No portal Azure, abra o seu Serviço de Prestação de Dispositivos, navegue para Gerir a secção de matrículas e clique em **Adicionar inscrição individual**.
  ![Adicionar inscrição individual](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Clique no ícone de ficheiro ao lado **do Certificado Primário .pem ou .cer ficheiro** para carregar o ficheiro `.pem` gerado.
  ![Upload .pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Verifique se o DevKit está registado no Azure IoT Hub

Prima o botão **Reset** no seu DevKit. Devia ver **o DPS Ligado!** no ecrã DevKit. Após o reinício do dispositivo, ocorrem as seguintes ações:

1. O dispositivo envia um pedido de registo ao Serviço Aprovisionamento de Dispositivos.
1. O serviço de Provisionamento de Dispositivos envia de volta um desafio de registo ao qual o seu dispositivo responde.
1. No registo bem sucedido, o serviço de Provisionamento de Dispositivos envia o IoT Hub URI, o ID do dispositivo e a chave encriptada de volta ao dispositivo.
1. A aplicação do cliente IoT Hub no dispositivo conecta-se ao seu hub.
1. Na ligação bem sucedida ao hub, vê-se o dispositivo a aparecer no IoT Hub Device Explorer.
  ![Dispositivo registado](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problemas e feedback

Se encontrar problemas, consulte as FAQs Iot DevKit ou contacte os [seguintes canais](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)de apoio:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a inscrever um dispositivo de forma segura no Serviço de Provisionamento de Dispositivos utilizando o Motor de Composição de Identidade do Dispositivo, para que o dispositivo possa registar-se automaticamente no Azure IoT Hub. 

Em resumo, aprendeu a:

> [!div class="checklist"]
> * Configure o ponto final global do serviço de provisionamento de dispositivos num dispositivo.
> * Utilize um dispositivo secreto único para gerar um certificado X.509.
> * Inscreva um dispositivo individual.
> * Verifique se o dispositivo está registado.

Saiba como [criar e providenciar um dispositivo simulado.](./quick-create-simulated-device.md)