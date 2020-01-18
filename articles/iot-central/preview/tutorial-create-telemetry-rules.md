---
title: Tutorial – criar e gerenciar regras no aplicativo IoT Central do Azure
description: Este tutorial mostra como as regras de IoT Central do Azure permitem que você monitore seus dispositivos quase em tempo real e invoque automaticamente ações, como enviar um email, quando a regra é disparada.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3889378f34d66f54ea408da4aa43b12f86e7c586
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262671"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Tutorial: criar uma regra e configurar notificações no aplicativo IoT Central do Azure (recursos de visualização)

*Este artigo aplica-se a operadores, construtores e administradores.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Você pode usar o Azure IoT Central para monitorar remotamente seus dispositivos conectados. As regras de IoT Central do Azure permitem que você monitore seus dispositivos quase em tempo real e invoque automaticamente as ações, como enviar um email. Com apenas alguns cliques, você pode definir uma condição para monitorar a telemetria de seus dispositivos e configurar uma ação correspondente. Este artigo explica como criar regras para monitorar a telemetria enviada pelo dispositivo.

Os dispositivos usam a telemetria para enviar dados numéricos do dispositivo. Uma regra é disparada quando a telemetria do dispositivo selecionado cruza um limite especificado.

Neste tutorial, você criará uma regra para enviar um email quando a temperatura em um dispositivo do sensor ambiental exceder 70&deg; F.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar uma regra
> * Adicionar uma ação de email

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir a [criação de um aplicativo de IOT central do Azure](./quick-deploy-iot-central.md) e [Adicionar um dispositivo simulado aos seus](./quick-create-pnp-device.md) guias de início rápido do aplicativo IOT central para criar o modelo de dispositivo do **sensor de ambiente** com o qual trabalhar.

## <a name="create-a-rule"></a>Criar uma regra

Para criar uma regra de telemetria, o modelo de dispositivo deve ter pelo menos uma medida de telemetria definida. Este tutorial usa um dispositivo de sensor ambiental que envia a telemetria de temperatura e umidade. Você adicionou esse modelo de dispositivo e criou um dispositivo simulado no início rápido [Adicionar um dispositivo simulado ao seu IOT central Application](./quick-create-pnp-device.md) . A regra monitora a temperatura relatada pelo dispositivo e envia um email quando ele ultrapassa 70 graus.

1. No painel esquerdo, selecione **regras**.

1. Se ainda não tiver criado nenhuma regra, você verá a tela a seguir:

    ![Ainda não existem regras](media/tutorial-create-telemetry-rules/rules-landing-page1.png)

1. Selecione **+** para adicionar uma nova regra.

1. Insira o nome _Monitor de temperatura_ para identificar a regra e pressione Enter.

1. Selecione o modelo de dispositivo do **sensor de ambiente** . Por padrão, a regra se aplica automaticamente a todos os dispositivos associados ao modelo de dispositivo. Para filtrar um subconjunto dos dispositivos, selecione **+ filtro** e use Propriedades do dispositivo para identificar os dispositivos. Para desabilitar a regra, alterne o botão **habilitado/desabilitado** no cabeçalho da regra:

    ![Filtros e habilitar](media/tutorial-create-telemetry-rules/device-filters.png)

### <a name="configure-the-rule-conditions"></a>Configurar as condições de regra

As condições definem os critérios que a regra monitora. Neste tutorial, você configura a regra a ser acionada quando a temperatura exceder 70&deg; F.

1. Selecione **temperatura** na lista suspensa **telemetria** .

1. Em seguida, escolha **é maior que** o **operador** e insira _70_ como o **valor**.

    ![Condição](media/tutorial-create-telemetry-rules/condition-filled-out1.png)

1. Opcionalmente, você pode definir uma **agregação de tempo**. Ao selecionar uma agregação de tempo, você também deve selecionar um tipo de agregação, como Average ou Sum, na lista suspensa agregação.

    * Sem agregação, a regra é disparada para cada ponto de dados de telemetria que atende à condição. Por exemplo, se a regra estiver configurada para disparar quando a temperatura estiver acima de 70, a regra será disparada quase instantaneamente quando o dispositivo relatar a temperatura > 70.
    * Com a agregação, a regra é disparada se o valor agregado dos pontos de dados de telemetria na janela de tempo atende à condição. Por exemplo, se a regra estiver configurada para disparar quando a temperatura estiver acima de 70, a agregação de tempo será definida como 10 minutos e o tipo de agregação será médio e a regra disparará quando o dispositivo relatar uma temperatura média > 70, calculada em um período de 10 minutos intervalo.

     ![Condição de agregação](media/tutorial-create-telemetry-rules/aggregate-condition-filled-out1.png)

Você pode adicionar várias condições a uma regra selecionando **+ condição**. Quando várias condições forem especificadas, todas as condições deverão ser atendidas para que a regra seja disparada. Cada condição é unida por uma cláusula de `AND` implícita. Se você estiver usando a agregação de tempo com várias condições, todos os valores de telemetria deverão ser agregados.

### <a name="configure-actions"></a>Configurar ações

Depois de definir a condição, você configura as ações a serem tomadas quando a regra é acionada. As ações são invocadas quando todas as condições especificadas na regra são avaliadas como true. Atualmente, o email é a única ação disponível.

1. Selecione **+ email** na seção **ações** .

1. Insira o _aviso de temperatura_ como o nome de exibição da ação, seu endereço de email no campo **para** e _Verifique o dispositivo!_ como uma observação para aparecer no corpo do email.

    > [!NOTE]
    > Os emails são enviados somente aos usuários que foram adicionados ao aplicativo e que fizeram logon pelo menos uma vez. Saiba mais sobre o [Gerenciamento de usuários](howto-administer.md) no Azure IOT central.

   ![Configurar ação](media/tutorial-create-telemetry-rules/configure-action1.png)

1. Para salvar a ação, escolha **concluído**. Você pode adicionar várias ações a uma regra.

1. Para salvar a regra, escolha **salvar**. A regra fica ativa em alguns minutos e inicia o monitoramento da telemetria que está sendo enviada ao seu aplicativo. Quando a condição especificada na regra é atendida, a regra dispara a ação de email configurada.

Após alguns instantes, você receberá uma mensagem de email quando a regra for acionada:

![Email de exemplo](media/tutorial-create-telemetry-rules/email.png)

## <a name="delete-a-rule"></a>Eliminar uma regra

Se você não precisar mais de uma regra, exclua-a abrindo a regra e escolhendo **excluir**.

## <a name="enable-or-disable-a-rule"></a>Habilitar ou desabilitar uma regra

Escolha a regra que você deseja habilitar ou desabilitar. Alterne o botão **habilitar** ou **desabilitar** na regra para habilitar ou desabilitar a regra para todos os dispositivos que têm o escopo definido na regra.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Habilitar ou desabilitar uma regra para um dispositivo

Escolha a regra que você deseja habilitar ou desabilitar. Adicione um filtro na seção **escopos** para incluir ou excluir um determinado dispositivo no modelo de dispositivo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Criar uma regra baseada em telemetria
* Adicionar uma ação

Agora que você definiu uma regra baseada em limite, a próxima etapa sugerida é aprender a:

> [!div class="nextstepaction"]
> [Configure a exportação de dados contínuas](./howto-export-data.md).
