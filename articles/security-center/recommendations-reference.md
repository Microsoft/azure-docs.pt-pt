---
title: Tabela de referência para todas as recomendações do Centro de Segurança Azure
description: Este artigo lista as recomendações de segurança do Azure Security Center que o ajudam a endurecer e proteger os seus recursos.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 04/06/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: a44a5e4e715238c42e51e65dfe16d4f70dfcf54d
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504822"
---
# <a name="security-recommendations---a-reference-guide"></a>Recomendações de segurança: um guia de referência

Este artigo lista as recomendações que pode ver no Centro de Segurança Azure. As recomendações apresentadas no seu ambiente dependem dos recursos que está a proteger e da sua configuração personalizada.

As recomendações do Centro de Segurança baseiam-se no [Benchmark de Segurança Azure.](../security/benchmarks/introduction.md) A azure Security Benchmark é o conjunto de diretrizes específicas da Microsoft para a segurança e conformidade das melhores práticas com base em quadros comuns de conformidade. Este referencial amplamente respeitado baseia-se nos controlos do [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e do Instituto Nacional de [Normalização e Tecnologia (NIST)](https://www.nist.gov/) com foco na segurança centrada na nuvem.

Para saber como responder a estas recomendações, consulte [recomendações do Remediate no Centro de Segurança Azure.](security-center-remediate-recommendations.md)

A sua pontuação segura baseia-se no número de recomendações do Centro de Segurança que completou. Para decidir quais as recomendações a resolver primeiro, olhe para a gravidade de cada um e o seu impacto potencial na sua pontuação segura.

> [!TIP]
> Se a descrição de uma recomendação diz "Nenhuma política relacionada", é geralmente porque essa recomendação depende de uma recomendação diferente e _da sua_ política. Por exemplo, a recomendação "As falhas de saúde de proteção endpoint devem ser remediadas...", baseia-se na recomendação de que verifica se uma solução de proteção de pontos finais é mesmo _instalada_ ("Solução de proteção de ponto final deve ser instalada..."). A recomendação subjacente _tem_ uma política.
> Limitar as políticas apenas à recomendação fundamental simplifica a gestão das políticas.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>Recomendações do AppServices

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Recomendações de cálculo

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Recomendações do contentor

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Recomendações de dados

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>Recomendações do IdentityAndAccess

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>Recomendações ioT

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Recomendações de redes

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Recomendações preprecadas

|Recomendação|Descrição & política relacionada|Gravidade|
|----|----|----|
|O acesso aos Serviços de Aplicações deve ser restringido|Restringir o acesso aos seus Serviços de Aplicações alterando a configuração de rede, para negar o tráfego de entrada de gamas demasiado amplas.<br>(Política relacionada: [Pré-visualização]: O acesso aos Serviços de Aplicações deve ser restringido)|Alto|
|As regras para aplicações web em IAAS NSGs devem ser endurecidas|Endureça o grupo de segurança de rede (NSG) das suas máquinas virtuais que estão a executar aplicações web, com regras NSG excessivamente permissivas no que diz respeito às portas de aplicações web.<br>(Política relacionada: as regras do INE para aplicações web no IAAS devem ser endurecidas)|Alto|
|As políticas de segurança do Pod devem ser definidas para reduzir o vetor de ataque removendo privilégios de aplicação desnecessários (Pré-visualização)|Defina as políticas de segurança do Pod para reduzir o vetor de ataque removendo privilégios de aplicação desnecessários. Recomenda-se configurar as políticas de segurança do pod para que as cápsulas só possam aceder aos recursos a que possam aceder.<br>(Política relacionada: [Pré-visualização]: As políticas de segurança do Pod devem ser definidas nos serviços kubernetes)|Médio|
|Instale o Azure Security Center para módulo de segurança IoT para obter mais visibilidade nos seus dispositivos IoT|Instale o Azure Security Center para módulo de segurança IoT para obter mais visibilidade nos seus dispositivos IoT.|Baixo|
|As suas máquinas devem ser reiniciadas para aplicar atualizações do sistema|Reinicie as suas máquinas para aplicar as atualizações do sistema e proteger a máquina de vulnerabilidades. (Política relacionada: As atualizações do sistema devem ser instaladas nas suas máquinas)|Médio|
|O agente de monitorização deve ser instalado nas suas máquinas|Esta ação instala um agente de monitorização nas máquinas virtuais selecionadas. Selecione um espaço de trabalho para o agente se apresentar. (Sem política relacionada)|Alto|
||||

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre recomendações, consulte o seguinte:

- [O que são políticas de segurança, iniciativas e recomendações?](security-policy-concept.md)
- [Reveja as suas recomendações de segurança](security-center-recommendations.md)
