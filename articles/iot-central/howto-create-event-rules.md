---
title: Criar e gerenciar regras de evento em seu aplicativo de IoT Central do Azure | Microsoft Docs
description: As regras de evento do Azure IoT Central permitem que você monitore seus dispositivos quase em tempo real e invoque automaticamente ações, como enviar um email, quando a regra é disparada.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c931cfbcff750d96828641669c4aaa15e7932970
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877414"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Criar uma regra de evento e configurar notificações no aplicativo IoT Central do Azure

*Este artigo aplica-se a operadores, construtores e administradores.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Você pode usar o Azure IoT Central para monitorar remotamente seus dispositivos conectados. As regras de IoT Central do Azure permitem monitorar seus dispositivos quase em tempo real e invocar ações automaticamente, como enviar um email ou disparador Microsoft Flow. Com apenas alguns cliques, você pode definir a condição para a qual monitorar os dados do dispositivo e configurar a ação correspondente. Este artigo explica como criar regras para monitorar eventos enviados pelo dispositivo.

Os dispositivos podem usar a medição de eventos para enviar eventos de dispositivo importantes ou informativos. Uma regra de evento é disparada quando o evento de dispositivo selecionado é relatado pelo dispositivo.

## <a name="create-an-event-rule"></a>Criar uma regra de eventos

Para criar uma regra de evento, o modelo de dispositivo deve ter pelo menos uma medida de evento definida. Este exemplo usa um dispositivo de máquina de venda com refrigeração que relata um evento de erro do motor do ventilador. A regra monitora o evento relatado pelo dispositivo e envia um email sempre que o evento é relatado.

1. Usando a página **modelos de dispositivo** , navegue até o modelo de dispositivo para o qual você está adicionando a regra.

1. Se ainda não tiver criado nenhuma regra, você verá a tela a seguir:

    ![Ainda não há regras](media/howto-create-event-rules/rules_landing_page1.png)

1. Na guia **regras** , selecione **+ nova regra** para ver os tipos de regras que você pode criar.

1. Escolha o bloco de **eventos** para criar uma regra de monitoramento de eventos.

    ![Tipos de regra](media/howto-create-event-rules/rule_types1.png)

1. Insira um nome que ajude a identificar a regra neste modelo de dispositivo.

1. Para habilitar imediatamente a regra para todos os dispositivos criados com base neste modelo, alterne **habilitar regra para todos os dispositivos deste modelo**.

    ![Detalhes da regra](media/howto-create-event-rules/rule_detail1.png)

    A regra se aplica automaticamente a todos os dispositivos no modelo de dispositivo.

### <a name="configure-the-rule-conditions"></a>Configurar as condições de regra

Condição define os critérios que são monitorados pela regra.

1. Escolha o **+** próximo às **condições** para adicionar uma nova condição.

1. Escolha o evento que você deseja monitorar na lista suspensa medida. Neste exemplo, o evento de **erro do motor do ventilador** foi selecionado.

   ![Condição](media/howto-create-event-rules/condition_filled_out1.png)

1. Opcionalmente, você também pode definir **Count** como **agregação** e fornecer o limite correspondente.

   - Sem agregação, a regra é disparada para cada ponto de dados de evento que atende à condição. Por exemplo, se você configurar a condição da regra para disparar quando ocorrer um evento de **erro do motor do ventilador** , a regra será disparada quase imediatamente quando o dispositivo relatar esse evento.
   - Se Count for usado como uma função de agregação, você precisará fornecer um **limite** e uma **janela de tempo agregada** sobre a qual a condição precisa ser avaliada. Nesse caso, a contagem de eventos é agregada e a regra será disparada somente se a contagem de eventos agregados corresponder ao limite.

     Por exemplo, se você deseja alertar quando há mais de três eventos de dispositivo em 5 minutos, selecione o evento e defina a função de agregação como "contagem", operador como "maior que" e "limite" como 3. Defina "período de tempo de agregação" como "5 minutos". A regra é disparada quando mais de três eventos são enviados pelo dispositivo em 5 minutos. A frequência de avaliação da regra é igual à **janela de tempo agregada**, o que significa que, neste exemplo, a regra é avaliada uma vez a cada 5 minutos.

     ![Adicionar condição de evento](media/howto-create-event-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Mais de uma medida de evento pode ser adicionada sob **Condition**. Quando várias condições forem especificadas, todas as condições deverão ser atendidas para que a regra seja disparada. Cada condição é unida por uma cláusula ' AND ' implicitamente. Ao usar Aggregate, cada medida deve ser agregada.

### <a name="configure-actions"></a>Configurar ações

Esta seção mostra como configurar ações a serem executadas quando a regra for acionada. As ações são invocadas quando todas as condições especificadas na regra são avaliadas como true.

1. Escolha o **+** próximo a **ações**. Aqui você vê a lista de ações disponíveis.

    ![Adicionar ação](media/howto-create-event-rules/add_action1.png)

1. Escolha a ação de **email** , insira um endereço de email válido no campo **para** e forneça uma observação para aparecer no corpo do email quando a regra for disparada.

    > [!NOTE]
    > Os emails são enviados somente aos usuários que foram adicionados ao aplicativo e que fizeram logon pelo menos uma vez. Saiba mais sobre o [Gerenciamento de usuários](howto-administer.md) no Azure IOT central.

   ![Configurar ação](media/howto-create-event-rules/configure_action1.png)

1. Para salvar a regra, escolha **salvar**. A regra fica ativa em alguns minutos e começa a monitorar os eventos que estão sendo enviados para seu aplicativo. Quando a condição especificada na regra corresponde, a regra dispara a ação de email configurada.

Você pode adicionar outras ações à regra, como Microsoft Flow e WebHooks. Você pode adicionar até 5 ações por regra.

- [Microsoft Flow ação](howto-add-microsoft-flow.md) para disparar um fluxo de trabalho em Microsoft Flow quando uma regra é disparada 
- [Ação](howto-create-webhooks.md) de webhook para notificar outros serviços quando uma regra é disparada

## <a name="parameterize-the-rule"></a>Parametrizar a regra

As ações também podem ser configuradas usando a **propriedade de dispositivo** como um parâmetro. Se um endereço de email for armazenado como uma propriedade de dispositivo, ele poderá ser usado quando você definir o endereço de **para** .

## <a name="delete-a-rule"></a>Eliminar uma regra

Se você não precisar mais de uma regra, exclua-a abrindo a regra e escolhendo **excluir**. A exclusão da regra a remove do modelo de dispositivo e de todos os dispositivos associados.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Habilitar ou desabilitar uma regra para um modelo de dispositivo

Navegue até o dispositivo e escolha a regra que deseja habilitar ou desabilitar. Alterne o botão **habilitar regra para todos os dispositivos deste modelo** para habilitar ou desabilitar a regra para todos os dispositivos associados ao modelo de dispositivo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Habilitar ou desabilitar uma regra para um dispositivo

Navegue até o dispositivo e escolha a regra que deseja habilitar ou desabilitar. Alterne o botão **habilitar regra para este dispositivo** para habilitar ou desabilitar a regra para o dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a criar regras em seu aplicativo IoT Central do Azure, aqui estão algumas próximas etapas:

- [Adicionar ação de Microsoft Flow em regras](howto-add-microsoft-flow.md)
- [Adicionar ação de webhook em regras](howto-create-webhooks.md)
- [Agrupar várias ações para executar a partir de uma ou mais regras](howto-use-action-groups.md)
- [Como gerenciar seus dispositivos](howto-manage-devices.md)
