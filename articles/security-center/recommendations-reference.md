---
title: Tabela de referência para todas as recomendações do Centro de Segurança Azure
description: Este artigo lista as recomendações de segurança do Azure Security Center que o ajudam a proteger os seus recursos.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 01/12/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 11d4830908b4e86da12cd5e40cc26b1c1b1aecbd
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133052"
---
# <a name="security-recommendations---a-reference-guide"></a>Recomendações de segurança: um guia de referência

Este artigo lista as recomendações que pode ver no Centro de Segurança Azure. As recomendações apresentadas no seu ambiente dependem dos recursos que está a proteger e da sua configuração personalizada.

As recomendações do Centro de Segurança são baseadas nas melhores práticas. Algumas estão alinhadas com a **Referência de Segurança do Azure**, as diretrizes criadas pela Microsoft e específicas do Azure para as melhores práticas de segurança e conformidade com base em estruturas de conformidade comuns.
[Saiba mais sobre a Referência de Segurança do Azure](../security/benchmarks/introduction.md).

Para saber como responder a estas recomendações, consulte [recomendações do Remediate no Centro de Segurança Azure.](security-center-remediate-recommendations.md)

A sua Pontuação Segura baseia-se no número de recomendações do Centro de Segurança que concluiu. Para decidir quais as recomendações a resolver primeiro, olhe para a gravidade de cada um e o seu impacto potencial na sua Pontuação Segura.

> [!TIP]
> Se a descrição de uma recomendação diz "Nenhuma política relacionada", é geralmente porque essa recomendação depende de uma recomendação diferente e _da sua_ política. Por exemplo, a recomendação "As falhas de saúde de proteção endpoint devem ser remediadas...", baseia-se na recomendação de que verifica se uma solução de proteção de pontos finais é mesmo _instalada_ ("Solução de proteção de ponto final deve ser instalada..."). A recomendação subjacente _tem_ uma política.
> Limitar as políticas apenas à recomendação fundamental simplifica a gestão das políticas.

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Recomendações de cálculo

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Recomendações de dados

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>Recomendações do IdentityAndAccess

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Recomendações de redes

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Recomendações preprecadas

|Recomendação|Descrição & política relacionada|Gravidade|Correção rápida ativada? (Saiba[mais)](security-center-remediate-recommendations.md#quick-fix-remediation)|Tipo de recurso|
|----|----|----|----|----|
|**O acesso aos Serviços de Aplicações deve ser restringido**|Restringir o acesso aos seus Serviços de Aplicações alterando a configuração de rede, para negar o tráfego de entrada de gamas demasiado amplas.<br>(Política relacionada: [Pré-visualização]: O acesso aos Serviços de Aplicações deve ser restringido)|Alto|N|Serviço de aplicações|
|**As regras para aplicações web em IAAS NSGs devem ser endurecidas**|Endureça o grupo de segurança de rede (NSG) das suas máquinas virtuais que estão a executar aplicações web, com regras NSG excessivamente permissivas no que diz respeito às portas de aplicações web.<br>(Política relacionada: as regras do INE para aplicações web no IAAS devem ser endurecidas)|Alto|N|Máquina virtual|
|**As políticas de segurança do Pod devem ser definidas para reduzir o vetor de ataque removendo privilégios de aplicação desnecessários (Pré-visualização)**|Defina as políticas de segurança do Pod para reduzir o vetor de ataque removendo privilégios de aplicação desnecessários. Recomenda-se configurar as políticas de segurança do pod para que as cápsulas só possam aceder aos recursos a que possam aceder.<br>(Política relacionada: [Pré-visualização]: As políticas de segurança do Pod devem ser definidas nos serviços kubernetes)|Médio|N|Recursos computacional (Contentores)|
|**Instale o Azure Security Center para módulo de segurança IoT para obter mais visibilidade nos seus dispositivos IoT**|Instale o Azure Security Center para módulo de segurança IoT para obter mais visibilidade nos seus dispositivos IoT.|Baixo|N|Dispositivo IoT|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre recomendações, consulte o seguinte:

- [Recomendações de segurança no Centro de Segurança do Azure](security-center-recommendations.md)
- [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
