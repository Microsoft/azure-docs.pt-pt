---
title: Tutorial - Crie e gerencie regras na sua aplicação Azure IoT Central
description: Este tutorial mostra-lhe como as regras da Central Azure IoT permitem monitorizar os seus dispositivos em tempo real e invocar automaticamente ações, como o envio de um e-mail, quando a regra dispara.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f61a41fa89c7006341db928472f6b20d272bc550
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77167455"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Tutorial: Crie uma regra e crie notificações na sua aplicação Azure IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

Pode utilizar a Central Azure IoT para monitorizar remotamente os seus dispositivos conectados. As regras da Central Azure IoT permitem-lhe monitorizar os seus dispositivos em tempo real e invocar automaticamente ações, como enviar um e-mail. Em apenas alguns cliques, pode definir uma condição para monitorizar a telemetria a partir dos seus dispositivos e configurar uma ação correspondente. Este artigo explica como criar regras para monitorizar a telemetria enviada pelo dispositivo.

Os dispositivos utilizam a telemetria para enviar dados numéricos do dispositivo. Uma regra dispara quando a telemetria do dispositivo selecionado atravessa um limiar especificado.

Neste tutorial, cria-se uma regra para enviar um e-mail quando a&deg; temperatura num dispositivo de sensor ambiental simulado exceder 70 F.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar uma regra
> * Adicione uma ação de e-mail

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve completar a [aplicação Create a Azure IoT Central](./quick-deploy-iot-central.md) e [adicionar um dispositivo simulado à sua aplicação IoT Central](./quick-create-pnp-device.md) rapidamente para criar o modelo de dispositivo **MXChip IoT DevKit** para trabalhar.

## <a name="create-a-rule"></a>Criar uma regra

Para criar uma regra de telemetria, o modelo do dispositivo deve ter pelo menos uma medição de telemetria definida. Este tutorial utiliza um dispositivo de sensor ambiental que envia telemetria de temperatura e humidade. Adicionou este modelo de dispositivo e criou um dispositivo simulado no [Add um dispositivo simulado à sua aplicação IoT Central.](./quick-create-pnp-device.md) A regra monitoriza a temperatura reportada pelo dispositivo e envia um e-mail quando ultrapassa os 70 graus.

1. No painel esquerdo, selecione **Regras**.

1. Se ainda não criou nenhuma regra, vê o seguinte ecrã:

    ![Ainda não existem regras](media/tutorial-create-telemetry-rules/rules-landing-page1.png)

1. Selecione **+** para adicionar uma nova regra.

1. Introduza o nome _Monitor de temperatura_ para identificar a regra e prima Entrar.

1. Selecione o modelo de dispositivo **MXChip IoT DevKit.** Por predefinição, a regra aplica-se automaticamente a todos os dispositivos associados ao modelo do dispositivo. Para filtrar para um subconjunto dos dispositivos, selecione **+ Filter** e utilize as propriedades do dispositivo para identificar os dispositivos. Para desativar a regra, alternar o botão **Ativado/Desativado** no cabeçalho da regra:

    ![Filtros e ativar](media/tutorial-create-telemetry-rules/device-filters.png)

### <a name="configure-the-rule-conditions"></a>Configure as condições da regra

As condições definem os critérios que a regra monitoriza. Neste tutorial, configura a regra para disparar quando a&deg; temperatura ultrapassa os 70 F.

1. Selecione **Temperatura** no dropdown da **Telemetria.**

1. Em seguida, escolha **é maior do que** como o **Operador** e insira _70_ como o **Valor**.

    ![Condição](media/tutorial-create-telemetry-rules/condition-filled-out1.png)

1. Opcionalmente, pode definir uma **agregação**do tempo. Quando selecionar uma agregação de tempo, também deve selecionar um tipo de agregação, como a média ou a soma da agregação.

    * Sem agregação, a regra dispara para cada ponto de dados de telemetria que cumpre a condição. Por exemplo, se a regra estiver configurada para disparar quando a temperatura for superior a 70, a regra dispara quase instantaneamente quando o dispositivo reporta temperatura > 70.
    * Com a agregação, a regra dispara se o valor agregado dos pontos de dados de telemetria na janela do tempo cumprir a condição. Por exemplo, se a regra for configurada para disparar quando a temperatura for superior a 70, a agregação do tempo é definida para 10 minutos, e o tipo de agregação é médio, então a regra dispara quando o dispositivo reporta uma temperatura média > 70, calculada num intervalo de 10 minutos.

     ![Condição agregada](media/tutorial-create-telemetry-rules/aggregate-condition-filled-out1.png)

Pode adicionar várias condições a uma regra selecionando **+ Condição**. Quando várias condições são especificadas, todas as condições devem ser satisfeitas para que a regra seja desencadeada. Cada condição é unida `AND` por uma cláusula implícita. Se estiver a utilizar a agregação do tempo com múltiplas condições, todos os valores da telemetria devem ser agregados.

### <a name="configure-actions"></a>Configurar ações

Depois de definir a condição, estabelece as ações a tomar quando a regra dispara. As ações são invocadas quando todas as condições especificadas na regra avaliam verdadeiramente.

1. Selecione **+ E-mail** na secção **Ações.**

1. Introduza o _aviso de temperatura_ como o nome de exibição para a ação, o seu endereço de e-mail no campo Para **e** deverá verificar _o dispositivo!_ como uma nota para aparecer no corpo do e-mail.

    > [!NOTE]
    > Os e-mails são enviados apenas para os utilizadores que foram adicionados à aplicação e que tenham registado pelo menos uma vez. Saiba mais sobre [a gestão de utilizadores](howto-administer.md) na Azure IoT Central.

   ![Configurar ação](media/tutorial-create-telemetry-rules/configure-action1.png)

1. Para salvar a ação, escolha **Done**. Pode adicionar múltiplas ações a uma regra.

1. Para salvar a regra, escolha **Guardar**. A regra entra em direto dentro de alguns minutos e começa a monitorizar a telemetria a ser enviada para a sua aplicação. Quando a condição especificada na regra é cumprida, a regra desencadeia a ação de e-mail configurada.

Passado algum tempo, recebe uma mensagem de e-mail quando a regra dispara:

![E-mail de exemplo](media/tutorial-create-telemetry-rules/email.png)

## <a name="delete-a-rule"></a>Eliminar uma regra

Se já não precisar de uma regra, apague-a abrindo a regra e escolhendo **Apagar**.

## <a name="enable-or-disable-a-rule"></a>Ativar ou desativar uma regra

Escolha a regra que pretende ativar ou desativar. Alternar o botão **Ativar** ou **Desativar** na regra para ativar ou desativar a regra para todos os dispositivos que estejam abrangidos pela regra.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Ativar ou desativar uma regra para um dispositivo

Escolha a regra que pretende ativar ou desativar. Adicione um filtro na secção **Scopes** para incluir ou excluir um determinado dispositivo no modelo do dispositivo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Criar uma regra baseada em telemetria
* Adicionar uma ação

Agora que definiu uma regra baseada no limiar, o próximo passo sugerido é aprender a:

> [!div class="nextstepaction"]
> [Configurar a exportação contínua de dados.](./howto-export-data.md)
