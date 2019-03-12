---
title: Criar e gerir regras de telemetria na sua aplicação do Azure IoT Central | Documentos da Microsoft
description: Regras de telemetria do Centro de IoT do Azure permitem-lhe monitorizar os seus dispositivos em tempo real e invocar ações, tal como enviar um e-mail quando a regra for acionada automaticamente.
author: ankitgupta
ms.author: ankitgup
ms.date: 02/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4668ffd30742f81552cd29f6cdba4c0f82549687
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773508"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Criar uma regra de telemetria e configurar notificações na sua aplicação do Azure IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

Pode utilizar o Azure IoT Central para monitorizar remotamente os seus dispositivos ligados. Regras de centro de IoT do Azure permitem-lhe monitorizar os seus dispositivos em tempo real e automaticamente invocar ações, tal como enviar um e-mail ou acionar o Microsoft Flow. Em apenas alguns cliques, pode definir a condição para a qual monitorizar os dados de dispositivo e configure a ação correspondente. Este artigo explica como criar regras para monitorizar a telemetria enviada pelo dispositivo.

Dispositivos podem utilizar a medição de telemetria para enviar dados numéricos do dispositivo. Uma regra de telemetria é acionada quando a telemetria do dispositivo selecionado ultrapassar um limiar especificado.

## <a name="create-a-telemetry-rule"></a>Criar uma regra de telemetria

Para criar uma regra de telemetria, o modelo do dispositivo tem de ter pelo menos uma medida de telemetria definida. Este exemplo utiliza um dispositivo de refrigerated de venda automática de máquina que envia telemetria de temperatura e humidade. A regra monitoriza a temperatura comunicada pelo dispositivo e envia um e-mail quando ele for superior a 80 graus.

1. Utilizar o **modelos de dispositivos** página, navegue para o modelo de dispositivo para o qual está a adicionar a regra para.

1. Se ainda não criou quaisquer regras, verá o ecrã seguinte:

    ![Ainda não existem regras](media/howto-create-telemetry-rules/Rules_Landing_Page.png)

1. Sobre o **regras** separador, selecione **+ nova regra** para ver os tipos de regras, pode criar.

1. Selecione **telemetria** para criar uma regra para monitorizar a telemetria do dispositivo.

    ![Tipos de regra](media/howto-create-telemetry-rules/Rule_Types.png)

1. Introduza um nome que ajuda a identificar a regra neste modelo de dispositivo.

1. Para ativar imediatamente a regra para todos os dispositivos que criou para este modelo, alternar **ativar regra para todos os dispositivos para este modelo**.

   ![Detalhes da regra](media/howto-create-telemetry-rules/Rule_Detail.png)

    A regra aplica-se automaticamente a todos os dispositivos sob o modelo de dispositivo.

### <a name="configure-the-rule-conditions"></a>Configurar as condições de regra

Condição define os critérios que é monitorizado pela regra.

1. Selecione **+** junto a **condições** para adicionar uma nova condição.

1. Selecione a telemetria que pretende monitorizar a partir da **medição** lista pendente.

1. Em seguida, escolha **agregação**, **operador**e fornecer uma **limiar** valor.
    - Agregação é opcional. Sem agregação, a regra é acionada para cada ponto de dados de telemetria que atenda à condição. Por exemplo, se a regra está configurada para o acionador quando a temperatura for superior a 80, em seguida, a regra é acionada quase instantaneamente quando o dispositivo comunica temperatura > 80.
    - Se uma função de agregação, como média, Mín, Máx, contagem for escolhida, em seguida, o utilizador tem de fornecer uma **janela de tempo agregado** ao longo do que a condição tem de ser avaliada. Por exemplo, se definir o período como "5 minutos" e a sua regra de procura de temperatura média superior a 80, a regra é acionada quando a temperatura média for superior a 80, para, pelo menos, 5 minutos. A frequência de avaliação da regra é igual a **janela de tempo agregado**, que significa que, neste exemplo, a regra é avaliada uma vez a cada 5 minutos.

    ![Condição](media/howto-create-telemetry-rules/Aggregate_Condition_Filled_Out.png)

    >[!NOTE]
    >É possível adicionar mais do que uma medida de telemetria em **condição**. Quando são especificadas várias condições, todas as condições têm de ser cumpridas para a regra acionar. Cada conditon obtém associado implicitamente por uma cláusula 'E'. Ao utilizar o agregado, cada medida tem de ser agregada.

### <a name="configure-actions"></a>Configurar ações

Esta secção mostra como configurar as ações a tomar quando a regra é disparada. Ações são invocadas quando todas as condições especificadas na regra avaliam como verdadeiro.

1. Escolha o **+** junto a **ações**. Aqui ver a lista de ações disponíveis.  

    ![Adicionar ação](media/howto-create-telemetry-rules/Add_Action.png)

1. Escolha o **E-Mail** ação, introduza um endereço de e-mail válido no **para** campo e fornecer uma nota a aparecer no corpo da mensagem de e-mail quando a regra for acionada.

    > [!NOTE]
    > Os e-mails são enviados apenas para os utilizadores que foram adicionados à aplicação e tem iniciado sessão pelo menos uma vez. Saiba mais sobre [gestão de utilizadores](howto-administer.md) no Azure IoT Central.

   ![Configurar ação](media/howto-create-telemetry-rules/Configure_Action.png)

1. Para guardar a regra, escolha **guardar**. A regra for executada dentro de alguns minutos e inicia a monitorização de envio de telemetria para seu aplicativo. Quando for cumprida a condição especificada na regra, a regra for acionada a ação de e-mail configurado.

Pode adicionar outras ações para a regra, como o Microsoft Flow e webhooks. Pode adicionar até 5 ações por regra.

- [Ação do Microsoft Flow](howto-add-microsoft-flow.md) para disparar um fluxo de trabalho no Microsoft Flow, quando é acionada uma regra 
- [Ação do Webhook](howto-create-webhooks.md) para notificar outros serviços, quando é acionada uma regra

## <a name="parameterize-the-rule"></a>Parametrizar a regra

Regras podem derivar determinados valores partir **propriedades do dispositivo** como parâmetros. O uso de parâmetros é útil em cenários em que os limiares de telemetria variam para diferentes dispositivos. Ao criar a regra, escolha uma propriedade de dispositivo que especifica o limiar, tal como **Ideal de limiar máximo**, em vez de fornecer um valor absoluto, como 80 graus. Quando a regra for executada, corresponde a telemetria do dispositivo com o valor definido na propriedade do dispositivo.

O uso de parâmetros é uma forma eficaz para reduzir o número de regras para gerir por modelo de dispositivo.

Ações também podem ser configuradas usando **propriedade do dispositivo** como um parâmetro. Se um endereço de e-mail é armazenado como uma propriedade, em seguida, ele pode ser usado quando define a **para** endereço.

## <a name="delete-a-rule"></a>Eliminar uma regra

Se já não precisar de uma regra, elimine-o ao abrir a regra e escolher **eliminar**. A eliminar a regra remove do modelo de dispositivo e todos os dispositivos associados.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Ativar ou desativar uma regra para um modelo de dispositivo

Navegue para o dispositivo e selecione a regra que pretende ativar ou desativar. Ativar/desativar a **ativar regra para todos os dispositivos deste modelo** botão na regra para ativar ou desativar a regra para todos os dispositivos que estão associados ao modelo de dispositivo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Ativar ou desativar uma regra para um dispositivo

Navegue para o dispositivo e selecione a regra que pretende ativar ou desativar. Ativar/desativar a **ativar regra para este dispositivo** botão para ativar ou desativar a regra para esse dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como criar regras na sua aplicação do Azure IoT Central, aqui estão alguns passo seguinte:

- [Adicionar ação do Microsoft Flow nas regras](howto-add-microsoft-flow.md)
- [Adicionar ação do Webhook nas regras](howto-create-webhooks.md)
- [Como gerir os seus dispositivos](howto-manage-devices.md)
