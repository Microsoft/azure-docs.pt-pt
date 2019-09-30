---
title: Como usar o provisionamento automático do serviço de provisionamento de dispositivos no Hub IoT do Azure para registrar o MXChip IoT DevKit com o Hub IoT | Microsoft Docs
description: Como usar o provisionamento automático do serviço de provisionamento de dispositivos no Hub IoT do Azure para registrar o MXChip IoT DevKit com o Hub IoT.
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 2731bbcd6a6b0c8f7d82334c022c017d5eae35f0
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677008"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Usar o provisionamento automático do serviço de provisionamento de dispositivos no Hub IoT do Azure para registrar o MXChip IoT DevKit com o Hub IoT

Este artigo descreve como usar o [provisionamento automático](concepts-auto-provisioning.md)do serviço de provisionamento de dispositivos no Hub IOT do Azure para registrar o MXChip IOT devkit com o Hub IOT do Azure. Neste tutorial, ficará a saber como:

* Configure o ponto de extremidade global do serviço de provisionamento de dispositivos em um dispositivo.
* Use um UDS (segredo de dispositivo exclusivo) para gerar um certificado X. 509.
* Registrar um dispositivo individual.
* Verifique se o dispositivo está registrado.

O [MXChip IOT devkit](https://aka.ms/iot-devkit) é uma placa compatível com Arduino tudo em um com periféricos e sensores avançados. Você pode desenvolver para ele usando o [Azure IOT Device Workbench](https://aka.ms/iot-workbench) ou o pacote de extensão das [Ferramentas do azure IOT](https://aka.ms/azure-iot-tools) no Visual Studio Code. O DevKit vem com um [Catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) crescente para guiar suas soluções de Internet das coisas de protótipo (IOT) que aproveitam os serviços do Azure.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas deste tutorial, primeiro execute as seguintes tarefas:

* Configure o Wi-Fi de seu DevKit e prepare seu ambiente de desenvolvimento seguindo a seção "preparar o ambiente de desenvolvimento" em [conectar o IOT DEVKIT AZ3166 ao Hub IOT do Azure na nuvem](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment).
* Atualize para o firmware mais recente (1.3.0 ou posterior) com o tutorial [atualizar firmware devkit](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) .
* Crie e vincule um hub IoT a uma instância do serviço de provisionamento de dispositivos seguindo as etapas em [Configurar o serviço de provisionamento de dispositivos no Hub IOT com o portal do Azure](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Abrir projeto de exemplo

1. Verifique se o DevKit de IoT **não está conectado** ao seu computador. Inicie VS Code primeiro e, em seguida, conecte o DevKit ao seu computador.

1. Clique `F1` para abrir a paleta de comandos, digite e **selecione Azure IOT Device Workbench: Abrir exemplos...** . Em seguida, selecione **IOT devkit** como placa.

1. Na página exemplos do IoT Workbench, encontre **registro de dispositivo com DPS** e clique em **abrir exemplo**. Em seguida, seleciona o caminho padrão para baixar o código de exemplo.
    exemplo de @no__t 0Open @ no__t-1

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Salvar um segredo de dispositivo exclusivo no armazenamento de segurança do dispositivo

O provisionamento automático pode ser configurado em um dispositivo baseado no [mecanismo de atestado](concepts-security.md#attestation-mechanism)do dispositivo. O MXChip IoT DevKit usa o [mecanismo de composição de identidade do dispositivo](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) do [Trusted Computing Group](https://trustedcomputinggroup.org). Um UDS ( **segredo de dispositivo exclusivo** ) salvo em um chip de segurança seguro ([A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) no devkit é usado para gerar o [certificado X. 509](concepts-security.md#x509-certificates)exclusivo do dispositivo. O certificado é usado posteriormente para o processo de registro no serviço de provisionamento de dispositivos e durante o registro no tempo de execução.

Um UDS típico é uma cadeia de caracteres de 64 caracteres, como mostrado no exemplo a seguir:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Para salvar um UDS no DevKit:

1. Em VS Code, clique na barra de status para selecionar a porta COM para o DevKit.
  ![Select porta COM @ no__t-1

1. No DevKit, mantenha o **botão a**, pressione e solte o botão **Redefinir** e, em seguida, solte o **botão a**. Seu DevKit entra no modo de configuração.

1. Clique `F1` para abrir a paleta de comandos, digite e **selecione Azure IOT Device Workbench: Definir configurações do dispositivo... UDS (cadeia de caracteres exclusiva de dispositivo) > config** .
  ![Configure UDS @ no__t-1

1. Anote a cadeia de caracteres UDS gerada. Você precisará dela para gerar o certificado X. 509. Em seguida, pressione `Enter`.
  ![Copy UDS @ no__t-1

1. Confirme a notificação de que o UDS foi configurado no cofre com êxito.
  Êxito de ![Configure UDS @ no__t-1

> [!NOTE]
> Como alternativa, você pode configurar o UDS por meio da porta serial usando utilitários como a saída. Siga [usar o modo de configuração](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) para fazer isso.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Atualizar o ponto de extremidade do dispositivo global e o escopo da ID

No código do dispositivo, você precisa especificar o [ponto de extremidade de provisionamento do dispositivo](/azure/iot-dps/concepts-service#device-provisioning-endpoint) e o escopo da ID para garantir o isolamento do locatário.

1. No portal do Azure, selecione o painel **visão geral** do serviço de provisionamento de dispositivos e anote os valores do **ponto de extremidade do dispositivo global** e do **escopo da ID** .
  Ponto de extremidade global do serviço de provisionamento @no__t 0Device e escopo de ID @ no__t-1

1. Abra **DevKitDPS. ino**. Localize e substitua `[Global Device Endpoint]` e `[ID Scope]` pelos valores que você acabou de anotar.
  Ponto de extremidade do serviço de provisionamento @no__t 0Device @ no__t-1

1. Preencha a variável `registrationId` no código. Somente a combinação alfanumérica, minúscula e hífen com um máximo de 128 caracteres é permitida. Também anotou o valor.
  ID de @no__t 0Registration @ no__t-1

1. Clique em `F1`, digite e selecione @no__t-Workbench do dispositivo IoT do 1Azure: Carregue o código**do dispositivo. Ele começa a compilar e carregar o código em DevKit.
  Upload de ![Device @ no__t-1

## <a name="generate-x509-certificate"></a>Gerar certificado X. 509

O [mecanismo de atestado](/azure/iot-dps/concepts-device#attestation-mechanism) usado por este exemplo é o certificado X. 509. Você precisa usar um utilitário para gerá-lo.

1. Em VS Code, clique em `F1`, digite e selecione **abrir novo terminal** para abrir a janela do terminal.

1. Execute `dps_cert_gen.exe` na pasta `tool`.

1. Especifique o local do arquivo binário compilado como `..\.build\DevKitDPS`. Em seguida, Cole o **UDS** e o **RegistrationId** que você acabou de anotar. 
  ![Generate X. 509 @ no__t-1

1. Um certificado `.pem` X. 509 é gerado na mesma pasta.
  arquivo ![X. 509 @ no__t-1

## <a name="create-a-device-enrollment-entry"></a>Criar uma entrada de inscrição de dispositivos

1. No portal do Azure, abra o serviço de provisionamento de dispositivo, navegue até a seção gerenciar registros e clique em **adicionar registro individual**.
  registro individual de @no__t 0Add @ no__t-1

1. Clique no ícone de arquivo ao lado do **arquivo. PEM ou. cer do certificado primário** para carregar o arquivo `.pem` gerado.
  ![Upload. pem @ no__t-1

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Verifique se o DevKit está registrado com o Hub IoT do Azure

Pressione o botão **Redefinir** em seu devkit. Você deve ver o **DPS conectado!** na tela DevKit. Após a reinicialização do dispositivo, ocorrerão as seguintes ações:

1. O dispositivo envia um pedido de registo ao Serviço Aprovisionamento de Dispositivos.
1. O serviço de provisionamento de dispositivos envia de volta um desafio de registro para o qual seu dispositivo responde.
1. Após o registro bem-sucedido, o serviço de provisionamento de dispositivos envia o URI do Hub IoT, a ID do dispositivo e a chave criptografada de volta para o dispositivo.
1. O aplicativo cliente do Hub IoT no dispositivo se conecta ao seu hub.
1. Após a conexão bem-sucedida com o Hub, você verá que o dispositivo aparece no Device Explorer do Hub IoT.
  ![Device registrado @ no__t-1

## <a name="problems-and-feedback"></a>Problemas e comentários

Se você tiver problemas, consulte as [perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)do IOT devkit ou entre em contato com os seguintes canais para obter suporte:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a registrar um dispositivo com segurança ao serviço de provisionamento de dispositivos usando o mecanismo de composição de identidade do dispositivo, para que o dispositivo possa se registrar automaticamente no Hub IoT do Azure. 

Em resumo, você aprendeu a:

> [!div class="checklist"]
> * Configure o ponto de extremidade global do serviço de provisionamento de dispositivos em um dispositivo.
> * Use um segredo de dispositivo exclusivo para gerar um certificado X. 509.
> * Registrar um dispositivo individual.
> * Verifique se o dispositivo está registrado.

Saiba como [criar e provisionar um dispositivo simulado](./quick-create-simulated-device.md).

