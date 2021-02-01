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
ms.openlocfilehash: 83c1d8ac316703d9d22d0716c86c5731c3db7133
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226476"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Mudanças importantes para o Centro de Segurança Azure

> [!IMPORTANT]
> As informações desta página dizem respeito a produtos ou funcionalidades pré-lançamento, que podem ser substancialmente modificadas antes de serem lançadas comercialmente, se é que alguma vez. A Microsoft não compromete ou garante, expressa ou implícita, no que diz respeito às informações fornecidas aqui.

Nesta página, você vai aprender sobre as mudanças que estão planejadas para o Centro de Segurança. Descreve modificações planeadas no produto que podem ter impacto em coisas como a sua pontuação segura ou fluxos de trabalho.

Se procura as últimas notas de lançamento, vai encontrá-las no [What's new in Azure Security Center.](release-notes.md)


## <a name="planned-changes"></a>Alterações planeadas

- [As recomendações de proteção da carga de trabalho da Kubernetes serão em breve lançadas para disponibilidade geral (GA)](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [Duas recomendações do controlo de segurança "Aplicar atualizações do sistema" que estão a ser depreciadas](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Melhorias à recomendação de classificação de dados sql](#enhancements-to-sql-data-classification-recommendation)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>As recomendações de proteção da carga de trabalho da Kubernetes serão em breve lançadas para disponibilidade geral (GA)

**Data estimada para a alteração:** janeiro de 2021

As recomendações de proteção da carga de trabalho kubernetes descritas na [Protect your Kubernetes workloads](kubernetes-workload-protections.md) estão atualmente em pré-visualização. Embora uma recomendação esteja em pré-visualização, não torna um recurso insalubre, e não está incluído nos cálculos da sua pontuação segura.

Estas recomendações serão em breve lançadas para disponibilidade geral (GA) e assim *serão* incluídas no cálculo da pontuação. Se ainda não os remedia, isto pode resultar num ligeiro impacto na sua pontuação segura.

Remediar sempre que possível (saiba como [em Remediar recomendações no Azure Security Center).](security-center-remediate-recommendations.md)

As recomendações de proteção da carga de trabalho kubernetes são:

- O complemento de política Azure para Kubernetes deve ser instalado e ativado nos seus clusters
- CpU do contentor e limites de memória devem ser aplicados
- Recipientes privilegiados devem ser evitados
- O sistema de ficheiros de raiz imutável (apenas para leitura) deve ser aplicado para os contentores
- Contentor com escalada de privilégio deve ser evitado
- Os recipientes de funcionamento como utilizador de raiz devem ser evitados
- Os recipientes que partilham espaços sensíveis de nome de hospedeiro devem ser evitados
- Capacidades linux menos privilegiadas devem ser aplicadas para contentores
- O uso dos suportes de volume pod HostPath deve ser restringido a uma lista conhecida
- Os contentores devem ouvir apenas as portas permitidas
- Os serviços devem ouvir apenas os portos permitidos
- A utilização da rede de acolhimento e das portas deve ser restringida
- Sobrevaor ou desativação de contentores O perfil appArmor deve ser restringido
- As imagens dos contentores devem ser implantadas apenas a partir de registos fidedignos             

Saiba mais sobre estas recomendações na [Protect your Kubernetes workloads](kubernetes-workload-protections.md).

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Duas recomendações do controlo de segurança "Aplicar atualizações do sistema" que estão a ser depreciadas 

**Data estimada para a alteração:** fevereiro de 2021

As duas recomendações que se seguem deverão ser depreadas em fevereiro de 2021:

- **As suas máquinas devem ser reiniciadas para aplicar atualizações do sistema**. Isto pode resultar num ligeiro impacto na sua pontuação segura.
- **O agente de monitorização deve ser instalado nas suas máquinas**. Esta recomendação diz respeito apenas a máquinas no local e parte da sua lógica será transferida para outra recomendação, **as questões de saúde do agente Log Analytics devem ser resolvidas nas suas máquinas.** Isto pode resultar num ligeiro impacto na sua pontuação segura.

Recomendamos verificar as suas configurações contínuas de automação de exportação e fluxo de trabalho para ver se estas recomendações estão incluídas nas suas recomendações. Além disso, quaisquer dashboards ou outras ferramentas de monitorização que possam estar a usá-los devem ser atualizados em conformidade.

Saiba mais sobre estas recomendações na página de referência das [recomendações de segurança](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Melhorias à recomendação de classificação de dados sql

**Data estimada para a alteração:** 2º trimestre de 2021

A versão atual da recomendação **Os dados sensíveis nas suas bases de dados SQL devem ser classificados** no controlo de segurança de **classificação de dados Apply** será substituído por uma nova versão mais alinhada com a estratégia de classificação de dados da Microsoft. Como resultado:

- A recomendação não afetará mais a sua pontuação segura
- O controlo de segurança ("Aplicar classificação de dados") deixará de afetar a sua pontuação segura
- O ID da recomendação também mudará (atualmente b0df6f56-862d-4730-8597-38c0fd4ebd59)



## <a name="next-steps"></a>Passos seguintes

Para todas as alterações recentes ao produto, veja [o que há de novo no Azure Security Center?](release-notes.md)
