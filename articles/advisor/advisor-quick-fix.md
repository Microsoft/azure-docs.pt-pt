---
title: Reparação rápida para recomendações do Consultor
description: Realizar a reparação a granel utilizando quick fix no advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: 3c3ac27f04aa516fbef1bfff30e3392659b58919
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502493"
---
# <a name="quick-fix-remediation-for-advisor"></a>Reparação de Correção Rápida para Consultor
**A Quick Fix** permite uma forma mais rápida e fácil de remediar para recomendação sobre vários recursos. Fornece capacidade para remediações a granel para recursos e ajuda-o a otimizar as suas subscrições mais rapidamente com a reparação em escala para os seus recursos.
A funcionalidade está disponível apenas para determinadas recomendações, via portal Azure.


## <a name="steps-to-use-quick-fix"></a>Passos para usar 'Quick Fix'

1. A partir da lista de recomendações que têm a etiqueta **Quick Fix,** clique na recomendação.

   ![Correção rápida do conselheiro](./media/quick-fix-1.png)
   
   *Os preços na imagem são, por exemplo, apenas para fins*

2. Na página de detalhes da Recomendação, verá a lista de recursos para os quais tem esta recomendação. Selecione todos os recursos que pretende remediar para a recomendação.

   ![Correção rápida do conselheiro](./media/quick-fix-2.png)
   
   *Os preços na imagem são, por exemplo, apenas para fins*

3. Depois de ter selecionado os recursos, clique no botão **Quick Fix** para remediar a granel.

   > [!NOTE]
   > Alguns dos recursos listados podem ser desativados, porque não tem as permissões adequadas para modificá-los.
   
   > [!NOTE]
   > Se houver outras implicações, além dos benefícios mencionados no Advisor, será comunicado na experiência para ajudá-lo a tomar decisões de reparação informadas.
   
4. Receberá uma notificação para a conclusão da reparação. Verá um erro se houver recursos que não sejam remediados e recursos no modo selecionado na vista da lista de recursos.  


## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre recomendações do Advisor, consulte:
* [Introdução ao Consultor Azure](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de Custos Consultivos](advisor-cost-recommendations.md)
* [Recomendações de Desempenho Do Conselheiro](advisor-performance-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de Excelência Operacional Consultiva](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
