---
title: Mudanças importantes que chegam ao Centro de Segurança Azure
description: Próximas alterações ao Azure Security Center que poderá ter de estar atento e para as quais poderá ter de planear
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: 8ef0fedcbf7ce06063db22fb345a0711acddcb4d
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526386"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Mudanças importantes para o Centro de Segurança Azure

> [!IMPORTANT]
> As informações desta página dizem respeito a produtos ou funcionalidades pré-lançamento, que podem ser substancialmente modificadas antes de serem lançadas comercialmente, se é que alguma vez. A Microsoft não compromete ou garante, expressa ou implícita, no que diz respeito às informações fornecidas aqui.

Nesta página, você vai aprender sobre as mudanças que estão planejadas para o Centro de Segurança. Descreve modificações planeadas no produto que podem ter impacto em coisas como a sua pontuação segura ou fluxos de trabalho.

Se procura as últimas notas de lançamento, vai encontrá-las no [What's new in Azure Security Center.](release-notes.md)


## <a name="planned-changes"></a>Alterações planeadas

- [Duas recomendações do controlo de segurança "Aplicar atualizações do sistema" que estão a ser depreciadas](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Melhorias à recomendação de classificação de dados sql](#enhancements-to-sql-data-classification-recommendation)

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Duas recomendações do controlo de segurança "Aplicar atualizações do sistema" que estão a ser depreciadas 

**Data estimada para a alteração:** fevereiro de 2021

As duas recomendações que se seguem deverão ser depreadas em fevereiro de 2021:

- **As suas máquinas devem ser reiniciadas para aplicar atualizações do sistema**. Isto pode resultar num ligeiro impacto na sua pontuação segura.
- **O agente de monitorização deve ser instalado nas suas máquinas**. Esta recomendação diz respeito apenas a máquinas no local e parte da sua lógica será transferida para outra recomendação, **as questões de saúde do agente Log Analytics devem ser resolvidas nas suas máquinas.** Isto pode resultar num ligeiro impacto na sua pontuação segura.

Recomendamos verificar as suas configurações contínuas de automação de exportação e fluxo de trabalho para ver se estas recomendações estão incluídas nas suas recomendações. Além disso, quaisquer dashboards ou outras ferramentas de monitorização que possam estar a usá-los devem ser atualizados em conformidade.

Saiba mais sobre estas recomendações na página de referência das [recomendações de segurança](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Melhorias à recomendação de classificação de dados sql

**Data estimada para a alteração:** 2º trimestre de 2021

A recomendação **Os dados sensíveis nas suas bases de dados SQL devem ser classificados** no controlo de segurança de classificação de **dados Apply** será substituído por uma nova versão mais alinhada com a estratégia de classificação de dados da Microsoft. Como resultado, o ID da recomendação também mudará (atualmente b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Passos seguintes

Para todas as alterações recentes ao produto, veja [o que há de novo no Azure Security Center?](release-notes.md)
