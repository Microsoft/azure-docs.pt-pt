---
title: Receber um email quando a porta for aberta usando o serviço SendGrid e o Azure Functions | Microsoft Docs
description: Monitore o sensor magnético para detectar quando uma porta é aberta e use Azure Functions para enviar uma notificação por email.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 6cbf3f906b511d9d76319f57f5508a2c584213cb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73484059"
---
# <a name="door-monitor"></a>Monitor da porta          

O MXChip IoT DevKit contém um sensor magnético interno. Neste projeto, você detecta a presença ou a ausência de um campo magnético forte próximo – nesse caso, proveniente de um ímã pequeno e permanente.

## <a name="what-you-learn"></a>O que irá aprender

Neste projeto, você aprende:
- Como usar o sensor magnético do MXChip IoT DevKit para detectar a movimentação de um ímã próximo.
- Como usar o serviço SendGrid para enviar uma notificação ao seu endereço de email.

> [!NOTE]
> Para um uso prático deste projeto, execute as seguintes tarefas:
> - Monte um ímã na borda de uma porta.
> - Monte o DevKit na porta jamb perto do ímã. Abrir ou fechar a porta disparará o sensor, resultando no recebimento de uma notificação por email do evento.

## <a name="what-you-need"></a>Do que precisa

Conclua o [Guia de introdução](iot-hub-arduino-iot-devkit-az3166-get-started.md) para:

* Ter seu DevKit conectado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

Uma subscrição ativa do Azure. Se você não tiver um, poderá se registrar por meio de um destes métodos:

* Ative uma [avaliação gratuita de 30 dias Microsoft Azure conta](https://azure.microsoft.com/free/).
* Solicite seu [crédito do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se você for um assinante do MSDN ou do Visual Studio.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Implantar o serviço SendGrid no Azure

[SendGrid](https://sendgrid.com/) é uma plataforma de entrega de email baseada em nuvem. Este serviço será usado para enviar notificações por email.

> [!NOTE]
> Se você já tiver implantado um serviço SendGrid, poderá prosseguir diretamente para [implantar o Hub IOT no Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Implantação do SendGrid

Para provisionar os serviços do Azure, use o botão **implantar no Azure** . Esse botão permite uma implantação rápida e fácil de seus projetos de código-fonte aberto para Microsoft Azure.

Clique no botão **implantar no Azure** abaixo. 

[![Implementar no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Se você ainda não tiver entrado em sua conta do Azure, entre agora. 

Agora você vê o formulário de inscrição SendGrid.

![Implantação do SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Preencha o formulário de inscrição:

   * **Grupo de recursos**: Crie um grupo de recursos para hospedar o serviço SendGrid ou use um existente. Consulte [usando grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/manage-resource-groups-portal.md).

   * **Nome**: o nome do serviço SendGrid. Escolha um nome exclusivo, diferente de outros serviços que você possa ter.

   * **Senha**: o serviço requer uma senha, que não será usada para nada neste projeto.

   * **Email**: o serviço SendGrid enviará a verificação para este endereço de email.

Marque a opção **fixar no painel** para facilitar a localização desse aplicativo no futuro e clique em **comprar** para enviar o formulário de entrada.
 
### <a name="sendgrid-api-key-creation"></a>Criação de chave de API SendGrid

Após a conclusão da implantação, clique nela e, em seguida, clique no botão **gerenciar** . A página da sua conta do SendGrid é exibida, onde você precisa verificar seu endereço de email.

![SendGrid gerenciar](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Na página SendGrid, clique em **configurações** > **chaves de API** > **criar chave de API**.

![SendGrid criar a API primeiro](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Na página **criar chave de API** , insira o **nome da chave de API** e clique em **criar & exibição**.

![SendGrid criar API segundo](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Sua chave de API é exibida apenas uma vez. Certifique-se de copiá-la e armazená-la com segurança, pois ela é usada na próxima etapa.

## <a name="deploy-iot-hub-in-azure"></a>Implantar o Hub IoT no Azure

As etapas a seguir provisionarão outros serviços relacionados ao Azure IoT e implantarão Azure Functions para este projeto.

Clique no botão **implantar no Azure** abaixo. 

[![Implementar no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

O formulário de inscrição é exibido.

![Implantação do IoTHub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Preencha os campos no formulário de inscrição.

   * **Grupo de recursos**: Crie um grupo de recursos para hospedar o serviço SendGrid ou use um existente. Consulte [usando grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/manage-resource-groups-portal.md).

   * **Nome do Hub IOT**: o nome do Hub IOT. Escolha um nome exclusivo, diferente de outros serviços que você possa ter.

   * **SKU do Hub IOT**: F1 (limitado a um por assinatura) é gratuito. Você pode ver mais informações sobre preços na [página de preços](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Do email**: esse campo deve ser o mesmo endereço de email que você usou ao configurar o serviço SendGrid.

Marque a opção **fixar no painel** para facilitar a localização desse aplicativo no futuro e clique em **comprar** quando estiver pronto para continuar para a próxima etapa.
 
## <a name="build-and-upload-the-code"></a>Compilar e carregar o código

Em seguida, carregue o código de exemplo em VS Code e provisione os serviços do Azure necessários.

### <a name="start-vs-code"></a>Iniciar VS Code

- Verifique se o DevKit **não** está conectado ao seu computador.
- Iniciar VS Code.
- Conecte o DevKit ao seu computador.

> [!NOTE]
> Ao iniciar o VS Code, você poderá receber uma mensagem de erro informando que ele não pode localizar o Arduino IDE ou o pacote de placa relacionado. Se você receber esse erro, feche VS Code, inicie o IDE Arduino novamente e VS Code deverá localizar o caminho do IDE do Arduino corretamente.

### <a name="open-arduino-examples-folder"></a>Abrir a pasta de exemplos do Arduino

Expanda a seção **exemplos de ARDUINO** do lado esquerdo, navegue até **exemplos para MXCHIP AZ3166 > AzureIoT**e selecione **DoorMonitor**. Essa ação abre uma nova janela VS Code com uma pasta do projeto.

![mini-solução-exemplos](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Você também pode abrir o aplicativo de exemplo na paleta de comandos. Use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino**e, em seguida, localize e selecione **Arduino: exemplos**.

### <a name="provision-azure-services"></a>Provisionar serviços do Azure

Na janela da solução, execute a tarefa de provisionamento de nuvem:
- Digite `Ctrl+P` (macOS: `Cmd+P`).
- Insira `task cloud-provision` na caixa de texto fornecida.

No VS Code terminal, uma linha de comando interativa orienta você pelo provisionamento dos serviços do Azure necessários. Selecione todos os mesmos itens da lista solicitada que você provisionou anteriormente em [implantar Hub IOT no Azure](#deploy-iot-hub-in-azure).

![Provisionamento de nuvem](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Se a página travar no status de carregamento ao tentar entrar no Azure, consulte a [seção "a página trava ao fazer logon" das perguntas frequentes sobre o IOT devkit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) para resolver esse problema. 

### <a name="build-and-upload-the-device-code"></a>Compilar e carregar o código do dispositivo

Em seguida, carregue o código para o dispositivo.

#### <a name="windows"></a>Windows

1. Use `Ctrl+P` para executar `task device-upload`.

2. O terminal solicita que você insira o modo de configuração. Para fazer isso, mantenha o botão A pressionado, em seguida, pressione e solte o botão redefinir. A tela exibe o número de identificação do DevKit e a *configuração*da palavra.

#### <a name="macos"></a>macOS

1. Coloque o DevKit no modo de configuração: Mantenha o botão A pressionado e, em seguida, pressione e solte o botão redefinir. A tela exibe ' configuração '.

2. Clique em `Cmd+P` para executar `task device-upload`.

#### <a name="verify-upload-and-run-the-sample-app"></a>Verificar, carregar e executar o aplicativo de exemplo

A cadeia de conexão que é recuperada da etapa [provisionar serviços do Azure](#provision-azure-services) agora está definida. 

VS Code, em seguida, começa a verificar e carregar o esboço Arduino para o DevKit.

![dispositivo-carregar](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

O DevKit reinicia e inicia a execução do código.

> [!NOTE]
> Ocasionalmente, você pode receber uma mensagem de erro "erro: AZ3166: pacote desconhecido". Esse erro ocorre quando o índice do pacote do quadro não é atualizado corretamente. Para resolver esse erro, consulte a [seção de desenvolvimento das perguntas frequentes sobre o IOT devkit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

O programa primeiro inicializa quando o DevKit está na presença de um campo magnético estável.

Após a inicialização, `Door closed` é exibida na tela. Quando há uma alteração no campo magnético, o estado muda para `Door opened`. Cada vez que o estado da porta for alterado, você receberá uma notificação por email. (Essas mensagens de email podem levar até cinco minutos para serem recebidas.)

![Ímã perto do sensor: porta fechada](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Ímã perto do sensor: porta fechada")

![Ímã afastado do sensor: porta aberta](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Ímã afastado do sensor: porta aberta")

## <a name="problems-and-feedback"></a>Problemas e comentários

Se você tiver problemas, consulte as [perguntas frequentes do IOT devkit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou conecte-se usando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Você aprendeu como conectar um dispositivo DevKit ao acelerador de solução de monitoramento remoto de IoT do Azure e usou o serviço SendGrid para enviar um email. Aqui estão as próximas etapas sugeridas:

* [Visão geral do acelerador de solução de monitoramento remoto do Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Conectar um dispositivo MXChip IoT DevKit ao aplicativo IoT Central do Azure](/azure/iot-central/core/howto-connect-devkit)
