---
title: Usar regras de telemetria em seu aplicativo de IoT Central do Azure | Microsoft Docs
description: As regras de telemetria do Azure IoT Central permitem que você monitore seus dispositivos quase em tempo real e invoque automaticamente ações, como enviar um email, quando a regra é disparada.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0b24c064424b00fa9acb96b03c0a3c5ca69f67f2
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264405"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Criar uma regra de telemetria e configurar notificações no aplicativo IoT Central do Azure

*Este artigo aplica-se a operadores, construtores e administradores.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Você pode usar o Azure IoT Central para monitorar remotamente seus dispositivos conectados. As regras de IoT Central do Azure permitem monitorar seus dispositivos quase em tempo real e invocar ações automaticamente, como enviar um email ou disparador Microsoft Flow. Com apenas alguns cliques, você pode definir a condição para a qual monitorar os dados do dispositivo e configurar a ação correspondente. Este artigo explica como criar regras para monitorar a telemetria enviada pelo dispositivo.

Os dispositivos podem usar a medição de telemetria para enviar dados numéricos do dispositivo. Uma regra de telemetria é disparada quando a telemetria do dispositivo selecionado cruza um limite especificado.

## <a name="create-a-telemetry-rule"></a>Criar uma regra de telemetria

Para criar uma regra de telemetria, o modelo de dispositivo deve ter pelo menos uma medida de telemetria definida. Este exemplo usa um dispositivo de máquina de venda com refrigeração que envia a telemetria de temperatura e umidade. A regra monitora a temperatura relatada pelo dispositivo e envia um email quando fica acima de 70&deg; F.

1. Usando a página **modelos de dispositivo** , navegue até o modelo de dispositivo para o qual você está adicionando a regra.

1. Se ainda não tiver criado nenhuma regra, você verá a tela a seguir:

    ![Ainda não existem regras](media/howto-create-telemetry-rules/rules_landing_page1.png)

1. Na guia **regras** , selecione **+ nova regra** para ver os tipos de regras que você pode criar.

1. Selecione **telemetria** para criar uma regra para monitorar a telemetria do dispositivo.

    ![Tipos de regra](media/howto-create-telemetry-rules/rule_types1.png)

1. Insira um nome que ajude a identificar a regra neste modelo de dispositivo.

1. Para habilitar imediatamente a regra para todos os dispositivos criados para este modelo, alterne **habilitar regra para todos os dispositivos deste modelo**.

   ![Detalhes da regra](media/howto-create-telemetry-rules/rule_detail1.png)

    A regra se aplica automaticamente a todos os dispositivos no modelo de dispositivo.

### <a name="configure-the-rule-conditions"></a>Configurar as condições de regra

Condição define os critérios que são monitorados pela regra.

1. Selecione **+** ao lado de **condições** para adicionar uma nova condição.

1. Selecione a telemetria que você deseja monitorar na lista suspensa **medida** .

1. Em seguida, escolha **agregação**, **operador**e forneça um valor de **limite** .
   - A agregação é opcional. Sem agregação, a regra é disparada para cada ponto de dados de telemetria que atende à condição. Por exemplo, se a regra estiver configurada para disparar quando a temperatura estiver acima de 70&deg; F, a regra será disparada quase instantaneamente quando o dispositivo relatar a temperatura > 70.
   - Se uma função de agregação como Average, min, Max e Count for escolhida, o usuário deverá fornecer uma **janela de tempo agregada** sobre a qual a condição precisa ser avaliada. Por exemplo, se você definir o período como "5 minutos" e a regra procurar a temperatura média acima de 70, a regra será disparada quando a temperatura média estiver acima de 70&deg; F por pelo menos 5 minutos. A frequência de avaliação da regra é igual à **janela de tempo agregada**, o que significa que, neste exemplo, a regra é avaliada uma vez a cada 5 minutos.

     ![Condição](media/howto-create-telemetry-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Mais de uma medida de telemetria pode ser adicionada sob a **condição**. Quando várias condições forem especificadas, todas as condições deverão ser atendidas para que a regra seja disparada. Cada condição é unida por uma cláusula ' AND ' implicitamente. Ao usar Aggregate, cada medida deve ser agregada.

### <a name="configure-actions"></a>Configurar ações

Esta seção mostra como configurar ações a serem executadas quando a regra for acionada. As ações são invocadas quando todas as condições especificadas na regra são avaliadas como true.

1. Escolha o **+** ao lado de **ações**. Aqui você vê a lista de ações disponíveis.  

    ![Adicionar ação](media/howto-create-telemetry-rules/add_action1.png)

1. Escolha a ação de **email** , insira um endereço de email válido no campo **para** e forneça uma observação para aparecer no corpo do email quando a regra for disparada.

    > [!NOTE]
    > Os emails são enviados somente aos usuários que foram adicionados ao aplicativo e que fizeram logon pelo menos uma vez. Saiba mais sobre o [Gerenciamento de usuários](howto-administer.md) no Azure IOT central.

   ![Configurar ação](media/howto-create-telemetry-rules/configure_action1.png)

1. Para salvar a regra, escolha **salvar**. A regra fica ativa em alguns minutos e inicia o monitoramento da telemetria que está sendo enviada ao seu aplicativo. Quando a condição especificada na regra é atendida, a regra dispara a ação de email configurada.

Você pode adicionar outras ações à regra, como Microsoft Flow e WebHooks. Você pode adicionar até 5 ações por regra.

- [Microsoft Flow ação](howto-add-microsoft-flow.md) para disparar um fluxo de trabalho em Microsoft Flow quando uma regra é disparada 
- [Ação de webhook](howto-create-webhooks.md) para notificar outros serviços quando uma regra é disparada

## <a name="parameterize-the-rule"></a>Parametrizar a regra

As regras podem derivar determinadas valores de **Propriedades de dispositivo** como parâmetros. O uso de parâmetros é útil em cenários nos quais os limites de telemetria variam para diferentes dispositivos. Ao criar a regra, escolha uma propriedade de dispositivo que especifique o limite, como o **limite máximo ideal**, em vez de fornecer um valor absoluto, como 70&deg; F. Quando a regra é executada, ela corresponde à telemetria do dispositivo com o valor definido na Propriedade do dispositivo.

O uso de parâmetros é uma maneira eficaz de reduzir o número de regras para gerenciar por modelo de dispositivo.

As ações também podem ser configuradas usando a **propriedade de dispositivo** como um parâmetro. Se um endereço de email for armazenado como uma propriedade, ele poderá ser usado quando você definir o endereço de **para** .

## <a name="delete-a-rule"></a>Eliminar uma regra

Se você não precisar mais de uma regra, exclua-a abrindo a regra e escolhendo **excluir**. A exclusão da regra a remove do modelo de dispositivo e de todos os dispositivos associados.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Habilitar ou desabilitar uma regra para um modelo de dispositivo

Navegue até o dispositivo e escolha a regra que deseja habilitar ou desabilitar. Alterne o botão **habilitar regra para todos os dispositivos deste modelo** na regra para habilitar ou desabilitar a regra para todos os dispositivos associados ao modelo de dispositivo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Habilitar ou desabilitar uma regra para um dispositivo

Navegue até o dispositivo e escolha a regra que deseja habilitar ou desabilitar. Alterne o botão **habilitar regra para este dispositivo** para habilitar ou desabilitar a regra para o dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a criar regras em seu aplicativo IoT Central do Azure, aqui estão algumas próximas etapas:

- [Adicionar ação de Microsoft Flow em regras](howto-add-microsoft-flow.md)
- [Adicionar ação de webhook em regras](howto-create-webhooks.md)
- [Agrupar várias ações para executar a partir de uma ou mais regras](howto-use-action-groups.md)
- [Como gerenciar seus dispositivos](howto-manage-devices.md)
