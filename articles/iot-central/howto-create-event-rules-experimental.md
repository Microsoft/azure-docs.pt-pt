---
title: Criar e gerir regras de evento na sua aplicação do Azure IoT Central | Documentos da Microsoft
description: As regras de eventos do Azure IoT Central permitem-lhe monitorizar os seus dispositivos em tempo real e invocar ações, tal como enviar um e-mail quando a regra for acionada automaticamente.
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7ed7ad6529035fbea018bdf1515947d37dfbbece
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308156"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Criar uma regra de evento e configure as notificações na sua aplicação do Azure IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

Pode utilizar o Azure IoT Central para monitorizar remotamente os seus dispositivos ligados. Regras de centro de IoT do Azure permitem-lhe monitorizar os seus dispositivos em tempo real e automaticamente invocar ações, tal como enviar um e-mail ou acionar o Microsoft Flow. Em apenas alguns cliques, pode definir a condição para a qual monitorizar os dados de dispositivo e configure a ação correspondente. Este artigo explica como criar regras para monitorar eventos enviados pelo dispositivo.

Dispositivos podem utilizar a medição de eventos para enviar eventos de dispositivo importante ou informativos. Uma regra de evento é acionada quando o evento de dispositivo selecionado é comunicado pelo dispositivo.

## <a name="create-an-event-rule"></a>Criar uma regra de eventos

Para criar uma regra de evento, o modelo do dispositivo tem de ter definida de medição, pelo menos, um evento. Este exemplo utiliza um dispositivo de refrigerated de venda automática de máquina que reporta um evento de erro motor ventoinha. A regra monitoriza o evento comunicado pelo dispositivo e envia um e-mail sempre que o evento é comunicado.

1. Utilizar o **modelos de dispositivos** página, navegue para o modelo de dispositivo para o qual está a adicionar a regra para.

1. Se ainda não criou quaisquer regras, verá o ecrã seguinte:

    ![Ainda não existem regras](media/howto-create-event-rules-experimental/Rules_Landing_Page.png)

1. Sobre o **regras** separador, selecione **+ nova regra** para ver os tipos de regras, pode criar.

1. Escolha o **evento** mosaico para criar uma regra de monitorização de eventos.

    ![Tipos de regra](media/howto-create-event-rules-experimental/Rule_Types.png)

1. Introduza um nome que ajuda a identificar a regra neste modelo de dispositivo.

1. Para ativar imediatamente a regra para todos os dispositivos criadas através deste modelo, alternar **ativar regra para todos os dispositivos deste modelo**.

    ![Detalhes da regra](media/howto-create-event-rules-experimental/Rule_Detail.png)

    A regra aplica-se automaticamente a todos os dispositivos sob o modelo de dispositivo.

### <a name="configure-the-rule-conditions"></a>Configurar as condições de regra

Condição define os critérios que é monitorizado pela regra.

1. Escolha o **+** junto a **condições** para adicionar uma nova condição.

1. Escolha o evento que pretende monitorizar na lista pendente medição. Neste exemplo, **erro de Motor de ventoinha** evento tiver sido selecionado.

   ![Condição](media/howto-create-event-rules-experimental/Condition_Filled_Out.png)

1. Opcionalmente, também pode definir **contagem** como **agregação** e forneça o limiar correspondente.

    - Sem agregação, a regra é acionada para cada ponto de dados de evento que atenda à condição. Por exemplo, se configurar a regra de condição para acionar quando uma **erro de Motor de ventoinha** evento ocorre, em seguida, a regra for acionada quase que imediatamente quando o dispositivo comunica que o evento.
    - Se a contagem é utilizada como uma função de agregação, então tem de fornecer um **limiar** e uma **janela de tempo agregado** ao longo do que a condição tem de ser avaliada. Neste caso, a contagem de eventos é agregada e a regra é acionada apenas se a contagem de eventos agregado corresponde ao limiar.

    Por exemplo, se deseja alertar quando há mais de três eventos de dispositivo dentro de 5 minutos, em seguida, selecione o evento e definir a função de agregação como "contagem", o operador como "maior que" e "limiar" como 3. Definir "Agregação período de tempo" como "5 minutos". A regra é acionada quando mais de três eventos são enviados pelo dispositivo dentro de 5 minutos. A frequência de avaliação da regra é igual a **janela de tempo agregado**, que significa que, neste exemplo, a regra é avaliada uma vez a cada 5 minutos.

    ![Adicionar condição de eventos](media/howto-create-event-rules-experimental/Aggregate_Condition_Filled_Out.png)

    >[!NOTE]
    >Mais do que uma medida de eventos pode ser adicionada sob **condição**. Quando são especificadas várias condições, todas as condições têm de ser cumpridas para a regra acionar. Cada condição obtém associada implicitamente por uma cláusula 'E'. Ao utilizar o agregado, cada medida tem de ser agregada.

### <a name="configure-actions"></a>Configurar ações

Esta secção mostra como configurar as ações a tomar quando a regra é disparada. Ações são invocadas quando todas as condições especificadas na regra avaliam como verdadeiro.

1. Escolha o **+** junto a **ações**. Aqui ver a lista de ações disponíveis.

    ![Adicionar ação](media/howto-create-event-rules-experimental/Add_Action.png)

1. Escolha o **E-Mail** ação, introduza um endereço de e-mail válido no **para** campo e fornecer uma nota a aparecer no corpo da mensagem de e-mail quando a regra for acionada.

    > [!NOTE]
    > Os e-mails são enviados apenas para os utilizadores que foram adicionados à aplicação e tem iniciado sessão pelo menos uma vez. Saiba mais sobre [gestão de utilizadores](howto-administer-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) no Azure IoT Central.

   ![Configurar ação](media/howto-create-event-rules-experimental/Configure_Action.png)

1. Para guardar a regra, escolha **guardar**. A regra for executada dentro de alguns minutos e inicia a monitorização de eventos a ser enviados ao seu aplicativo. Quando a condição especificada na regra corresponde, a regra for acionada a ação de e-mail configurado.

Pode adicionar outras ações para a regra, como o Microsoft Flow e webhooks. Pode adicionar até 5 ações por regra.

- [Ação do Microsoft Flow](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) para disparar um fluxo de trabalho no Microsoft Flow, quando é acionada uma regra 
- [Ação do Webhook](howto-create-webhooks-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) para notificar outros serviços, quando é acionada uma regra

## <a name="parameterize-the-rule"></a>Parametrizar a regra

Ações também podem ser configuradas usando **propriedade do dispositivo** como um parâmetro. Se um endereço de e-mail é armazenado como uma propriedade do dispositivo, em seguida, ele pode ser usado quando define a **para** endereço.

## <a name="delete-a-rule"></a>Eliminar uma regra

Se já não precisar de uma regra, elimine-o ao abrir a regra e escolher **eliminar**. A eliminar a regra remove do modelo de dispositivo e todos os dispositivos associados.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Ativar ou desativar uma regra para um modelo de dispositivo

Navegue para o dispositivo e selecione a regra que pretende ativar ou desativar. Ativar/desativar a **ativar regra para todos os dispositivos deste modelo** botão para ativar ou desativar a regra para todos os dispositivos que estão associados ao modelo de dispositivo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Ativar ou desativar uma regra para um dispositivo

Navegue para o dispositivo e selecione a regra que pretende ativar ou desativar. Ativar/desativar a **ativar regra para este dispositivo** botão para ativar ou desativar a regra para esse dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como criar regras na sua aplicação do Azure IoT Central, aqui estão alguns passo seguinte:

- [Adicionar ação do Microsoft Flow nas regras](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [Adicionar ação do Webhook nas regras](howto-create-webhooks-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [Como gerir os seus dispositivos](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
