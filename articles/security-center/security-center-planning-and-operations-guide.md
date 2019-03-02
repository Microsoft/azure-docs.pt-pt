---
title: Guia de Operações e Planeamento do Centro de Segurança | Microsoft Docs
description: Este documento ajuda-o a planear antes de adotar o Centro de Segurança do Azure e as considerações relativas à operações diárias.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f984e4a2-ac97-40bf-b281-2f7f473494c4
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: 80c6e3cc29bdd6502640723385273328739d6ebb
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245269"
---
# <a name="azure-security-center-planning-and-operations-guide"></a>Guia de operações e planeamento do Centro de Segurança do Azure
Este guia destina-se a profissionais de tecnologias da informação (TI), arquitetos de TI, analistas de segurança de informações e administradores de nuvem cujas organizações estejam a planear utilizar o Centro de Segurança do Azure.


## <a name="planning-guide"></a>Guia de planeamento
Este guia aborda um conjunto de passos e tarefas que pode seguir para otimizar a utilização do Centro de Segurança do Azure com base nos requisitos de segurança e o modelo de gestão de nuvem da sua organização. Para tirar o máximo partido do Centro de Segurança, é importante compreender de que forma diferentes pessoas ou equipas na sua organização utilizam o serviço para dar resposta às necessidades de desenvolvimento e operações seguras, monitorização, governação e resposta a incidentes. As principais áreas a considerar quando planear utilizar o Centro de Segurança são:

* Funções de Segurança e Controlos de Acesso
* Recomendações e Políticas de Segurança
* Armazenamento e Recolha de Dados
* Recursos não Azure contínuos
* Monitorização de Segurança em Curso
* Resposta a Incidentes

Na secção seguinte irá aprender a planear cada uma dessas áreas e aplicar essas recomendações com base nos seus requisitos.


> [!NOTE]
> Leia as [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure](security-center-faq.md) para obter uma lista de perguntas comuns que também podem ser úteis durante a fase de conceção e planeamento.
>

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

> [!NOTE]
> Um utilizador tem de ser, pelo menos, um contribuidor ou proprietário de uma subscrição ou grupo de recursos para conseguir ver o Centro de Segurança no Azure.
>
>

Utilizando as pessoas fidedignas explicadas no diagrama anterior, é necessário o seguinte RBAC:

**Jorge (Proprietário de Carga de Trabalho)**

* Proprietário/Colaborador de Grupo de Recursos

**Diogo (Segurança de TI)**

* Proprietário/Colaborador de Subscrição ou Administrador de Segurança

**Júlia (Operações de Segurança)**

* Leitor de Subscrição ou Leitor de Segurança para visualizar Alertas
* Proprietário/Colaborador de Subscrição ou Administrador de Sistema necessário para dispensar Alertas

**Samuel (Analista de Segurança)**

* Leitor de Subscrição para visualizar Alertas
* Proprietário/Colaborador de Subscrição necessário para dispensar Alertas
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
- [Política de segurança](https://docs.microsoft.com/azure/security-center/security-center-policies): um [Azure Policy](../governance/policy/overview.md) que determina que controlos são monitorizados e recomendados pelo centro de segurança ou da utilização do Azure Policy para criar definições novas, definir políticas adicionais e atribuir políticas vários grupos de gestão.
- [Notificações por e-mail](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details): contactos de segurança e definições de notificação.
- [Escalão de preço](https://docs.microsoft.com/azure/security-center/security-center-pricing): seleção de preço gratuito ou standard, que determina quais as funcionalidades do Centro de Segurança que estão disponíveis para os recursos no âmbito (pode ser especificado para subscrições, grupos de recursos e áreas de trabalho).

> [!NOTE]
> Especificar um contacto de segurança garante que o Azure consegue contactar a pessoa certa da sua organização se ocorrer um incidente de segurança. Leia [Provide security contact details in Azure Security Center (Fornecer detalhes de contacto de segurança no Centro de Segurança do Azure)](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) para obter mais informações sobre como ativar esta recomendação.

### <a name="security-policies-definitions-and-recommendations"></a>Definições e recomendações de políticas de segurança
O Centro de Segurança cria automaticamente uma política de segurança predefinida para cada uma das suas subscrições do Azure. Pode editá-la no Centro de Segurança ou utilizar a Política do Azure para criar definições novas, definir políticas adicionais e atribuir políticas a vários Grupos de Gestão (que podem representar toda a organização, uma unidade de negócio, etc.) e monitorizar a conformidade com estas políticas nestes âmbitos.

Antes de configurar as políticas de segurança, reveja cada uma das [recomendações de segurança](https://docs.microsoft.com/azure/security-center/security-center-recommendations) e determine se estas são adequadas para as suas várias subscrições e grupos de recursos. Também é importante entender que ação deve ser realizada para abordar as recomendações de segurança e quem na sua organização será responsável por monitorizar novas recomendações e tomar as medidas necessárias.

## <a name="data-collection-and-storage"></a>Armazenamento e recolha de dados
Centro de segurança do Azure utiliza o Microsoft Monitoring Agent – este é o mesmo agente utilizado pelo serviço do Azure Monitor – para recolher dados de segurança das suas máquinas virtuais. Os [dados recolhidos](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) neste agente serão armazenados nas suas áreas de trabalho do Log Analytics.

### <a name="agent"></a>Agente

Quando o aprovisionamento automático está ativado na política de segurança, o Microsoft Monitoring Agent (para [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) ou [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)) é instalado em todas as VMs do Azure suportadas e nas VMs novas que forem criadas. Se a VM ou o computador já tiver instalado o Microsoft Monitoring Agent, o Centro de Segurança do Azure tirará partido do agente atual instalado. O processo do agente foi concebido para ser não invasivo e ter um impacto muito reduzido no desempenho da VM.

O Agente de Monitorização Microsoft para Windows requer a utilização da Porta TCP 443. Veja o [artigo Resolução de Problemas](security-center-troubleshooting-guide.md) para mais detalhes.

Se a determinada altura pretender desativar a Recolha de Dados, pode desativá-la na política de segurança. No entanto, como o Agente de Monitorização Microsoft pode ser utilizado por outros serviços de gestão e monitorização do Azure, o agente não será desinstalado automaticamente quando desligar a recolha de dados no Centro de Segurança. Pode desinstalar o agente manualmente, se necessário.

> [!NOTE]
> Para encontrar uma lista de VMs suportadas, leia as [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure ](security-center-faq.md).
>

### <a name="workspace"></a>Área de trabalho

Uma área de trabalho é um recurso do Azure que funciona como um contentor para os dados. O utilizador ou outros membros da sua organização podem utilizar várias áreas de trabalho para gerir diferentes conjuntos de dados recolhidos da totalidade ou de partes da sua infraestrutura de TI.

Os dados recolhidos pelo Agente de Monitorização Microsoft (em nome do Centro de Segurança do Azure) serão armazenados nas áreas de trabalho do Log Analytics existentes, associadas à sua subscrição do Azure ou a novas áreas de trabalho, tendo em consideração a Geo da VM.

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

A secção **Deteção** é mais reativa, pois trata-se de alertas relativos a problemas que estão a ocorrer ou que ocorreram anteriormente e foram detetados pelos controlos do Centro de Segurança e por sistemas de terceiros. O mosaico Alertas de Segurança mostrará gráficos de barras que representam o número de alertas de deteção de ameaças encontradas em cada dia e a respetiva distribuição entre as diferentes categorias de gravidade (baixa, média, alta). Para obter mais informações acerca dos Alertas de Segurança, leia [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md).

Considere visitar a opção [informações sobre ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) como parte das suas operações de segurança diárias. Aí, pode identificar ameaças de segurança relativas a cada ambiente, como, por exemplo, identificar se um determinado computador faz parte de um botnet.

### <a name="monitoring-for-new-or-changed-resources"></a>Monitorização de recursos novos ou alterados
A maior parte dos ambientes do Azure são dinâmicos, com recursos novos que são regularmente acelerados ou desacelerados, configurações ou alterações, etc. O Centro de Segurança ajuda-o a garantir que tem visibilidade em relação ao estado de segurança destes novos recursos.

Quando adicionar novos recursos (VMs, BDs SQL) ao seu Ambiente do Azure, o Centro de Segurança irá detetar automaticamente estes recursos e começar a monitorizar a segurança dos mesmos. Isto também inclui funções da Web e funções de trabalho PaaS. Se a Recolha de Dados estiver ativada na [Política de Segurança](tutorial-security-policy.md), serão ativadas automaticamente capacidades de monitorização adicionais para as suas máquinas virtuais.

![Áreas principais](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. Para máquinas virtuais, clique em **Computação**, na secção **Prevenção**. Quaisquer problemas com a ativação de dados ou recomendações relacionadas aparecerão no separador **Descrição Geral** e na secção **Recomendações de Monitorização**.
2. Visualize as **Recomendações** para ver que riscos de segurança foram identificados para o novo recurso, se for o caso.
3. É muito comum que, quando novas VMs são adicionadas ao seu ambiente, apenas o sistema operativo é inicialmente instalado. O proprietário do recurso pode necessitar de algum tempo para implementar outras aplicações que serão utilizadas por estas VMs.  Idealmente, deve ter conhecimento da intenção final desta carga de trabalho. Vai ser um Servidor de Aplicações? Com base no que esta nova carga de trabalho vai ser, pode ativar a **Política de Segurança** adequada, que é o terceiro passo deste fluxo de trabalho.
4. À medida que são adicionados novos recursos ao seu ambiente do Azure, é possível que apareçam novos alertas no mosaico **Alertas de Segurança**. Verifique sempre se existem novos alertas neste mosaico e tome medidas de acordo com as recomendações do Centro de Segurança.

Também é aconselhável que monitorize regularmente o estado dos recursos existentes para identificar as alterações de configuração que criaram riscos de segurança, que se desviam das linhas de base recomendadas e dos alertas de segurança. Comece no dashboard do Centro de Segurança. A partir daí tem três áreas principais para rever de forma consistente.

![Operações](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. O painel da secção **Prevenção** fornece-lhe acesso rápido aos seus recursos principais. Utilize esta opção para monitorizar a Computação, a Rede, o Armazenamento e dados e as Aplicações.
2. O painel **Recomendações** permite-lhe rever as recomendações do Centro de Segurança. Durante a monitorização contínua, pode constatar que não tem recomendações diariamente. Tal é normal, uma vez que abordou todas as recomendações na configuração inicial do Centro de Segurança. Por este motivo, é possível que não tenha novas informações nesta secção todos os dias e que apenas tenha de aceder à mesma conforme seja necessário.
3. A secção **Deteção** pode ser alterada de forma bastante frequente ou pouco frequente. Reveja sempre os alertas de segurança e tome medidas com base nas recomendações do Centro de Segurança.

### <a name="hardening-access-and-applications"></a>Proteger o acesso e as aplicações

Como parte das suas operações de segurança, deve também adotar medidas preventivas para limitar o acesso às VMs e controlar as aplicações que estão a ser executadas nas mesmas. Ao restringir o tráfego de entrada às VMs do Azure, reduz a exposição a ataques e, paralelamente, proporciona acesso fácil para ligar a VMs sempre que necessário. Utilize a funcionalidade de acesso a [VM Just in Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) para proteger o acesso às suas VMs.

Pode utilizar [Controlos de Aplicações Adaptativos](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) para ajudar a controlar que aplicações podem ser executadas nas suas VMs localizadas no Azure, o que, entre outros benefícios, permite proteger as VMs contra malware. O Centro de Segurança utiliza machine learning para analisar os processos que estão a ser executados na VM e ajuda-o a aplicar regras de inclusão nas listas de permissões com base nessas informações.


## <a name="incident-response"></a>Resposta a incidentes
O Centro de Segurança deteta e alerta-o relativamente a ameaças à medida que ocorrem. As organizações devem monitorizar a existência de novos alertas de segurança e tomar medidas conforme seja necessário para uma investigação mais aprofundada ou para remediar o ataque. Para obter mais informações sobre como funciona a deteção de ameaças do Centro de Segurança, leia [Azure Security Center detection capabilities (Capacidades de deteção do Centro de Segurança do Azure)](security-center-detection-capabilities.md).

Embora este artigo não pretenda ajudá-lo a criar o seu próprio plano de Resposta a Incidentes, iremos utilizar o Microsoft Azure Security Response no ciclo de vida da Nuvem como a base para as fases de resposta a incidentes. As fases são apresentadas no diagrama seguinte:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Pode utilizar o [Computer Security Incident Handling Guide](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Guia de Processamento de Incidentes de Segurança Informática) do National Institute of Standards and Technology (NIST) (Instituto Nacional de Normas e Tecnologia dos Estados Unidos) como referência para o ajudar a criar o seu próprio.
>

Pode utilizar Alertas do Centro de Segurança durante as seguintes fases:

* **Detetar**: identifique uma atividade suspeita num ou mais recursos.
* **Avaliar**: efetue a avaliação inicial para obter mais informações sobre a atividade suspeita.
* **Diagnosticar**: utilize os passos de remediação para realizar o procedimento técnico para resolver o problema.

Cada Alerta de Segurança faculta informações que podem ser utilizadas para compreender melhor a natureza do ataque e sugerir possíveis mitigações. Alguns alertas também fornecem ligações para mais informações ou para outras fontes de informação no Azure. Pode utilizar as informações fornecidas para uma pesquisa aprofundada e começar a mitigação, e também pode pesquisar os dados relacionados com a segurança que estejam armazenados na área de trabalho.

O exemplo seguinte mostra uma atividade suspeita de RDP em curso:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Esta página mostra informações detalhadas acerca da hora em que ocorreu o ataque, o nome do anfitrião de origem, a VM visada e também lhe disponibiliza passos de recomendação. Em determinadas circunstâncias, as informações da origem do ataque podem estar vazias. Leia [Informações da Origem em Falta nos Alertas do Centro de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) para obter mais informações acerca deste tipo de comportamento.

Nesta página, também pode iniciar uma [investigação](https://docs.microsoft.com/azure/security-center/security-center-investigation) para compreender melhor a linha cronológica do ataque, como este ocorreu, que sistemas estão potencialmente comprometidos, quais as credenciais que foram utilizadas e ver uma representação gráfica da cadeia de ataque completa.

Depois de identificar o sistema comprometido, pode executar os [playbooks](https://docs.microsoft.com/azure/security-center/security-center-playbooks) de segurança que foram criados anteriormente. O playbook de segurança é uma coleção de procedimentos que podem ser executados no Centro de Segurança quando um alerta selecionado aciona um determinado playbook.

No vídeo [Como tirar partido do Centro de segurança do Azure e do Microsoft Operations Management Suite para uma resposta a incidentes](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) pode ver algumas demonstrações que podem ajudá-lo a compreender como o Centro de segurança pode ser utilizado em cada uma destas fases.

> [!NOTE]
> Leia [Tirar Partido do Centro de Segurança do Azure para Resposta a Incidentes](security-center-incident-response.md) para obter mais informações sobre como utilizar as capacidades do Centro de Segurança para o ajudar durante os processos de Resposta a Incidentes.
>
>

## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a planear a adoção do Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
