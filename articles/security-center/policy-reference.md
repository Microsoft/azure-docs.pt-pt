---
title: Definições políticas incorporadas para o Azure Security Center
description: Lista definições de políticas incorporadas para o Azure Security Center. Estas definições políticas incorporadas fornecem abordagens comuns para gerir os seus recursos Azure.
ms.date: 03/24/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: a96dcc3b6f4abafd6cf72c11b2f52480fe3780ec
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105036713"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Definições incorporadas do Azure Policy para o Centro de Segurança do Azure

Esta página é um índice de definições políticas incorporadas da [Azure Policy](../governance/policy/overview.md) relacionadas com o Azure Security Center. Estão disponíveis os seguintes agrupamentos de definições políticas:

- O grupo [de iniciativas](#azure-security-center-initiatives) lista as definições de iniciativa da Política Azure na categoria "Centro de Segurança".
- O grupo [de iniciativa padrão](#azure-security-center-initiatives) lista todas as definições da Política Azure que fazem parte da iniciativa padrão do Security Center, [Azure Security Benchmark](../security/benchmarks/introduction.md). Esta referência de autoria da Microsoft, amplamente respeitada, baseia-se nos controlos do [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e do National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) com foco na segurança centrada na nuvem.
- O grupo [de categoria](#azure-security-center-category) lista todas as definições de Política Azure na categoria "Centro de Segurança".

Para obter mais informações sobre políticas de segurança, consulte [Trabalhar com políticas de segurança.](./tutorial-security-policy.md) Para obter mais incorporados em Azure Policy para outros serviços, consulte [definições incorporadas da Política Azure](../governance/policy/samples/built-in-policies.md).

O nome de cada definição de política incorporada liga-se à definição de política no portal Azure. Utilize o link na coluna **versão** para visualizar a fonte no [repo GitHub da Política Azure](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Iniciativas do Centro de Segurança Azure

Para conhecer as iniciativas incorporadas que são monitorizadas pelo Centro de Segurança, consulte a seguinte tabela:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Iniciativa padrão do Centro de Segurança (Azure Security Benchmark)

Para conhecer as políticas incorporadas que são monitorizadas pelo Centro de Segurança, consulte a seguinte tabela:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Categoria Azure Security Center

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre as definições de política de segurança da Azure Policy no Centro de Segurança. Para saber mais sobre iniciativas, políticas e como se relacionam com as recomendações do Centro de Segurança, veja [o que são políticas de segurança, iniciativas e recomendações?](security-policy-concept.md)
