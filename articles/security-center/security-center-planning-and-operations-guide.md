---
title: Guia de Planeamento e Operações do Centro de Segurança
description: Este documento ajuda-o a planear antes de adotar o Centro de Segurança do Azure e as considerações relativas à operações diárias.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 8244a0f164c8578bf9f79e4b66beb529b6a15f67
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586003"
---
# <a name="planning-and-operations-guide"></a>Guia de planeamento e operações
Este guia destina-se a profissionais de tecnologias da informação (TI), arquitetos de TI, analistas de segurança da informação e administradores de nuvem que planeiam usar o Azure Security Center.


## <a name="planning-guide"></a>Guia de planeamento
Este guia cobre tarefas que pode seguir para otimizar a sua utilização do Security Center com base nos requisitos de segurança da sua organização e no modelo de gestão de nuvens. Para tirar o máximo partido do Centro de Segurança, é importante compreender de que forma diferentes pessoas ou equipas na sua organização utilizam o serviço para dar resposta às necessidades de desenvolvimento e operações seguras, monitorização, governação e resposta a incidentes. As principais áreas a considerar quando planear utilizar o Centro de Segurança são:

* Funções de Segurança e Controlos de Acesso
* Recomendações e Políticas de Segurança
* Armazenamento e Recolha de Dados
* Recursos não Azure contínuos
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

O Centro de Segurança utiliza o [Controlo de Acesso Baseado em Funções (RBAC)](../role-based-access-control/role-assignments-portal.md), que fornece [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços no Azure. Quando um utilizador abre o Centro de Segurança, vê apenas informações relacionadas com os recursos a que tem acesso. Isso significa que é atribuído ao utilizador a função de Proprietário, Contribuinte ou Leitor para a subscrição ou grupo de recursos a que pertence um recurso. Além destas funções, há duas funções específicas do Centro de Segurança:

- **Leitor de segurança**: os utilizadores que pertencem a esta função só conseguem ver as configurações do Centro de Segurança, que incluem recomendações, alertas, políticas e estado de funcionamento, mas não podem fazer alterações.
- **Administrador de segurança**: igual ao leitor de segurança, mas também pode atualizar a política de segurança, bem como ignorar recomendações e alertas.

As funções do Centro de Segurança descritas acima não têm acesso a outras áreas de serviço do Azure como o Armazenamento, Web e Móvel ou a Internet das Coisas.

Utilizando as pessoas fidedignas explicadas no diagrama anterior, é necessário o seguinte RBAC:

**Jorge (Proprietário de Carga de Trabalho)**

* Proprietário/Colaborador do Grupo de Recursos

**Helena (CISO/CIO)**

* Proprietário/Colaborador ou Administrador de Segurança

**Diogo (Segurança de TI)**

* Proprietário/Colaborador ou Administrador de Segurança

**Júlia (Operações de Segurança)**

* Leitor de Subscrição ou Leitor de Segurança para visualizar Alertas
* Proprietário/Colaborador ou Administrador de Segurança obrigado a dispensar alertas

**Samuel (Analista de Segurança)**

* Leitor de Subscrição para visualizar Alertas
* Proprietário/Colaborador de Assinatura obrigado a rejeitar alertas
* O acesso à área de trabalho poderá ser necessário

Mais algumas informações importantes a considerar:

* Apenas os Proprietários/Contribuintes e os Administradores de Segurança das subscrições podem editar uma política de segurança.
* Apenas os Proprietários e os Contribuintes do grupo de recursos e da subscrição podem aplicar recomendações de segurança a um recurso

Quando planear o controlo de acesso através do RBAC para o Centro de Segurança, certifique-se de que compreende quem na sua organização vai utilizar o Centro de Segurança. Além disso, os tipos de tarefas que vão executar e, em seguida, configura o RBAC em conformidade.

> [!NOTE]
> Recomendamos que atribua a função menos permissiva necessária para que os utilizadores concluam as respetivas tarefas. Por exemplo, os utilizadores que apenas necessitam de ver informações acerca do estado de segurança dos recursos, mas não têm de tomar medidas, como aplicar as recomendações ou editar políticas, também devem ter atribuída a função de Leitor.
>
>

## <a name="security-policies-and-recommendations"></a>Recomendações e políticas de segurança
As políticas de segurança definem a configuração pretendida para as suas cargas de trabalho e ajudam a garantir a conformidade com os requisitos da empresa ou regulamentares. No Centro de Segurança, pode definir políticas para as suas subscrições do Azure, que podem ser adaptadas ao tipo de carga de trabalho ou à sensibilidade dos dados.

As políticas do Centro de Segurança contêm os componentes seguintes:
- [Recolha de dados](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection): aprovisionamento de agente e definições de recolha de dados.
- [Política de segurança](https://docs.microsoft.com/azure/security-center/security-center-policies): uma [Política Azure](../governance/policy/overview.md) que determina quais os controlos que são monitorizados e recomendados pelo Centro de Segurança, ou usar a Política Azure para criar novas definições, definir políticas adicionais e atribuir políticas entre grupos de gestão.
- [Notificações por e-mail](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details): contactos de segurança e definições de notificação.
- [Escalão de preço](https://docs.microsoft.com/azure/security-center/security-center-pricing): seleção de preço gratuito ou standard, que determina quais as funcionalidades do Centro de Segurança que estão disponíveis para os recursos no âmbito (pode ser especificado para subscrições, grupos de recursos e áreas de trabalho).

> [!NOTE]
> Especificar um contacto de segurança garante que o Azure consegue contactar a pessoa certa da sua organização se ocorrer um incidente de segurança. Leia [Provide security contact details in Azure Security Center (Fornecer detalhes de contacto de segurança no Centro de Segurança do Azure)](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) para obter mais informações sobre como ativar esta recomendação.

### <a name="security-policies-definitions-and-recommendations"></a>Definições e recomendações de políticas de segurança
O Centro de Segurança cria automaticamente uma política de segurança predefinida para cada uma das suas subscrições do Azure. Pode editá-la no Centro de Segurança ou utilizar a Política do Azure para criar definições novas, definir políticas adicionais e atribuir políticas a vários Grupos de Gestão (que podem representar toda a organização, uma unidade de negócio, etc.) e monitorizar a conformidade com estas políticas nestes âmbitos.

Antes de configurar as políticas de segurança, reveja cada uma das [recomendações de segurança](https://docs.microsoft.com/azure/security-center/security-center-recommendations) e determine se estas são adequadas para as suas várias subscrições e grupos de recursos. Também é importante entender que ação deve ser realizada para abordar as recomendações de segurança e quem na sua organização será responsável por monitorizar novas recomendações e tomar as medidas necessárias.

## <a name="data-collection-and-storage"></a>Armazenamento e recolha de dados
O Azure Security Center utiliza o agente Log Analytics – este é o mesmo agente utilizado pelo serviço Azure Monitor – para recolher dados de segurança das suas máquinas virtuais. Os [dados recolhidos](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) neste agente serão armazenados nas suas áreas de trabalho do Log Analytics.

### <a name="agent"></a>Agente

Quando o fornecimento automático está ativado na política de segurança, o agente Log Analytics (para [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) ou [Linux)](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)está instalado em todos os VMs Azure suportados e quaisquer novos que sejam criados. Se o VM ou o computador já tiver o agente Log Analytics instalado, o Azure Security Center irá alavancar o agente instalado atualmente. O processo do agente foi concebido para não ser invasivo e ter um impacto muito mínimo no desempenho da VM.

O agente Log Analytics para Windows requer a utilização da porta TCP 443. Veja o [artigo Resolução de Problemas](security-center-troubleshooting-guide.md) para mais detalhes.

Se a determinada altura pretender desativar a Recolha de Dados, pode desativá-la na política de segurança. No entanto, uma vez que o agente Log Analytics pode ser utilizado por outros serviços de gestão e monitorização do Azure, o agente não será desinstalado automaticamente quando desativar a recolha de dados no Security Center. Pode desinstalar o agente manualmente, se necessário.

> [!NOTE]
> Para encontrar uma lista de VMs suportadas, leia as [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure ](faq-vms.md).

### <a name="workspace"></a>Área de trabalho

Uma área de trabalho é um recurso do Azure que funciona como um contentor para os dados. O utilizador ou outros membros da sua organização podem utilizar várias áreas de trabalho para gerir diferentes conjuntos de dados recolhidos da totalidade ou de partes da sua infraestrutura de TI.

Os dados recolhidos do agente Log Analytics (em nome do Azure Security Center) serão armazenados num espaço de trabalho de Log Analytics existente associado à sua subscrição Azure ou a um novo espaço de trabalho, tendo em conta o Geo do VM.

No portal do Azure, pode procurar uma lista das áreas de trabalho do Log Analytics, incluindo as que foram criadas pelo Centro de Segurança do Azure. Um grupo de recursos relacionado será criado para as novas áreas de trabalho. Ambos seguirão esta convenção de nomenclatura:

* Área de trabalho: *DefaultWorkspace-[subscription-ID]-[geo]*
* Grupo de Recursos: *DefaultResourceGroup-[geo]*

Para áreas de trabalho criadas pelo Centro de Segurança do Azure, os dados são retidos durante 30 dias. Para as áreas de trabalho existentes, a retenção baseia-se no escalão de preço da área de trabalho. Se quiser, também pode utilizar uma área de trabalho existente.

> [!NOTE]
> A Microsoft está extremamente empenhada em proteger a privacidade e a segurança destes dados. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço. Para obter mais informações sobre o processamento de dados e privacidade, leia [Segurança de Dados do Centro de Segurança do Azure](security-center-data-security.md).
>

## <a name="onboarding-non-azure-resources"></a>Incluir recursos não Azure

O Centro de Segurança pode monitorizar a postura de segurança dos seus computadores não Azure, mas, primeiro, tem de incluir esses recursos. Leia [Onboarding to Azure Security Center Standard for enhanced security](https://docs.microsoft.com/azure/security-center/security-center-onboarding#onboard-non-azure-computers) (Incluir no Centro de Segurança Standard do Azure para mais segurança) para obter mais informações sobre como incluir recursos não Azure.

## <a name="ongoing-security-monitoring"></a>Monitorização de segurança em curso
Após a configuração inicial e a aplicação das recomendações do Centro de Segurança, o passo seguinte é considerar os processos operacionais do Centro de Segurança.

A Descrição Geral do Centro de Segurança proporciona uma vista unificada da segurança de todos os seus recursos do Azure e de eventuais recursos não Azure que tenha ligado. O exemplo abaixo mostra um ambiente com muitos problemas para resolver:

![dashboard](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> O Centro de Segurança não irá interferir com os seus procedimentos operacionais normais, irá monitorizar passivamente as implementações e facultar recomendações com base nas políticas de segurança que ativou.

Quando optar pela primeira vez por utilizar o Centro de Segurança no atual ambiente do Azure, certifique-se de que revê todas as recomendações, o que pode ser feito no mosaico **Recomendações** ou por recurso (**Computação**, **Rede**, **Armazenamento e dados**, **Aplicação**).

Assim que abordar todas as recomendações, a secção **Prevenção** deve estar verde em todos os recursos que foram abordados. A monitorização contínua neste momento torna-se mais fácil, uma vez que apenas realizará ações com base em alterações nos mosaicos de estado de funcionamento de segurança de recursos e de recomendações.

A secção **Deteção** é mais reativa, pois trata-se de alertas relativos a problemas que estão a ocorrer ou que ocorreram anteriormente e foram detetados pelos controlos do Centro de Segurança e por sistemas de terceiros. O azulejo Security Alerts mostrará gráficos de barras que representam o número de alertas que foram encontrados em cada dia, e a sua distribuição entre as diferentes categorias de gravidade (baixa, média, alta). Para obter mais informações acerca dos Alertas de Segurança, leia [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md).

Considere visitar a opção informações sobre ameaças como parte das suas operações de segurança diárias. Aí, pode identificar ameaças de segurança relativas a cada ambiente, como, por exemplo, identificar se um determinado computador faz parte de um botnet.

### <a name="monitoring-for-new-or-changed-resources"></a>Monitorização de recursos novos ou alterados
A maioria dos ambientes Azure são dinâmicos, com recursos regularmente criados, girados para cima ou para baixo, reconfigurados e alterados. O Centro de Segurança ajuda-o a garantir que tem visibilidade em relação ao estado de segurança destes novos recursos.

Quando adicionar novos recursos (VMs, BDs SQL) ao seu Ambiente do Azure, o Centro de Segurança irá detetar automaticamente estes recursos e começar a monitorizar a segurança dos mesmos. Isto também inclui funções da Web e funções de trabalho PaaS. Se a Recolha de Dados estiver ativada na [Política de Segurança](tutorial-security-policy.md), serão ativadas automaticamente capacidades de monitorização adicionais para as suas máquinas virtuais.

![Áreas principais](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. Para máquinas virtuais, clique em **Compute & aplicações,** no âmbito da secção higiene de segurança de **recursos.** Quaisquer problemas com a ativação de dados ou recomendações relacionadas aparecerão no separador **Descrição Geral** e na secção **Recomendações de Monitorização**.
2. Visualize as **Recomendações** para ver que riscos de segurança foram identificados para o novo recurso, se for o caso.
3. É muito comum que, quando novas VMs são adicionadas ao seu ambiente, apenas o sistema operativo é inicialmente instalado. O proprietário do recurso pode necessitar de algum tempo para implementar outras aplicações que serão utilizadas por estas VMs.  Idealmente, deve ter conhecimento da intenção final desta carga de trabalho. Vai ser um Servidor de Aplicações? Com base no que esta nova carga de trabalho vai ser, pode ativar a **Política de Segurança** adequada, que é o terceiro passo deste fluxo de trabalho.
4. À medida que novos recursos são adicionados ao seu ambiente Azure, novos alertas podem aparecer no azulejo Alertas de **Segurança.** Procure novos alertas neste azulejo e siga as recomendações.

Também deve monitorizar regularmente os recursos existentes para alterações de configuração que possam ter criado riscos de segurança, derivar das linhas de base recomendadas e alertas de segurança. Comece no dashboard do Centro de Segurança. A partir daí, tem três grandes áreas para rever de forma consistente.

![Operações](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. O painel da secção **Prevenção** fornece-lhe acesso rápido aos seus recursos principais. Utilize esta opção para monitorizar a Computação, a Rede, o Armazenamento e dados e as Aplicações.
2. O painel **Recomendações** permite-lhe rever as recomendações do Centro de Segurança. Durante a sua monitorização contínua, pode descobrir que não tem recomendações diariamente, o que é normal, uma vez que abordou todas as recomendações sobre a configuração inicial do Centro de Segurança. Por este motivo, é possível que não tenha novas informações nesta secção todos os dias e que apenas tenha de aceder à mesma conforme seja necessário.
3. A secção **Deteção** pode ser alterada de forma bastante frequente ou pouco frequente. Reveja sempre os alertas de segurança e tome medidas com base nas recomendações do Centro de Segurança.

### <a name="hardening-access-and-applications"></a>Proteger o acesso e as aplicações

Como parte das suas operações de segurança, deve também adotar medidas preventivas para limitar o acesso às VMs e controlar as aplicações que estão a ser executadas nas mesmas. Ao restringir o tráfego de entrada às VMs do Azure, reduz a exposição a ataques e, paralelamente, proporciona acesso fácil para ligar a VMs sempre que necessário. Utilize a funcionalidade de acesso [VM just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) para reforçar o acesso aos seus VMs.

Pode utilizar controlos de [aplicações adaptáveis](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) para limitar quais as aplicações que podem ser executadas nos seus VMs localizados em Azure. Entre outros benefícios, isto ajuda a endurecer os seus VMs contra malware. Utilizando machine learning, o Security Center analisa processos em execução no VM para ajudá-lo a criar regras de whitelisting.


## <a name="incident-response"></a>Resposta a incidentes
O Centro de Segurança deteta e alerta-o relativamente a ameaças à medida que ocorrem. As organizações devem monitorizar a existência de novos alertas de segurança e tomar medidas conforme seja necessário para uma investigação mais aprofundada ou para remediar o ataque. Para obter mais informações sobre como funciona a proteção contra ameaças do Security Center, leia como o [Azure Security Center deteta e responde a ameaças](security-center-alerts-overview.md#detect-threats).

Embora este artigo não tenha a intenção de ajudá-lo a criar o seu próprio plano de resposta a incidentes, vamos usar a Resposta de Segurança do Microsoft Azure no ciclo de vida da Cloud como base para fases de resposta a incidentes. As fases são apresentadas no diagrama seguinte:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

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

Esta página mostra informações detalhadas acerca da hora em que ocorreu o ataque, o nome do anfitrião de origem, a VM visada e também lhe disponibiliza passos de recomendação. Em algumas circunstâncias, a informação de origem do ataque pode estar vazia. Leia [Informações da Origem em Falta nos Alertas do Centro de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) para obter mais informações acerca deste tipo de comportamento.

Nesta página, também pode iniciar uma investigação para compreender melhor a linha cronológica do ataque, como este ocorreu, que sistemas estão potencialmente comprometidos, quais as credenciais que foram utilizadas e ver uma representação gráfica da cadeia de ataque completa.

Assim que identificar o sistema comprometido, pode executar uma [Automatização](workflow-automation.md) de Fluxo de Trabalho que foi previamente criada. Estes são uma coleção de procedimentos que podem ser executados a partir do Centro de Segurança uma vez desencadeados por um alerta.

No ["How to Leverage the Azure Security Center" & Microsoft Operations Management Suite para um](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) vídeo de Resposta a Incidentes, pode ver algumas demonstrações que podem ajudá-lo a entender como o Security Center pode ser usado em cada uma dessas fases.

> [!NOTE]
> Leia [Gerir e responder a alertas](security-center-managing-and-responding-alerts.md) de segurança no Centro de Segurança Azure para obter mais informações sobre como usar as capacidades do Security Center para o ajudar durante o seu processo de Resposta a Incidentes.
>
>

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a planear a adoção do Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Monitorizar soluções parceiras com](security-center-partner-solutions.md) o Azure Security Center — Saiba como monitorizar o estado de saúde das soluções do seu parceiro.
* [Azure Security Center FAQ](faq-general.md) — Encontre perguntas frequentes sobre a utilização do serviço.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — Encontre posts de blog sobre segurança e conformidade do Azure.
