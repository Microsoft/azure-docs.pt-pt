---
title: Deteção de ameaças avançadas do Azure | Documentos da Microsoft
description: Saiba mais sobre o Azure AD Identity Protection e as respetivas capacidades.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 60a0d7c2da25f63d0728dbb86f9a6c2328def811
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245260"
---
# <a name="azure-advanced-threat-detection"></a>Deteção de ameaças avançada do Azure

Ofertas do Azure criadas na funcionalidade de deteção de ameaças avançada por meio de serviços como o Azure Active Directory (Azure AD), os registos do Azure Monitor e o Centro de segurança do Azure. Este conjunto de capacidades e serviços de segurança fornece uma forma simple e rápida de compreender o que está acontecendo dentro de suas implementações do Azure.

O Azure oferece um vasto leque de opções para configurar e personalizar a segurança para satisfazer os requisitos das suas implementações de aplicações. Este artigo discute como a cumprir esses requisitos.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[O Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é um [do Azure Active Directory Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) funcionalidade de edição que fornece uma descrição geral dos eventos de risco e potenciais vulnerabilidades que podem afetar as identidades da sua organização. O Identity Protection utiliza capacidades de deteção de anomalias do Azure AD existentes que estão disponíveis por meio [do Azure relatórios atividade anómala AD](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports)e introduz novos tipos de eventos de risco que podem detetar anomalias em tempo real.

![Diagrama do Azure AD Identity Protection](./media/azure-threat-detection/azure-threat-detection-fig1.png)

Identity Protection utiliza algoritmos de machine learning adaptáveis e heurística para detetar anomalias e eventos de risco que podem indicar que uma identidade foi comprometida. Utilizando estes dados, Identity Protection gera relatórios e alertas para que possa investigar estes eventos de risco e efetuar remediação adequadas ou ação de atenuação.

O Azure Active Directory Identity Protection é mais do que uma monitorização e a ferramenta de relatório. Com base em eventos de risco, Identity Protection calcula um nível de risco de utilizador para cada utilizador, para que possam configurar políticas baseadas no risco para proteger automaticamente as identidades da sua organização.

Estas políticas com base no risco, além de outros [controlos de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) que são fornecidos pelo Azure Active Directory e [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), automaticamente pode bloquear ou oferecer ações de remediação adaptável que inclua reposições de palavra-passe e a imposição de multi-factor authentication.

### <a name="identity-protection-capabilities"></a>Capacidades de proteção de identidade

O Azure Active Directory Identity Protection é mais do que uma monitorização e a ferramenta de relatório. Para proteger as identidades da sua organização, pode configurar as políticas baseadas em risco, que respondam automaticamente aos problemas detetados quando for atingido um nível de risco especificado. Automaticamente estas políticas, além de outros Controles de acesso condicional fornecidas pelo Azure Active Directory e o EMS, podem bloquear ou iniciar ações de remediação adaptável redefinições de senha incluindo e imposição de multi-factor authentication.

Exemplos de algumas das formas em que a proteção de identidade do Azure pode ajudar a proteger as suas contas e identidades incluem:

[Detetar eventos de risco e contas de risco](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Detete seis tipos de eventos de risco com o machine learning e heurísticas regras.
-   Calcule os níveis de risco do utilizador.
-   Fornece recomendações personalizadas para melhorar a postura de segurança geral por destacar vulnerabilidades.

[Investigar eventos de risco](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Envie notificações para eventos de risco.
-   Investigue os eventos de risco com informações relevantes e contextuais.
-   Forneça os fluxos de trabalho básicos para controlar as investigações.
-   Fornece acesso fácil a ações de remediação, como a reposição de palavra-passe.

[Políticas com base no risco, o acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
-   Mitigar o risco de inícios de sessão ao inícios de sessão de bloquear ou exigir que os desafios de autenticação multifator.
-   Bloquear ou proteger contas de utilizador de risco.
-   Exigir que os utilizadores para se registarem na autenticação multifator.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

Com o [do Azure Active Directory Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), pode gerir, controlar e monitorizar o acesso dentro da sua organização. Esta funcionalidade inclui acesso a recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

![Diagrama do Azure AD Privileged Identity Management](./media/azure-threat-detection/azure-threat-detection-fig2.png)

PIM ajuda-o:

-   Obtenha alertas e relatórios sobre os administradores do Azure AD e o acesso administrativo de just-in-time (JIT) ao Microsoft online services, como o Office 365 e Intune.

-   Obter relatórios sobre o histórico de acesso de administrador e as alterações em atribuições de administrador.

-   Obtenha alertas sobre o acesso a uma função com privilégios.

## <a name="azure-monitor-logs"></a>Registos de Monitor do Azure

[Registos de Monitor do Azure](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) é uma Microsoft baseado na nuvem gestão solução de TI que ajuda a gerenciar e proteger no local e a infraestrutura na cloud. Uma vez que os registos do Azure Monitor é implementado como um serviço baseado na nuvem, pode tê-lo em funcionamento rapidamente com um investimento mínimo em serviços de infraestrutura. Novos recursos de segurança são entregues automaticamente, reduzindo a manutenção contínua e os custos de atualização.

Além de fornecer serviços importantes sobre o seu próprio, o Azure Monitor registos podem ser integrado com componentes do System Center, como [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), para expandir a sua segurança existente investimentos de gestão para o na cloud. Registos do System Center e o Azure Monitor podem trabalhar em conjunto para fornecer a experiência de um gestão híbrida completa.

### <a name="holistic-security-and-compliance-posture"></a>Postura de segurança e conformidade holística

O [dashboard de segurança de análise de registo e auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) fornece uma visão abrangente da sua organização postura de segurança IT, com consultas de pesquisa incorporada para problemas relevantes que necessitam da sua atenção. O dashboard de segurança e auditoria é o ecrã principal para tudo relacionado à segurança nos registos do Azure Monitor. Fornece informações de alto nível sobre o estado de segurança dos computadores. Também pode ver todos os eventos a partir das últimas 24 horas, 7 dias, ou qualquer outro período de tempo personalizado.

Monitor do Azure regista ajudam a perceber rapidamente e facilmente a postura de segurança geral de qualquer ambiente, tudo dentro do contexto de operações de TI, incluindo a avaliação de atualização de software, avaliação de antimalware e linhas de base de configuração. Dados de registo de segurança são imediatamente acessíveis para simplificar os processos de auditoria de segurança e conformidade.

![O dashboard de segurança de análise de registo e auditoria](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

O dashboard de segurança de análise de registo e auditoria está organizado em quatro categorias principais:

-   **Domínios de segurança**: Permite-lhe explorar ainda mais os registos de segurança ao longo do tempo; avaliações de software maligno do acesso; avaliações de atualização; ver a segurança de rede, identidade e aceder às informações; Ver computadores com eventos de segurança; e aceder rapidamente ao dashboard do Centro de segurança do Azure.

-   **Problemas relevantes**: Permite-lhe identificar rapidamente o número de problemas ativos e a gravidade dos problemas.

-   **Deteções (pré-visualização)**: Permite-lhe identificar padrões de ataque ao apresentar alertas de segurança à medida que ocorrem em relação aos recursos.

-   **Informações sobre ameaças**: Permite-lhe identificar padrões de ataque ao apresentar o número total de servidores com tráfego IP malicioso de saída, o tipo malicioso da ameaça e um mapa das localizações de IPs.

-   **Consultas de segurança comuns**: Lista as consultas de segurança mais comuns que pode utilizar para monitorizar o seu ambiente. Ao selecionar qualquer consulta, o painel de pesquisa abre e apresenta os resultados para essa consulta.

### <a name="insight-and-analytics"></a>O Insight and analytics
No centro do [registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) é o repositório, o que está alojado pelo Azure.

![Diagrama de informações e análise](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Recolher dados para o repositório de origens ligadas mediante a configuração de origens de dados e adicionar soluções à sua subscrição.

![O dashboard de registos do Azure Monitor](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Origens de dados e soluções criam tipos de registo separado com seu próprio conjunto de propriedades, mas pode ainda analisá-los em conjunto em consultas para o repositório. Pode utilizar as mesmas ferramentas e métodos para trabalhar com uma variedade de dados que são recolhidos por várias origens.


Maior parte da interação com os registos do Azure Monitor é através do portal do Azure, o que é executado em qualquer browser e fornece a acesso a definições de configuração e a várias ferramentas para analisar e atuar sobre os dados recolhidos. No portal, pode utilizar:
* [Pesquisas de registos](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) onde constrói as consultas para analisar os dados recolhidos.
* [Dashboards](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards), que pode personalizar com vistas gráficas das suas pesquisas mais valiosas.
* [Soluções](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), que fornecem ferramentas de análise e funcionalidade adicionais.

![Ferramentas de análise](./media/azure-threat-detection/azure-threat-detection-fig6.png)

As soluções acrescentam funcionalidades aos registos do Azure Monitor. Eles principalmente executar na cloud e fornecem uma análise de dados que são recolhidos no repositório do log analytics. Soluções também podem definir novos tipos de registos a serem recolhidos que podem ser analisados com pesquisas de registos ou por meio de uma interface de usuário adicionais que a solução fornece no dashboard de análise do registo.

O dashboard de segurança e auditoria é um exemplo desses tipos de soluções.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatização e controlo: Alerta sobre drifts de configuração de segurança

A automatização do Azure automatiza processos administrativos com os runbooks que são baseados no PowerShell e executados na cloud. Os runbooks podem ser também executados no seu centro de dados local para gerir recursos locais. A automatização do Azure fornece gestão de configuração com o PowerShell Desired State Configuration (DSC).

![Diagrama de automatização do Azure](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Pode criar e gerir recursos de DSC que estão alojados no Azure e aplicá-las a sistemas na cloud e no local. Assim, pode definir e automaticamente aplicar suas configurações ou obter relatórios sobre os descompassos para ajudar a garantir a segurança configurações permanecem na diretiva.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

Centro de segurança do Azure ajuda a proteger os seus recursos do Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure. No serviço, pode definir políticas em relação a ambas as suas subscrições do Azure e [grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) para maior granularidade.

![Diagrama de centro de segurança do Azure](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Os investigadores de segurança da Microsoft estão constantemente atentos a ameaças. Estes têm acesso a um conjunto amplo de telemetria obtida através da presença global da Microsoft na nuvem e no local. Esta coleção abrangente e diversificada de conjuntos de dados permite à Microsoft descobrir novos padrões de ataque e tendências dos seus produtos de consumidor e empresariais no local, assim como os seus serviços online.

Portanto, o Centro de segurança pode atualizar rapidamente os algoritmos de deteção à medida que os atacantes disponibilizam novas e cada vez mais sofisticadas explorações. Esta abordagem ajuda-o a manter o ritmo com um ambiente de ameaça rápida evolução.

![Deteção de ameaças do Centro de segurança](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

A deteção de ameaças do Centro de Segurança funciona através da recolha automática de informações de segurança a partir dos seus recursos do Azure, da rede e das soluções de parceiros ligadas. Analisa esta informação, correlacionando informações de várias origens, para identificar ameaças.

Os alertas de segurança são prioritários no Centro de Segurança, juntamente com recomendações sobre como remediar a ameaça.

O Centro de Segurança emprega análises de segurança avançadas, que ultrapassam as abordagens baseadas na assinatura. Soluções de macrodados e [aprendizagem](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) tecnologias são utilizadas para avaliar eventos em recursos de infraestrutura do integralmente na nuvem. Análise avançada pode detetar ameaças que seriam impossíveis de identificar através de abordagens manuais e a previsão da evolução de ataques. Esses tipos de análise de segurança são abordados nas secções seguintes.

### <a name="threat-intelligence"></a>Informações sobre ameaças

A Microsoft tem acesso a uma grande quantidade de informações sobre ameaças globais.

A telemetria provém de várias origens, como o Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, unidade de Crimes digitais da Microsoft (DCU) e Microsoft Security Response Center (MSRC).

![Descobertas de inteligência de ameaças](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Os investigadores também recebem informações sobre ameaças que são partilhadas entre fornecedores de serviços na cloud principal, e eles subscrevem feeds de informações sobre ameaças de terceiros. O Centro de Segurança do Azure pode utilizar estas informações para alertá-lo de ameaças de pessoas mal-intencionadas conhecidas. Alguns exemplos incluem:

-   **Tirar partido do poder do machine learning**: Centro de segurança do Azure tem acesso a uma vasta quantidade de dados sobre atividade de rede na cloud, que pode ser utilizada para detetar ameaças direcionadas para as suas implementações do Azure.

-   **Deteção de força bruta**: Aprendizagem automática é utilizada para criar um padrão histórico de tentativas de acesso remoto, que lhe permite detetar ataques de força bruta contra portas de Secure Shell (SSH), o protocolo RDP (Remote Desktop) e o SQL.

-   **Deteção de DDoS e botnet saída**: Um objetivo de comuns de ataques direcionados para recursos na cloud é usar o poder de computação destes recursos para executar outros ataques.

-   **Novos servidores de análise comportamental e VMs**: Depois de um servidor ou a máquina virtual for comprometida, os atacantes empregam uma grande variedade de técnicas para executar código malicioso nesse sistema ao evitar a deteção, garantir a persistência e evitando controlos de segurança.

-   **A deteção de ameaças da base de dados SQL do Azure**: Deteção de ameaças para a SQL Database do Azure, que identifica atividades anómalas da base de dados que indicam invulgares e potencialmente prejudiciais tenta aceder ou explorar bases de dados.

### <a name="behavioral-analytics"></a>Análise comportamental

A análise comportamental é uma técnica que analisa e compara os dados para uma coleção de padrões conhecidos. No entanto, estes padrões não são simples assinaturas. São determinados através de algoritmos complexos de machine learning aplicados a conjuntos de dados gigantescos.

![Descobertas da análise comportamental](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)

Os padrões também são determinados através de uma análise cuidada de comportamentos maliciosos pelos analistas especialistas. Centro de segurança do Azure pode utilizar a análise comportamental para identificar recursos comprometidos com base na análise de registos de máquina virtual, registos de dispositivos de rede virtual, registos de recursos de infraestrutura, despejos de memória e outras origens.

Além disso, os padrões são correlacionados com outros sinais para procurar provas de uma campanha ampla. Esta correlação ajuda a identificar eventos que são consistentes com indicadores estabelecidos de comprometimento.

Alguns exemplos incluem:
-   **Execução do processo suspeito**: Os atacantes empregam várias técnicas para executar o software malicioso sem deteção. Por exemplo, um atacante pode dar a software maligno igual dos ficheiros de sistema legítimos, mas colocar esses ficheiros numa localização alternativa, utilize um nome que é semelhante de um ficheiro benigno ou mascarar a verdadeira extensão do ficheiro. Modelos do Centro de segurança processam comportamentos e execuções de processo do monitor para detetar valores atípicos como estes.

-   **Oculto a software maligno e tentativas de explorações**: Software maligno sofisticado pode se esconder dos produtos de antimalware tradicionais ao nunca escrever no disco ou ao encriptar componentes de software armazenados no disco. No entanto, esse software maligno pode ser detetado através de análise de memória, uma vez que o software maligno tem de deixar rastreios na memória para a função. Quando o software falha, uma informação de falha de sistema captura uma parte da memória no momento da falha. Ao analisar a memória na informação de falha, o Centro de segurança do Azure pode detetar técnicas utilizadas para explora vulnerabilidades no software, aceder a dados confidenciais e persistir clandestinamente dentro de uma máquina comprometida sem afetar o desempenho da sua máquina.

-   **Movimento lateral e reconhecimento interno**: Para manter-se numa rede comprometida e localizar e coletar dados valiosos, os atacantes tentam frequentemente mover-se lateralmente da máquina comprometida para outras dentro da mesma rede. Centro de segurança monitoriza as atividades de processo e início de sessão para detetar as tentativas para expandir sua presença um invasor dentro da rede, como a execução do comando remoto, pesquisa de rede e enumeração de contas.

-   **Scripts do PowerShell maliciosos**: PowerShell pode ser utilizado pelos atacantes para executar código malicioso em máquinas virtuais de destino para várias finalidades. O Centro da Segurança inspeciona a atividade do PowerShell quanto a provas de atividade suspeita.

-   **Ataques de saída**: Os atacantes muitas vezes nos recursos da cloud com o objetivo de utilizar os mesmos para preparar ataques adicionais. Máquinas virtuais comprometidas, por exemplo, pode ser usadas para iniciar ataques de força bruta contra outras máquinas virtuais, enviar spam ou analisar portas abertas e outros dispositivos na internet. Ao aplicar o machine learning ao tráfego de rede, o Centro de Segurança pode detetar quando as comunicações da rede de saída excedem o normal. Quando é detetado spam, o Centro de segurança correlaciona também o tráfego de e-mail invulgar com informações do Office 365 para determinar se o email é provável que possui conteúdo nefasto ou o resultado de uma campanha de correio eletrónico legítimo.

### <a name="anomaly-detection"></a>Deteção de anomalias

O Centro de Segurança do Azure utiliza também a deteção de anomalias para identificar ameaças. Contrariamente à análise comportamental (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a deteção de anomalias é mais “personalizada” e concentra-se nas linhas de base específicas das suas implementações. Machine learning é utilizado para determinar a atividade normal para as implementações e, em seguida, são geradas regras para definir condições de valores atípicos que podem representar um evento de segurança. Segue-se um exemplo:

-   **Ataques de força bruta de RDP/SSH de entrada**: As implementações podem ter máquinas virtuais ocupadas com muitos inícios de sessão por dia e outras máquinas virtuais que tenha poucos ou nenhum, inícios de sessão. Centro de segurança do Azure pode determinar a atividade de início de sessão de linha de base para estas máquinas virtuais e utilizar o machine learning para definir em torno das atividades de início de sessão normal. Se houver qualquer discrepância com a linha de base definida para o início de sessão relacionada com características, poderá ser gerado um alerta. Novamente, o machine learning determina o que é significativo.

### <a name="continuous-threat-intelligence-monitoring"></a>Monitorização de informações sobre ameaças contínua

Centro de segurança do Azure funciona com dados de pesquisa e ciência equipes de segurança em todo o mundo que monitorizam continuamente as alterações no campo das ameaças. Isto inclui as seguintes iniciativas:

-   **Monitorização de inteligência de ameaças**: Informações sobre ameaças incluem mecanismos, indicadores, implicações e conselhos acionáveis sobre ameaças existentes ou emergentes. Esta informação é partilhada na Comunidade de segurança e a Microsoft monitoriza continuamente os feeds de inteligência de ameaças de origens internas e externas.

-   **Partilha de sinal**: Informações de segurança equipas em todo o portefólio amplo do Microsoft da nuvem e dispositivos de ponto de extremidade de serviços, servidores e cliente são partilhados e analisados no local.

-   **Especialistas de segurança da Microsoft**: Envolvimento contínuo com equipas da Microsoft que trabalham em campos de segurança especializada, como deteção de ataques web e forense.

-   **Otimização da deteção**: Algoritmos são executados em conjuntos de dados de clientes reais e pesquisadores de segurança de trabalham com os clientes para validar os resultados. Os verdadeiros e falsos positivos são utilizados para refinar os algoritmos do machine learning.

Estes esforços combinados culminate em novas e melhoradas deteções das quais pode beneficiar de imediato. Não existe nenhuma ação que tome.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Funcionalidades de deteção de ameaças avançada: Outros serviços do Azure

### <a name="virtual-machines-microsoft-antimalware"></a>Máquinas virtuais: Microsoft antimalware

[O Microsoft antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Azure é uma solução de agente de único para aplicações e ambientes de inquilino, concebido para ser executado em segundo plano sem intervenção humana. Pode implementar proteção com base nas necessidades de suas cargas de trabalho de aplicação, com qualquer um dos básica seguras por padrão ou avançadas de configuração personalizada, incluindo a monitorização de antimalware. Antimalware do Azure é uma opção de segurança para máquinas virtuais do Azure que é instalada automaticamente em todas as máquinas de virtuais de PaaS do Azure.

#### <a name="microsoft-antimalware-core-features"></a>Principais recursos do Microsoft antimalware

Seguem-se as funcionalidades do Azure que implementar e ativar o antimalware da Microsoft para as suas aplicações:

-   **Proteção em tempo real**: Monitora a atividade nos serviços cloud e nas máquinas virtuais para detetar e bloquear a execução de software maligno.

-   **Agendado a análise**: Periodicamente, executa a análise de destino para detetar software maligno, incluindo programas ativamente em execução.

-   **Remediação de software maligno**: Automaticamente age em software maligno detetado, como a eliminação ou os colocar em quarentena ficheiros maliciosos e limpeza de entradas de registro malicioso.

-   **Atualizações de assinatura**: Instala automaticamente as últimas assinaturas de proteção (definições de vírus) para garantir que a proteção é atualizada numa frequência previamente determinada.

-   **Atualizações do motor antimalware**: O Microsoft Antimalware Engine é atualizada automaticamente.

-   **Atualizações de plataforma antimalware**: Atualiza automaticamente a plataforma de antimalware da Microsoft.

-   **Proteção ativa**: Metadados de telemetria de relatórios sobre ameaças detetadas e os recursos suspeitos para o Microsoft Azure para garantir que a resposta rápida para o Panorama de ameaças em constante evolução, permitindo a entrega de assinatura de síncrono em tempo real através do sistema de proteção ativa Microsoft.

-   **Exemplos da geração de relatórios**: Fornece e relatórios exemplos para o serviço Microsoft antimalware para ajudar a refinar o serviço e ativar a solução de problemas.

-   **Exclusões**: Permite que aplicativos e administradores de serviço configurar a determinados ficheiros, processos e unidades de exclusão da proteção e a análise de desempenho e outros motivos.

-   **Recolha de eventos de antimalware**: Regista o estado de funcionamento de serviço de antimalware, atividades suspeitas e ações de remediação executadas no registo de eventos de sistema operativo e recolhe-los para a conta de armazenamento do Azure do cliente.

### <a name="azure-sql-database-threat-detection"></a>Deteção de ameaças da base de dados SQL do Azure

[A deteção de ameaças da base de dados SQL do Azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) é um novo recurso de inteligência de segurança incorporado no serviço de base de dados do Azure SQL. Para aprender, criar o perfil e detetar atividades anómalas da base de dados a trabalhar intensivamente, o Azure SQL Database Threat Detection identifica potenciais ameaças à base de dados.

Responsáveis pela segurança ou outros administradores designados podem obter uma notificação imediata sobre atividades suspeitas da base de dados à medida que ocorrem. Cada notificação fornece detalhes da atividade suspeita e recomenda como investigar e mitigar a ameaça.

Atualmente, o Azure SQL Database Threat Detection Deteta potenciais vulnerabilidades e ataques de injeção de SQL e padrões de acesso de base de dados anómalas.

Após receber uma notificação de e-mail de deteção de ameaças, os utilizadores são capazes de navegar e ver os registos de auditoria relevante por meio de uma ligação avançada no e-mail. A hiperligação abre um Visualizador de auditoria ou um modelo de Excel de auditoria pré-configurada que mostra os registos de auditoria relevantes perto da hora do evento suspeito, acordo com o seguinte:

-   Armazenamento para o servidor/base de dados com as atividades anómalas da base de dados de auditoria.

-   Tabela de armazenamento de auditoria relevantes que foi utilizada no momento do evento para escrever o registo de auditoria.

-   Registos da hora imediatamente após a ocorrência do evento de auditoria.

-   Registos com um ID de evento semelhante de auditoria no momento do evento (opcional para alguns detectores).

Detectores de ameaças da base de dados SQL, utilize uma das metodologias de deteção seguintes:

-   **A deteção determinista**: Deteta padrões suspeitos (regras com base) nas consultas de cliente SQL que correspondem aos ataques conhecidos. Este método tem deteção alta e baixa falso positiva, mas limitada cobertura porque ele está na categoria de "detecções atômicas."

-   **Deteção comportamental**: Deteta atividade anómala, que é o comportamento anormal na base de dados que não foi visualizado durante os 30 dias mais recentes. Exemplos de atividade anómala de cliente SQL podem ser um pico de inícios de sessão falhados ou consultas, um grande volume de dados que está a ser extraídos, consultas canônicas invulgares ou familiarizados endereços IP utilizados para aceder a base de dados.

### <a name="application-gateway-web-application-firewall"></a>Firewall de aplicações da Web de Gateway de aplicação

[Web Application Firewall (WAF)](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) é uma funcionalidade do [Gateway de aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) que fornece proteção de aplicações web que utilizam um gateway de aplicação para o standard [o controlo de entrega de aplicações](https://kemptechnologies.com/in/application-delivery-controllers) funções. Firewall de aplicações Web fá-lo ao protegê-los contra a maioria da [Open Web Application Security Project (OWASP) top 10 vulnerabilidades web](https://www.owasp.org/index.php/Top_10_2010-Main).

![Diagrama de Firewall de aplicação Web do Gateway de aplicação](./media/azure-threat-detection/azure-threat-detection-fig13.png)

Proteções incluem:

-   Proteção contra injeção de SQL.

-   Cruzada proteção de criação de scripts do site.

-   Comuns proteção contra ataques Web, como injeção de comandos, contrabando, divisão de respostas HTTP de pedidos HTTP e ataques de inclusão de ficheiros remota.

-   Proteção contra violações de protocolo HTTP.

-   Proteção contra anomalias de protocolo HTTP, como em falta alojar o agente do usuário e cabeçalhos de aceitação.

-   Prevenção de contra bots, crawlers e scanners.

-   Deteção de aplicação incorretas comuns (ou seja, Apache, IIS e assim por diante).

Configurar a WAF no gateway de aplicação fornece as seguintes vantagens:

-   Protege a sua aplicação web contra vulnerabilidades e ataques sem modificações no código de back-end web.

-   Protege várias aplicações web em simultâneo por trás de um gateway de aplicação. Um gateway de aplicação suporta o alojamento de até 20 Web sites.

-   Monitores de aplicativos contra ataques da web através de relatórios em tempo real gerados pelos registos de WAF do gateway de aplicação.

-   Ajuda a cumpre requisitos de conformidade. Determinados controlos de conformidade requerem que todos os pontos finais de acesso à internet para ser protegido por uma solução de WAF.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>API de deteção de anomalias: Criado com o Azure Machine Learning

A API de deteção de anomalias é uma API que é útil para detectar uma variedade de padrões anómalos em seus dados de séries de tempo. A API atribui uma classificação de anomalia a cada ponto de dados na série de tempo, o que pode ser utilizado para gerar alertas, monitorizar através de dashboards ou ligar aos seus sistemas de pedidos de suporte.

O [API de deteção de anomalias](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) pode detetar os seguintes tipos de anomalias nos dados de séries de tempo:

-   **Picos e quedas**: Quando estiver a monitorizar o número de falhas de início de sessão a um serviço ou um número de finalizações de compras num site de comércio eletrônico, picos ou dips podem indicar ataques de segurança ou interrupções ao serviço.

-   **Tendências positivas e negativas**: Quando está a monitorizar utilização da memória na computação, reduzir o tamanho de memória livre indica uma potencial fuga de memória. Para o comprimento da fila do serviço de monitorização, uma tendência de subida persistente pode indicar um problema de software subjacente.

-   **Alterações do nível e no intervalo dinâmico de valores**: Alterações ao nível de um serviço após uma atualização ou níveis mais baixos de exceções após a atualização pode ser interessante monitorizar.

Machine learning com base em API que proporciona:

-   **Deteção de flexível e robusta**: Os modelos de deteção de anomalias permitem aos utilizadores configurar definições sensibilidade e detetar anomalias entre conjuntos de dados sazonais e não sazonais. Os usuários podem ajustar o modelo de deteção de anomalias para fazer a API de deteção de mais ou menos sensível, de acordo com suas necessidades. Isso poderia significar que detectar as anomalias visível menos ou mais em dados com e sem padrões sazonais.

-   **Deteção dimensionável e em tempo hábil**: A maneira tradicional de monitorização com limiares presentes definido pelo conhecimento do domínio dos especialistas são dispendiosos e não dimensionáveis para milhões de conjuntos de dados de alteração dinâmica. Os modelos de deteção de anomalias nesta API adquiridos e modelos são ajustados automaticamente a partir dos dados históricos e em tempo real.

-   **Deteção proativa e passíveis de ação**: Tendência lenta e a deteção de alteração de nível de podem ser aplicadas para deteção de anomalias antecipada. Os sinais anormais iniciais que são detetados podem ser utilizados para direcionar os seres humanos para analisar e atuar sobre áreas problemáticas. Além disso, os modelos de análise de causa raiz e ferramentas de alertas podem ser desenvolvidas na parte superior deste serviço de API de deteção de anomalias.

A API de deteção de anomalias é uma solução eficaz e eficiente para uma vasta gama de cenários, tais como estado de funcionamento do serviço e monitorização, IoT, monitorização de desempenho e monitorização do tráfego de rede KPI. Seguem-se alguns cenários populares em que esta API pode ser útil:

- Os departamentos de TI precisam de ferramentas para controlar eventos, o código de erro, o registo de utilização e o desempenho (CPU, memória etc.) em tempo hábil.

-   Sites de comércio online pretendem controlar atividades do cliente, vistas de página, cliques e assim por diante.

-   Utilitários de empresas pretendem controlar o consumo de água, gás, eletricidade e outros recursos.

-   Serviços de gestão de instalações ou edifício útil monitorizar a temperatura, moisture, tráfego e assim por diante.

-   IoT/fabricantes deve usar dados de sensores na série de tempo para o fluxo de trabalho do monitor, qualidade e assim por diante.

-   Provedores de serviço, como centros de chamada, tem de monitorizar a tendência de pedido de serviço, volume de incidente, comprimento da fila de espera e assim por diante.

-   Grupos de análise de negócios que pretende monitorizar os KPIs de empresas (por exemplo, o volume de vendas, sentimentos de cliente ou de preços) anormal movimento em tempo real.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) é um componente fundamental da pilha do Microsoft Cloud Security. É uma solução abrangente que pode ajudar sua organização quando migra para tirar partido da promessa das aplicações na cloud. Ele mantém o utilizador no controlo com visibilidade melhorada para a atividade. Também aumenta a proteção dos dados vitais nas várias aplicações em cloud.

Com ferramentas para ajudar a desvendar Shadow IT, avaliar o risco, aplicar políticas, investigar atividades e parar ameaças, a sua organização pode migrar com mais segurança para a cloud sem perder o controlo dos dados vitais.

| | |
|---|---|
| Descobrir | Desvende shadow IT com o Cloud App Security. Obtenha visibilidade ao detetar aplicações, atividades, utilizadores, dados e ficheiros no seu ambiente de cloud. Detete as aplicações de terceiros que estão ligadas à sua cloud.|
|Investigar | Investigue as suas aplicações na cloud, utilizando ferramentas forenses de nuvem para aprofundada das aplicações arriscadas, utilizadores específicos e ficheiros na sua rede. Detetar padrões nos dados recolhidos a partir da nuvem. Gere relatórios para monitorizar a sua cloud. |
| Controlo | Reduza o risco através da definição de políticas e alertas para obter o máximo controlo sobre o tráfego de rede de nuvem. Utilize o Cloud App Security para migrar os seus utilizadores para alternativas de aplicação na cloud seguras e aprovadas. |
| Proteger | Utilizar o Cloud App Security para aprovar ou proíbem as aplicações, aplicar prevenção de perda de dados, controlar permissões e partilhas e gerar relatórios e alertas personalizados. |
| Controlo | Reduza o risco através da definição de políticas e alertas para obter o máximo controlo sobre o tráfego de rede de nuvem. Utilize o Cloud App Security para migrar os seus utilizadores para alternativas de aplicação na cloud seguras e aprovadas. |
| | |


![Diagrama do cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

O cloud App Security a integração de visibilidade com a sua cloud:

-   utilizar a Cloud Discovery para mapear e identificar o seu ambiente na cloud e as aplicações na cloud é através de sua organização.

-   Agora, a aprovação e a proibição de aplicações na sua cloud.

-   utilização de conectores de aplicações fáceis de implementar que tiram partido das APIs de fornecedores, para visibilidade e governação de aplicações que ligam à.

-   ajudá-lo a ter controlo contínuo, por definição e, em seguida, ao ajustar continuamente, as políticas.

No recolher dados a partir destas origens, Cloud App Security executa uma análise sofisticada nos mesmos. Imediatamente alerta-o para atividades anómalas e dá-lhe mais visibilidade relativamente à seu ambiente na cloud. Pode configurar uma política no Cloud App Security e utilizá-la para proteger todo o seu ambiente na cloud.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Capacidades de deteção de ameaças avançada de terceiros no Azure Marketplace

### <a name="web-application-firewall"></a>Firewall de Aplicação Web

Web Application Firewall inspeciona o tráfego da web de entrada e bloqueia SQL injeções, scripts entre sites, carregamentos de malware, ataques de DDoS de aplicações e outros ataques destinados a seus aplicativos web. Ele também inspeciona as respostas dos servidores web de back-end para prevenção de perda de dados (DLP). O mecanismo de controlo de acesso integrada permite aos administradores criar acesso granular políticas de controlo para autenticação, autorização e contabilidade (AAA), que dá às organizações forte autenticação e controle de usuário.

Firewall de aplicações Web fornece as seguintes vantagens:

-   Deteta e bloqueia SQL injeções, scripts entre sites, carregamentos de malware, DDoS de aplicações ou outros ataques lançados contra a sua aplicação.

-   Controlo de acesso e autenticação.

-   Analisa o tráfego de saída para detetar dados confidenciais e pode mascarar ou bloquear as informações de impedir o vazamento.

-   Acelera a entrega de conteúdos de aplicação web, utilizando as funcionalidades como a colocação em cache, compressão e outras otimizações de tráfego.

Para obter exemplos de firewalls de aplicações web que estão disponíveis no Azure Marketplace, consulte [Barracuda WAF, firewall de aplicações virtual web (vWAF) Brocade, Imperva SecureSphere e o firewall do IP de ThreatSTOP](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Passos Seguintes

- [Capacidades de deteção do Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities): Ajuda a identificar ameaças ativas que os recursos do Azure de destino e fornece as informações que precisa para responder rapidamente.

- [A deteção de ameaças da base de dados SQL do Azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): Ajuda a solucionar suas preocupações sobre ameaças em potencial para seus bancos de dados.
