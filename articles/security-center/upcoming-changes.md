---
title: Mudanças importantes que chegam ao Centro de Segurança Azure
description: Próximas alterações ao Azure Security Center que poderá ter de estar atento e para as quais poderá ter de planear
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 49141f7f11c0e8ead090459238e15b56f57b990b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102633721"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Mudanças importantes para o Centro de Segurança Azure

> [!IMPORTANT]
> As informações desta página dizem respeito a produtos ou funcionalidades pré-lançamento, que podem ser substancialmente modificadas antes de serem lançadas comercialmente, se é que alguma vez. A Microsoft não compromete ou garante, expressa ou implícita, no que diz respeito às informações fornecidas aqui.

Nesta página, você vai aprender sobre as mudanças que estão planejadas para o Centro de Segurança. Descreve modificações planeadas no produto que podem ter impacto em coisas como a sua pontuação segura ou fluxos de trabalho.

Se procura as últimas notas de lançamento, vai encontrá-las no [What's new in Azure Security Center.](release-notes.md)


## <a name="planned-changes"></a>Alterações planeadas

- [As recomendações da AWS serão divulgadas para disponibilidade geral (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)
- [Duas recomendações do controlo de segurança "Aplicar atualizações do sistema" que estão a ser depreciadas](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Melhorias à recomendação de classificação de dados sql](#enhancements-to-sql-data-classification-recommendation)
- [Depreciação de 11 alertas do Azure Defender](#deprecation-of-11-azure-defender-alerts)


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>As recomendações da AWS serão divulgadas para disponibilidade geral (GA)

**Data estimada para a alteração:** abril de 2021

O Azure Security Center protege cargas de trabalho em Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

As recomendações provenientes do AWS Security Hub têm estado em pré-visualização desde que os conectores de nuvem foram introduzidos. As recomendações assinaladas como **Preview** não estão incluídas nos cálculos da sua pontuação segura, mas devem continuar a ser remediadas sempre que possível, para que quando o período de pré-visualização terminar contribuam para a sua pontuação.

Com esta mudança, dois conjuntos de recomendações da AWS passarão para GA:

- [Controlos PCI DSS do Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Controlos de benchmark da CIS AWS Foundations do Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Quando estes são GA e as avaliações são executadas nos seus recursos AWS, os resultados impactarão a sua pontuação segura combinada para todos os seus recursos multi e híbridos em nuvem. 


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Duas recomendações do controlo de segurança "Aplicar atualizações do sistema" que estão a ser depreciadas 

**Data estimada para a alteração:** março de 2021

As duas recomendações que se seguem deverão ser depreadas em fevereiro de 2021:

- **As suas máquinas devem ser reiniciadas para aplicar atualizações do sistema**. Isto pode resultar num ligeiro impacto na sua pontuação segura.
- **O agente de monitorização deve ser instalado nas suas máquinas**. Esta recomendação diz respeito apenas a máquinas no local e parte da sua lógica será transferida para outra recomendação, **as questões de saúde do agente Log Analytics devem ser resolvidas nas suas máquinas.** Isto pode resultar num ligeiro impacto na sua pontuação segura.

Recomendamos verificar as suas configurações contínuas de automação de exportação e fluxo de trabalho para ver se estas recomendações estão incluídas nas suas recomendações. Além disso, quaisquer dashboards ou outras ferramentas de monitorização que possam estar a usá-los devem ser atualizados em conformidade.

Saiba mais sobre estas recomendações na página de referência das [recomendações de segurança](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Melhorias à recomendação de classificação de dados sql

**Data estimada para a alteração:** 2º trimestre de 2021

A recomendação **Os dados sensíveis nas suas bases de dados SQL devem ser classificados** no controlo de segurança de classificação de **dados Apply** será substituído por uma nova versão mais alinhada com a estratégia de classificação de dados da Microsoft. Como resultado, o ID da recomendação também mudará (atualmente, é b0df6f56-862d-4730-8597-38c0fd4ebd59).


### <a name="deprecation-of-11-azure-defender-alerts"></a>Depreciação de 11 alertas do Azure Defender

**Data estimada para a alteração:** março de 2021

No próximo mês, os onze alertas do Azure Defender listados abaixo serão depreciados.

- Novos alertas irão substituir estes dois alertas e fornecer uma melhor cobertura:

    | AlertaType                | Nome de Alertadisplay                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PREVIEW - Kit de ferramentas MicroBurst "Get-AzureDomainInfo" executado |
    | ARM_MicroBurstRunbook    | PREVIEW - O conjunto de ferramentas MicroBurst "Get-AzurePasswords" executado  |
    |                          |                                                                          |

- Estes nove alertas dizem respeito a um conector de proteção de identidade do Diretório Ativo Azure que já foi depreciado:

    | AlertaType           | Nome de Alertadisplay              |
    |---------------------|-------------------------------|
    | Localização desconhecida  | Propriedades de inícios de sessão desconhecidos |
    | Anónimos      | Endereço IP anónimo          |
    | InfectuadoDeviceLogin | Endereço IP ligado a malware     |
    | ImpossibleTravel    | Viagem atípica               |
    | MaliciousIP         | Endereço IP malicioso          |
    | Fugas de informação   | Fuga de credenciais            |
    | PasswordSpray       | Spray de palavra-passe                |
    | Fugas de informação   | Inteligência de ameaça Azure AD  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 



## <a name="next-steps"></a>Passos seguintes

Para todas as alterações recentes ao produto, veja [o que há de novo no Azure Security Center?](release-notes.md)
