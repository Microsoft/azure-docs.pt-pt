---
title: Criar fluxos de trabalho com o conector do Centro de IoT no Azure Logic Apps | Documentos da Microsoft
description: Utilizar o conector de centro de IoT no Azure Logic Apps para acionar fluxos de trabalho e criar, atualizar e eliminar dispositivos em fluxos de trabalho.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 2aced85828e5563b838af327151b2b94bcafb931
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773760"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Criar fluxos de trabalho com o conector do Centro de IoT no Azure Logic Apps

*Este tópico aplica-se para criadores e administradores.*

Utilize o Azure Logic Apps para criar dimensionáveis fluxos de trabalho automatizados que integram aplicações e dados em serviços de nuvem e sistemas no local. O Azure Logic Apps tem muitos conetores em muitos serviços do Azure, serviços da Microsoft e outros produtos de Software-como-serviço (SaaS). Utilizar o conector do Centro de IoT no Azure Logic Apps, pode acionar fluxos de trabalho quando é acionada uma regra no Centro de IoT. Também pode utilizar as ações no conector do Centro de IoT para criar um dispositivo, atualizar as propriedades e definições de um dispositivo ou eliminar um dispositivo. 

Pode utilizar o conector do Centro de IoT no Microsoft Flow. Azure Logic Apps e Microsoft Flow são serviços de integração de designer em primeiro lugar, mas ligeiramente diferente público-alvo. Flow capacita qualquer trabalhador de escritório para compilar os fluxos de trabalho de empresas que precisam. O Logic Apps capacita os profissionais de TI para criar integrações que necessitam para ligar a dados. Comparar Flow e Logic Apps [aqui](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Saiba mais sobre como criar fluxos de trabalho com o conector do Centro de IoT no Microsoft Flow [aqui](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). 

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo de pay as you go
- Uma conta do Azure e subscrição para criar e gerir aplicações lógicas

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Acionar um fluxo de trabalho quando é acionada uma regra

Esta secção mostra-lhe como publicar uma mensagem no Microsoft Teams quando é acionada uma regra. Pode configurar o fluxo de trabalho utilizar outros conectores para fazer coisas como enviar um evento ao seu hub de eventos, crie um novo item de trabalho de DevOps do Azure ou inserir uma nova linha no SQL server.

1. Comece por [criar uma regra no Centro de IoT](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). Depois de guardar as condições de regra, clique nas **do Azure Logic Apps** mosaico como uma nova ação. Clique em **criar no portal do Azure**. Será levado para o portal do Azure para criar uma nova aplicação lógica. Terá de iniciar sessão na sua conta do Azure.

1. Introduza as informações necessárias para criar uma nova aplicação lógica. Pode escolher uma subscrição do Azure para aprovisionar a sua nova aplicação lógica em. Ele não tem de ser a mesma subscrição que a aplicação de IoT Central foi criada no. Clique em **Criar**.

    ![Criar aplicação lógica no portal do Azure](./media/howto-build-azure-logic-apps-experimental/createinazureportal.png)

1. Depois da aplicação lógica foi criada com êxito, será automaticamente direcionado para o estruturador de aplicações lógicas. Clique em **aplicação lógica em branco**. 

    ![Criar uma aplicação lógica em branco](./media/howto-build-azure-logic-apps-experimental/blanklogicapp.png)

1. No estruturador, procure "iot central" e escolha o **quando uma regra é disparada** acionador. Inicie sessão no conector com a conta que iniciar sessão na aplicação com o IoT Central. 

    ![Inicie sessão no conector do Centro de IoT](./media/howto-build-azure-logic-apps-experimental/addtrigger.png)

1. Após o início de sessão com êxito, deverá ver os campos são apresentados. Selecione o **aplicativo** e **regra** das listas pendentes.

    ![Escolher a aplicação e regra](./media/howto-build-azure-logic-apps-experimental/pickappandrule.png)

1. Adicione uma nova ação. Procure **postar as equipes de mensagem** e escolha **publicar uma mensagem** partir do conector do Microsoft Teams. Inicie sessão no conector com a conta que utiliza no Microsoft Teams. 

1. Na ação, escolha o **equipe** e **canal**. Preencha os **mensagem** campo com o que deseja que cada mensagem a dizer. Pode incluir *conteúdo dinâmico* da sua regra de IoT Central, passando informações importantes, como o nome do dispositivo e timestamp para a notificação.
    > [!NOTE]
    > Clique em "Ver mais" texto na janela de conteúdo dinâmico para obter a medição e valores de propriedade que disparou a regra.

    ![Ação edição da aplicação lógica com o painel dinâmico abrir](./media/howto-build-azure-logic-apps-experimental/buildworkflow.png)

1. Quando tiver concluído a ação de edição, clique em **guardar**.

1. Se voltar à sua aplicação IoT Central, verá que esta regra tem uma ação do Azure Logic Apps, sob a área de ações.

Sempre que pode começar a criar um fluxo de trabalho com o conector de IoT Central no Logic Apps no Portal do Azure. Em seguida, pode escolher que aplicação IoT Central e quais regras para ligar a e, ainda funcionará da mesma forma. Não é necessário para iniciar a partir da página de regras do Centro de IoT cada vez.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Criar, atualizar e eliminar um dispositivo num fluxo de trabalho

Quando estiver criando um fluxo de trabalho na sua aplicação lógica, pode adicionar uma ação com o conector do Centro de IoT. Encontrará a **criar um dispositivo**, **um dispositivo de atualização**, e **eliminar um dispositivo**.

> [!NOTE]
> Para **um dispositivo de atualização** e **eliminar um dispositivo**, será necessário um ID do dispositivo existente que pretende atualizar ou eliminar. Pode obter o ID do dispositivo IoT Central no **Device Explorer**

![ID de dispositivo do Explorador de dispositivo do IoT Central](./media/howto-build-azure-logic-apps-experimental/iotcdeviceid.png)


## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu como utilizar o Microsoft Flow para criar fluxos de trabalho, a próxima etapa sugerida é [gerir dispositivos](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
