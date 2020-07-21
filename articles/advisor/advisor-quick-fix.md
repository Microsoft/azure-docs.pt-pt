---
title: Remediação de Correção Rápida para recomendações do Advisor
description: Executar remediação a granel usando Quick Fix no Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: ebd993733c1aca9cd4f7d92f0a75e22d68a87ffe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518067"
---
# <a name="quick-fix-remediation-for-advisor"></a>Remediação de Correção Rápida para Assessor
**O Quick Fix** permite uma forma mais rápida e fácil de remediar para recomendação sobre múltiplos recursos. Fornece capacidade para remediações a granel para recursos e ajuda-o a otimizar as suas subscrições mais rapidamente com a remediação em escala para os seus recursos.
A funcionalidade encontra-se disponível apenas para determinadas recomendações, através do portal Azure.


## <a name="steps-to-use-quick-fix"></a>Passos para usar 'Quick Fix'

1. A partir da lista de recomendações que têm a etiqueta **Quick Fix,** clique na recomendação.

   ![Correção rápida do conselheiro](./media/quick-fix-1.png)
   
   *Os preços na imagem são apenas para fins*

2. Na página de detalhes da Recomendação, você verá a lista de recursos para os quais tem esta recomendação. Selecione todos os recursos que pretende remediar para a recomendação.

   ![Correção rápida do conselheiro](./media/quick-fix-2.png)
   
   *Os preços na imagem são apenas para fins*

3. Uma vez selecionados os recursos, clique no botão **Quick Fix** para remediar em massa.

   > [!NOTE]
   > Alguns dos recursos listados podem ser desativados, porque não tem as permissões apropriadas para os modificar.
   
   > [!NOTE]
   > Se houver outras implicações, para além dos benefícios mencionados no Advisor, será comunicado na experiência para o ajudar a tomar decisões de remediação informadas.
   
4. Receberá uma notificação para a conclusão da reparação. Verá um erro se houver recursos que não sejam remediados e recursos no modo selecionado na vista da lista de recursos.  


## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre recomendações do Advisor, consulte:
* [Introdução ao Conselheiro Azure](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de Custos Do Consultor](advisor-cost-recommendations.md)
* [Recomendações de Desempenho do Conselheiro](advisor-performance-recommendations.md)
* [Recomendações de Segurança do Conselheiro](advisor-security-recommendations.md)
* [Recomendações de Excelência Operacional Do Consultor](advisor-operational-excellence-recommendations.md)
* [Conselheiro REST API](/rest/api/advisor/)
