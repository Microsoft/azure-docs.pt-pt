---
title: Construir fluxos de trabalho com o conector IoT Central em Aplicações Lógicas Azure  Microsoft Docs
description: Utilize o conector IoT Central em Aplicações Lógicas Azure para desencadear fluxos de trabalho e criar, atualizar e eliminar dispositivos em fluxos de trabalho.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 3625d7374090bca73e2f054f7da4b58e951e7719
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990271"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Construir fluxos de trabalho com o conector IoT Central em Aplicações Lógicas Azure

*Este tópico se aplica a construtores e administradores.*

Utilize aplicativos da Lógica Azure para construir fluxos de trabalho automatizados escaláveis que integram aplicações e dados através de serviços na nuvem e sistemas no local. As Aplicações Lógicas Azure têm muitos conectores em muitos serviços Azure, serviços microsoft e outros produtos software-As-A-Service (SaaS). Utilizando o conector IoT Central em Aplicações Lógicas Azure, pode desencadear fluxos de trabalho quando uma regra é desencadeada na IoT Central. Também pode utilizar as ações no conector IoT Central para criar um dispositivo, atualizar as propriedades e configurações de um dispositivo ou apagar um dispositivo.

Pode utilizar o conector IoT Central no Microsoft Flow. Tanto as Aplicações Lógicas Azure como o Microsoft Flow são serviços de integração de primeiro si, mas visam públicos ligeiramente diferentes. O fluxo capacita qualquer trabalhador de escritório para construir os fluxos de trabalho de negócios de que precisam. A Logic Apps capacita os IT Pros para construir as integrações de que necessitam para ligar dados. Compare [aqui](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs)as Aplicações De Fluxo e Lógica . Saiba como construir fluxos de trabalho com o conector IoT Central no Microsoft Flow [aqui](howto-add-microsoft-flow.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma aplicação criada usando um plano de preços padrão
- Uma conta Azure e subscrição para criar e gerir aplicações Lógica

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Desencadear um fluxo de trabalho quando uma regra é desencadeada

Esta secção mostra-lhe como publicar uma mensagem nas Equipas da Microsoft quando uma regra é desencadeada. Pode configurar o seu fluxo de trabalho para utilizar outros conectores para fazer coisas como enviar um evento para o seu hub de eventos, criar um novo item de trabalho do Azure DevOps ou inserir uma nova linha no servidor SQL.

1. Comece por [criar uma regra na IoT Central.](howto-create-telemetry-rules.md) Depois de guardar as condições de regra, selecione o azulejo **Azure Logic Apps** como uma nova ação. Selecione **Criar no portal Azure**. Você é levado para o portal Azure para criar uma nova aplicação lógica. Pode precisar assinar a sua conta Azure.

1. Introduza as informações necessárias para criar uma nova aplicação lógica. Pode escolher uma subscrição Azure para fornecer a sua nova aplicação lógica. Não tem de ser a mesma subscrição em que a sua aplicação IoT Central foi criada. Selecione **Criar**.

    ![Criar app lógica no portal Azure](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. Depois de a sua aplicação lógica ter sido criada com sucesso, é automaticamente navegada para o Logic Apps Designer. Selecione **App lógica em branco**. 

    ![Criar uma aplicação lógica em branco](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. No designer, procure "iot central", e escolha o **Gatilho quando uma regra é disparada.** Inscreva-se no conector com a conta com a que assina na sua aplicação IoT Central.

    ![Assine no conector Central IoT](./media/howto-build-azure-logic-apps/addtrigger.png)

1. Depois de um sucesso de inscrição, deve ver os campos aparecerem. Selecione a **Aplicação** e **regra** a partir dos dropdowns.

    ![Escolha aplicação e regra](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Adicione uma nova ação. Procure por **equipas de mensagens post** e escolha Post uma **mensagem** do conector Microsoft Teams. Assine no conector com a conta que utiliza nas Equipas microsoft.

1. Na ação, escolha a **Equipa** e **o Canal.** Preencha o campo **mensagem** com o que quer que cada mensagem diga. Pode incluir *conteúdo dinâmico* a partir da sua regra IoT Central, transmitindo informações importantes, como o nome do dispositivo e o carimbo temporal para a sua notificação.
    > [!NOTE]
    > Selecione o texto **Ver mais** texto na janela de conteúdo Dinâmico para obter valores de medição e propriedade que desencadearam a regra.

    ![Ação de edição de aplicações lógicas com painel dinâmico aberto](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. Quando terminar de editar a sua ação, selecione **Guardar**.

1. Se voltar à sua aplicação IoT Central, verá que esta regra tem uma ação de Aplicações Azure Logic na área de Ações.

Pode sempre começar a construir um fluxo de trabalho utilizando o conector IoT Central em Aplicações Lógicas no portal Azure. Em seguida, pode escolher qual aplicação IoT Central e qual a regra a que se ligar, e ainda funciona da mesma forma. Não há necessidade de começar sempre a partir da página de regras da IoT Central.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Criar, atualizar e eliminar um dispositivo num fluxo de trabalho

Quando estiver a construir um fluxo de trabalho na sua aplicação lógica, pode adicionar uma ação utilizando o conector IoT Central. As ações disponíveis são criar **um dispositivo,** **atualizar um dispositivo**e apagar um **dispositivo**.

> [!NOTE]
> Para **atualizar um dispositivo** e eliminar um **dispositivo,** necessitará de uma identificação do dispositivo existente que pretende atualizar ou eliminar. Pode obter a identificação do dispositivo IoT Central no **Device Explorer**

![ID do dispositivo de explorador de dispositivos ioT central](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar o Microsoft Flow para construir fluxos de trabalho, o próximo passo sugerido é [gerir os dispositivos](howto-manage-devices.md).
