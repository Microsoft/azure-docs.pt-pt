---
title: Envie e-mail quando a porta é aberta usando funções Azure
description: Monitorize o sensor magnético para detetar quando uma porta é aberta e utilize as Funções Azure para enviar uma notificação por e-mail.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: e3649ac786c07459695a1e6ca6ad52620e23d59f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148795"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Monitor de porta -- Usando funções Azure e SendGrid, envie e-mail quando uma porta é aberta           

O MXChip IoT DevKit contém um sensor magnético incorporado. Neste projeto, você deteta a presença ou ausência de um campo magnético forte próximo - neste caso, vindo de um pequeno íman permanente.

## <a name="what-you-learn"></a>O que irá aprender

Neste projeto, aprende-se:
- Como usar o sensor magnético do MXChip IoT DevKit para detetar o movimento de um íman próximo.
- Como utilizar o serviço SendGrid para enviar uma notificação para o seu endereço de e-mail.

> [!NOTE]
> Para uma utilização prática deste projeto, execute as seguintes tarefas:
> - Monte um íman na borda de uma porta.
> - Monte o DevKit no bate-portas perto do íman. Abrir ou fechar a porta irá acionar o sensor, resultando na sua receção de uma notificação por e-mail do evento.

## <a name="what-you-need"></a>O que precisa

Termine o [Guia de Início](iot-hub-arduino-iot-devkit-az3166-get-started.md) para:

* Tenha o seu DevKit ligado a Wi-Fi
* Preparar o ambiente de desenvolvimento

Uma subscrição ativa do Azure. Se não tiver um, pode registar-se através de um destes métodos:

* Ative uma [conta gratuita de 30 dias do Microsoft Azure](https://azure.microsoft.com/free/).
* Reclame o seu [crédito Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se for assinante da MSDN ou do Visual Studio.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Implementar o serviço SendGrid em Azure

[SendGrid](https://sendgrid.com/) é uma plataforma de entrega de e-mail baseada na nuvem. Este serviço será utilizado para enviar notificações por e-mail.

> [!NOTE]
> Se já implementou um serviço SendGrid, poderá proceder diretamente ao [Deploy IoT Hub em Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Implantação sendGrid

Para providenciar serviços Azure, utilize o botão **Implementar para Azure.** Este botão permite uma implementação rápida e fácil dos seus projetos de código aberto para o Microsoft Azure.

Clique no botão **Implementar para Azure** abaixo. 

[![Implementar no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Se ainda não assinou a sua conta Azure, inscreva-se agora. 

Agora vê o formulário de inscrição da SendGrid.

![Implantação sendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Preencha o formulário de inscrição:

   * **Grupo de recursos**: Criar um grupo de recursos para hospedar o serviço SendGrid ou utilizar um existente. Consulte [a utilização de grupos de recursos para gerir os seus recursos Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Nome**: O nome do seu serviço SendGrid. Escolha um nome único, diferindo de outros serviços que possa ter.

   * **Senha**: O serviço requer uma senha, que não será utilizada para nada neste projeto.

   * **Email**: O serviço SendGrid enviará a verificação para este endereço de e-mail.

Consulte a opção **Pin para dashboard** para tornar esta aplicação mais fácil de encontrar no futuro e, em seguida, clique em **Comprar** para submeter o formulário de inscrição.
 
### <a name="sendgrid-api-key-creation"></a>Criação da Chave SendGrid API

Depois de concluída a implementação, clique nele e, em seguida, clique no botão **Gerir.** A sua página de conta SendGrid aparece, onde precisa de verificar o seu endereço de e-mail.

![SendGrid Manage](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Na página SendGrid, clique em **Definições**  >  **API Keys**  >  **Create API Key**.

![SendGrid Criar API Primeiro](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Na página **'Criar Chave API',** insira o **Nome da Chave API** e clique em **Criar & Ver**.

![SendGrid Criar API Segundo](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

A sua tecla API é exibida apenas uma vez. Certifique-se de que copia e armazena com segurança, pois é utilizado no passo seguinte.

## <a name="deploy-iot-hub-in-azure"></a>Implementar hub IoT em Azure

As seguintes etapas irão providenciar outros serviços relacionados com a Azure IoT e implementar funções Azure para este projeto.

Clique no botão **Implementar para Azure** abaixo. 

[![Implementar no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

O formulário de inscrição aparece.

![Implantação do IoTHub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Preencha os campos no formulário de inscrição.

   * **Grupo de recursos**: Criar um grupo de recursos para hospedar o serviço SendGrid ou utilizar um existente. Consulte [a utilização de grupos de recursos para gerir os seus recursos Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Iot Hub Name**: O nome do seu hub IoT. Escolha um nome único, diferindo de outros serviços que possa ter.

   * **Iot Hub Sku**: F1 (limitado a um por subscrição) é gratuito. Pode ver mais informações sobre preços na [página de preços.](https://azure.microsoft.com/pricing/details/iot-hub/)

   * **A partir do Email**: Este campo deve ser o mesmo endereço de e-mail que usou ao configurar o serviço SendGrid.

Consulte a opção **Pin para dashboard** para tornar esta aplicação mais fácil de encontrar no futuro e, em seguida, clique em **Comprar** quando estiver pronto para continuar para o próximo passo.
 
## <a name="build-and-upload-the-code"></a>Construa e carreie o código

Em seguida, carregue o código de amostra no Código VS e disposi os serviços Azure necessários.

### <a name="start-vs-code"></a>Iniciar código VS

- Certifique-se de que o seu DevKit **não** está ligado ao computador.
- Inicie o VS Code.
- Ligue o DevKit ao seu computador.

> [!NOTE]
> Quando lançar o Código VS, poderá receber uma mensagem de erro indicando que não encontra o Arduino IDE ou o pacote de placas relacionados. Se receber este erro, feche o Código VS, volte a lançar o Arduino IDE e o Código VS deverá localizar corretamente o caminho Arduino IDE.

### <a name="open-arduino-examples-folder"></a>Abra a pasta Arduino Exemplos

Expanda a secção **ARDUINO EXEMPLOS** do lado esquerdo, navegue **para Exemplos para MXCHIP AZ3166 > AzureIoT,** e selecione **DoorMonitor**. Esta ação abre uma nova janela vs Code com uma pasta de projeto.

![mini-exemplos de soluções](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Também pode abrir a aplicação de exemplo a partir da paleta de comando. Utilize `Ctrl+Shift+P` (macOS: `Cmd+Shift+P` ) para abrir a paleta de comando, **digite Arduino,** e depois encontre e selecione **Arduino: Exemplos**.

### <a name="provision-azure-services"></a>Prestação de serviços Azure

Na janela da solução, executar a tarefa de provisionamento em nuvem:
- Tipo `Ctrl+P` (macOS: `Cmd+P` ).
- `task cloud-provision`Insira na caixa de texto fornecida.

No terminal de Código VS, uma linha de comando interativa guia-o através do fornecimento dos serviços Azure necessários. Selecione todos os mesmos itens da lista solicitada que previamente a provisionou no [Deploy IoT Hub em Azure](#deploy-iot-hub-in-azure).

![Provisão de Nuvens](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Se a página estiver pendurada no estado de carregamento ao tentar iniciar sessão no Azure, consulte a [secção "página pendurada ao iniciar sessão" do IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) para resolver este problema. 

### <a name="build-and-upload-the-device-code"></a>Construa e carreie o código do dispositivo

Em seguida, faça o upload do código para o dispositivo.

#### <a name="windows"></a>Windows

1. Use `Ctrl+P` para correr `task device-upload` .

2. O terminal solicita-lhe que entre no modo de configuração. Para tal, mantenha premida o botão A, em seguida, pressione e liberte o botão de reset. O ecrã apresenta o número de identificação DevKit e a palavra *Configuração*.

#### <a name="macos"></a>macOS

1. Coloque o DevKit no modo de configuração: Mantenha premido o botão A e, em seguida, pressione e liberte o botão de reset. O ecrã apresenta 'Configuração'.

2. Clique `Cmd+P` para `task device-upload` correr.

#### <a name="verify-upload-and-run-the-sample-app"></a>Verifique, carre fique e execute a aplicação da amostra

A cadeia de ligação que é recuperada a partir do passo dos [serviços Provision Azure](#provision-azure-services) está agora definida. 

O Código VS começa então a verificar e a enviar o esboço Arduino para o DevKit.

![A screenshot mostra o Código do Estúdio Visual a verificar e a carregar o esboço do Arduino.](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

O DevKit reinicia e começa a executar o código.

> [!NOTE]
> Ocasionalmente, poderá receber uma mensagem de erro "Error: AZ3166: Unknown package". Este erro ocorre quando o índice de pacote de placa não é atualizado corretamente. Para resolver este erro, consulte a [secção de desenvolvimento do IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

O programa inidia pela primeira vez quando o DevKit está na presença de um campo magnético estável.

Após a inicialização, `Door closed` é apresentado no ecrã. Quando há uma mudança no campo magnético, o estado muda para `Door opened` . Cada vez que o estado da porta muda, recebe uma notificação por e-mail. (Estas mensagens de e-mail podem demorar até cinco minutos a serem recebidas.)

![Ímanes perto do sensor: Porta fechada](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Ímanes perto do sensor: Porta fechada")

![Íman afastado do sensor: Porta aberta](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Íman afastado do sensor: Porta aberta")

## <a name="problems-and-feedback"></a>Problemas e feedback

Se encontrar problemas, consulte o [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou conecte-se utilizando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Aprendeu a ligar um dispositivo DevKit ao seu acelerador de solução de monitorização remota Azure IoT e utilizou o serviço SendGrid para enviar um e-mail. Eis o próximo passo sugerido: Visão geral do[acelerador de solução de monitorização remota Azure IoT](/azure/iot-suite/)