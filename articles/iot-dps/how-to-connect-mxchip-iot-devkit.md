---
title: Como utilizar o Serviço de Provisionamento automático de Dispositivos Azure IoT Hub para registar o MXChip IoT DevKit com IoT Hub [ Hub IoT ] Microsoft Docs
description: Como utilizar o fornecimento automático do Serviço de Fornecimento de Dispositivos Hub Azure IoT (DPS) para registar o MXChip IoT DevKit com ioT Hub.
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: f05e92f0452b1cfff23e2094354203fd7eaea48b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74975657"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Utilize o serviço de fornecimento automático de dispositivos Azure IoT Hub para registar o MXChip IoT DevKit com IoT Hub

Este artigo descreve como utilizar o fornecimento automático do Serviço de [Provisionamento](concepts-auto-provisioning.md)automático do Serviço de Fornecimento de Dispositivos Azure IoT Hub, para registar o MXChip IoT DevKit com o Hub Azure IoT. Neste tutorial, ficará a saber como:

* Configure o ponto final global do serviço de fornecimento de dispositivos num dispositivo.
* Utilize um segredo único do dispositivo (UDS) para gerar um certificado X.509.
* Inscreva um dispositivo individual.
* Verifique se o dispositivo está registado.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é uma placa compatível com Arduino com periféricos e sensores ricos. Pode desenvolver-se para ele utilizando a bancada de trabalho do [dispositivo Azure IoT](https://aka.ms/iot-workbench) ou o pacote de extensão [Azure IoT Tools](https://aka.ms/azure-iot-tools) no Código do Estúdio Visual. O DevKit vem com um catálogo de [projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) em crescimento para orientar o seu protótipo de soluções Internet of Things (IoT) que aproveitam os serviços azure.

## <a name="before-you-begin"></a>Antes de começar

Para completar os passos neste tutorial, faça primeiro as seguintes tarefas:

* Configure o Wi-Fi do seu DevKit e prepare o seu ambiente de desenvolvimento seguindo os passos da secção "Prepare o ambiente de desenvolvimento" no [Connect IoT DevKit AZ3166 até azure IoT Hub na nuvem](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment).
* Atualize para o firmware mais recente (1.3.0 ou mais tarde) com o tutorial de [firmware Update DevKit.](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)
* Crie e ligue um Hub IoT com uma instância de serviço de provisionamento de dispositivos seguindo os passos em [Configurar o Serviço de Provisionamento de Dispositivos IoT Hub com o portal Azure](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Projeto de amostra aberta

1. Certifique-se de que o seu IoT DevKit não está **ligado** ao computador. Inicie primeiro o Código VS e, em seguida, ligue o DevKit ao seu computador.

1. Clique `F1` para abrir a paleta de comando, digite e selecione Bancada de Trabalho do **Dispositivo Azure IoT: Exemplos abertos...**. Em seguida, selecione **IoT DevKit** como placa.

1. Na página IoT Workbench Exemplos, encontre o **Registo do Dispositivo com DPS** e clique em Amostra **Aberta**. Em seguida, seleciona o caminho predefinido para descarregar o código da amostra.
    ![Amostra aberta](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Guarde um segredo único do dispositivo no armazenamento de segurança do dispositivo

O fornecimento automático pode ser configurado num dispositivo com base no mecanismo de [atestado](concepts-security.md#attestation-mechanism)do dispositivo . O MXChip IoT DevKit utiliza o Motor de [Composição](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) de Identidade do Dispositivo do [Grupo de Computação Fidedigno](https://trustedcomputinggroup.org). Um **Unique Device Secret** (UDS) guardado num chip de segurança STSAFE[(STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) no DevKit é utilizado para gerar o [certificado X.509](concepts-security.md#x509-certificates)único do dispositivo. O certificado é utilizado posteriormente para o processo de inscrição no serviço de fornecimento de dispositivos e durante o registo em tempo de execução.

Um UDS típico é uma cadeia de 64 caracteres, como visto na seguinte amostra:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Para salvar um UDS no DevKit:

1. No Código VS, clique na barra de estado para selecionar a porta COM para o DevKit.
  ![Selecione porta COM](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. No DevKit, segure o **botão A**, pressione e liberte o botão **de reset** e, em seguida, desbloqueie o **botão A**. O seu DevKit entra no modo de configuração.

1. Clique `F1` para abrir a paleta de comandos, digite e selecione bancada de trabalho do **dispositivo Azure IoT: Configurar definições de dispositivo... > Config Unique Device String (UDS)**.
  ![Configurar UDS](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Note a cadeia uDS gerada. Vai precisar dele para gerar o certificado X.509. Em `Enter`seguida, prima .
  ![Copiar UDs](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Confirme a partir da notificação que a UDS foi configurada com sucesso no STSAFE.
  ![Configurar o sucesso dos UDS](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Em alternativa, pode configurar UDS através de uma porta de série utilizando utilitários como o Putty. Siga o [modo de configuração Use](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) para o fazer.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Atualizar o ponto final do dispositivo global e o âmbito de identificação

No código do dispositivo, é necessário especificar o [ponto final](/azure/iot-dps/concepts-service#device-provisioning-endpoint) de fornecimento do Dispositivo e o âmbito de identificação para garantir o isolamento do arrendatário.

1. No portal Azure, selecione o painel de **visão geral** do seu serviço de fornecimento de dispositivos e note os valores de ponto final do **dispositivo Global** e do âmbito de **identificação.**
  ![Dispositivo provisioning serviço global endpoint e id scope](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Open **DevKitDPS.ino**. Encontre e `[Global Device Endpoint]` `[ID Scope]` substitua e com os valores que acabou de notar.
  ![Ponto final do serviço de provisionamento de dispositivos](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Preencha `registrationId` a variável no código. É permitida apenas uma combinação alfanumérica, minúscula e hífen com um máximo de 128 caracteres. Também notou abaixo o valor.
  ![ID de registo](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Clique, escreva `F1`e selecione bancada de trabalho do **dispositivo Azure IoT: Carregar código do dispositivo**. Começa a compilar e enviar o código para o DevKit.
  ![Upload do dispositivo](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Gerar certificado X.509

O mecanismo de [atestado](/azure/iot-dps/concepts-device#attestation-mechanism) utilizado por esta amostra é certificado X.509. Tens de usar uma utilidade para a gerar.

1. No Código VS, clique em escrever, `F1`digite e selecione Open New **Terminal** para abrir a janela do terminal.

1. Executar `dps_cert_gen.exe` `tool` na pasta.

1. Especifique a localização `..\.build\DevKitDPS`do ficheiro binário compilado como . Em seguida, colhe o **UDS** e **o registo Id** que acabou de notar. 
  ![Gerar X.509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. Um `.pem` certificado X.509 gera na mesma pasta.
  ![Ficheiro X.509](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Criar uma entrada de inscrição de dispositivos

1. No portal Azure, abra o seu Serviço de Fornecimento de Dispositivos, navegue para gerir a secção de matrículas e clique em **Adicionar inscrição individual**.
  ![Adicionar inscrição individual](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Clique no ícone do ficheiro ao lado do **Certificado Primário .pem ou .cer file** para carregar o `.pem` ficheiro gerado.
  ![Upload .pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Verifique se o DevKit está registado no Azure IoT Hub

Prima o botão **Reset** no seu DevKit. Devia ver **o DPS Ligado!** no ecrã DevKit. Após o reboot do dispositivo, realizam-se as seguintes ações:

1. O dispositivo envia um pedido de registo ao Serviço Aprovisionamento de Dispositivos.
1. O serviço de fornecimento de dispositivos envia de volta um desafio de registo ao qual o seu dispositivo responde.
1. No registo bem sucedido, o serviço de fornecimento de dispositivos envia o IoT Hub URI, o ID do dispositivo e a chave encriptada de volta ao dispositivo.
1. A aplicação do cliente IoT Hub no dispositivo liga-se ao seu hub.
1. Na ligação bem sucedida ao hub, vê-se o dispositivo aparecer no IoT Hub Device Explorer.
  ![Dispositivo registado](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problemas e feedback

Se encontrar problemas, consulte as [FAQs](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)Iot DevKit, ou contacte os seguintes canais de apoio:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a inscrever um dispositivo de forma segura no Serviço de Provisionamento de Dispositivos utilizando o Motor de Composição de Identidade do Dispositivo, para que o dispositivo possa registar-se automaticamente com o Hub Azure IoT. 

Em resumo, aprendeu a:

> [!div class="checklist"]
> * Configure o ponto final global do serviço de fornecimento de dispositivos num dispositivo.
> * Use um segredo de dispositivo único para gerar um certificado X.509.
> * Inscreva um dispositivo individual.
> * Verifique se o dispositivo está registado.

Aprenda a [criar e fornecer um dispositivo simulado.](./quick-create-simulated-device.md)

