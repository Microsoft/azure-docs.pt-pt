---
title: Azure Security Center free vs Azure Defender habilitado
description: Conheça os benefícios de permitir o Azure Defender para proteção da carga de trabalho em nuvem no Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/08/2021
ms.openlocfilehash: d45dae8b0b3725555bd83a05032339671a9595be
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454369"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Azure Security Center free vs Azure Defender habilitado
O Azure Defender está livre nos primeiros 30 dias. Ao fim de 30 dias, caso opte por continuar a utilizar o serviço, começaremos automaticamente a cobrar para uso.

Pode atualizar a partir da página de **definições & de preços,** conforme descrito no [Quickstart: Enable Azure Defender](enable-azure-defender.md). Para obter detalhes sobre preços na sua moeda de eleição e de acordo com a sua região, consulte [os preços do Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Quais são os benefícios de permitir o Azure Defender?

O Centro de Segurança é oferecido em dois modos:

- **Azure Defender OFF** (Grátis) - O Security Center sem Azure Defender está habilitado gratuitamente em todas as suas subscrições Azure quando visitar o dashboard do Centro de Segurança Azure no portal Azure pela primeira vez, ou se ativado programáticamente através da API. A utilização deste modo gratuito fornece uma política de segurança, uma avaliação contínua da segurança e recomendações de segurança accitivas para o ajudar a proteger os seus recursos Azure.

- **Azure Defender ON** - Habilitando o Azure Defender alarga as capacidades do modo livre a cargas de trabalho em funcionamento em nuvens privadas e outras nuvens públicas, proporcionando uma gestão unificada de segurança e proteção de ameaças através das suas cargas de trabalho em nuvem híbrida. Algumas das principais características do Azure Defender:

    - **Microsoft Defender for Endpoint** - Azure Defender para servidores inclui [o Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) para uma deteção e resposta abrangentes de pontos finais (EDR). Saiba mais sobre os benefícios de utilizar o Microsoft Defender para Endpoint juntamente com a [solução EDR integrada do](security-center-wdatp.md)Azure Defender in Use Security Center .
    - **Pesquisa de vulnerabilidades para máquinas virtuais e registos** de contentores - Facilmente implemente um scanner em todas as suas máquinas virtuais que fornece a solução mais avançada da indústria para a gestão de vulnerabilidades. Ver, investigar e remediar as descobertas diretamente dentro do Centro de Segurança. 
    - **Segurança híbrida** – Obtenha uma visão unificada da segurança em todos os seus locais e cargas de trabalho em nuvem. Aplique políticas de segurança e avalie continuamente a segurança das suas cargas de trabalho em nuvem híbrida para garantir o cumprimento das normas de segurança. Recolher, pesquisar e analisar dados de segurança de várias fontes, incluindo firewalls e outras soluções parceiras.
    - **Alertas de proteção contra** ameaças - Análise comportamental avançada e o Gráfico de Segurança Inteligente da Microsoft fornecem uma vantagem sobre os ciberataques em evolução. A análise comportamental incorporada e a aprendizagem automática podem identificar ataques e explorações de dias zero. Monitorize redes, máquinas e serviços na nuvem para ataques de entrada e atividade pós-violação. Dinamize a investigação com ferramentas interativas e inteligência de ameaça contextual.
    - **Controlos de acesso e aplicação** (AAC) - Bloqueie malware e outras aplicações indesejadas aplicando recomendações de aprendizagem automática adaptadas às suas cargas de trabalho específicas para criar listas de permitir e negar. Reduza a superfície de ataque da rede com acesso controlado e justamente a portas de gestão em VMs Azure. O AAC reduz drasticamente a exposição à força bruta e a outros ataques de rede.
    - **Características** de segurança do contentor - Beneficie da gestão da vulnerabilidade e da proteção contra ameaças em tempo real nos seus ambientes contentorizados. Ao ativar o **Azure Defender para registos de contentores,** pode demorar até 12 horas até que todas as funcionalidades estejam ativadas. As cargas baseiam-se no número de imagens únicas de contentores empurradas para o seu registo ligado. Depois de uma imagem ter sido digitalizada uma vez, não será cobrado novamente, a menos que seja modificado e empurrado mais uma vez.
    - **Proteção contra ameaças de amplitude para recursos ligados ao ambiente Azure** - Azure Defender inclui proteção de ameaças de largura nativa azure para os serviços Azure comuns a todos os seus recursos: Azure Resource Manager, Azure DNS, camada de rede Azure e Cofre de Chaves Azure. O Azure Defender tem uma visibilidade única na camada de gestão Azure e na camada Azure DNS, podendo, portanto, proteger recursos em nuvem que estão ligados a essas camadas.


## <a name="faq---pricing-and-billing"></a>FAQ - Preços e faturação 

- [Como posso rastrear quem na minha organização permitiu mudanças no Azure Defender no Centro de Segurança?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Quais são os planos oferecidos pelo Centro de Segurança?](#what-are-the-plans-offered-by-security-center)
- [Como devo proceder para ativar o Azure Defender para a minha subscrição?](#how-do-i-enable-azure-defender-for-my-subscription)
- [Posso ativar o Azure Defender para os servidores num subconjunto de servidores na minha subscrição?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Se já tenho licença para o Microsoft Defender para o Endpoint, posso ter um desconto para o Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [A minha subscrição tem o Azure Defender para servidores ativados, pago por servidores não funcionando?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Serei cobrado por máquinas sem o agente Log Analytics instalado?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Se um agente do Log Analytics reportar a vários espaços de trabalho, serei cobrado duas vezes?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Se um agente do Log Analytics reportar a vários espaços de trabalho, a ingestão gratuita de dados de 500-MB está disponível em todos eles?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [A ingestão gratuita de dados de 500-MB é calculada para todo um espaço de trabalho ou estritamente por máquina?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)

### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Como posso rastrear quem na minha organização permitiu mudanças no Azure Defender no Centro de Segurança?
As Subscrições Azure podem ter vários administradores com permissões para alterar as definições de preços. Para saber que utilizador fez uma alteração, utilize o Registo de Atividades Azure.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Registo de atividades Azure mostrando um evento de mudança de preços":::

Se a informação do utilizador não estiver listada no **Evento iniciado por** coluna, explore o JSON do evento para obter os detalhes relevantes.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Azure Activity log JSON explorer":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Quais são os planos oferecidos pelo Centro de Segurança? 
O Centro de Segurança tem duas ofertas: 

- Centro de Segurança do Azure gratuito 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Como devo proceder para ativar o Azure Defender para a minha subscrição? 
Pode utilizar qualquer uma das seguintes formas de ativar o Azure Defender para a sua subscrição: 

| Método                                          | Instruções                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Páginas do Centro de Segurança Azure do portal Azure | [Ativar o Azure Defender](enable-azure-defender.md)                                                                                                  |
| API REST                                        | [Preços API](/rest/api/securitycenter/pricings)                                                                                                  |
| CLI do Azure                                       | [preços de segurança az](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Preços do pacote](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Posso ativar o Azure Defender para os servidores num subconjunto de servidores na minha subscrição?
N.º Quando ativar [o Azure Defender para servidores](defender-for-servers-introduction.md) numa subscrição, todos os servidores da subscrição serão protegidos pelo Azure Defender. 

Uma alternativa é ativar o Azure Defender para servidores ao nível do espaço de trabalho Log Analytics. Se o fizeres, apenas os servidores que reportem a esse espaço de trabalho serão protegidos e faturados. No entanto, várias capacidades estarão indisponíveis. Estes incluem acesso vm just-in-time, deteções de rede, conformidade regulamentar, endurecimento adaptativo da rede, controlo de aplicações adaptativos, e muito mais. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Se já tenho licença para o Microsoft Defender para o Endpoint, posso ter um desconto para o Azure Defender?
Se já tem uma licença para o Microsoft Defender para o Endpoint, não terá de pagar essa parte da licença do Azure Defender.

Para confirmar o seu desconto, contacte a equipa de apoio do Security Center e forneça o ID do espaço de trabalho relevante, região e informações de licença para cada licença relevante.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>A minha subscrição tem o Azure Defender para servidores ativados, pago por servidores não funcionando? 
N.º Quando ativar [o Azure Defender para servidores](defender-for-servers-introduction.md) numa subscrição, não será cobrado por nenhuma máquina que esteja no estado de energia translocado enquanto estiver nesse estado. As máquinas são faturadas de acordo com o seu estado de poder, tal como mostrado na tabela seguinte:

| Estado        | Descrição                                                                                                                                      | Taxa de utilização de instâncias |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| A iniciar     | O VM está a começar.                                                                                                                               | Não cobrado            |
| Em Execução      | Estado de trabalho normal para um VM                                                                                                                    | Faturado                |
| A parar     | Este é um estado de transição. Quando estiver concluído, mostrará como "Parado".                                                                           | Faturado                |
| Parada      | O VM foi desligado a partir do so do hóspede ou utilizando as APIs PowerOff. O hardware ainda está atribuído ao VM e permanece no anfitrião. | Faturado                |
| A desalocar | Estado de transição. Quando concluído, o VM mostrará como Deallocated.                                                                             | Não cobrado            |
| Desalocada  | O VM foi interrompido com sucesso e removido do hospedeiro.                                                                                  | Não cobrado            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="Máquinas virtuais Azure mostrando uma máquina deallocated":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Serei cobrado por máquinas sem o agente Log Analytics instalado?
Sim. Quando ativa [o Azure Defender para servidores](defender-for-servers-introduction.md) numa subscrição, as máquinas nessa subscrição obtêm uma série de proteções mesmo que não tenha instalado o agente Log Analytics.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Se um agente do Log Analytics reportar a vários espaços de trabalho, serei cobrado duas vezes? 
Sim. Se configurar o seu agente Log Analytics para enviar dados para dois ou mais espaços de trabalho do Log Analytics (multi-homing), será cobrado por cada espaço de trabalho que tenha uma solução 'Security' ou 'AntiMalware' instaladas. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Se um agente do Log Analytics reportar a vários espaços de trabalho, a ingestão gratuita de dados de 500-MB está disponível em todos eles?
Sim. Se configurar o seu agente Log Analytics para enviar dados para dois ou mais espaços de trabalho do Log Analytics (multi-homing), obterá ingestão gratuita de dados de 500 MB. É calculado por nó, por espaço de trabalho reportado, por dia, e disponível para cada espaço de trabalho que tenha uma solução 'Security' ou 'AntiMalware' instaladas. Será cobrado por quaisquer dados ingeridos ao longo dos 500 MB.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>A ingestão gratuita de dados de 500-MB é calculada para todo um espaço de trabalho ou estritamente por máquina?
Você receberá 500-MB de ingestão gratuita de dados por dia, para cada máquina ligada ao espaço de trabalho. Especificamente para tipos de dados de segurança recolhidos diretamente pelo Azure Security Center.

Estes dados são uma taxa diária média de todos os nós. Assim, mesmo que algumas máquinas enviem 100-MB e outras enviem 800-MB, se o total não exceder o limite de livre **[número de máquinas] x 500-MB,** não será cobrado extra.

## <a name="next-steps"></a>Passos seguintes
Este artigo explicava as opções de preços do Centro de Segurança. Para obter material relacionado, consulte:

- [Como otimizar os seus custos de carga de trabalho Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Detalhes de preços na sua moeda de eleição, e de acordo com a sua região](https://azure.microsoft.com/pricing/details/security-center/)
- Pode querer gerir os seus custos e limitar a quantidade de dados recolhidos para uma solução, limitando-os a um determinado conjunto de agentes. [A segmentação da solução](../azure-monitor/insights/solution-targeting.md) permite-lhe aplicar um âmbito à solução e direcionar um subconjunto de computadores no espaço de trabalho. Se estiver a utilizar a solução-alvo, o Centro de Segurança lista o espaço de trabalho como não tendo uma solução.