---
title: Remediação de Correção Rápida para recomendações do Advisor
description: Executar remediação a granel usando Quick Fix no Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: a9c86a7ae510d9657f64c71db2aa8c4e3e558f52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968591"
---
# <a name="quick-fix-remediation-for-advisor"></a>Remediação de Correção Rápida para Assessor
**O Quick Fix** permite uma forma mais rápida e fácil de remediar para recomendação sobre múltiplos recursos. Fornece capacidade para remediações a granel para recursos e ajuda-o a otimizar as suas subscrições mais rapidamente com a remediação em escala para os seus recursos.
A funcionalidade encontra-se disponível apenas para determinadas recomendações, através do portal Azure.


## <a name="steps-to-use-quick-fix"></a>Passos para usar 'Quick Fix'

1. A partir da lista de recomendações que têm a etiqueta **Quick Fix,** clique na recomendação.

   :::image type="content" source="./media/quick-fix-1.png" alt-text="{Screenshot do Azure Advisor mostrando etiquetas Quick Fix nas recomendações.}":::
   
   *Os preços na imagem são apenas para fins*

2. Na página de detalhes da Recomendação, você verá a lista de recursos para os quais tem esta recomendação. Selecione todos os recursos que pretende remediar para a recomendação.

   :::image type="content" source="./media/quick-fix-2.png" alt-text="{Screenshot do Azure Advisor mostrando etiquetas Quick Fix nas recomendações.}":::
   
   *Os preços na imagem são apenas para fins*

3. Uma vez selecionados os recursos, clique no botão **Quick Fix** para remediar em massa.

   > [!NOTE]
   > Alguns dos recursos listados podem ser desativados, porque não tem as permissões apropriadas para os modificar.
   
   > [!NOTE]
   > Se houver outras implicações, para além dos benefícios mencionados no Advisor, será comunicado na experiência para o ajudar a tomar decisões de remediação informadas.
   
4. Receberá uma notificação para a conclusão da reparação. Verá um erro se houver recursos que não sejam remediados e recursos no modo selecionado na vista da lista de recursos.  


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre recomendações do Advisor, consulte:
* [Introdução ao Assistente do Azure](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de Custos Do Consultor](advisor-cost-recommendations.md)
* [Recomendações de Desempenho do Conselheiro](advisor-performance-recommendations.md)
* [Recomendações de Segurança do Conselheiro](advisor-security-recommendations.md)
* [Recomendações de Excelência Operacional Do Consultor](advisor-operational-excellence-recommendations.md)
* [Conselheiro REST API](/rest/api/advisor/)
