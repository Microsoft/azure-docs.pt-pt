---
title: Como utilizar o aprovisionamento automático do serviço de aprovisionamento de dispositivos do Azure IoT Hub para registar o MXChip IoT DevKit com o IoT Hub | Documentos da Microsoft
description: Como utilizar o aprovisionamento automático do serviço de aprovisionamento de dispositivos do Azure IoT Hub para registar o MXChip IoT DevKit com o IoT Hub.
author: liydu
ms.author: liydu
ms.date: 12/18/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: a7131a66e9d722265282ac98b67b52db8de086e2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551905"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Utilizar o aprovisionamento automático do serviço de aprovisionamento de dispositivos do Azure IoT Hub para registar o MXChip IoT DevKit com o IoT Hub

Este artigo descreve como utilizar o serviço de aprovisionamento de dispositivos do Azure IoT Hub [aprovisionamento automático](concepts-auto-provisioning.md), para registar o MXChip IoT DevKit com o IoT Hub do Azure. Neste tutorial, ficará a saber como:

* Configure o ponto final global do serviço aprovisionamento de dispositivos num dispositivo.
* Utilize um segredo de dispositivo exclusivo (UDS) para gerar um certificado X.509.
* Inscreva um dispositivo individual.
* Certifique-se de que o dispositivo está registado.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é um quadro de compatível com Arduino tudo-em-um com periféricos avançados e sensores. Pode desenvolver para ele usando [Bancada de trabalho de dispositivo do Azure IoT](https://aka.ms/iot-workbench) ou [ferramentas do Azure IoT](https://aka.ms/azure-iot-tools) pacote de extensão no Visual Studio Code. O DevKit vem com um crescimento [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) para orientar suas soluções de Internet das coisas (IoT) de protótipo que tiram partido dos serviços do Azure.

## <a name="before-you-begin"></a>Antes de começar

Para concluir os passos neste tutorial, primeiro faça as seguintes tarefas:

* Preparar seu DevKit ao seguir os passos em [ligar IoT DevKit AZ3166 hub do IoT do Azure na cloud](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Atualizar para o firmware mais recente (1.3.0 ou posterior) com o [DevKit de atualização de firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) tutorial.
* Criar e ligar um Hub de IoT com uma instância do serviço aprovisionamento de dispositivos ao seguir os passos em [configurar o serviço de aprovisionamento das dispositivo IoT Hub no portal do Azure](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Projeto de exemplo aberto

1. Certificar-se de que é o IoT DevKit **não ligado** para o seu computador. Inicie o VS Code primeiro e, em seguida, ligue o DevKit para o seu computador.

1. Clique em `F1` para abrir a paleta de comandos, escreva e selecione **Bancada de trabalho de dispositivo do Azure IoT: Abrir os exemplos...** . Em seguida, selecione **IoT DevKit** como quadro.

1. Na página de exemplos da bancada de trabalho de IoT, encontrar **registo de dispositivos no DPS** e clique em **exemplo aberto**. Em seguida, seleciona o caminho predefinido para transferir o código de exemplo.
    ![Exemplo aberto](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Guardar um segredo de dispositivo exclusivo no armazenamento de segurança do dispositivo

Aprovisionamento automático pode ser configurado num dispositivo com base do dispositivo [mecanismo de atestado](concepts-security.md#attestation-mechanism). O MXChip IoT DevKit utiliza a [mecanismo de composição de identidade do dispositivo](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) da [Trusted Computing Group](https://trustedcomputinggroup.org). R **segredo de dispositivo exclusivo** (UDS) salvos num chip de segurança STSAFE ([STSAFE A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) no DevKit é usado para gerar o dispositivo do exclusivo [certificado X.509](concepts-security.md#x509-certificates). O certificado é utilizado mais tarde para o processo de inscrição no serviço aprovisionamento de dispositivos e durante o registo em tempo de execução.

Um típico domínios de Atualização é uma cadeia de caracteres de 64, como visto no exemplo a seguir:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Para guardar um domínios de Atualização no DevKit:

1. No VS Code, clique na barra de status para selecionar a porta COM para o DevKit.
  ![Selecione porta de COM](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. No DevKit, mantenha premida **botão A**, push e de versão do **repor** botão e, em seguida, versão **botão A**. Sua DevKit entra em modo de configuração.

1. Clique em `F1` para abrir a paleta de comandos, escreva e selecione **Bancada de trabalho de dispositivo do Azure IoT: Configurar as definições de dispositivos... > cadeia de configuração do dispositivo exclusivo (UDS)**.
  ![Configurar domínios de Atualização](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Aponte a cadeia de caracteres de domínios de Atualização gerada. Irá precisar dele para gerar o certificado X.509. Em seguida, prima `Enter`.
  ![Copie os domínios de Atualização](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Certifique-se de que a notificação de que os domínios de Atualização tem sido configurado no STSAFE com êxito.
  ![Configurar o sucesso de domínios de Atualização](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Em alternativa, pode configurar domínios de Atualização por meio da porta serial usando utilitários como o Putty. Siga [modo de configuração de uso](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) para fazer isso.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Atualizar o ponto final do dispositivo Global e o âmbito do ID

No código de dispositivo, tem de especificar o [ponto final de aprovisionamento de dispositivos](/azure/iot-dps/concepts-service#device-provisioning-endpoint) e o âmbito do ID para garantir o isolamento de inquilino.

1. No portal do Azure, selecione o **descrição geral** painel do seu serviço aprovisionamento de dispositivos e anote o **ponto final do dispositivo Global** e **âmbito do ID** valores.
  ![Ponto final Global do serviço e o âmbito do ID de aprovisionamento de dispositivos](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Open **DeKitDPS.ino**. Localizar e substituir `[Global Device Endpoint]` e `[ID Scope]` com os valores que anotou apenas para baixo.
  ![Ponto final do serviço aprovisionamento dispositivo](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Preencher o `registrationId` variável no código. Apenas de alfanuméricos, minúsculas, e o hífen combinação com um máximo de 128 carateres é permitida. Também é indicado para baixo o valor.
  ![ID de registo](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Clique em `F1`, introduza e selecione **Bancada de trabalho de dispositivo do Azure IoT: Carregar o código de dispositivo**. Ele começa a compilação e carregar o código para DevKit.
  ![Carregamento de dispositivo](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Gerar certificado X.509

O [mecanismo de atestado](/azure/iot-dps/concepts-device#attestation-mechanism) utilizada por este exemplo é o certificado X.509. Tem de utilizar um utilitário para gerá-lo.

> [!NOTE]
> O gerador de certificados X.509 suporta apenas Windows agora.

1. No VS Code, clique em `F1`, introduza e selecione **abra o Terminal nova** para abrir a janela de terminal.

1. Execute `dps_cert_gen.exe` em `tool` pasta.

1. Especifique a localização de ficheiro binário compilado como `..\.build\DevKitDPS`. Em seguida, cole a **domínios de Atualização** e **registrationId** anotou para baixo. 
  ![Gerar X.509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. A `.pem` certificado X.509 gera na mesma pasta.
  ![Ficheiro X.509](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Criar uma entrada de inscrição de dispositivos

1. No portal do Azure, abra o serviço de aprovisionamento de dispositivo, navegue até à secção de inscrições de gerir e clique em **adicionar inscrição individual**.
  ![Adicionar inscrição individual](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Clique junto ao ícone de arquivo **ficheiro de certificado primário. pem ou. cer** para carregar o `.pem` ficheiro gerado.
  ![Carregar. pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Certifique-se de que o DevKit está registado no IoT Hub do Azure

Prima a **repor** botão no seu DevKit. Deverá ver **DPS ligado!** no ecrã de DevKit. Depois do dispositivo é reiniciado, as seguintes ações:

1. O dispositivo envia um pedido de registo ao Serviço Aprovisionamento de Dispositivos.
1. O serviço de aprovisionamento de dispositivos envia de volta um desafio de registo para o qual o seu dispositivo vai responder.
1. O registo bem-sucedido, o serviço de aprovisionamento de dispositivos envia o URI do Hub IoT, ID de dispositivo e a chave de encriptação volta para o dispositivo.
1. A aplicação de cliente do IoT Hub no dispositivo liga ao seu hub.
1. Em ligação com êxito para o hub, verá o dispositivo aparecer no do IoT Hub Device Explorer.
  ![Dispositivo registado](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problemas e comentários

Se tiver problemas, consulte a Iot DevKit [FAQs](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), ou entrar em contacto com os seguintes canais de suporte:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a inscrever um dispositivo de forma segura para o serviço de aprovisionamento de dispositivos por meio do mecanismo de composição de identidade de dispositivo, para que o dispositivo pode ser registado automaticamente no IoT Hub do Azure. 

Em resumo, aprendeu como:

> [!div class="checklist"]
> * Configure o ponto final global do serviço aprovisionamento de dispositivos num dispositivo.
> * Utilize um segredo de dispositivo exclusivo para gerar um certificado X.509.
> * Inscreva um dispositivo individual.
> * Certifique-se de que o dispositivo está registado.

Saiba como [criar e aprovisionar um dispositivo simulado](./quick-create-simulated-device.md).

