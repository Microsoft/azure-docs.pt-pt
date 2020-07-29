---
title: Consulte as recomendações do Azure Advisor que lhe interessam
description: Ver e filtrar recomendações do Azure Advisor para reduzir o ruído.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75422361"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Consulte as recomendações do Azure Advisor que lhe interessam

O Azure Advisor fornece recomendações para o ajudar a otimizar as suas implementações do Azure. Dentro do Advisor, tem acesso a algumas funcionalidades que o ajudam a reduzir as suas recomendações apenas para as que lhe interessam.

## <a name="configure-subscriptions-and-resource-groups"></a>Configurar subscrições e grupos de recursos

O Advisor dá-lhe a capacidade de selecionar Subscrições e Grupos de Recursos que lhe interessam e à sua organização. Só vê recomendações para as subscrições e grupos de recursos que seleciona. Por predefinição, todos são selecionados. As definições de configuração aplicam-se ao grupo de subscrição ou recursos, pelo que as mesmas definições se aplicam a todos os que tenham acesso a essa subscrição ou grupo de recursos. As definições de configuração podem ser alteradas no portal Azure ou programáticamente.

Para fazer alterações no portal Azure:

1. Abrir [O Advisor Azure](https://aka.ms/azureadvisordashboard) no portal Azure.

1. Selecione **Configuração** do menu.

   ![Menu de configuração de conselheiro](./media/view-recommendations/configuration.png)

1. Consulte a caixa na coluna **Incluir** para que quaisquer subscrições ou grupos de recursos recebam recomendações do Advisor. Se a caixa estiver desativada, poderá não ter permissão para escoar uma alteração de configuração nessa subscrição ou grupo de recursos. Saiba mais sobre [permissões no Azure Advisor](permissions.md).

1. Clique em **Aplicar** na parte inferior depois de fazer uma alteração.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrar a sua vista no portal Azure

As definições de configuração permanecem ativas até ser alteradas. Se quiser limitar a visão das recomendações para uma única visualização, pode utilizar as descidas fornecidas no topo do painel Advisor. A partir da Visão Geral, Alta Disponibilidade, Segurança, Desempenho, Custo e Todos os painéis de Recomendação, pode selecionar as subscrições, tipos de recursos e o estado de recomendação que deseja ver.

   ![Menu de filtragem do conselheiro](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Rejeitar e adiar recomendações

O Azure Advisor permite-lhe dispensar ou adiar recomendações sobre um único recurso. Se rejeitar uma recomendação, não a verá novamente a menos que a ative manualmente. No entanto, adiar uma recomendação permite especificar uma duração após a qual a recomendação é automaticamente ativada novamente. O adiamento pode ser feito no portal Azure ou programático.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Adie uma única recomendação no portal Azure 

1. Abrir [O Advisor Azure](https://aka.ms/azureadvisordashboard) no portal Azure.
1. Selecione uma categoria de recomendação para ver as suas recomendações
1. Selecione uma recomendação da lista de recomendações
1. Selecione Adiar ou Dispensar a recomendação que pretende adiar ou demitir

     ![Menu de filtragem do conselheiro](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Adiar ou rejeitar várias recomendações no portal Azure

1. Abrir [O Advisor Azure](https://aka.ms/azureadvisordashboard) no portal Azure.
1. Selecione uma categoria de recomendação para ver as suas recomendações.
1. Selecione uma recomendação da lista de recomendações.
1. Selecione a caixa de verificação à esquerda da fila para todos os recursos que pretende adiar ou rejeitar a recomendação.
1. Selecione **Adiar** ou **Dispensar** na parte superior esquerda da tabela.

     ![Menu de filtragem do conselheiro](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> Precisa de autorização do colaborador ou do proprietário para dispensar ou adiar uma recomendação. Saiba mais sobre permissões no Azure Advisor.

> [!NOTE]
> Se as caixas de seleção estiverem desativadas, as recomendações podem ainda estar a ser carregadas. Por favor, aguarde que todas as recomendações carreguem antes de tentar adiar ou despedir.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Reativar uma recomendação adiada ou rejeitada

Pode ativar uma recomendação que foi adiada ou dispensada. Esta ação pode ser feita no portal Azure ou programáticamente. No portal do Azure:

1. Abrir [O Advisor Azure](https://aka.ms/azureadvisordashboard) no portal Azure.

1. Altere o filtro no painel de visão geral para **adiado**. Em seguida, o conselheiro apresenta recomendações adiadas ou rejeitadas.

    ![Menu de filtragem do conselheiro](./media/view-recommendations/activate-postponed.png)

1. Selecione uma categoria para ver recomendações **adiadas** e **rejeitadas.**

1. Selecione uma recomendação da lista de recomendações. Isto abre recomendações com o separador **adiado & rejeitado** já selecionado para mostrar os recursos para os quais esta recomendação foi adiada ou rejeitada.

1. Clique em **Ativar** no final da linha. Uma vez clicado, a recomendação está ativa para esse recurso e assim removida desta tabela. A recomendação está agora visível no **separador Ative.**
 
     ![Menu de filtragem do conselheiro](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Próximos passos

Este artigo explica como pode ver recomendações que lhe interessam no Azure Advisor. Para saber mais sobre o Conselheiro, consulte: 

- [O que é o Assistente do Azure?](advisor-overview.md)
- [Começar com o Advisor](advisor-get-started.md)
- [Permissões em Azure Advisor](permissions.md)



