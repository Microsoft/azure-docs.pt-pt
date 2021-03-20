---
title: Integrar o Windows Virtual Desktop com O Azure Advisor - Azure
description: Como utilizar o Azure Advisor com a sua implementação de Ambiente de Trabalho Virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76b7f97b6020a3a0d5571a3a105d15f7d7893485
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89147655"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>Use o Azure Advisor com o Windows Virtual Desktop

O Azure Advisor pode ajudar os utilizadores a resolver problemas comuns por si só, sem terem de arquivar casos de suporte. As recomendações reduzem a necessidade de apresentar pedidos de ajuda, poupando-lhe tempo e custos.

Este artigo irá dizer-lhe como configurar o Azure Advisor na sua implementação virtual do Windows Desktop para ajudar os seus utilizadores.

## <a name="what-is-azure-advisor"></a>O que é o Assistente do Azure?

O Azure Advisor analisa as suas configurações e telemetria para oferecer recomendações personalizadas para resolver problemas comuns. Com estas recomendações, pode otimizar os seus recursos Azure para fiabilidade, segurança, excelência operacional, desempenho e custo. Saiba mais [no site do Azure Advisor.](https://azure.microsoft.com/services/advisor/)

## <a name="how-to-start-using-azure-advisor"></a>Como começar a usar o Azure Advisor

Tudo o que precisa para começar é uma conta Azure no portal Azure. Primeiro, abra o portal Azure em <https://portal.azure.com/#home> , em seguida, selecione **Advisor** under **Azure Services**, como mostra a imagem seguinte. Também pode introduzir "Azure Advisor" na barra de pesquisa no portal Azure.

> [!div class="mx-imgBorder"]
> ![Uma imagem do portal Azure. O utilizador está a pairar sobre o cursor do rato sobre o link Azure Advisor, fazendo com que apareça um menu suspenso.](media/azure-advisor.png)

Quando abrir o Azure Advisor, verá cinco categorias:

- Custo
- Segurança
- Fiabilidade
- Excelência Operacional
- Desempenho

> [!div class="mx-imgBorder"]
> ![Uma imagem do Azure Advisor mostrando os cinco menus para cada categoria. Os cinco itens exibidos nas suas próprias caixas são Custo, Segurança, Fiabilidade, Excelência Operacional e Desempenho.](media/advisor-categories.png)

Ao selecionar uma categoria, irá à página de recomendações ativas. Nesta página, pode ver quais as recomendações que o Azure Advisor tem para si, como mostra a seguinte imagem.

> [!div class="mx-imgBorder"]
> ![Uma imagem da lista de recomendações ativas para excelência operacional. A lista apresenta sete recomendações com diferentes níveis de prioridade.](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Dicas adicionais para O Advisor Azure

- Certifique-se de verificar as suas recomendações com frequência, pelo menos mais de uma vez por semana. O Azure Advisor atualiza as suas recomendações ativas várias vezes por dia. A verificação de novas recomendações pode prevenir problemas maiores ajudando-o a detetar e a resolver as mais pequenas.

- Tente sempre resolver os problemas com o mais alto nível de prioridade no Azure Advisor. Questões de alta prioridade são marcadas com vermelho. Deixar recomendações de alta prioridade por resolver pode levar a problemas.

- Se uma recomendação parecer menos importante, pode descartá-la ou adiá-la. Para dispensar ou adiar uma recomendação, vá à coluna **Action** e altere o estado do artigo.

- Não rejeite as recomendações até saber porque estão a aparecer e tem a certeza de que não terá um impacto negativo sobre si ou para os seus utilizadores. **Selecione Sempre Saiba mais** para ver qual é o problema. Se resolver um problema seguindo as instruções do Azure Advisor, desaparecerá automaticamente da lista. É melhor resolver problemas do que adiando-os repetidamente.

- Sempre que encontrar um problema no Windows Virtual Desktop, verifique sempre o Azure Advisor primeiro. O Azure Advisor irá dar-lhe instruções para como resolver o problema, ou pelo menos apontar-lhe-á para um recurso que possa ajudar.

## <a name="next-steps"></a>Passos seguintes

Para aprender a resolver recomendações, consulte [como resolver as recomendações do Azure Advisor](azure-advisor-recommendations.md).

Se tiver sugestões para novas recomendações, publique-as no nosso [fórum Azure Advisor User Voice](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations).
