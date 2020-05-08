---
title: Preços dos níveis do Azure Security Center
description: O Azure Security Center é oferecido em dois níveis - Grátis e Standard. Esta página mostra como atualizar de Free para Standard.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2020
ms.author: memildin
ms.openlocfilehash: 2eb302462e402e701cc5ae664239c457c90ab1b9
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612797"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Upgrade para nível Standard para maior segurança

O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, no local e noutras clouds. Proporciona visibilidade e controlo sobre cargas de trabalho híbridas em nuvem, defesas ativas que reduzem a sua exposição a ameaças, e deteção inteligente para ajudá-lo a manter o ritmo com ataques cibernéticos em rápida evolução.

## <a name="pricing-tiers"></a>Escalões de preço
O Centro de Segurança é disponibilizado em dois escalões:

- O **Free** free tier está ativado em todas as suas subscrições Azure quando visita pela primeira vez o painel do Azure Security Center no portal Azure, ou se estiver programado programáticamente através da API. O nível livre fornece política de segurança, avaliação contínua de segurança e recomendações de segurança atoveis para ajudá-lo a proteger os seus recursos Azure.

- O nível **Standard** alarga as capacidades do nível livre às cargas de trabalho em funcionamento em nuvens privadas e outras nuvens públicas, proporcionando uma gestão de segurança unificada e proteção contra ameaças em todas as suas cargas de trabalho híbridas em nuvem. O nível padrão também adiciona capacidades de proteção contra ameaças, que usam análises comportamentais incorporadas e machine learning para identificar ataques e explorações de zero dias, controlos de acesso e aplicações para reduzir a exposição a ataques de rede e malware, e muito mais. Além disso, o nível padrão adiciona a verificação de vulnerabilidade para as suas máquinas virtuais. Pode experimentar gratuitamente o nível padrão. O padrão do Security Center suporta recursos Azure, incluindo VMs, conjuntos de escala de máquinas virtuais, Serviço de Aplicações, servidores SQL e contas de Armazenamento. Se tiver o padrão do Azure Security Center, pode optar por não suportar com base no tipo de recurso. 

A maioria das avaliações de segurança de nível livre para VMs, bem como muitos dos alertas de segurança padrão, requerem a instalação do agente Log Analytics. Pode permitir que o fornecimento automático no Security Center implemente automaticamente o agente nos seus VMs Azure.

## <a name="try-standard-tier-free-for-30-days"></a>Experimente o nível padrão gratuito durante 30 dias
O nível padrão é gratuito nos primeiros 30 dias. Ao fim de 30 dias, caso opte por continuar a utilizar o serviço, começaremos automaticamente a cobrar para utilização.

Pode atualizar toda uma subscrição azure para o nível padrão, que é herdado por todos os recursos dentro da subscrição.

Para obter o nível padrão:

1. Selecione **definições de preços &** no menu principal do **Security Center.**
2. Selecione a subscrição que pretende atualizar para o padrão.
3. Selecione **o nível de preços**.
4. Selecione **Standard** para atualizar.
5. Clique em **Guardar**.

[![Preços do Centro de Segurança](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Para ativar todas as funcionalidades do Centro de Segurança, incluindo capacidades de proteção contra ameaças, deve aplicar o nível de preços padrão à subscrição que contém as cargas de trabalho aplicáveis. Configurar preços para um espaço de trabalho não permite o acesso a vm just-in-time, controlos adaptáveis de aplicações e deteções de rede para recursos Azure. 
>
> Pode ativar a proteção contra ameaças para **as contas de Armazenamento Azure** ao nível de subscrição ou ao nível de recursos.
> Pode ativar a proteção contra ameaças para **servidores SQL Database SQL Do Azure,** quer ao nível da subscrição, quer ao nível dos recursos.
> Pode ativar a proteção contra ameaças para a Base de **Dados Azure para MariaDB/ MySQL/ PostgreSQL** apenas ao nível dos recursos.


## <a name="why-upgrade-to-standard"></a>Por que fazer upgrade para o padrão?
O Security Center oferece uma maior segurança e proteção contra ameaças para as suas cargas de trabalho híbridas em nuvem, incluindo:

- **Segurança híbrida** – Obtenha uma visão unificada de segurança em todas as suas instalações e cargas de trabalho em nuvem. Aplique políticas de segurança e avalie continuamente a segurança das suas cargas de trabalho híbridas em nuvem para garantir o cumprimento das normas de segurança. Recolher, pesquisar e analisar dados de segurança de várias fontes, incluindo firewalls e outras soluções de parceiros.
- **Alertas de segurança** - Utilize análises avançadas e o Microsoft Intelligent Security Graph para obter uma vantagem sobre ciberataques em evolução. Alavancar a análise comportamental incorporada e a aprendizagem automática para identificar ataques e explorações de zero dias. Monitorize redes, máquinas e serviços de nuvem para ataques e atividade pós-violação. Agilize a investigação com ferramentas interativas e inteligência de ameaça contextual.
- **Vulnerabilidade de pesquisa de máquinas virtuais** - Facilmente implemente um scanner para todas as suas máquinas virtuais que fornece a solução mais avançada da indústria para a gestão de vulnerabilidades. Ver, investigar e remediar as descobertas diretamente dentro do Centro de Segurança. 
- **Controlos** de acesso e aplicação - Bloqueie malware e outras aplicações indesejadas aplicando recomendações de whitelisting alimentadas a machine learning adaptadas às suas cargas de trabalho específicas. Reduza a superfície de ataque da rede com acesso controlado e justo aos portos de gestão em VMs Azure. Isto reduz drasticamente a exposição à força bruta e a outros ataques de rede.
- **Características de segurança** de contentores - Beneficie da gestão da vulnerabilidade e da proteção contra ameaças em tempo real nos seus ambientes contentorizados. Ao ativar o recurso de registo de contentores, pode demorar até 12 horas até que todas as funcionalidades estejam ativadas.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, foi apresentado aos preços do Centro de Segurança. Para saber mais sobre a segurança reforçada do nível Standard e a proteção avançada contra ameaças, consulte:

- [Proteção contra ameaças no Centro de Segurança do Azure](threat-protection.md)
- [Controlo de acesso VM just-in-time](security-center-just-in-time.md)
- [Descrição geral da segurança do contentor](container-security.md)
- [Detalhes de preços na sua moeda de eleição, e de acordo com a sua região](https://azure.microsoft.com/pricing/details/security-center/)