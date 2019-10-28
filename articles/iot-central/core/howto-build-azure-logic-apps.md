---
title: Crie fluxos de trabalho com o conector de IoT Central no aplicativo lógico do Azure | Microsoft Docs
description: Use o conector de IoT Central no aplicativo lógico do Azure para disparar fluxos de trabalho e criar, atualizar e excluir dispositivos em fluxos de trabalho.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 2269ede83ad64172e316a208871ab65053b2d10e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951591"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Crie fluxos de trabalho com o conector de IoT Central nos aplicativos lógicos do Azure

*Este tópico se aplica a construtores e administradores.*

Use os aplicativos lógicos do Azure para criar fluxos de trabalho escalonáveis automatizados que integram aplicativos e dados entre serviços de nuvem e sistemas locais. Os aplicativos lógicos do Azure têm muitos conectores em vários serviços do Azure, serviços da Microsoft e outros produtos SaaS (software como serviço). Usando o conector de IoT Central nos aplicativos lógicos do Azure, você pode disparar fluxos de trabalho quando uma regra é disparada no IoT Central. Você também pode usar as ações no conector de IoT Central para criar um dispositivo, atualizar as propriedades e configurações de um dispositivo ou excluir um dispositivo.

Você pode usar o conector de IoT Central no Microsoft Flow. Tanto os aplicativos lógicos do Azure quanto os Microsoft Flow são serviços de integração do designer primeiro, mas têm como alvo um público ligeiramente diferente. O Flow capacita qualquer operador do Office a criar os fluxos de trabalho de negócios de que precisam. Os aplicativos lógicos permitem que os profissionais de ti criem as integrações necessárias para conectar os dados. Compare o Flow e os aplicativos lógicos [aqui](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Saiba mais sobre como criar fluxos de trabalho com o conector de IoT Central em Microsoft Flow [aqui](howto-add-microsoft-flow.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo pago conforme o uso
- Uma conta do Azure e uma assinatura para criar e gerenciar aplicativos lógicos

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Disparar um fluxo de trabalho quando uma regra for disparada

Esta seção mostra como postar uma mensagem no Microsoft Teams quando uma regra é disparada. Você pode configurar seu fluxo de trabalho para usar outros conectores para fazer coisas como enviar um evento para o Hub de eventos, criar um novo item de trabalho DevOps do Azure ou inserir uma nova linha no SQL Server.

1. Comece [criando uma regra no IOT central](howto-create-telemetry-rules.md). Depois de salvar as condições de regra, selecione o bloco **aplicativos lógicos do Azure** como uma nova ação. Selecione **criar em portal do Azure**. Você será levado para a portal do Azure para criar um novo aplicativo lógico. Talvez seja necessário entrar em sua conta do Azure.

1. Insira as informações necessárias para criar um novo aplicativo lógico. Você pode escolher uma assinatura do Azure para provisionar seu novo aplicativo lógico no. Ele não precisa ser a mesma assinatura em que seu aplicativo IoT Central foi criado. Selecione **Criar**.

    ![Criar aplicativo lógico no portal do Azure](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. Depois que o aplicativo lógico tiver sido criado com êxito, você será automaticamente navegado para o designer de aplicativos lógicos. Selecione **aplicativo lógico em branco**. 

    ![Criar uma aplicação lógica em branco](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. No designer, pesquise "central do IOT" e escolha o gatilho **quando uma regra é acionada** . Entre no conector com a conta com a qual você entra no seu aplicativo IoT Central.

    ![Entrar no conector de IoT Central](./media/howto-build-azure-logic-apps/addtrigger.png)

1. Após a entrada bem-sucedida, você deverá ver os campos aparecerem. Selecione o **aplicativo** e a **regra** nos menus suspensos.

    ![Escolher aplicativo e regra](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Adicione uma nova ação. Procure **equipes de mensagens post** e escolha **postar uma mensagem** no conector do Microsoft Teams. Entre no conector com a conta que você usa no Microsoft Teams.

1. Na ação, escolha a **equipe** e o **canal**. Preencha o campo de **mensagem** com o que você deseja que cada mensagem diga. Você pode incluir *conteúdo dinâmico* de sua regra de IOT central, passando informações importantes, como o nome do dispositivo e o carimbo de data/hora para sua notificação.
    > [!NOTE]
    > Selecione a janela **Ver mais** texto no conteúdo dinâmico para obter valores de medição e propriedade que dispararam a regra.

    ![Ação de edição de aplicativo lógico com painel dinâmico aberto](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. Quando terminar de editar sua ação, selecione **salvar**.

1. Se voltar para seu aplicativo IoT Central, você verá que essa regra tem uma ação aplicativos lógicos do Azure na área ações.

Você sempre pode começar a criar um fluxo de trabalho usando o conector de IoT Central em aplicativos lógicos no portal do Azure. Você pode escolher a qual IoT Central aplicativo e a qual regra se conectar e ainda funciona da mesma maneira. Não é necessário iniciar na página regras de IoT Central a cada vez.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Criar, atualizar e excluir um dispositivo em um fluxo de trabalho

Ao criar um fluxo de trabalho em seu aplicativo lógico, você pode adicionar uma ação usando o conector de IoT Central. As ações disponíveis são **criar um dispositivo**, **atualizar um dispositivo**e **excluir um dispositivo**.

> [!NOTE]
> Para **atualizar um dispositivo** e **excluir um dispositivo**, você precisará de uma ID do dispositivo existente que deseja atualizar ou excluir. Você pode obter a ID do dispositivo IoT Central no **Device Explorer**

![ID do dispositivo IoT Central Gerenciador de dispositivos](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a usar Microsoft Flow para criar fluxos de trabalho, a próxima etapa sugerida é [gerenciar dispositivos](howto-manage-devices.md).
