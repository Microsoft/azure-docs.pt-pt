---
title: Envie e-mail quando a porta for aberta usando funções azure
description: Monitorize o sensor magnético para detetar quando uma porta é aberta e utilize funções Azure para enviar uma notificação por e-mail.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 6bebe8ac6b9869466938600d6267fd0062c84477
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75977304"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Monitor de porta -- Usando funções Azure e SendGrid, envie e-mail quando uma porta é aberta           

O MXChip IoT DevKit contém um sensor magnético incorporado. Neste projeto, você deteta a presença ou ausência de um campo magnético forte nas proximidades, neste caso, vindo de um pequeno íman permanente.

## <a name="what-you-learn"></a>O que irá aprender

Neste projeto, aprende-se:
- Como utilizar o sensor magnético do MXChip IoT DevKit para detetar o movimento de um íman próximo.
- Como utilizar o serviço SendGrid para enviar uma notificação para o seu endereço de e-mail.

> [!NOTE]
> Para uma utilização prática deste projeto, execute as seguintes tarefas:
> - Monte um íman na borda de uma porta.
> - Monte o DevKit na porta perto do íman. Abrir ou fechar a porta irá acionar o sensor, resultando na receção de uma notificação por e-mail do evento.

## <a name="what-you-need"></a>Do que precisa

Termine o [Guia de Início de Partida](iot-hub-arduino-iot-devkit-az3166-get-started.md) para:

* Tenha o seu DevKit ligado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

Uma subscrição ativa do Azure. Se não tiver um, pode registar-se através de um destes métodos:

* Ativar uma [conta gratuita de 30 dias do Microsoft Azure.](https://azure.microsoft.com/free/)
* Reclame o seu [crédito Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se for assinante da MSDN ou do Visual Studio.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Implementar o serviço SendGrid em Azure

[SendGrid](https://sendgrid.com/) é uma plataforma de entrega de e-mail baseada na nuvem. Este serviço será utilizado para enviar notificações por e-mail.

> [!NOTE]
> Se já implementou um serviço SendGrid, poderá avançar diretamente para a implantação do [IoT Hub em Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Implantação sendGrid

Para fornecer serviços Azure, utilize o botão **Deploy para Azure.** Este botão permite a implementação rápida e fácil dos seus projetos de código aberto para o Microsoft Azure.

Clique no botão **Deploy para Azure** abaixo. 

[![Desdobre para Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Se ainda não assinou a sua conta Azure, inscreva-se agora. 

Agora vê o formulário de inscrição da SendGrid.

![Implantação sendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Preencha o formulário de inscrição:

   * **Grupo de recursos**: Criar um grupo de recursos para acolher o serviço SendGrid ou utilizar um existente. Consulte [a utilização de grupos de recursos para gerir os seus recursos Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Nome**: O nome do seu serviço SendGrid. Escolha um nome único, diferente de outros serviços que possa ter.

   * **Palavra-passe**: O serviço requer uma senha, que não será utilizada para nada neste projeto.

   * **E-mail**: O serviço SendGrid enviará verificação para este endereço de e-mail.

Verifique a opção **Pin para o dashboard** para tornar esta aplicação mais fácil de encontrar no futuro e, em seguida, clique em **Comprar** para submeter o formulário de entrada.
 
### <a name="sendgrid-api-key-creation"></a>Criação de chave SendGrid API

Depois de a implementação estar concluída, clique nele e, em seguida, clique no botão **Gerir.** A página da sua conta SendGrid aparece, onde precisa de verificar o seu endereço de e-mail.

![Gestão de SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Na página SendGrid, clique em **Definições** > **De Teclas** > API**Criar Tecla API**.

![SendGrid Criar API primeiro](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Na página **'Criar Tecla API',** insere o nome da **chave API** e clique **em Criar & Ver**.

![SendGrid Criar API Segundo](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

A sua chave API só é exibida uma vez. Certifique-se de copiar e guardar com segurança, pois é utilizado no próximo passo.

## <a name="deploy-iot-hub-in-azure"></a>Implementar hub IoT em Azure

As seguintes etapas fornecerão outros serviços relacionados com o Azure IoT e implementarão funções Azure para este projeto.

Clique no botão **Deploy para Azure** abaixo. 

[![Desdobre para Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

O formulário de inscrição aparece.

![Implantação do IoTHub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Preencha os campos no formulário de inscrição.

   * **Grupo de recursos**: Criar um grupo de recursos para acolher o serviço SendGrid ou utilizar um existente. Consulte [a utilização de grupos de recursos para gerir os seus recursos Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Nome hub iot**: O nome para o seu hub IoT. Escolha um nome único, diferente de outros serviços que possa ter.

   * **Iot Hub Sku**: F1 (limitado a um por subscrição) é gratuito. Pode ver mais informações sobre preços na [página de preços](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **A partir do email**: Este campo deve ser o mesmo endereço de e-mail utilizado na configuração do serviço SendGrid.

Consulte a opção **Pin para o dashboard** para tornar esta aplicação mais fácil de encontrar no futuro e, em seguida, clique em **Comprar** quando estiver pronto para continuar até ao próximo passo.
 
## <a name="build-and-upload-the-code"></a>Construir e carregar o código

Em seguida, carregue o código da amostra no Código VS e forme os serviços Azure necessários.

### <a name="start-vs-code"></a>Iniciar código VS

- Certifique-se de que o seu DevKit **não** está ligado ao computador.
- Inicie o VS Code.
- Ligue o DevKit ao seu computador.

> [!NOTE]
> Ao lançar o Código VS, poderá receber uma mensagem de erro indicando que não encontra o Arduino IDE ou o pacote de placas relacionados. Se receber este erro, feche o Código VS, volte a lançar o Arduino IDE e o Código VS deverá localizar corretamente o caminho Arduino IDE.

### <a name="open-arduino-examples-folder"></a>Abrir a pasta Arduino Exemplos

Expanda a secção **DE EXEMPLOS ARDUINO** do lado esquerdo, navegue para **Exemplos para MXCHIP AZ3166 > AzureIoT,** e selecione **DoorMonitor**. Esta ação abre uma nova janela vs Código com uma pasta de projeto nele.

![mini-solução-exemplos](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Também pode abrir a aplicação de exemplo a partir da paleta de comando. Utilize `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comando, digite **Arduino,** e depois encontre e selecione **Arduino: Exemplos**.

### <a name="provision-azure-services"></a>Serviços De prestação de serviços Azure

Na janela de solução, execute a tarefa de fornecimento de nuvens:
- Tipo `Ctrl+P` (macOS: `Cmd+P`).
- Introduza `task cloud-provision` na caixa de texto fornecida.

No terminal vs Código, uma linha de comando interativa guia-o através do fornecimento dos serviços Azure necessários. Selecione todos os mesmos itens da lista solicitada que previamente aprovisionou no [Deploy IoT Hub em Azure](#deploy-iot-hub-in-azure).

![Fornecimento de Nuvens](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Se a página estiver pendurada no estado de carregamento ao tentar iniciar sessão no Azure, consulte a [secção "page hanges when slogging in" do IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) para resolver este problema. 

### <a name="build-and-upload-the-device-code"></a>Construir e carregar o código do dispositivo

Em seguida, faça upload do código para o dispositivo.

#### <a name="windows"></a>Windows

1. Use `Ctrl+P` para `task device-upload`correr.

2. O terminal pede-lhe para entrar no modo de configuração. Para tal, segure o botão A e, em seguida, pressione e liberte o botão de reset. O ecrã apresenta o número de identificação DevKit e a palavra *Configuração*.

#### <a name="macos"></a>macOS

1. Coloque o DevKit no modo de configuração: Mantenha premido o botão A, em seguida, pressione e liberte o botão de reset. O ecrã apresenta 'Configuração'.

2. Clique `Cmd+P` para `task device-upload`correr .

#### <a name="verify-upload-and-run-the-sample-app"></a>Verifique, carregue e execute a aplicação de amostra

A cadeia de ligação que é recuperada da etapa de [serviços da Provision Azure](#provision-azure-services) está agora definida. 

O Código VS começa então a verificar e a carregar o esboço arduino para o DevKit.

![dispositivo-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

O DevKit reinicia e começa a executar o código.

> [!NOTE]
> Ocasionalmente, pode receber uma mensagem de erro "Error: AZ3166: Unknown package". Este erro ocorre quando o índice do pacote de placa não é atualizado corretamente. Para resolver este erro, consulte a [secção de desenvolvimento do IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

O programa inicializa primeiro quando o DevKit está na presença de um campo magnético estável.

Após a `Door closed` inicialização, é apresentado no ecrã. Quando há uma mudança no campo magnético, `Door opened`o estado muda para . Cada vez que o estado da porta muda, recebe uma notificação por e-mail. (Estas mensagens de e-mail podem demorar até cinco minutos a receber.)

![Ímanes perto do sensor: Porta fechada](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Ímanes perto do sensor: Porta fechada")

![Íman afastado do sensor: Porta aberta](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Íman afastado do sensor: Porta aberta")

## <a name="problems-and-feedback"></a>Problemas e feedback

Se encontrar problemas, consulte o [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou ligue-se utilizando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Aprendeu a ligar um dispositivo DevKit ao seu acelerador de solução de monitorização remota Azure IoT e utilizou o serviço SendGrid para enviar um e-mail. Aqui estão os próximos passos sugeridos:

* [Visão geral do acelerador de solução de monitorização remota Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Ligue um dispositivo MXChip IoT DevKit à sua aplicação Central Azure IoT](/azure/iot-central/core/howto-connect-devkit)
