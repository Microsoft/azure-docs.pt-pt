---
title: Utilize a solução de Monitorização Remota para detetar problemas do dispositivo - Azure / Microsoft Docs
description: Este tutorial mostra-lhe como utilizar as regras e ações para detetar automaticamente problemas com dispositivos baseados no limiar na solução de Monitorização Remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 695d1b1124c042b61837f0a1257ac2a201ae4860
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539406"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>Tutorial: Detetar problemas dos dispositivos ligados à sua solução de monitorização

Neste tutorial, vai configurar o acelerador de soluções de Monitorização Remota para detetar problemas com os seus dispositivos IoT ligados. Para detetar os problemas dos seus dispositivos, adicione regras que geram alertas no dashboard de soluções.

Para apresentar as regras e alertas, o tutorial utiliza um dispositivo de arrefecimento simulado. O arrefecimento é gerido por uma organização chamada Contoso e é ligado ao acelerador de soluções de Monitorização Remota. A Contoso já tem uma regra que gera um alerta crítico quando a pressão num sistema de arrefecimento ultrapassa 298 PSI. Enquanto operador na Contoso, deve identificar os dispositivos de arrefecimento que possam ter sensores problemáticos, ao procurar picos de pressão iniciais. Para identificar esses dispositivos, adicione uma regra que gera um alerta de aviso quando a pressão no sistema de arrefecimento ultrapassa 150 PSI.

Também lhe foi pedido para criar um alerta crítico para um sistema de arrefecimento quando, durante os últimos cinco minutos, a humidade média no dispositivo foi superior a 80% e a temperatura do dispositivo foi superior a 75 graus fahrenheit.

Neste tutorial:

>[!div class="checklist"]
> * Ver as regras na sua solução
> * Criar uma regra
> * Criar uma regra com várias condições
> * Editar uma regra existente
> * Ativar e desativar regras

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="review-the-existing-rules"></a>Rever as regras existentes

A página **Regras** no acelerador de soluções apresenta uma lista de todas as regras atuais:

[![Página de regras](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Para ver apenas as regras que se aplicam a dispositivos de arrefecimento, aplique um filtro. Pode ver mais informações sobre uma regra e editá-la ao selecionar na lista:

[![Ver detalhes da regra](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>Criar uma regra

Para criar uma regra que gera um aviso quando a pressão num dispositivo de arrefecimento ultrapassa 150 PSI, clique em **Nova regra**. Utilize os seguintes valores para criar a regra:

| Definição          | Valor                                 |
| ---------------- | ------------------------------------- |
| Nome da regra        | Aviso do sistema de arrefecimento                       |
| Descrição      | A pressão do sistema de arrefecimento excedeu 150 PSI |
| Grupo de dispositivos     | Grupo de dispositivos do **sistema de arrefecimento**             |
| Cálculo      | Instantâneo                               |
| Condição 1 Campo| pressão                              |
| Condição 1 operador | Maior do que                      |
| Condição 1 valor    | 150                               |
| Nível de gravidade  | Aviso                               |

[![Criar regra de aviso](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

Para guardar a nova regra, clique em **Aplicar**.

Pode ver quando a regra está acionada na página **Regras** ou na página **Dashboard**:

[![Regra de aviso desencadeada](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-an-advanced-rule"></a>Criar uma regra avançada

Para criar uma regra com várias condições que gera um alerta crítico quando, nos últimos cinco minutos para um dispositivo de arrefecimento, a humidade média é superior a 80% e a temperatura média é superior a 75 graus fahrenheit, clique em **Nova regra**. Utilize os seguintes valores para criar a regra:

| Definição          | Valor                                 |
| ---------------- | ------------------------------------- |
| Nome da regra        | Humidade de arrefecimento e temp essenciais    |
| Descrição      | A humidade e a temperatura são essenciais |
| Grupo de dispositivos     | Grupo de dispositivos do **sistema de arrefecimento**             |
| Cálculo      | Média                               |
| Período de tempo      | 5                                     |
| Condição 1 Campo| humidade                              |
| Condição 1 operador | Maior do que                      |
| Condição 1 valor    | 80                                |
| Nível de gravidade  | Crítico                              |

[![Criar a regra de várias condições parte um](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

Para adicionar a segunda condição, clique em "+Adicionar condição". Utilize os seguintes valores para a nova condição:

| Definição          | Valor                                 |
| ---------------- | ------------------------------------- |
| Condição 2 Campo| temperatura                           |
| Condição 2 operador | Maior do que                      |
| Condição 2 valor    | 75                                |

[![Criar a regra de várias condições parte dois](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

Para guardar a nova regra, clique em **Aplicar**.

Pode ver quando a regra está acionada na página **Regras** ou na página **Dashboard**:

[![Regra de condição múltipla desencadeada](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>Editar uma regra existente

Para fazer uma alteração a uma regra existente, selecione-a na lista de regras e clique em **Editar**:

[![Editar regra](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>Desativar uma regra

Para desativar temporariamente uma regra, pode desativá-la na lista de regras. Selecione a regra a desativar e, em seguida, escolha **Desativar**. O **Estado** da regra é alterado na lista para indicar que a regra está agora desativada. Pode voltar a ativar uma regra que desativou anteriormente com o mesmo procedimento.

[![Regra de desativação](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

Pode ativar e desativar várias regras ao mesmo tempo, ao selecionar várias regras na lista.

## <a name="delete-a-rule"></a>Eliminar uma regra

Para eliminar permanentemente uma regra, pode eliminá-la na lista de regras. Selecione a regra a eliminar e, em seguida, escolha **Eliminar**.

[![Screenshot que mostra a página "Regras" com a janela "Eliminar Regra" realçada.](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-expanded.png#lightbox)

Depois de confirmar que pretende eliminar a regra, é-lhe dada a oportunidade de eliminar todos os alertas associados à regra na página **Manutenção**.

[![Eliminar regra](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-expanded.png#lightbox)

Só é possível eliminar uma regra de cada vez.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Este tutorial mostrou como utilizar a página **Regras** no acelerador de soluções de Monitorização Remota, para criar e gerir regras que acionam alertas na solução. Para saber como utilizar o acelerador de soluções para gerir e configurar os seus dispositivos ligados, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Configurar e gerir dispositivos ligados à sua solução de monitorização](iot-accelerators-remote-monitoring-manage.md)