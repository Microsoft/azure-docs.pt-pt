---
title: Preços dos níveis do Centro de Segurança Azure
description: O Azure Security Center é oferecido em dois níveis - Grátis e Standard. Esta página mostra como fazer upgrade de Free to Standard.
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
ms.openlocfilehash: 882f8ab7e4f55809759f3a94fd04fb2c410fe188
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089724"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Upgrade para nível padrão para segurança reforçada

O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, no local e noutras clouds. Proporciona visibilidade e controlo sobre cargas de trabalho em nuvem híbridas, defesas ativas que reduzem a sua exposição a ameaças e deteção inteligente para ajudá-lo a manter o ritmo com ataques cibernéticos em rápida evolução.

## <a name="pricing-tiers"></a>Escalões de preço
O Centro de Segurança é disponibilizado em dois escalões:

- O nível **Gratuito** está ativado em todas as suas subscrições Azure quando visita o painel do Centro de Segurança Azure no portal Azure pela primeira vez, ou se ativado programáticamente através da API. O nível livre fornece política de segurança, avaliação contínua de segurança e recomendações de segurança accitivas para ajudá-lo a proteger os seus recursos Azure.

- O nível **Standard** alarga as capacidades do nível livre a cargas de trabalho em áreas de trabalho em nuvens privadas e outras nuvens públicas, proporcionando uma gestão unificada de segurança e proteção de ameaças através das suas cargas de trabalho em nuvem híbrida. O nível padrão também adiciona capacidades de proteção de ameaças, que usam análises comportamentais incorporadas e machine learning para identificar ataques e explorações de dia zero, controlos de acesso e aplicação para reduzir a exposição a ataques de rede e malware, e muito mais. Além disso, o nível padrão adiciona a verificação de vulnerabilidades para as suas máquinas virtuais. Pode experimentar o nível padrão de graça. O padrão do Security Center suporta recursos Azure, incluindo VMs, conjuntos de escala de máquina virtual, Serviço de Aplicações, servidores SQL e contas de Armazenamento. Se tiver a norma do Centro de Segurança Azure, pode optar por não suportar com base no tipo de recurso. 

A maioria das avaliações de segurança de nível livre para VMs, bem como muitos dos alertas de segurança de nível padrão, requerem a instalação do agente Log Analytics. Pode permitir que o provisionamento automático no Centro de Segurança implemente automaticamente o agente nos seus VMs Azure.

## <a name="try-standard-tier-free-for-30-days"></a>Experimente o nível padrão livre por 30 dias
O nível padrão é gratuito para os primeiros 30 dias. Ao fim de 30 dias, caso opte por continuar a utilizar o serviço, começaremos automaticamente a cobrar para uso.

Pode atualizar toda uma subscrição do Azure para o nível padrão, que é herdado por todos os recursos dentro da subscrição.

Para obter o nível padrão:

1. Selecione **as definições de preços &** no menu principal do Centro de **Segurança.**
2. Selecione a subscrição que pretende atualizar para standard.
3. **Selecione o nível de preços**.
4. Selecione **Standard** para atualizar.
5. Clique em **Save** (Guardar).

[![Preços do Centro de Segurança](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Para ativar todas as funcionalidades do Centro de Segurança, incluindo capacidades de proteção de ameaças, deve aplicar o nível de preços padrão à subscrição que contém as cargas de trabalho aplicáveis. Configurar os preços para um espaço de trabalho não permite o acesso em VM a tempo, os controlos de aplicações adaptativos e as deteções de rede para recursos Azure. 
>
> Pode permitir a proteção contra ameaças das **contas de Armazenamento Azure** ao nível da subscrição ou ao nível de recursos.
> Pode ativar a proteção contra ameaças para **servidores SQL base de dados SQL Azure,** quer ao nível da subscrição quer ao nível dos recursos.
> Pode ativar a proteção contra ameaças para **a Base de Dados Azure para MariaDB/ MySQL/ PostgreSQL apenas** ao nível dos recursos.


## <a name="why-upgrade-to-standard"></a>Porquê atualizar para o padrão?
O Security Center oferece uma maior segurança e proteção contra ameaças para as suas cargas de trabalho em nuvem híbrida, incluindo:

- **Segurança híbrida** – Obtenha uma visão unificada da segurança em todos os seus locais e cargas de trabalho em nuvem. Aplique políticas de segurança e avalie continuamente a segurança das suas cargas de trabalho em nuvem híbrida para garantir o cumprimento das normas de segurança. Recolher, pesquisar e analisar dados de segurança de várias fontes, incluindo firewalls e outras soluções parceiras.
- Alertas de segurança - Utilize **análises avançadas** e o Gráfico de Segurança Inteligente da Microsoft para obter uma vantagem sobre os ciberataques em evolução. Alavancar a análise comportamental incorporada e a aprendizagem automática para identificar ataques e explorações de zero dias. Monitorize redes, máquinas e serviços na nuvem para ataques de entrada e atividade pós-violação. Dinamize a investigação com ferramentas interativas e inteligência de ameaça contextual.
- **Pesquisa de vulnerabilidades para máquinas virtuais** - Implemente facilmente um scanner em todas as suas máquinas virtuais que fornece a solução mais avançada da indústria para a gestão de vulnerabilidades. Ver, investigar e remediar as descobertas diretamente dentro do Centro de Segurança. 
- **Controlos de acesso e aplicação** - Bloqueie malware e outras aplicações indesejadas aplicando recomendações de whitelisting alimentadas por machine learning adaptadas às suas cargas de trabalho específicas. Reduza a superfície de ataque da rede com acesso controlado e justamente a portas de gestão em VMs Azure. Isto reduz drasticamente a exposição à força bruta e a outros ataques de rede.
- **Características** de segurança do contentor - Beneficie da gestão da vulnerabilidade e da proteção contra ameaças em tempo real nos seus ambientes contentorizados. Ao ativar o recurso de registo de contentores, pode demorar até 12 horas até que todas as funcionalidades estejam ativadas. As cargas baseiam-se no número de imagens únicas de contentores empurradas para o seu registo ligado. Depois de uma imagem ter sido digitalizada uma vez, não será carregada novamente, a menos que seja modificada e empurrada mais uma vez. 




## <a name="next-steps"></a>Passos seguintes
Neste artigo, foi introduzido no preço do Centro de Segurança. Para saber mais sobre a segurança reforçada do nível padrão e a proteção avançada de ameaças, consulte:

- [Proteção contra ameaças no Centro de Segurança do Azure](threat-protection.md)
- [Controlo de acesso em VM just-in-time](security-center-just-in-time.md)
- [Descrição geral da segurança do contentor](container-security.md)
- [Detalhes de preços na sua moeda de eleição, e de acordo com a sua região](https://azure.microsoft.com/pricing/details/security-center/)