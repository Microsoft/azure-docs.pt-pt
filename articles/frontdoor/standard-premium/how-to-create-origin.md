---
title: Configurar uma origem padrão/premium da porta frontal Azure (Preview)
description: Este artigo mostra como configurar uma origem com o Endpoint Manager.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ebc71ea2d354caf0c8f31b1231ecc1487237dd29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741893"
---
# <a name="set-up-an-azure-front-door-standardpremium-preview-origin"></a>Configurar uma origem padrão/premium da porta frontal Azure (Preview)

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Este artigo irá mostrar-lhe como criar uma origem Azure Front Door Standard/Premium num grupo de origem existente. 

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder criar uma origem Azure Front Door Standard/Premium, deve ter criado pelo menos um grupo de origem.

## <a name="create-a-new-azure-front-door-standardpremium-origin"></a>Criar um novo Azure Front Door Standard/Premium Origin

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu perfil Azure Front Door Standard/Premium.

1. Selecione **Grupo de Origem**. Em seguida, **selecione + Adicione** para criar um novo grupo de origem.
   
    :::image type="content" source="../media/how-to-create-origin/select-add-origin.png" alt-text="Screenshot da página de aterragem do grupo de origem.":::

1. Na página de **grupo Add uma origem,** insira um **nome** único para o novo grupo de origem.

1. Em seguida, **selecione + Adicione uma origem** para adicionar uma nova origem a este grupo de origem. 

    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="Screenshot de adicionar uma página de origem.":::
  
    | Definição | Valor |
    | --- | --- |
    | Nome | Insira um nome único para a nova origem da Porta Frontal Azure. |   
    | Tipo de Origem | O tipo de recurso que pretende adicionar. O Azure Front Door Standard/Premium suporta a autodiscovery da sua aplicação originária do serviço de aplicações, serviço na nuvem ou armazenamento. Se quiser um recurso diferente em Azure ou um backend não-Azure, selecione **Anfitrião Personalizado**. |
    | Nome do Anfitrião  | Se não selecionou **o anfitrião personalizado** para o tipo de anfitrião de origem, selecione o seu backend escolhendo o nome do anfitrião de origem no dropdown. |
    | Cabeçalho anfitrião de origem | Introduza o valor do cabeçalho do anfitrião sendo enviado para o backend para cada pedido. Para obter mais informações, consulte [o cabeçalho do anfitrião Origin](concept-origin.md#hostheader). |
    | Http Porto | Introduza o valor para a porta que a origem suporta para o protocolo HTTP. |
    | Porto HTTPS | Introduza o valor para a porta que a origem suporta para o protocolo HTTPS. |
    | Prioridade | Atribua prioridades à sua origem diferente quando pretender utilizar uma origem de serviço primário para todo o tráfego. Além disso, forneça cópias de segurança se a origem primária ou a origem da cópia de segurança não estiver disponível. Para mais informações, consulte [Prioridade.](concept-origin.md#priority) |
    | Peso | Atribua pesos às suas diferentes origens para distribuir o tráfego por um conjunto de origens, quer uniformemente, quer de acordo com coeficientes de peso. Para mais informações, consulte [Weights](concept-origin.md#weighted). |
    | Estado | Selecione esta opção para ativar a origem. |
    | Regra | Selecione Conjuntos de regras que serão aplicados a esta Rota. Para obter mais informações sobre como configurar regras, consulte [Configurar uma regra definida para porta frontal azul](how-to-configure-rule-set.md) | 

    > [!IMPORTANT]
    > Durante a configuração, as APIs não validam se a origem for inacessível dos ambientes da Porta Frontal. Certifique-se de que a Porta da Frente pode chegar à sua origem.

1. **Selecione Adicionar** para criar a nova origem. A origem criada deve figurar na lista de origem com o grupo.
  
    :::image type="content" source="../media/how-to-create-origin/origin-list-view.png" alt-text="Screenshot de origem na vista da lista.":::

1. **Selecione Adicionar** para adicionar o grupo de origem ao ponto final atual. O grupo de origem deve figurar no painel do grupo Origin.

## <a name="clean-up-resources"></a>Limpar os recursos
Para eliminar um grupo Origin quando já não precisar, clique no **...** e, em seguida, selecione **Delete** from the drop-down.

:::image type="content" source="../media/how-to-create-origin/delete-origin-group.png" alt-text="Screenshot de como apagar um grupo de origem.":::

Para eliminar uma origem quando já não precisar, clique no **...** e, em seguida, selecione **Delete** from the drop-down. 

:::image type="content" source="../media/how-to-create-origin/delete-origin-view.png" alt-text="Screenshot de como apagar uma origem.":::

## <a name="next-steps"></a>Passos seguintes

Para conhecer os domínios personalizados, consulte [a adição de um domínio personalizado](how-to-add-custom-domain.md) ao seu ponto final Azure Front Door Standard/Premium.
