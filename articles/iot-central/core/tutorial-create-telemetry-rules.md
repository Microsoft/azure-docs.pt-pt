---
title: Tutorial – criar e gerenciar regras no aplicativo IoT Central do Azure
description: Este tutorial mostra como as regras de IoT Central do Azure permitem que você monitore seus dispositivos quase em tempo real e invoque automaticamente ações, como enviar um email, quando a regra é disparada.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f61a41fa89c7006341db928472f6b20d272bc550
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167455"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Tutorial: Crie uma regra e crie notificações na sua aplicação Azure IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

Você pode usar o Azure IoT Central para monitorar remotamente seus dispositivos conectados. As regras de IoT Central do Azure permitem que você monitore seus dispositivos quase em tempo real e invoque automaticamente as ações, como enviar um email. Com apenas alguns cliques, você pode definir uma condição para monitorar a telemetria de seus dispositivos e configurar uma ação correspondente. Este artigo explica como criar regras para monitorar a telemetria enviada pelo dispositivo.

Os dispositivos usam a telemetria para enviar dados numéricos do dispositivo. Uma regra é disparada quando a telemetria do dispositivo selecionado cruza um limite especificado.

Neste tutorial, cria-se uma regra para enviar um e-mail quando a temperatura num dispositivo de sensor ambiental simulado exceder 70&deg; F.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar uma regra
> * Adicionar uma ação de email

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve completar a [aplicação Create a Azure IoT Central](./quick-deploy-iot-central.md) e [adicionar um dispositivo simulado à sua aplicação IoT Central](./quick-create-pnp-device.md) rapidamente para criar o modelo de dispositivo **MXChip IoT DevKit** para trabalhar.

## <a name="create-a-rule"></a>Criar uma regra

Para criar uma regra de telemetria, o modelo de dispositivo deve ter pelo menos uma medida de telemetria definida. Este tutorial usa um dispositivo de sensor ambiental que envia a telemetria de temperatura e umidade. Adicionou este modelo de dispositivo e criou um dispositivo simulado no [Add um dispositivo simulado à sua aplicação IoT Central.](./quick-create-pnp-device.md) A regra monitora a temperatura relatada pelo dispositivo e envia um email quando ele ultrapassa 70 graus.

1. No painel esquerdo, selecione **Regras**.

1. Se ainda não tiver criado nenhuma regra, você verá a tela a seguir:

    ![Ainda não há regras.](media/tutorial-create-telemetry-rules/rules-landing-page1.png)

1. Selecione **+** para adicionar uma nova regra.

1. Introduza o nome _Monitor de temperatura_ para identificar a regra e prima Entrar.

1. Selecione o modelo de dispositivo **MXChip IoT DevKit.** Por padrão, a regra se aplica automaticamente a todos os dispositivos associados ao modelo de dispositivo. Para filtrar para um subconjunto dos dispositivos, selecione **+ Filter** e utilize as propriedades do dispositivo para identificar os dispositivos. Para desativar a regra, alternar o botão **Ativado/Desativado** no cabeçalho da regra:

    ![Filtros e habilitar](media/tutorial-create-telemetry-rules/device-filters.png)

### <a name="configure-the-rule-conditions"></a>Configurar as condições de regra

As condições definem os critérios que a regra monitora. Neste tutorial, configura a regra para disparar quando a temperatura excede 70&deg; F.

1. Selecione **Temperatura** no dropdown da **Telemetria.**

1. Em seguida, escolha **é maior do que** como o **Operador** e insira _70_ como o **Valor**.

    ![Condição](media/tutorial-create-telemetry-rules/condition-filled-out1.png)

1. Opcionalmente, pode definir uma **agregação**do tempo. Ao selecionar uma agregação de tempo, você também deve selecionar um tipo de agregação, como Average ou Sum, na lista suspensa agregação.

    * Sem agregação, a regra é disparada para cada ponto de dados de telemetria que atende à condição. Por exemplo, se a regra estiver configurada para disparar quando a temperatura estiver acima de 70, a regra será disparada quase instantaneamente quando o dispositivo relatar a temperatura > 70.
    * Com a agregação, a regra é disparada se o valor agregado dos pontos de dados de telemetria na janela de tempo atende à condição. Por exemplo, se a regra estiver configurada para disparar quando a temperatura estiver acima de 70, a agregação de tempo será definida como 10 minutos e o tipo de agregação será médio e a regra disparará quando o dispositivo relatar uma temperatura média > 70, calculada em um período de 10 minutos intervalo.

     ![Condição de agregação](media/tutorial-create-telemetry-rules/aggregate-condition-filled-out1.png)

Pode adicionar várias condições a uma regra selecionando **+ Condição**. Quando várias condições forem especificadas, todas as condições deverão ser atendidas para que a regra seja disparada. Cada condição é acompanhada por uma cláusula de `AND` implícita. Se você estiver usando a agregação de tempo com várias condições, todos os valores de telemetria deverão ser agregados.

### <a name="configure-actions"></a>Configurar ações

Depois de definir a condição, você configura as ações a serem tomadas quando a regra é acionada. As ações são invocadas quando todas as condições especificadas na regra são avaliadas como true.

1. Selecione **+ E-mail** na secção **Ações.**

1. Introduza o _aviso de temperatura_ como o nome de exibição para a ação, o seu endereço de e-mail no campo Para **e** deverá verificar _o dispositivo!_ como uma observação para aparecer no corpo do email.

    > [!NOTE]
    > Os emails são enviados somente aos usuários que foram adicionados ao aplicativo e que fizeram logon pelo menos uma vez. Saiba mais sobre [a gestão de utilizadores](howto-administer.md) na Azure IoT Central.

   ![Configurar ação](media/tutorial-create-telemetry-rules/configure-action1.png)

1. Para salvar a ação, escolha **Done**. Você pode adicionar várias ações a uma regra.

1. Para salvar a regra, escolha **Guardar**. A regra fica ativa em alguns minutos e inicia o monitoramento da telemetria que está sendo enviada ao seu aplicativo. Quando a condição especificada na regra é atendida, a regra dispara a ação de email configurada.

Após alguns instantes, você receberá uma mensagem de email quando a regra for acionada:

![Email de exemplo](media/tutorial-create-telemetry-rules/email.png)

## <a name="delete-a-rule"></a>Eliminar uma regra

Se já não precisar de uma regra, apague-a abrindo a regra e escolhendo **Apagar**.

## <a name="enable-or-disable-a-rule"></a>Habilitar ou desabilitar uma regra

Escolha a regra que você deseja habilitar ou desabilitar. Alternar o botão **Ativar** ou **Desativar** na regra para ativar ou desativar a regra para todos os dispositivos que estejam abrangidos pela regra.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Habilitar ou desabilitar uma regra para um dispositivo

Escolha a regra que você deseja habilitar ou desabilitar. Adicione um filtro na secção **Scopes** para incluir ou excluir um determinado dispositivo no modelo do dispositivo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Criar uma regra baseada em telemetria
* Adicionar uma ação

Agora que você definiu uma regra baseada em limite, a próxima etapa sugerida é aprender a:

> [!div class="nextstepaction"]
> [Configurar a exportação contínua de dados.](./howto-export-data.md)
