---
title: Criar e gerenciar regras de evento em seu aplicativo de IoT Central do Azure | Microsoft Docs
description: As regras de evento do Azure IoT Central permitem que você monitore seus dispositivos quase em tempo real e invoque automaticamente ações, como enviar um email, quando a regra é disparada.
author: dominicbetts
ms.author: dobett
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1a7fc2b38e8354fb29255bb7f9d6b2c03ed53725
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879958"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Criar uma regra de evento e configurar notificações no aplicativo IoT Central do Azure (recursos de visualização)

*Este artigo aplica-se a operadores, construtores e administradores.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Você pode usar o Azure IoT Central para monitorar remotamente seus dispositivos conectados. As regras de IoT Central do Azure permitem que você monitore seus dispositivos quase em tempo real e invoque automaticamente as ações, como enviar um email. Com apenas alguns cliques, você pode definir a condição para a qual monitorar os dados do dispositivo e configurar a ação correspondente. Este artigo explica como criar regras para monitorar eventos enviados pelo dispositivo.

Os dispositivos podem usar a medição de eventos para enviar eventos de dispositivo importantes ou informativos. Uma regra de evento é disparada quando o evento de dispositivo selecionado é relatado pelo dispositivo.

## <a name="create-an-event-rule"></a>Criar uma regra de eventos

Para criar uma regra de evento, o modelo de dispositivo deve ter pelo menos uma medida de evento definida. Este exemplo usa um dispositivo de máquina de venda com refrigeração que relata um evento de erro do motor do ventilador. A regra monitora o evento relatado pelo dispositivo e envia um email sempre que o evento é relatado.

1. Navegue até a página **regras** .

1. Se ainda não tiver criado nenhuma regra, você verá a tela a seguir:

   ![Ainda não há regras](media/howto-create-event-rules-pnp/rules-landing-page1.png)

1. Selecione **+ nova regra** para ver os tipos de regras que você pode criar.

1. Escolha **evento** para criar uma regra de monitoramento de eventos.

   ![Tipos de regra](media/howto-create-event-rules-pnp/rule-types1.png)

1. Insira um nome que ajude a identificar a regra e pressione Enter.

1. Selecione a definição de dispositivo na qual definir o escopo desta regra na seção Escopos. Essa tela também é onde você pode filtrar os dispositivos aos quais a regra se aplica usando **+ Filter**. A regra se aplica automaticamente a todos os dispositivos no modelo de dispositivo. Para desabilitar a regra, selecione o botão **desabilitar** no cabeçalho.

### <a name="configure-the-rule-conditions"></a>Configurar as condições de regra

Condição define os critérios que são monitorados pela regra.

1. Selecione se deseja definir a **agregação** como ativada ou desativada.

   - Sem agregação, a regra é disparada para cada ponto de dados de evento que atende à condição. Por exemplo, se você configurar a condição da regra para disparar quando ocorrer um evento de **erro do motor do ventilador** , a regra será disparada quase imediatamente quando o dispositivo relatar esse evento.
   - Se **Count** for usado como uma função de agregação, você precisará fornecer um **valor** e uma **janela de tempo** sobre a qual a condição precisa ser avaliada. Nesse caso, a contagem de eventos é agregada e a regra será disparada somente se a contagem de eventos agregados corresponder ao valor.

1. Escolha o evento que você deseja monitorar na lista suspensa **medida** . Neste exemplo, o evento de **erro do motor do ventilador** foi selecionado.

1. Opcionalmente, você também pode definir **Count** como **Aggregation** e fornecer o valor no qual a regra será disparada.

     Por exemplo, se você deseja alertar quando há mais de três eventos de dispositivo em 5 minutos, selecione o evento e defina a função de agregação como **contagem**, operador como **maior que**e **valor** como 3. Defina a **janela de tempo** como **5 minutos**. A regra é disparada quando mais de três eventos são enviados pelo dispositivo em 5 minutos. A frequência de avaliação da regra é igual à **janela de tempo**, o que significa que, neste exemplo, a regra é avaliada uma vez a cada 5 minutos.

 ![Condição](media/howto-create-event-rules-pnp/aggregate-condition-filled-out1.png)

> [!NOTE]
> Mais de uma medida de evento pode ser adicionada sob **Condition**. Quando várias condições forem especificadas, todas as condições deverão ser atendidas para que a regra seja disparada. Cada condição é unida por uma cláusula ' AND ' implicitamente. Ao usar Aggregate, cada medida deve ser agregada.

### <a name="configure-actions"></a>Configurar ações

Esta seção mostra como configurar ações a serem executadas quando a regra for acionada. As ações são invocadas quando todas as condições especificadas na regra são avaliadas como true.

1. Clique na **ação +** nas seções **ação** . Aqui você vê a lista de ações disponíveis.  

   ![Adicionar ação](media/howto-create-event-rules-pnp/add-action1.png)

1. Escolha a ação de **email** , insira um nome de exibição para a ação, um endereço de email válido no campo **para** e forneça uma observação para aparecer no corpo do email quando a regra for disparada.

   > [!NOTE]
   > Os emails são enviados somente aos usuários que foram adicionados ao aplicativo e que fizeram logon pelo menos uma vez. Saiba mais sobre o [Gerenciamento de usuários](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) no Azure IOT central.

   ![Configurar ação](media/howto-create-event-rules-pnp/configure-action1.png)

1. Para salvar a ação, escolha **concluído**.

1. Para salvar a regra, escolha **salvar**. A regra fica ativa em alguns minutos e começa a monitorar os eventos que estão sendo enviados para seu aplicativo. Quando a condição especificada na regra é atendida, a regra dispara a ação de email configurada.

## <a name="parameterize-the-rule"></a>Parametrizar a regra

As regras podem derivar determinadas valores de **Propriedades de dispositivo** como parâmetros. O uso de parâmetros é útil em cenários nos quais os limites de eventos variam para diferentes dispositivos. Ao criar a regra, escolha uma propriedade de dispositivo que especifique o limite, como o **limite máximo ideal**, em vez de fornecer um valor absoluto, como 3 eventos. Quando a regra é executada, ela corresponde aos eventos do dispositivo com o valor definido na Propriedade do dispositivo.

O uso de parâmetros é uma maneira eficaz de reduzir o número de regras a serem gerenciadas.

As ações também podem ser configuradas usando a **propriedade de dispositivo** como um parâmetro. Se um endereço de email for armazenado como uma propriedade de dispositivo, ele poderá ser usado quando você definir o endereço de **para** .

## <a name="delete-a-rule"></a>Eliminar uma regra

Se você não precisar mais de uma regra, exclua-a abrindo a regra e escolhendo **excluir**. A exclusão da regra a remove do modelo de dispositivo e de todos os dispositivos associados.

## <a name="enable-or-disable-a-rule"></a>Habilitar ou desabilitar uma regra

Escolha a regra que você deseja habilitar ou desabilitar. Alterne o botão **habilitar** ou **desabilitar** na regra para habilitar ou desabilitar a regra para todos os dispositivos que estão no escopo da regra.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Habilitar ou desabilitar uma regra para um dispositivo

Escolha a regra que você deseja habilitar ou desabilitar. Adicione um filtro na seção escopos para incluir ou excluir um determinado dispositivo no modelo de dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a criar regras em seu aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [gerenciar seus dispositivos](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
