---
title: Receber um e-mail quando a porta é aberta com o serviço SendGrid e as funções do Azure | Documentos da Microsoft
description: Monitorize o sensor magnético para detectar quando uma porta está aberta e utilizar as funções do Azure para enviar uma notificação por e-mail.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: a620b592a33f9de11de53d623d257f203da2157b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547706"
---
# <a name="door-monitor"></a>Monitor de porta          

O MXChip IoT DevKit contém um sensor magnético incorporado. Neste projeto, detectar a presença ou ausência de um campo magnético forte próximos – nesse caso, proveniente de um pequeno, ímã permanente.

## <a name="what-you-learn"></a>O que irá aprender

Este projeto, vai aprender:
- Como utilizar o sensor magnético do MXChip IoT DevKit para detetar o movimento de um ímã próximos.
- Como utilizar o serviço SendGrid para enviar uma notificação para o seu endereço de e-mail.

> [!NOTE]
> Para um uso prático deste projeto, realizar as seguintes tarefas:
> - Monte um ímã na periferia da porta.
> - Monte o DevKit no jamb a porta de entrada perto o ímã. Abrir ou fechar a porta irá acionar o sensor, resultando na receber uma notificação de e-mail do evento.

## <a name="what-you-need"></a>Do que precisa

Concluir o [guia de introdução](iot-hub-arduino-iot-devkit-az3166-get-started.md) para:

* Sua DevKit tiver ligado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

Uma subscrição ativa do Azure. Se não tiver um, pode registrar-se através de um dos seguintes métodos:

* Ativar uma [conta de Microsoft Azure avaliação de 30 dias gratuita](https://azure.microsoft.com/free/).
* Afirmação sua [crédito do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se for um assinante do MSDN ou o Visual Studio.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Implementar o serviço SendGrid no Azure

[O SendGrid](https://sendgrid.com/) é uma plataforma de entrega de correio eletrónico com base na cloud. Este serviço irá ser utilizado para enviar notificações por e-mail.

> [!NOTE]
> Se já tiver implementado um serviço do SendGrid, pode avançar diretamente para [implementar o IoT Hub no Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Implementação de SendGrid

Para aprovisionar os serviços do Azure, utilize o **implementar no Azure** botão. Este botão permite a implementação rápida e fácil dos seus projetos de código-fonte aberto para o Microsoft Azure.

Clique nas **implementar no Azure** botão abaixo. 

[![Implementar no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Se já não está inscrito na sua conta do Azure, inicie sessão agora. 

Pode agora ver o formulário de inscrição do SendGrid.

![Implementação de SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Preencha o formulário de inscrição:

   * **Grupo de recursos**: Criar um grupo de recursos para alojar o serviço SendGrid ou utilize um já existente. Ver [utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/manage-resource-groups-portal.md).

   * **Nome**: O nome para o seu serviço SendGrid. Escolha um nome exclusivo, que difere de outros serviços que pode ter.

   * **palavra-passe**: O serviço requer uma palavra-passe, que não será utilizada para qualquer coisa neste projeto.

   * **e-mail**: O serviço SendGrid irá enviar verificação para este endereço de e-mail.

Verifique os **afixar ao dashboard** a opção de tornar este aplicativo mais fácil encontrar no futuro, em seguida, clique em **Compra** submeter o formulário de início de sessão.
 
### <a name="sendgrid-api-key-creation"></a>Criar a chave de API de SendGrid

Depois de concluída a implementação, clique no mesmo e, em seguida, clique nas **gerir** botão. É apresentada a página de conta do SendGrid, em que precisa verificar o seu endereço de e-mail.

![Gerir o SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Na página do SendGrid, clique em **configurações** > **chaves de API** > **criar chave de API**.

![O SendGrid primeiro criar API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Na **criar chave de API** página, introduza o **nome da chave de API** e clique em **Criar & vista**.

![O SendGrid criar API em segundo lugar](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

A chave de API é apresentada apenas uma vez. Certifique-se de que copie e armazene com segurança, como é utilizado no próximo passo.

## <a name="deploy-iot-hub-in-azure"></a>Implementar o Hub IoT no Azure

Os passos seguintes irão aprovisionar outras IoT do Azure relacionados com serviços e implementar as funções do Azure para este projeto.

Clique nas **implementar no Azure** botão abaixo. 

[![Implementar no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

O formulário de inscrição é apresentado.

![Implementação do IoTHub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Preencha os campos do formulário de inscrição.

   * **Grupo de recursos**: Criar um grupo de recursos para alojar o serviço SendGrid ou utilize um já existente. Ver [utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/manage-resource-groups-portal.md).

   * **Nome do IOT Hub**: O nome do hub IoT. Escolha um nome exclusivo, que difere de outros serviços que pode ter.

   * **Sku do IOT Hub**: F1 (limitado a um por subscrição) é gratuito. Pode ver informações de preços mais sobre o [página de preços](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Do E-Mail**: Este campo deve ter o mesmo endereço de e-mail utilizado durante a configuração do serviço do SendGrid.

Verifique os **afixar ao dashboard** a opção de tornar este aplicativo mais fácil encontrar no futuro, em seguida, clique em **Compra** quando estiver pronto para continuar para o passo seguinte.
 
## <a name="build-and-upload-the-code"></a>Criar e carregar o código

Em seguida, carregue o código de exemplo no VS Code e aprovisionar os serviços do Azure necessários.

### <a name="start-vs-code"></a>Inicie o VS Code

- Certifique-se de seu DevKit **não** conectado ao computador.
- Inicie o VS Code.
- Ligar o DevKit ao seu computador.

> [!NOTE]
> Quando inicia o VS Code, poderá receber uma mensagem de erro informando que não é possível localizar o Arduino IDE ou o pacote de quadro relacionados. Se receber este erro, fechar o VS Code, inicie novamente o Arduino IDE e VS Code devem localizar o caminho de Arduino IDE corretamente.

### <a name="open-arduino-examples-folder"></a>Abrir pasta de exemplos de Arduino

Expanda o lado esquerdo **exemplos de ARDUINO** secção, navegue até **exemplos para MXCHIP AZ3166 > AzureIoT**e selecione **DoorMonitor**. Esta ação abre uma nova janela do VS Code com uma pasta de projeto.

![mini-solution-exemplos](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Também é possível abrir a aplicação de exemplo da paleta de comandos. Uso `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, escreva **Arduino**e, em seguida, localize e selecione **Arduino: Exemplos**.

### <a name="provision-azure-services"></a>Aprovisionar serviços do Azure

Na janela da solução, execute a tarefa de aprovisionamento de cloud:
- Tipo `Ctrl+P` (macOS: `Cmd+P`).
- Introduza `task cloud-provision` na caixa de texto fornecido.

No terminal VS Code, uma linha de comandos interativa orienta-o através do aprovisionamento os serviços do Azure necessários. Selecione todos os itens de mesmo a partir da lista de pedido que lhe foram atribuídos anteriormente no [implementar o IoT Hub no Azure](#deploy-iot-hub-in-azure).

![Aprovisionamento de cloud](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Se a página paradas do Estado de carregamento ao tentar iniciar sessão no Azure, consulte a ["página hanges quando iniciar sessão" secção de IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) para resolver este problema. 

### <a name="build-and-upload-the-device-code"></a>Criar e carregar o código de dispositivo

Em seguida, carregue o código para o dispositivo.

#### <a name="windows"></a>Windows

1. Uso `Ctrl+P` para executar `task device-upload`.

2. Terminal pede-lhe para introduzir o modo de configuração. Para fazer isso, premido o botão A, em seguida, push e largue o botão de reposição. A tela exibe o número de identificação de DevKit e a palavra *configuração*.

#### <a name="macos"></a>macOS

1. Coloca o DevKit no modo de configuração: Premido o botão A, em seguida, enviar por push e largue o botão de reposição. A tela exibe 'Configuração'.

2. Clique em `Cmd+P` para executar `task device-upload`.

#### <a name="verify-upload-and-run-the-sample-app"></a>Certifique-se, carregar e executar a aplicação de exemplo

A cadeia de ligação que é obtida a partir da [dos serviços do Azure de aprovisionar](#provision-azure-services) passo está agora definido. 

VS Code, em seguida, começa a verificar e carregar o Arduino criar esboços para o DevKit.

![carregamento de dispositivo](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

O DevKit reinicia e começa a execução do código.

> [!NOTE]
> Ocasionalmente, poderá receber um "erro: AZ3166: Mensagem de erro do pacote desconhecido". Este erro ocorre quando o índice de pacote de quadro não é atualizado corretamente. Para resolver este erro, consulte a [secção do desenvolvimento de IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

O programa inicializa, primeiro, quando o DevKit está na presença de um campo magnético estável.

Após a inicialização, `Door closed` é apresentada no ecrã. Quando existe uma alteração no campo magnético, o estado é alterado para `Door opened`. Cada vez que as alterações de estado de porta de entrada, recebe uma notificação por e-mail. (Estas mensagens de e-mail podem demorar até cinco minutos para que sejam recebidas.)

![Ímãs próximos o sensor: Porta fechado](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Ímãs próximos o sensor: Porta fechada")

![Ímã movidos para fora o sensor: Porta aberto](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Ímã movidos para fora o sensor: Porta aberta")

## <a name="problems-and-feedback"></a>Problemas e comentários

Se tiver problemas, consulte a [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou ligue-se com os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos Seguintes

Ter ficou a saber como ligar um dispositivo de DevKit ao seu acelerador de solução de monitorização remota do Azure IoT e utilizado o serviço SendGrid para enviar uma mensagem de e-mail. Eis os passos sugeridos seguintes:

* [Monitorização remota do IoT solution accelerator descrição geral do Azure](https://docs.microsoft.com/azure/iot-suite/)
* [Ligar um dispositivo de MXChip IoT DevKit a sua aplicação do Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
