---
title: Guia de Planeamento e Operações do Centro de Segurança
description: Este documento ajuda-o a planear antes de adotar o Centro de Segurança do Azure e as considerações relativas à operações diárias.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: a8b285ca0b8d0dae88bfe8dd19ac7e1bc8372e32
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099204"
---
# <a name="planning-and-operations-guide"></a>Guia de planeamento e operações
Este guia destina-se a profissionais de tecnologias da informação (TI), arquitetos de TI, analistas de segurança da informação e administradores de nuvem que planeiam usar o Azure Security Center.


## <a name="planning-guide"></a>Planning guide (Guia de planeamento)
Este guia cobre tarefas que pode seguir para otimizar a sua utilização do Security Center com base nos requisitos de segurança da sua organização e no modelo de gestão da nuvem. Para tirar o máximo partido do Centro de Segurança, é importante compreender de que forma diferentes pessoas ou equipas na sua organização utilizam o serviço para dar resposta às necessidades de desenvolvimento e operações seguras, monitorização, governação e resposta a incidentes. As principais áreas a considerar quando planear utilizar o Centro de Segurança são:

* Funções de Segurança e Controlos de Acesso
* Recomendações e Políticas de Segurança
* Armazenamento e Recolha de Dados
* Incluir recursos não Azure
* Monitorização de Segurança em Curso
* Resposta a Incidentes

Na secção seguinte irá aprender a planear cada uma dessas áreas e aplicar essas recomendações com base nos seus requisitos.


> [!NOTE]
> Leia as [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure](faq-general.md) para obter uma lista de perguntas comuns que também podem ser úteis durante a fase de conceção e planeamento.

## <a name="security-roles-and-access-controls"></a>Funções de segurança e controlos de acesso
Dependendo do tamanho e da estrutura da sua organização, várias pessoas e equipas podem utilizar o Centro de Segurança para executar diversas tarefas relacionadas com a segurança. No diagrama abaixo, segue-se um exemplo de pessoas fictícias e as respetivas funções e responsabilidades de segurança:

![Funções](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

O Centro de Segurança permite que estes indivíduos cumpram estas variadas responsabilidades. Por exemplo:

**Jorge (Proprietário de Carga de Trabalho)**

* Gerir uma carga de trabalho de nuvem e os respetivos recursos relacionados
* Responsável pela implementação e manutenção de proteções de acordo com a política de segurança da empresa

**Helena (CISO/CIO)**

* Responsável por todos os aspetos de segurança na empresa
* Pretende compreender a posição sobre segurança da empresa em cargas de trabalho de nuvem
* Tem de ser informado sobre principais ataques e riscos

**Diogo (Segurança de TI)**

* Define as políticas de segurança da empresa para assegurar que estão implementadas as proteções adequadas
* Monitoriza a conformidade com as políticas
* Gera relatórios para liderança ou auditores

**Júlia (Operações de Segurança)**

* Monitoriza e responde a alertas de segurança 24/7
* Escala para o proprietário de carga de trabalho da nuvem ou Analista de Segurança de TI

**Samuel (Analista de Segurança)**

* Investigar ataques
* Funciona com o Proprietário de Carga de Trabalho da Cloud para aplicar a remediação

O Security Center utiliza [o controlo de acesso baseado em funções Azure (Azure RBAC),](../role-based-access-control/role-assignments-portal.md)que fornece [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços em Azure. Quando um utilizador abre o Centro de Segurança, vê apenas informações relacionadas com os recursos a que tem acesso. Isso significa que é atribuído ao utilizador a função de Proprietário, Contribuinte ou Leitor para a subscrição ou grupo de recursos a que pertence um recurso. Além destas funções, há duas funções específicas do Centro de Segurança:

- **Leitor de segurança**: os utilizadores que pertencem a esta função só conseguem ver as configurações do Centro de Segurança, que incluem recomendações, alertas, políticas e estado de funcionamento, mas não podem fazer alterações.
- **Administrador de segurança**: igual ao leitor de segurança, mas também pode atualizar a política de segurança, bem como ignorar recomendações e alertas.

As funções do Centro de Segurança descritas acima não têm acesso a outras áreas de serviço do Azure como o Armazenamento, Web e Móvel ou a Internet das Coisas.

Utilizando as personas explicadas no diagrama anterior, seria necessário o seguinte Azure RBAC:

**Jorge (Proprietário de Carga de Trabalho)**

* Proprietário/Colaborador do Grupo de Recursos

**Helena (CISO/CIO)**

* Proprietário/Colaborador de Assinatura ou Administrador de Segurança

**Diogo (Segurança de TI)**

* Proprietário/Colaborador de Assinatura ou Administrador de Segurança

**Júlia (Operações de Segurança)**

* Leitor de Subscrição ou Leitor de Segurança para visualizar Alertas
* Proprietário/Colaborador de Assinatura ou Administrador de Segurança obrigado a dispensar alertas

**Samuel (Analista de Segurança)**

* Leitor de Subscrição para visualizar Alertas
* Proprietário/Colaborador de Assinatura obrigado a dispensar alertas
* O acesso à área de trabalho poderá ser necessário

Mais algumas informações importantes a considerar:

* Apenas os Proprietários/Contribuintes e os Administradores de Segurança das subscrições podem editar uma política de segurança.
* Apenas os Proprietários e os Contribuintes do grupo de recursos e da subscrição podem aplicar recomendações de segurança a um recurso

Ao planear o controlo de acesso usando o Azure RBAC para o Security Center, não se esqueça de quem na sua organização estará a usar o Security Center. Além disso, que tipos de tarefas irão realizar e, em seguida, configurar a Azure RBAC em conformidade.

> [!NOTE]
> Recomendamos que atribua a função menos permissiva necessária para que os utilizadores concluam as respetivas tarefas. Por exemplo, os utilizadores que apenas necessitam de ver informações acerca do estado de segurança dos recursos, mas não têm de tomar medidas, como aplicar as recomendações ou editar políticas, também devem ter atribuída a função de Leitor.
>
>

## <a name="security-policies-and-recommendations"></a>Recomendações e políticas de segurança
As políticas de segurança definem a configuração pretendida para as suas cargas de trabalho e ajudam a garantir a conformidade com os requisitos da empresa ou regulamentares. No Centro de Segurança, pode definir políticas para as suas subscrições do Azure, que podem ser adaptadas ao tipo de carga de trabalho ou à sensibilidade dos dados.

As políticas do Centro de Segurança contêm os componentes seguintes:
- [Recolha de dados](security-center-enable-data-collection.md): aprovisionamento de agente e definições de recolha de dados.
- [Política de](tutorial-security-policy.md)segurança : uma [Política Azure](../governance/policy/overview.md) que determina quais os controlos monitorizados e recomendados pelo Security Center, ou usar a Política Azure para criar novas definições, definir políticas adicionais e atribuir políticas entre grupos de gestão.
- [Notificações por e-mail](security-center-provide-security-contact-details.md): contactos de segurança e definições de notificação.
- [Nível de preços](security-center-pricing.md): com ou sem Azure Defender, que determina quais as funcionalidades do Centro de Segurança disponíveis para recursos de âmbito (pode ser especificado para subscrições e espaços de trabalho, ou grupos de recursos que utilizam a API).

> [!NOTE]
> Especificar um contacto de segurança garante que o Azure consegue contactar a pessoa certa da sua organização se ocorrer um incidente de segurança. Leia [Provide security contact details in Azure Security Center (Fornecer detalhes de contacto de segurança no Centro de Segurança do Azure)](security-center-provide-security-contact-details.md) para obter mais informações sobre como ativar esta recomendação.

### <a name="security-policies-definitions-and-recommendations"></a>Definições e recomendações de políticas de segurança
O Centro de Segurança cria automaticamente uma política de segurança predefinida para cada uma das suas subscrições do Azure. Pode editá-la no Centro de Segurança ou utilizar a Política do Azure para criar definições novas, definir políticas adicionais e atribuir políticas a vários Grupos de Gestão (que podem representar toda a organização, uma unidade de negócio, etc.) e monitorizar a conformidade com estas políticas nestes âmbitos.

Antes de configurar as políticas de segurança, reveja cada uma das [recomendações de segurança](security-center-recommendations.md) e determine se estas são adequadas para as suas várias subscrições e grupos de recursos. Também é importante entender que ação deve ser realizada para abordar as recomendações de segurança e quem na sua organização será responsável por monitorizar novas recomendações e tomar as medidas necessárias.

## <a name="data-collection-and-storage"></a>Armazenamento e recolha de dados
O Azure Security Center utiliza o agente Log Analytics – este é o mesmo agente utilizado pelo serviço Azure Monitor – para recolher dados de segurança das suas máquinas virtuais. Os [dados recolhidos](security-center-enable-data-collection.md) neste agente serão armazenados nas suas áreas de trabalho do Log Analytics.

### <a name="agent"></a>Agente

Quando o fornecimento automático é ativado na política de segurança, o agente Log Analytics (para [Windows](../azure-monitor/agents/agent-windows.md) ou [Linux)](../azure-monitor/vm/quick-collect-linux-computer.md)é instalado em todos os VMs Azure suportados e quaisquer novos que sejam criados. Se o VM ou o computador já tiver o agente Log Analytics instalado, o Azure Security Center alavancará o agente instalado atual. O processo do agente foi concebido para não ser invasivo e ter um impacto muito mínimo no desempenho do VM.

O agente Log Analytics para Windows requer a utilização da porta TCP 443. Veja o [artigo Resolução de Problemas](security-center-troubleshooting-guide.md) para mais detalhes.

Se a determinada altura pretender desativar a Recolha de Dados, pode desativá-la na política de segurança. No entanto, uma vez que o agente Log Analytics pode ser utilizado por outros serviços de gestão e monitorização da Azure, o agente não será desinstalado automaticamente quando desativar a recolha de dados no Centro de Segurança. Pode desinstalar o agente manualmente, se necessário.

> [!NOTE]
> Para encontrar uma lista de VMs suportadas, leia as [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure ](faq-vms.md).

### <a name="workspace"></a>Área de trabalho

Uma área de trabalho é um recurso do Azure que funciona como um contentor para os dados. O utilizador ou outros membros da sua organização podem utilizar várias áreas de trabalho para gerir diferentes conjuntos de dados recolhidos da totalidade ou de partes da sua infraestrutura de TI.

Os dados recolhidos do agente Log Analytics (em nome do Azure Security Center) serão armazenados num espaço de trabalho existente do Log Analytics associado à sua subscrição Azure ou num novo espaço de trabalho, tendo em conta o Geo do VM.

No portal do Azure, pode procurar uma lista das áreas de trabalho do Log Analytics, incluindo as que foram criadas pelo Centro de Segurança do Azure. Um grupo de recursos relacionado será criado para as novas áreas de trabalho. Ambos seguirão esta convenção de nomenclatura:

* Área de trabalho: *DefaultWorkspace-[subscription-ID]-[geo]*
* Grupo de Recursos: *DefaultResourceGroup-[geo]*

Para áreas de trabalho criadas pelo Centro de Segurança do Azure, os dados são retidos durante 30 dias. Para as áreas de trabalho existentes, a retenção baseia-se no escalão de preço da área de trabalho. Se quiser, também pode utilizar uma área de trabalho existente.

> [!NOTE]
> A Microsoft compromete-se fortemente a proteger a privacidade e a segurança destes dados. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço. Para obter mais informações sobre o processamento de dados e privacidade, leia [Segurança de Dados do Centro de Segurança do Azure](security-center-data-security.md).
>

## <a name="onboard-non-azure-resources"></a>Recursos não-Azure a bordo

O Centro de Segurança pode monitorizar a postura de segurança dos seus computadores não Azure, mas, primeiro, tem de incluir esses recursos. Leia [os computadores não-Azure](quickstart-onboard-machines.md) a bordo para obter mais informações sobre como embarcar recursos não-Azure.

## <a name="ongoing-security-monitoring"></a>Monitorização de segurança em curso
Após a configuração inicial e a aplicação das recomendações do Centro de Segurança, o passo seguinte é considerar os processos operacionais do Centro de Segurança.

A Descrição Geral do Centro de Segurança proporciona uma vista unificada da segurança de todos os seus recursos do Azure e de eventuais recursos não Azure que tenha ligado. O exemplo abaixo mostra um ambiente com muitos problemas para resolver:

![Dashboard](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> O Centro de Segurança não irá interferir com os seus procedimentos operacionais normais, irá monitorizar passivamente as implementações e facultar recomendações com base nas políticas de segurança que ativou.

Quando optar pela primeira vez a utilizar o Security Center para o seu ambiente Azure atual, certifique-se de que revê todas as recomendações, que podem ser feitas na página **recomendações.**

Considere visitar a opção informações sobre ameaças como parte das suas operações de segurança diárias. Aí, pode identificar ameaças de segurança relativas a cada ambiente, como, por exemplo, identificar se um determinado computador faz parte de um botnet.

### <a name="monitoring-for-new-or-changed-resources"></a>Monitorização de recursos novos ou alterados

A maioria dos ambientes Azure são dinâmicos, com recursos regularmente criados, girados para cima ou para baixo, reconfigurados e alterados. O Centro de Segurança ajuda-o a garantir que tem visibilidade em relação ao estado de segurança destes novos recursos.

Quando adicionar novos recursos (VMs, BDs SQL) ao seu Ambiente do Azure, o Centro de Segurança irá detetar automaticamente estes recursos e começar a monitorizar a segurança dos mesmos. Isto também inclui funções da Web e funções de trabalho PaaS. Se a Recolha de Dados estiver ativada na [Política de Segurança](tutorial-security-policy.md), serão ativadas automaticamente capacidades de monitorização adicionais para as suas máquinas virtuais.

Também deve monitorizar regularmente os recursos existentes para alterações de configuração que possam ter criado riscos de segurança, deriva das linhas de base recomendadas e alertas de segurança. 

### <a name="hardening-access-and-applications"></a>Proteger o acesso e as aplicações

Como parte das suas operações de segurança, deve também adotar medidas preventivas para limitar o acesso às VMs e controlar as aplicações que estão a ser executadas nas mesmas. Ao restringir o tráfego de entrada às VMs do Azure, reduz a exposição a ataques e, paralelamente, proporciona acesso fácil para ligar a VMs sempre que necessário. Utilize a funcionalidade de acesso [vm just-in-time](security-center-just-in-time.md) para endurecer o acesso aos seus VMs.

Pode utilizar [controlos de aplicação adaptativos](security-center-adaptive-application.md) para limitar quais aplicações podem ser executadas nos seus VMs localizados em Azure. Entre outros benefícios, isto ajuda a endurecer os seus VMs contra malware. Utilizando machine learning, o Security Center analisa os processos em execução no VM para ajudá-lo a criar regras de listagem.


## <a name="incident-response"></a>Resposta a incidentes
O Centro de Segurança deteta e alerta-o relativamente a ameaças à medida que ocorrem. As organizações devem monitorizar a existência de novos alertas de segurança e tomar medidas conforme seja necessário para uma investigação mais aprofundada ou para remediar o ataque. Para obter mais informações sobre o funcionamento da proteção contra ameaças do Centro de Segurança, leia [como o Azure Security Center deteta e responde a ameaças](security-center-alerts-overview.md#detect-threats).

Embora este artigo não tenha a intenção de ajudá-lo a criar o seu próprio plano de resposta a incidentes, vamos usar a Resposta de Segurança do Microsoft Azure no ciclo de vida da Cloud como base para fases de resposta a incidentes. As fases são apresentadas no diagrama seguinte:

![Fases da resposta do incidente no ciclo de vida da nuvem](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Pode utilizar o [Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Guia de Processamento de Incidentes de Segurança Informática) do National Institute of Standards and Technology (NIST) (Instituto Nacional de Normas e Tecnologia dos Estados Unidos) como referência para o ajudar a criar o seu próprio.
>

Pode utilizar Alertas do Centro de Segurança durante as seguintes fases:

* **Detetar**: identifique uma atividade suspeita num ou mais recursos.
* **Avaliar**: efetue a avaliação inicial para obter mais informações sobre a atividade suspeita.
* **Diagnosticar**: utilize os passos de remediação para realizar o procedimento técnico para resolver o problema.

Cada Alerta de Segurança faculta informações que podem ser utilizadas para compreender melhor a natureza do ataque e sugerir possíveis mitigações. Alguns alertas também fornecem ligações para mais informações ou para outras fontes de informação no Azure. Pode utilizar as informações fornecidas para uma pesquisa aprofundada e começar a mitigação, e também pode pesquisar os dados relacionados com a segurança que estejam armazenados na área de trabalho.

O exemplo seguinte mostra uma atividade suspeita de RDP em curso:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Esta página mostra informações detalhadas acerca da hora em que ocorreu o ataque, o nome do anfitrião de origem, a VM visada e também lhe disponibiliza passos de recomendação. Em algumas circunstâncias, a informação de origem do ataque pode estar vazia. Leia [Informações da Origem em Falta nos Alertas do Centro de Segurança do Azure](/archive/blogs/azuresecurity/missing-source-information-in-azure-security-center-alerts) para obter mais informações acerca deste tipo de comportamento.

Uma vez identificado o sistema comprometido, pode executar uma [automatização de fluxo de trabalho](workflow-automation.md) que foi previamente criada. Trata-se de uma coleção de procedimentos que podem ser executados a partir do Centro de Segurança uma vez desencadeados por um alerta.

No [Como Alavancar o Azure Security Center & Microsoft Operations Management Suite para um](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) vídeo de Resposta a Incidentes, pode ver algumas demonstrações que mostram como o Security Center pode ser usado em cada uma dessas fases.

> [!NOTE]
> Leia [Gerir e responder aos alertas de segurança no Azure Security Center](security-center-managing-and-responding-alerts.md) para obter mais informações sobre como utilizar as capacidades do Centro de Segurança para o ajudar durante o seu processo de Resposta a Incidentes.
>
>

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a planear a adoção do Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Monitorizar soluções de parceiros com o Azure Security Center](./security-center-partner-integration.md) — Saiba como monitorizar o estado de saúde das soluções do seu parceiro.
* [Azure Security Center FAQ](faq-general.md) — Encontre perguntas frequentes sobre a utilização do serviço.
* [Blog da Azure Security](/archive/blogs/azuresecurity/) — Encontre posts de blog sobre segurança e conformidade da Azure.