---
title: Criar fluxos de trabalho com o conector do Centro de IoT no Azure Logic Apps | Documentos da Microsoft
description: Utilizar o conector de centro de IoT no Azure Logic Apps para acionar fluxos de trabalho e criar, atualizar e eliminar dispositivos em fluxos de trabalho.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 635c8d0f149895798eece8cf3b48712ab74374ea
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759887"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Criar fluxos de trabalho com o conector do Centro de IoT no Azure Logic Apps

*Este tópico aplica-se para criadores e administradores.*

Utilize o Azure Logic Apps para criar dimensionáveis fluxos de trabalho automatizados que integram aplicações e dados em serviços de nuvem e sistemas no local. O Azure Logic Apps tem muitos conetores em muitos serviços do Azure, serviços da Microsoft e outros produtos de Software-como-serviço (SaaS). Utilizar o conector do Centro de IoT no Azure Logic Apps, pode acionar fluxos de trabalho quando é acionada uma regra no Centro de IoT. Também pode utilizar as ações no conector do Centro de IoT para criar um dispositivo, atualizar as propriedades e definições de um dispositivo ou eliminar um dispositivo.

Pode utilizar o conector do Centro de IoT no Microsoft Flow. Azure Logic Apps e Microsoft Flow são serviços de integração de designer em primeiro lugar, mas ligeiramente diferente público-alvo. Flow capacita qualquer trabalhador de escritório para compilar os fluxos de trabalho de empresas que precisam. O Logic Apps capacita os profissionais de TI para criar integrações que necessitam para ligar a dados. Comparar Flow e Logic Apps [aqui](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Saiba mais sobre como criar fluxos de trabalho com o conector do Centro de IoT no Microsoft Flow [aqui](howto-add-microsoft-flow.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo de pay as you go
- Uma conta do Azure e subscrição para criar e gerir aplicações lógicas

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Acionar um fluxo de trabalho quando é acionada uma regra

Esta secção mostra-lhe como publicar uma mensagem no Microsoft Teams quando é acionada uma regra. Pode configurar o fluxo de trabalho utilizar outros conectores para fazer coisas como enviar um evento ao seu hub de eventos, crie um novo item de trabalho de DevOps do Azure ou inserir uma nova linha no SQL server.

1. Comece por [criar uma regra no Centro de IoT](howto-create-telemetry-rules.md). Depois de guardar as condições de regra, selecione o **do Azure Logic Apps** mosaico como uma nova ação. Selecione **criar no portal do Azure**. Será direcionado para o portal do Azure para criar uma nova aplicação lógica. Terá de iniciar sessão na sua conta do Azure.

1. Introduza as informações necessárias para criar uma nova aplicação lógica. Pode escolher uma subscrição do Azure para aprovisionar a sua nova aplicação lógica em. Ele não tem de ser a mesma subscrição que a aplicação de IoT Central foi criada no. Selecione **Criar**.

    ![Criar aplicação lógica no portal do Azure](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. Depois da aplicação lógica foi criada com êxito, são automaticamente direcionado para o estruturador de aplicações lógicas. Selecione **aplicação lógica em branco**. 

    ![Criar uma aplicação lógica em branco](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. No estruturador, procure "iot central" e escolha o **quando uma regra é disparada** acionador. Inicie sessão no conector com a conta que iniciar sessão na aplicação com o IoT Central.

    ![Inicie sessão no conector do Centro de IoT](./media/howto-build-azure-logic-apps/addtrigger.png)

1. Após o início de sessão com êxito, deverá ver os campos são apresentados. Selecione o **aplicativo** e **regra** das listas pendentes.

    ![Escolher a aplicação e regra](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Adicione uma nova ação. Procure **postar as equipes de mensagem** e escolha **publicar uma mensagem** partir do conector do Microsoft Teams. Inicie sessão no conector com a conta que utiliza no Microsoft Teams.

1. Na ação, escolha o **equipe** e **canal**. Preencha os **mensagem** campo com o que deseja que cada mensagem a dizer. Pode incluir *conteúdo dinâmico* da sua regra de IoT Central, passando informações importantes, como o nome do dispositivo e timestamp para a notificação.
    > [!NOTE]
    > Selecione o **ver mais** texto na janela de conteúdo dinâmico para obter valores de propriedade e de medição que disparou a regra.

    ![Ação edição da aplicação lógica com o painel dinâmico abrir](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. Quando tiver concluído a ação de edição, selecione **guardar**.

1. Se voltar à sua aplicação IoT Central, verá que esta regra tem uma ação do Azure Logic Apps, sob a área de ações.

Sempre que pode começar a criar um fluxo de trabalho com o conector de IoT Central no Logic Apps no Portal do Azure. Em seguida, pode escolher que aplicação IoT Central e quais regras para ligar a e, ainda funciona da mesma forma. Não é necessário para iniciar a partir da página de regras do Centro de IoT cada vez.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Criar, atualizar e eliminar um dispositivo num fluxo de trabalho

Quando estiver criando um fluxo de trabalho na sua aplicação lógica, pode adicionar uma ação com o conector do Centro de IoT. As ações disponíveis são os **criar um dispositivo**, **um dispositivo de atualização**, e **eliminar um dispositivo**.

> [!NOTE]
> Para **um dispositivo de atualização** e **eliminar um dispositivo**, será necessário um ID do dispositivo existente que pretende atualizar ou eliminar. Pode obter o ID do dispositivo IoT Central no **Device Explorer**

![ID de dispositivo do Explorador de dispositivo do IoT Central](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar o Microsoft Flow para criar fluxos de trabalho, a próxima etapa sugerida é [gerir dispositivos](howto-manage-devices.md).
