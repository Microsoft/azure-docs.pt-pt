---
title: IoT DevKit com a nuvem – ligar o MXChip de IoT DevKit ao IoT Hub do Azure | Documentos da Microsoft
description: Neste tutorial, saiba como enviar o status de sensores no IoT DevKit AZ3166 para o acelerador de solução de monitorização remota do Azure IoT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: ae8dc263e08528c6e3b3bae8c779162c96d51f43
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100790"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Ligar o MXChip IoT DevKit ao solution accelerator do monitoramento remoto do Azure IoT

Neste tutorial, saiba como executar uma aplicação de exemplo no seu DevKit para enviar dados de sensor para seu acelerador de solução de monitorização remota do Azure IoT.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é uma área de compatível de Arduino tudo-em-um com periféricos avançados e sensores. Pode desenvolver para ele usando [extensão do Visual Studio Code para Arduino](https://aka.ms/arduino). E ele vem com um crescimento [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) para orientá-lo a soluções de Internet das coisas (IoT) de protótipo que tiram partido dos serviços do Microsoft Azure.

## <a name="what-you-need"></a>Do que precisa

Concluir o [guia de introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Sua DevKit tiver ligado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

Uma subscrição ativa do Azure. Se não tiver um, pode registrar-se através de um dos seguintes dois métodos:

* Ativar um [conta de Microsoft Azure avaliação de 30 dias gratuita](https://azure.microsoft.com/free/)

* Afirmação sua [crédito do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se for assinante do MSDN ou o Visual Studio

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Criar um acelerador de solução de monitorização remota do Azure IoT

1. Aceda a [site de Aceleradores de solução de IoT do Azure](https://www.azureiotsolutions.com/) e clique em **criar uma nova solução**.

   ![Selecione o tipo de acelerador de solução de IoT do Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Por predefinição, este exemplo cria um Hub de IoT S2 depois de criar um acelerador de solução de monitorização remota do IoT. Se este hub IoT não está a ser utilizado com grande número de dispositivos, é altamente recomendável que mudar para a versão de S2 para S1 e eliminar o acelerador de solução de monitorização remota do IoT, para que o IoT Hub relacionados também pode ser eliminado, quando já não precisam dele. 

2. Selecione **monitorização remota**.

3. Introduza um nome de solução, selecione uma subscrição e uma região e, em seguida, clique em **criar solução**. A solução pode demorar algum tempo a ser aprovisionado.
  
   ![Criar solução](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Após o aprovisionamento estiver concluído, clique em **inicie**. Alguns dispositivos simulados são criados para a solução durante o processo de aprovisionamento. Clique em **dispositivos** verificá-la.

   ![Dashboard](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Consola](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Clique em **adicionar um dispositivo**.

6. Clique em **adicionar novo** para **personalizadas dos dispositivos**.
  
   ![Adicionar um novo dispositivo](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Clique em **deixe-me a definir o meu próprio ID do dispositivo**, introduza `AZ3166`e, em seguida, clique em **criar**.
  
   ![Criar o dispositivo com o ID](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Anote **o nome de anfitrião do IoT Hub**e clique em **feito**.

## <a name="open-the-remotemonitoring-sample"></a>Abra o exemplo de RemoteMonitoring

1. Desligar o DevKit do seu computador, se estiver ligado.

2. Inicie o VS Code.

3. Ligar o DevKit ao seu computador. O VS Code automaticamente Deteta sua DevKit e abre-se as seguintes páginas:

   * A página de introdução de DevKit.
   * Exemplos de Arduino: Exemplos práticos para começar com DevKit.

4. Expanda o lado esquerdo **exemplos de ARDUINO** secção, navegue até **exemplos para MXCHIP AZ3166 > AzureIoT**e selecione **RemoteMonitoring**. Abre uma nova janela do VS Code com uma pasta de projeto.

   > [!NOTE]
   > Se fechar o painel, pode abri-lo novamente. Uso `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, escreva **Arduino**e, em seguida, localize e selecione **Arduino: Exemplos**.

## <a name="provision-required-azure-services"></a>Aprovisionar os serviços do Azure necessários

Na janela da solução, executar a tarefa através de `Ctrl+P` (macOS: `Cmd+P`) ao introduzir `task cloud-provision` na caixa de texto fornecido.

No terminal VS Code, uma linha de comandos interativa orienta-o através do aprovisionamento os serviços do Azure necessários.

![Aprovisionar recursos do Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Criar e carregar o código de dispositivo

1. Uso `Ctrl+P` (macOS: `Cmd + P`) e o tipo **ligação de dispositivos de configuração de tarefas**.

2. Terminal pede-lhe se pretende utilizar uma cadeia de ligação que obtém a partir do `task cloud-provision` passo. Também poderia de entrada sua própria cadeia de ligação do dispositivo ao clicar em "Criar nova..."

3. Terminal pede-lhe para introduzir o modo de configuração. Para fazer isso, premido o botão A, em seguida, push e largue o botão de reposição. A tela exibe a DevKit ID e a 'Configuração'.

   ![Cadeia de ligação de entrada](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Após `task config-device-connection` estiver concluída, clique em `F1` para carregar os comandos de código VS e selecione `Arduino: Upload`. Código de VS começa a verificar e carregar o esboço de Arduino.
  
   ![Verificação e o carregamento do esboço de Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

O DevKit reinicia e começa a execução do código.

## <a name="test-the-project"></a>Testar o projeto

Quando a aplicação de exemplo é executado, DevKit envia dados de sensores através de Wi-Fi para o seu acelerador de solução de monitorização remota do Azure IoT. Para ver o resultado, siga estes passos:

1. Aceda ao seu acelerador de solução de monitorização remota do Azure IoT e clique em **DASHBOARD**.

2. Na consola de solução de monitorização remota, verá seu estado de sensor DevKit.

   ![Dados de sensores em solution accelerator do monitoramento remoto do Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Alterar ID do dispositivo

Se pretender alterar o codificado **AZ3166** para um ID de dispositivo personalizadas no código, modifique a linha de código apresentado na [exemplo de monitorização remoto](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23).

## <a name="problems-and-feedback"></a>Problemas e comentários

Se tiver problemas, consulte [o kit de programadores de IoT FAQs](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contacte-nos com os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como ligar um dispositivo de DevKit ao seu acelerador de solução de monitorização remota do Azure IoT e visualizar os dados de sensor, aqui estão os passos sugeridos seguintes:

* [Azure descrição geral do Aceleradores de solução IoT](https://docs.microsoft.com/azure/iot-suite/)

* [Ligar um dispositivo de MXChip IoT DevKit a sua aplicação do Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)

* [Kit de programação de IoT](https://microsoft.github.io/azure-iot-developer-kit/) 
