---
title: Definições políticas incorporadas para o Azure Security Center
description: Lista definições de políticas incorporadas para o Azure Security Center. Estas definições políticas incorporadas fornecem abordagens comuns para gerir os seus recursos Azure.
ms.date: 01/25/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 568b8761f2b528b1f5d8e3971503834ab5321ed5
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806916"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Definições incorporadas do Azure Policy para o Centro de Segurança do Azure

Esta página é um índice de definições políticas incorporadas da [Azure Policy](../governance/policy/overview.md) relacionadas com o Azure Security Center. Estão disponíveis os seguintes agrupamentos de definições políticas:

- O grupo [de iniciativas](#azure-security-center-initiatives) enumera as definições de iniciativa da Política Azure na categoria "Centro de Segurança".
- O grupo [de iniciativa padrão](#azure-security-center-initiatives) lista todas as definições da Política Azure que fazem parte da iniciativa padrão do Security Center, [Azure Security Benchmark](../security/benchmarks/introduction.md). Esta referência de autoria da Microsoft, amplamente respeitada, baseia-se nos controlos do [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e do National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) com foco na segurança centrada na nuvem.
- O grupo [de categoria](#azure-security-center-category) lista todas as definições da Política Azure na categoria "Centro de Segurança".

Para obter mais informações sobre políticas de segurança, consulte [Trabalhar com políticas de segurança.](./tutorial-security-policy.md) Para obter mais incorporados em Azure Policy para outros serviços, consulte [definições incorporadas da Política Azure](../governance/policy/samples/built-in-policies.md).

O nome de cada definição de política incorporada liga-se à definição de política no portal Azure. Utilize o link na coluna **versão** para visualizar a fonte no [repo GitHub da Política Azure](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Iniciativas do Centro de Segurança Azure

Para conhecer as iniciativas incorporadas que são monitorizadas pelo Centro de Segurança, consulte a seguinte tabela:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="azure-security-center-default-initiative"></a>Iniciativa padrão do Centro de Segurança Azure

Para conhecer as políticas incorporadas que são monitorizadas pelo Centro de Segurança, consulte a seguinte tabela:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Categoria Azure Security Center

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Próximos passos

Neste artigo, você aprendeu sobre as definições de política de segurança da Azure Policy no Centro de Segurança. Para saber mais, consulte os seguintes artigos.

- Veja as incorporações no [repositório do GitHub do Azure Policy](https://github.com/Azure/azure-policy).
- Reveja a [estrutura de definição do Azure Policy](../governance/policy/concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../governance/policy/concepts/effects.md).
- [Guia de planeamento e operações do Azure Security Center](./security-center-planning-and-operations-guide.md): Saiba como planear e compreender considerações de design no Centro de Segurança Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](./security-center-monitoring.md): saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
- [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](./security-center-managing-and-responding-alerts.md): saiba como gerir e responder a alertas de segurança.
- [Monitor partner solutions with Azure Security Center](./security-center-partner-integration.md) (Monitorizar soluções de parceiros com o Centro de Segurança do Azure): saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [Política Azure](../governance/policy/overview.md): Aprenda a auditar e a governar os seus recursos Azure.
