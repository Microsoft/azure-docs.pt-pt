---
title: Tutorial - Crie e gere regras na sua aplicação Azure IoT Central
description: Este tutorial mostra-lhe como as regras da Azure IoT Central permitem monitorizar os seus dispositivos em tempo real e invocar automaticamente ações, como o envio de um e-mail, quando a regra dispara.
author: dominicbetts
ms.author: dobett
ms.date: 04/06/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 555da74da65f3b1897a276cf819a263334cfa053
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80999045"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Tutorial: Criar uma regra e configurar notificações na sua aplicação do Azure IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

Pode utilizar o Azure IoT Central para monitorizar remotamente os seus dispositivos ligados. As regras da Azure IoT Central permitem monitorizar os seus dispositivos em tempo real e invocar automaticamente ações, como o envio de um e-mail. Este artigo explica como criar regras para monitorizar a telemetria que os seus dispositivos enviam.

Os dispositivos utilizam a telemetria para enviar dados numéricos do dispositivo. Uma regra dispara quando a telemetria do dispositivo selecionado cruza um limiar especificado.

Neste tutorial, cria-se uma regra para enviar um e-mail quando a temperatura num dispositivo de sensor ambiental simulado excede 70 &deg; F.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar uma regra
> * Adicione uma ação de e-mail

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, preencha a [aplicação Create a Azure IoT Central](./quick-deploy-iot-central.md) e [Adicione um dispositivo simulado à sua aplicação IoT Central](./quick-create-simulated-device.md) para criar o modelo de dispositivo **MXChip IoT DevKit** para trabalhar.

## <a name="create-a-rule"></a>Criar uma regra

Para criar uma regra de telemetria, o modelo do dispositivo deve incluir pelo menos um valor de telemetria. Este tutorial utiliza um dispositivo **simulado MXChip IoT DevKit** que envia telemetria de temperatura e humidade. Adicionou este modelo de dispositivo e criou um dispositivo simulado no [Add um dispositivo simulado à sua aplicação IoT Central.](./quick-create-simulated-device.md) A regra monitoriza a temperatura reportada pelo dispositivo e envia um e-mail quando ultrapassa os 70 graus.

1. No painel esquerdo, selecione **Regras**.

1. Se ainda não criou nenhuma regra, vê o seguinte ecrã:

    ![Ainda não existem regras](media/tutorial-create-telemetry-rules/rules-landing-page1.png)

1. Selecione **+** para adicionar uma nova regra.

1. Introduza o nome _Monitor de temperatura_ para identificar a regra e prima Enter.

1. Selecione o modelo de dispositivo **MXChip IoT DevKit.** Por predefinição, a regra aplica-se automaticamente a todos os dispositivos associados ao modelo do dispositivo. Para filtrar um subconjunto dos dispositivos, selecione **+ Filtrar** e utilizar as propriedades do dispositivo para identificar os dispositivos. Para desativar a regra, alternar o botão **Ativado/Desativado** no cabeçalho de regras:

    ![Filtros e ativar](media/tutorial-create-telemetry-rules/device-filters.png)

### <a name="configure-the-rule-conditions"></a>Configure as condições de regra

As condições definem os critérios que a regra monitoriza. Neste tutorial, configura-se a regra para disparar quando a temperatura for superior a 70 &deg; F.

1. Selecione **Temperatura** na queda da **telemetria.**

1. Em seguida, escolha **É maior do que** como **Operador** e _introduza 70_ como **valor**.

    ![Condição](media/tutorial-create-telemetry-rules/condition-filled-out1.png)

1. Opcionalmente, pode definir **uma agregação de tempo**. Quando selecionar uma agregação de tempo, também deve selecionar um tipo de agregação, como a média ou a soma a partir do recuo da agregação.

    * Sem agregação, a regra dispara para cada ponto de dados de telemetria que satisfaça a condição. Por exemplo, se configurar a regra para acionar quando a temperatura é superior a 70, então a regra dispara quase instantaneamente quando a temperatura do dispositivo excede este valor.
    * Com a agregação, a regra dispara se o valor agregado dos pontos de dados de telemetria na janela de tempo satisfaz a condição. Por exemplo, se configurar a regra para acionar quando a temperatura é superior a 70 e com uma agregação média de tempo de 10 minutos, então a regra dispara quando o dispositivo reporta uma temperatura média superior a 70, calculada num intervalo de 10 minutos.

     ![Condição agregada](media/tutorial-create-telemetry-rules/aggregate-condition-filled-out1.png)

Pode adicionar várias condições a uma regra selecionando **+ Condição**. Quando várias condições são especificadas, todas as condições devem ser satisfeitas para que a regra desencadeie. Cada condição é unida por uma `AND` cláusula implícita. Se estiver a utilizar a agregação de tempo com múltiplas condições, todos os valores da telemetria devem ser agregados.

### <a name="configure-actions"></a>Configure ações

Depois de definir a condição, estabelece as ações a tomar quando a regra dispara. As ações são invocadas quando todas as condições especificadas na regra avaliam a verdade.

1. Selecione **+ Email** na secção **Ações.**

1. Introduza o _aviso de temperatura_ como o nome de visualização para a ação, o seu endereço de e-mail no campo **To** e deverá verificar _o dispositivo!_ como uma nota para aparecer no corpo do e-mail.

    > [!NOTE]
    > Os e-mails são enviados apenas para os utilizadores que foram adicionados à aplicação e que iniciaram sessão pelo menos uma vez. Saiba mais sobre [a gestão de utilizadores](howto-administer.md) no Azure IoT Central.

   ![Configure ação](media/tutorial-create-telemetry-rules/configure-action1.png)

1. Para salvar a ação, escolha **Feito**. Pode adicionar múltiplas ações a uma regra.

1. Para salvar a regra, escolha **Guardar**. A regra entra em direto dentro de poucos minutos e começa a monitorizar a telemetria a ser enviada para a sua aplicação. Quando a condição especificada na regra é cumprida, a regra desencadeia a ação de e-mail configurada.

Passado algum tempo, recebe uma mensagem de e-mail quando a regra dispara:

![Exemplo e-mail](media/tutorial-create-telemetry-rules/email.png)

## <a name="delete-a-rule"></a>Eliminar uma regra

Se já não precisar de uma regra, elimine-a abrindo a regra e escolhendo **Eliminar**.

## <a name="enable-or-disable-a-rule"></a>Ativar ou desativar uma regra

Escolha a regra que pretende ativar ou desativar. Alternar o botão **Ativado/Desativado** na regra para ativar ou desativar a regra para todos os dispositivos que estão abrangidos pela regra.

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>Ativar ou desativar uma regra para dispositivos específicos

Escolha a regra que pretende personalizar. Utilize um ou mais filtros na secção **dispositivos Target** para limitar o âmbito da regra aos dispositivos que pretende monitorizar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Criar uma regra baseada em telemetria
* Adicionar uma ação

Agora que definiu uma regra baseada no limiar, o próximo passo sugerido é aprender a:

> [!div class="nextstepaction"]
> [Configure a exportação contínua de dados.](./howto-export-data.md)
