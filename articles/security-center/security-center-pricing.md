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
ms.openlocfilehash: bc17b63ef8f83e4086262d52334a518ec85f4fc6
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95792609"
---
# <a name="pricing-of-azure-security-center"></a>Preços do Centro de Segurança Azure
O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, no local e noutras clouds. Proporciona visibilidade e controlo sobre cargas de trabalho em nuvem híbrida, defesas ativas que reduzem a sua exposição a ameaças e deteção inteligente para ajudá-lo a manter o ritmo com riscos cibernéticos em rápida evolução.


## <a name="free-option-vs-azure-defender-enabled"></a>Opção gratuita vs Azure Defender ativado

O Centro de Segurança é oferecido em dois modos:

- **Azure Defender OFF** (Grátis) - O Security Center sem Azure Defender está habilitado gratuitamente em todas as suas subscrições Azure quando visitar o dashboard do Centro de Segurança Azure no portal Azure pela primeira vez, ou se ativado programáticamente através da API. A utilização deste modo gratuito fornece uma política de segurança, uma avaliação contínua da segurança e recomendações de segurança accitivas para o ajudar a proteger os seus recursos Azure.

- **Azure Defender ON** - Habilitando o Azure Defender alarga as capacidades do modo livre a cargas de trabalho em funcionamento em nuvens privadas e outras nuvens públicas, proporcionando uma gestão unificada de segurança e proteção de ameaças através das suas cargas de trabalho em nuvem híbrida. Algumas das principais características do Azure Defender:

    - **Microsoft Defender for Endpoint** - Azure Defender para servidores inclui [o Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) para uma deteção e resposta abrangentes de pontos finais (EDR). Saiba mais sobre os benefícios de utilizar o Microsoft Defender para Endpoint juntamente com a [solução EDR integrada do](security-center-wdatp.md)Azure Defender in Use Security Center .
    - **Pesquisa de vulnerabilidades para máquinas virtuais e registos** de contentores - Facilmente implemente um scanner em todas as suas máquinas virtuais que fornece a solução mais avançada da indústria para a gestão de vulnerabilidades. Ver, investigar e remediar as descobertas diretamente dentro do Centro de Segurança. 
    - **Segurança híbrida** – Obtenha uma visão unificada da segurança em todos os seus locais e cargas de trabalho em nuvem. Aplique políticas de segurança e avalie continuamente a segurança das suas cargas de trabalho em nuvem híbrida para garantir o cumprimento das normas de segurança. Recolher, pesquisar e analisar dados de segurança de várias fontes, incluindo firewalls e outras soluções parceiras.
    - **Alertas de proteção contra** ameaças - Análise comportamental avançada e o Gráfico de Segurança Inteligente da Microsoft fornecem uma vantagem sobre os ciberataques em evolução. A análise comportamental incorporada e a aprendizagem automática podem identificar ataques e explorações de dias zero. Monitorize redes, máquinas e serviços na nuvem para ataques de entrada e atividade pós-violação. Dinamize a investigação com ferramentas interativas e inteligência de ameaça contextual.
    - **Controlos de acesso e aplicação** (AAC) - Bloqueie malware e outras aplicações indesejadas aplicando recomendações de aprendizagem automática adaptadas às suas cargas de trabalho específicas para criar listas de permitir e negar. Reduza a superfície de ataque da rede com acesso controlado e justamente a portas de gestão em VMs Azure. O AAC reduz drasticamente a exposição à força bruta e a outros ataques de rede.
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


## <a name="faq---pricing-and-billing"></a>FAQ - Preços e faturação 

### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-azure-security-center"></a>Como posso rastrear quem na minha organização permitiu mudanças no Azure Defender no Azure Security Center
As Subscrições Azure podem ter vários administradores com permissões para alterar as definições de preços. Para saber que utilizador fez uma alteração, utilize o Registo de Atividades Azure.

Se a informação do utilizador não estiver listada no **Evento iniciado por** coluna, explore o evento para obter os detalhes relevantes.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Azure Event Log mostrando um evento de mudança de preços":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Quais são os planos oferecidos pelo Centro de Segurança? 
O Centro de Segurança tem duas ofertas: 

- Centro de Segurança do Azure gratuito 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Como devo proceder para ativar o Azure Defender para a minha subscrição? 
Pode utilizar qualquer uma das seguintes formas de ativar o Azure Defender para a sua subscrição: 

|Método  |Instruções  |
|---------|---------|
|Páginas do Centro de Segurança Azure do portal Azure|[Ativar o Azure Defender](#enable-azure-defender)|
|API REST|[Preços API](https://docs.microsoft.com/rest/api/securitycenter/pricings)|
|CLI do Azure|[preços de segurança az](https://docs.microsoft.com/cli/azure/security/pricing)|
|PowerShell|[Set-AzSecurityPricing](https://docs.microsoft.com/powershell/module/az.security/set-azsecuritypricing)|
|Azure Policy|[Preços do pacote](https://github.com/Azure/Azure-Security-Center/tree/master/Pricing%20%26%20Settings/Azure%20Policy%20definitions/Bundle%20Pricings)|
|||

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Posso ativar o Azure Defender para os servidores num subconjunto de servidores na minha subscrição?
Não. Quando ativar [o Azure Defender para servidores](defender-for-servers-introduction.md) numa subscrição, todos os servidores da subscrição serão protegidos pelo Azure Defender. 

Uma alternativa é ativar o Azure Defender para servidores ao nível do espaço de trabalho Log Analytics. Se o fizeres, apenas os servidores que reportem a esse espaço de trabalho serão protegidos e faturados. No entanto, várias capacidades estarão indisponíveis. Estes incluem acesso vm just-in-time, deteções de rede, conformidade regulamentar, endurecimento adaptativo da rede, controlo de aplicações adaptativos, e muito mais. 


### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>A minha subscrição tem o Azure Defender para servidores ativados, pago por servidores não funcionando? 
Não. Quando ativar [o Azure Defender para servidores](defender-for-servers-introduction.md) numa subscrição, será cobrado de hora a hora apenas para executar servidores. Não será cobrado por nenhum servidor desligado durante o tempo de desligação. 

> [!TIP]
> Isto também se aplica aos outros tipos de recursos protegidos pelo Security Center. 

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Serei cobrado por máquinas sem o agente Log Analytics instalado?
Sim. Quando ativa [o Azure Defender para servidores](defender-for-servers-introduction.md) numa subscrição, as máquinas nessa subscrição obtêm uma série de proteções mesmo que não tenha instalado o agente Log Analytics.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Se um agente do Log Analytics reportar a vários espaços de trabalho, serei cobrado duas vezes? 
Sim. Se configurar o seu agente Log Analytics para enviar dados para dois ou mais espaços de trabalho do Log Analytics (multi-homing), será cobrado por cada espaço de trabalho que tenha uma solução 'Security' ou 'AntiMalware' instaladas. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Se um agente do Log Analytics reportar a vários espaços de trabalho, a ingestão gratuita de dados de 500-MB está disponível em todos eles?
Sim. Se configurar o seu agente Log Analytics para enviar dados para dois ou mais espaços de trabalho do Log Analytics (multi-homing), obterá ingestão gratuita de dados de 500 MB. É calculado por nó, por espaço de trabalho reportado, por dia, e disponível para cada espaço de trabalho que tenha uma solução 'Security' ou 'AntiMalware' instaladas. Será cobrado por quaisquer dados ingeridos ao longo dos 500 MB.


## <a name="next-steps"></a>Passos seguintes
Este artigo explicava as opções de preços do Centro de Segurança. Para obter material relacionado, consulte:

- [Como otimizar os seus custos de carga de trabalho Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Detalhes de preços na sua moeda de eleição, e de acordo com a sua região](https://azure.microsoft.com/pricing/details/security-center/)
- Pode querer gerir os seus custos e limitar a quantidade de dados recolhidos para uma solução, limitando-os a um determinado conjunto de agentes. [A segmentação da solução](../azure-monitor/insights/solution-targeting.md) permite-lhe aplicar um âmbito à solução e direcionar um subconjunto de computadores no espaço de trabalho. Se estiver a utilizar a solução-alvo, o Centro de Segurança lista o espaço de trabalho como não tendo uma solução.