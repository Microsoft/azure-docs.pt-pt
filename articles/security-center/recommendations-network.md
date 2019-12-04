---
title: Recomendações da central de segurança do Azure para rede
description: Este artigo lista as recomendações de segurança da central de segurança do Azure que ajudam a proteger seus recursos de rede.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2019
ms.author: memildin
ms.openlocfilehash: 1b8d84286d14949c007d1ece3d089a7606464aaf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781972"
---
# <a name="network-recommendations---reference-guide"></a>Recomendações de rede – guia de referência

Este artigo fornece a lista completa das recomendações que você pode ver na central de segurança do Azure sobre a topologia da sua rede e seus pontos de extremidade voltados para a Internet.

Para obter uma explicação de como encontrá-las e como resolvê-las, consulte [aqui](security-center-network-recommendations.md).

## <a name="network-recommendations"></a>Recomendações de rede

|Nome da recomendação|Descrição|Gravidade|Classificação de segurança|Tipo de recurso|
|----|----|----|----|----|----|
|Os grupos de segurança de rede no nível de sub-rede devem ser habilitados|Habilite grupos de segurança de rede para controlar o acesso à rede de recursos implantados em suas sub-redes.|Alta/média|30|Subrede|
|As máquinas virtuais devem ser associadas a um grupo de segurança de rede|Habilite grupos de segurança de rede para controlar o acesso à rede de suas máquinas virtuais.|Alta/média|30|Máquina virtual|
|O acesso deve ser restrito para grupos de segurança de rede permissivos com VMs voltadas para a Internet|Proteja os grupos de segurança de rede de suas VMs voltadas para a Internet restringindo o acesso de suas regras de permissão existentes.|Elevado|20|Máquina virtual|
|As regras para aplicativos Web em IaaS NSGs devem ser protegidas|Proteger o NSG (grupo de segurança de rede) de suas máquinas virtuais que estão executando aplicativos Web, com regras do NSG que são excessivamente permissivas com relação às portas do aplicativo Web.|Elevado|20|Máquina virtual|
|O acesso aos serviços de aplicativos deve ser restrito|Restrinja o acesso aos serviços de aplicativos alterando a configuração de rede, para negar o tráfego de entrada de intervalos muito amplos.|Elevado|10|Serviço de aplicativo|
|As portas de gerenciamento devem ser fechadas em suas máquinas virtuais|Proteger o grupo de segurança de rede de suas máquinas virtuais para restringir o acesso às portas de gerenciamento.|Elevado|10|Máquina virtual|
A proteção contra DDoS Standard deve ser habilitada|Proteja redes virtuais que contêm aplicativos com IPs públicos habilitando o padrão de serviço de proteção contra DDoS. A proteção contra DDoS permite a mitigação de ataques de volumétricos de rede e de protocolo.|Elevado|10|Rede virtual|
|O encaminhamento IP em sua máquina virtual deve ser desabilitado|Desabilite o encaminhamento de IP. Quando o encaminhamento de IP está habilitado na NIC de uma máquina virtual, o computador pode receber o tráfego endereçado a outros destinos. O encaminhamento de IP raramente é necessário (por exemplo, ao usar a VM como uma solução de virtualização de rede) e, portanto, isso deve ser revisado pela equipe de segurança de rede.|Médio|10|Máquina virtual|
|O aplicativo Web só deve ser acessível via HTTPS|Habilite o acesso "somente HTTPS" para aplicativos Web. O uso de HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede.|Médio|20|Aplicação Web|
|O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais|Aplique o controle de acesso de máquina virtual (JIT) just-in-time para bloquear permanentemente o acesso às portas selecionadas e habilite os usuários autorizados a abri-los, por meio do JIT, apenas por uma quantidade limitada de tempo.|Elevado|20|Máquina virtual|
|Os aplicativos de funções só devem ser acessíveis via HTTPS|Habilite o acesso "somente HTTPS" para aplicativos de funções. O uso de HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede.|Médio|20|Function app|
|A transferência segura para contas de armazenamento deve ser habilitada|Habilite a transferência segura para contas de armazenamento. Transferência segura é uma opção que força sua conta de armazenamento a aceitar solicitações somente de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camada de rede, como Man-in-the-Middle, espionagem e seqüestro de sessão.|Elevado|20|Conta de armazenamento|


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Monitorar identidade e acesso](security-center-identity-access.md)
* [Protegendo seus computadores e aplicativos](security-center-virtual-machine-protection.md)
* [Protegendo seu serviço SQL do Azure](security-center-sql-service-recommendations.md)

