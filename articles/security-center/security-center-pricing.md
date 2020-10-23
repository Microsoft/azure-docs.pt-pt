---
title: Preços do Centro de Segurança Azure
description: O Azure Security Center é oferecido em dois modos com e sem Azure Defender.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 01d7d29e9fffff3be2c98c0ec52c01774439e3f7
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341193"
---
# <a name="pricing-of-azure-security-center"></a>Preços do Centro de Segurança Azure
O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, no local e noutras clouds. Proporciona visibilidade e controlo sobre cargas de trabalho em nuvem híbridas, defesas ativas que reduzem a sua exposição a ameaças e deteção inteligente para ajudá-lo a manter o ritmo com ataques cibernéticos em rápida evolução.


## <a name="free-option-vs-azure-defender-enabled"></a>Opção gratuita vs Azure Defender ativado

O Centro de Segurança é oferecido em dois modos:

- **Azure Defender OFF** (Grátis) - O Security Center sem Azure Defender está habilitado gratuitamente em todas as suas subscrições Azure quando visitar o dashboard do Centro de Segurança Azure no portal Azure pela primeira vez, ou se ativado programáticamente através da API. A utilização deste modo gratuito fornece uma política de segurança, uma avaliação contínua da segurança e recomendações de segurança accitivas para o ajudar a proteger os seus recursos Azure.

- **Azure Defender ON** - Habilitando o Azure Defender alarga as capacidades do modo livre a cargas de trabalho em funcionamento em nuvens privadas e outras nuvens públicas, proporcionando uma gestão unificada de segurança e proteção de ameaças através das suas cargas de trabalho em nuvem híbrida. Algumas das principais características do Azure Defender:

    - **Microsoft Defender for Endpoint** - Azure Defender para servidores inclui [o Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) para uma deteção e resposta abrangentes de pontos finais (EDR). Saiba mais sobre os benefícios de utilizar o Microsoft Defender para Endpoint juntamente com a [solução EDR integrada do](security-center-wdatp.md)Azure Defender in Use Security Center .
    - **Pesquisa de vulnerabilidades para máquinas virtuais e registos** de contentores - Facilmente implemente um scanner em todas as suas máquinas virtuais que fornece a solução mais avançada da indústria para a gestão de vulnerabilidades. Ver, investigar e remediar as descobertas diretamente dentro do Centro de Segurança. 
    - **Segurança híbrida** – Obtenha uma visão unificada da segurança em todos os seus locais e cargas de trabalho em nuvem. Aplique políticas de segurança e avalie continuamente a segurança das suas cargas de trabalho em nuvem híbrida para garantir o cumprimento das normas de segurança. Recolher, pesquisar e analisar dados de segurança de várias fontes, incluindo firewalls e outras soluções parceiras.
    - **Alertas de proteção contra** ameaças - Análise comportamental avançada e o Gráfico de Segurança Inteligente da Microsoft fornecem uma vantagem sobre os ciberataques em evolução. Alavancar a análise comportamental incorporada e a aprendizagem automática para identificar ataques e explorações de zero dias. Monitorize redes, máquinas e serviços na nuvem para ataques de entrada e atividade pós-violação. Dinamize a investigação com ferramentas interativas e inteligência de ameaça contextual.
    - **Controlos de acesso e aplicação** - Bloqueie malware e outras aplicações indesejadas aplicando recomendações de aprendizagem automática adaptadas às suas cargas de trabalho específicas para criar listas de permitir e negar. Reduza a superfície de ataque da rede com acesso controlado e justamente a portas de gestão em VMs Azure. Isto reduz drasticamente a exposição à força bruta e a outros ataques de rede.
    - **Características** de segurança do contentor - Beneficie da gestão da vulnerabilidade e da proteção contra ameaças em tempo real nos seus ambientes contentorizados. Ao ativar o **Azure Defender para registos de contentores,** pode demorar até 12 horas até que todas as funcionalidades estejam ativadas. As cargas baseiam-se no número de imagens únicas de contentores empurradas para o seu registo ligado. Depois de uma imagem ter sido digitalizada uma vez, não será cobrado novamente, a menos que seja modificado e empurrado mais uma vez. 

## <a name="try-azure-defender-free-for-30-days"></a>Experimentar o Azure Defender gratuitamente durante 30 dias

O Azure Defender está livre nos primeiros 30 dias. Ao fim de 30 dias, caso opte por continuar a utilizar o serviço, começaremos automaticamente a cobrar para uso.

## <a name="enable-azure-defender"></a>Ativar o Azure Defender

Pode proteger toda uma subscrição do Azure com o Azure Defender e as proteções serão herdadas por todos os recursos dentro da subscrição.

Para ativar o Azure Defender:

1. A partir do menu principal do Security Center, **selecione definições de preços &**.
1. Selecione a subscrição que pretende atualizar.
1. Selecione **Azure Defender** para atualizar.
1. Selecione **Guardar**.

Abaixo está a página de preços para uma subscrição de exemplo. Você vai notar que cada plano em Azure Defender tem um preço separado e pode ser definido individualmente para dentro ou fora.

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Página de preços do Centro de Segurança no portal":::

> [!NOTE]
> Para ativar todas as funcionalidades do Centro de Segurança, incluindo capacidades de proteção contra ameaças, deve ativar o Azure Defender na subscrição que contenha as cargas de trabalho aplicáveis. Permitir isso ao nível do espaço de trabalho não permite o acesso de VM a tempo, controlos de aplicações adaptativos e deteções de rede para recursos Azure. Além disso, os únicos planos do Azure Defender disponíveis ao nível do espaço de trabalho são o Azure Defender para servidores e o Azure Defender para servidores SQL em máquinas.
>
> Pode ativar **contas do Azure Defender para armazenamento** ao nível da subscrição ou ao nível de recursos.
> Pode ativar **o Azure Defender para SQL** a nível de subscrição ou ao nível de recursos.
> Pode ativar a proteção contra ameaças para **a Base de Dados Azure para MariaDB/ MySQL/ PostgreSQL apenas** ao nível dos recursos.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, foi introduzido no preço do Centro de Segurança. Para materiais relacionados ver:

- [Como otimizar os seus custos de carga de trabalho Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Detalhes de preços na sua moeda de eleição, e de acordo com a sua região](https://azure.microsoft.com/pricing/details/security-center/)
- Pode querer gerir os seus custos e limitar a quantidade de dados recolhidos para uma solução, limitando-os a um determinado conjunto de agentes. [A segmentação da solução](../azure-monitor/insights/solution-targeting.md) permite-lhe aplicar um âmbito à solução e direcionar um subconjunto de computadores no espaço de trabalho. Se estiver a utilizar a solução-alvo, o Centro de Segurança lista o espaço de trabalho como não tendo uma solução.