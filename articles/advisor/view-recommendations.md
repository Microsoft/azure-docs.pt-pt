---
title: Ver recomendações do Assistente do Azure é que são importantes para si
description: Ver e filtrar recomendações do Assistente do Azure para reduzir o ruído.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: 9f599a63fd5f52420f1b79e769d4f7bca9683b32
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60467911"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Ver recomendações do Assistente do Azure é que são importantes para si

O Assistente do Azure fornece recomendações para o ajudar a otimizar as suas implementações do Azure. Dentro do Advisor, tem acesso a alguns recursos que ajudam a limitar as suas recomendações para apenas os que são importantes para si.

## <a name="configure-subscriptions-and-resource-groups"></a>Configurar subscrições e grupos de recursos

Advisor dá-lhe a capacidade de selecionar as subscrições e grupos de recursos que são importantes para e sua organização. Apenas verá as recomendações para as subscrições e grupos de recursos que selecionar. Por predefinição, todas estão selecionadas. Definições de configuração aplicam-se para o subscrição ou grupo de recursos, pelo que as mesmas definições que se aplicam a todos os utilizadores que tenha acesso a essa subscrição ou grupo de recursos. Definições de configuração podem ser alteradas no portal do Azure ou através de programação.

Para fazer alterações no portal do Azure:

1. Open [do Assistente do Azure](https://aka.ms/azureadvisordashboard) no portal do Azure.

1. Selecione **configuração** no menu.

   ![Menu de configuração do Advisor](./media/view-recommendations/configuration.png)

1. A caixa de verificação **inclusão** coluna para quaisquer subscrições ou grupos de recursos para receber recomendações do assistente. Se a caixa estiver desativada, pode não tem permissão para fazer uma alteração dessa subscrição ou grupo de recursos de configuração. Saiba mais sobre [permissões no Assistente do Azure](permissions.md).

1. Clique em **aplicar** na parte inferior depois de efetuar uma alteração.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrar a vista no portal do Azure

Definições de configuração permanecem ativas até que foi alterado. Se pretende limitar a vista de recomendações para uma única visualização, pode utilizar as listas pendentes fornecidas na parte superior do painel do Advisor. Os painéis de descrição geral, elevada disponibilidade, segurança, desempenho, custo e todas as recomendações, pode selecionar as subscrições, tipos de recursos e o estado da recomendação que pretende ver.

   ![Menu de filtragem do Advisor](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>A dispensar e a adiar a recomendações

O Assistente do Azure permite-lhe ignorar ou adiar recomendações sobre um único recurso. Se dispensar uma recomendação, não verá novamente, a menos que ativá-lo manualmente. No entanto, a adiar a recomendação permite-lhe especificar um período de tempo após o qual a recomendação automaticamente é ativada novamente. A adiar a pode ser feita no portal do Azure ou através de programação.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Adiar a uma única recomendação no portal do Azure 

1. Open [do Assistente do Azure](https://aka.ms/azureadvisordashboard) no portal do Azure.
1. Selecione uma categoria de recomendação para ver as suas recomendações
1. Selecione uma recomendação na lista de recomendações
1. Selecione ' Adiar ' ou dispensar para a recomendação de que pretende adiar ou ignorar

     ![Menu de filtragem do Advisor](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Adiar ou ignorar uma várias recomendações no portal do Azure

1. Open [do Assistente do Azure](https://aka.ms/azureadvisordashboard) no portal do Azure.
1. Selecione uma categoria de recomendação para ver as suas recomendações.
1. Selecione uma recomendação na lista de recomendações.
1. Selecione a caixa de verificação à esquerda da linha para todos os recursos que pretende adiar ou ignorar a recomendação.
1. Selecione **atrasar** ou **dispensar** na parte superior esquerda da tabela.

     ![Menu de filtragem do Advisor](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> Necessita de permissão de Contribuidor ou proprietário para dispensar ou adiar uma recomendação. Saiba mais sobre as permissões no Assistente do Azure.

> [!NOTE]
> Se as caixas de seleção estiverem desativadas, ainda podem ser carregar recomendações. Aguarde até que todas as recomendações carregar antes de tentar adiar ou ignorar.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Reativar uma recomendação ignorada ou adiada

Pode ativar uma recomendação que foi adiada ou dispensada. Esta ação pode ser feita no portal do Azure ou através de programação. No portal do Azure:

1. Open [do Assistente do Azure](https://aka.ms/azureadvisordashboard) no portal do Azure.

1. Alterar o filtro no painel de descrição geral para **Postponed**. Em seguida, o assistente apresenta recomendações adiadas ou ignoradas.

    ![Menu de filtragem do Advisor](./media/view-recommendations/activate-postponed.png)

1. Selecione uma categoria para ver **Postponed** e **dispensados** recomendações.

1. Selecione uma recomendação na lista de recomendações. Esta ação abre recomendações com o **adiada & dispensada** separador já selecionada para mostrar os recursos para o qual esta recomendação foi adiada ou dispensada.

1. Clique em **Activate** no final da linha. Quando clicado, a recomendação é Active Directory para esse recurso e então removidos desta tabela. A recomendação agora está visível no **Active Directory** separador.
 
     ![Menu de filtragem do Advisor](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Passos Seguintes

Este artigo explica como pode ver recomendações importantes para si no Assistente do Azure. Para saber mais sobre o Advisor, consulte: 

- [O que é o Azure Advisor?](advisor-overview.md)
- [Introdução ao Advisor](advisor-get-started.md)
- [Permissões no Assistente do Azure](permissions.md)



