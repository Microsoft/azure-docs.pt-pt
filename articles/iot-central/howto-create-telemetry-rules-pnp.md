---
title: Criar e gerenciar regras de telemetria em seu aplicativo de IoT Central do Azure | Microsoft Docs
description: As regras de telemetria do Azure IoT Central permitem que você monitore seus dispositivos quase em tempo real e invoque automaticamente ações, como enviar um email, quando a regra é disparada.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d6deecf558105701be591c1f08923eca2c1e3bbd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879945"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Criar uma regra de telemetria e configurar notificações no aplicativo IoT Central do Azure (recursos de visualização)

*Este artigo aplica-se a operadores, construtores e administradores.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Você pode usar o Azure IoT Central para monitorar remotamente seus dispositivos conectados. As regras de IoT Central do Azure permitem monitorar seus dispositivos quase em tempo real e invocar ações automaticamente, como enviar um email ou disparador Microsoft Flow. Com apenas alguns cliques, você pode definir a condição para a qual monitorar os dados do dispositivo e configurar a ação correspondente. Este artigo explica como criar regras para monitorar a telemetria enviada pelo dispositivo.

Os dispositivos podem usar a medição de telemetria para enviar dados numéricos do dispositivo. Uma regra de telemetria é disparada quando a telemetria do dispositivo selecionado cruza um limite especificado.

## <a name="create-a-telemetry-rule"></a>Criar uma regra de telemetria

Para criar uma regra de telemetria, a definição do dispositivo deve ter pelo menos uma medida de telemetria definida. Este exemplo usa um dispositivo de máquina de venda com refrigeração que envia a telemetria de temperatura e umidade. A regra monitora a temperatura relatada pelo dispositivo e envia um email quando ele ultrapassa 80 graus.

1. Navegue até a página **regras** .

1. Se ainda não tiver criado nenhuma regra, você verá a tela a seguir:

    ![Ainda não há regras](media/howto-create-telemetry-rules-pnp/rules-landing-page1.png)

1. Selecione **+ nova regra** para ver os tipos de regras que você pode criar.

1. Selecione **telemetria** para criar uma regra para monitorar a telemetria do dispositivo.

    ![Tipos de regra](media/howto-create-telemetry-rules-pnp/rule-types1.png)


1. Insira um nome que ajude a identificar a regra e pressione Enter.

1. Selecione a definição de dispositivo na qual você deseja definir o escopo desta regra na seção Escopos. Essa tela também é onde você pode filtrar os dispositivos aos quais a regra se aplica usando **+ Filter**. A regra se aplica automaticamente a todos os dispositivos no modelo de dispositivo. Para desabilitar a regra, selecione o botão **desabilitar** no cabeçalho.

### <a name="configure-the-rule-conditions"></a>Configurar as condições de regra

Condição define os critérios que são monitorados pela regra.

1. Selecione se deseja definir a **agregação** como ativada ou desativada.

      - A agregação é opcional. Sem agregação, a regra é disparada para cada ponto de dados de telemetria que atende à condição. Por exemplo, se a regra estiver configurada para disparar quando a temperatura estiver acima de 80, a regra será disparada quase instantaneamente quando o dispositivo relatar a temperatura > 80.
      - Se uma função de agregação como Average, min, Max e Count for escolhida, o usuário deverá fornecer uma **janela de tempo** sobre a qual a condição precisa ser avaliada. Por exemplo, se você definir o período como "5 minutos" e a regra procurar a temperatura média acima de 80, a regra será disparada quando a temperatura média estiver acima de 80 por pelo menos 5 minutos. A frequência de avaliação da regra é igual à **janela de tempo**, o que significa que, neste exemplo, a regra é avaliada uma vez a cada 5 minutos.

1. Selecione a telemetria que você deseja monitorar na lista suspensa **medida** .

1. Em seguida, escolha um **operador** e forneça um **valor**.

     ![Condição](media/howto-create-telemetry-rules-pnp/aggregate-condition-filled-out1.png)


>[!NOTE]
>Mais de uma medida de telemetria pode ser adicionada selecionando **+ condição**. Quando várias condições forem especificadas, todas as condições deverão ser atendidas para que a regra seja disparada. Cada condição é unida por uma cláusula ' AND ' implicitamente. Ao usar Aggregate, cada medida deve ser agregada.

### <a name="configure-actions"></a>Configurar ações

Esta seção mostra como configurar ações a serem executadas quando a regra for acionada. As ações são invocadas quando todas as condições especificadas na regra são avaliadas como true.

1. Clique na **ação +** nas seções **ação** . Aqui você vê a lista de ações disponíveis.  

    ![Adicionar ação](media/howto-create-telemetry-rules-pnp/add-action1.png)


1. Escolha a ação de **email** , insira um nome de exibição para a ação, um endereço de email válido no campo **para** e forneça uma observação para aparecer no corpo do email quando a regra for disparada.

    > [!NOTE]
    > Os emails são enviados somente aos usuários que foram adicionados ao aplicativo e que fizeram logon pelo menos uma vez. Saiba mais sobre o [Gerenciamento de usuários](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) no Azure IOT central.

   ![Configurar ação](media/howto-create-telemetry-rules-pnp/configure-action1.png)


1. Para salvar a ação, escolha **concluído**.

1. Para salvar a regra, escolha **salvar**. A regra fica ativa em alguns minutos e inicia o monitoramento da telemetria que está sendo enviada ao seu aplicativo. Quando a condição especificada na regra é atendida, a regra dispara a ação de email configurada.

## <a name="parameterize-the-rule"></a>Parametrizar a regra

As regras podem derivar determinadas valores de **Propriedades de dispositivo** como parâmetros. O uso de parâmetros é útil em cenários nos quais os limites de telemetria variam para diferentes dispositivos. Ao criar a regra, escolha uma propriedade de dispositivo que especifique o limite, como o **limite máximo ideal**, em vez de fornecer um valor absoluto, como 80 graus. Quando a regra é executada, ela corresponde à telemetria do dispositivo com o valor definido na Propriedade do dispositivo.

O uso de parâmetros é uma maneira eficaz de reduzir o número de regras a serem gerenciadas.

As ações também podem ser configuradas usando a **propriedade de dispositivo** como um parâmetro. Se um endereço de email for armazenado como uma propriedade, ele poderá ser usado quando você definir o endereço de **para** .

## <a name="delete-a-rule"></a>Eliminar uma regra

Se você não precisar mais de uma regra, exclua-a abrindo a regra e escolhendo **excluir**. A exclusão da regra a remove do modelo de dispositivo e de todos os dispositivos associados.

## <a name="enable-or-disable-a-rule"></a>Habilitar ou desabilitar uma regra

Escolha a regra que você deseja habilitar ou desabilitar. Alterne o botão **habilitar** ou **desabilitar** na regra para habilitar ou desabilitar a regra para todos os dispositivos que têm o escopo definido na regra.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Habilitar ou desabilitar uma regra para um dispositivo

Escolha a regra que você deseja habilitar ou desabilitar. Adicione um filtro na seção escopos para incluir ou excluir um determinado dispositivo no modelo de dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a criar regras em seu aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [gerenciar seus dispositivos](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
