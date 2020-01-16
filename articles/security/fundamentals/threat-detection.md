---
title: Detecção avançada de ameaças do Azure | Microsoft Docs
description: Saiba mais sobre Azure AD Identity Protection e seus recursos.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 3c1c385a87fc302d180729ec2e4bcd1c4a315f6f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981469"
---
# <a name="azure-advanced-threat-detection"></a>Detecção avançada de ameaças do Azure

O Azure oferece a funcionalidade de detecção avançada de ameaças integrada por meio de serviços como o Azure Active Directory (Azure AD), os logs de Azure Monitor e a central de segurança do Azure. Essa coleção de serviços e recursos de segurança fornece uma maneira simples e rápida de entender o que está acontecendo em suas implantações do Azure.

O Azure fornece uma ampla gama de opções para configurar e personalizar a segurança para atender aos requisitos de suas implantações de aplicativo. Este artigo discute como atender a esses requisitos.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[Azure ad Identity Protection](../../active-directory/identity-protection/overview.md) é um recurso de edição [Azure Active Directory Premium P2](../../active-directory/active-directory-whatis.md) que fornece uma visão geral das detecções de risco e das possíveis vulnerabilidades que podem afetar as identidades da sua organização. A proteção de identidade usa recursos de detecção de anomalias do Azure AD existentes que estão disponíveis por meio de [relatórios de atividade anômala do Azure ad](../../active-directory/active-directory-reporting-azure-portal.md)e apresenta novos tipos de detecção de riscos que podem detectar anomalias em tempo real.

![Diagrama de Azure AD Identity Protection](./media/threat-detection/azure-threat-detection-fig1.png)

A proteção de identidade usa algoritmos de aprendizado de máquina adaptáveis e heurística para detectar anomalias e detecções de risco que podem indicar que uma identidade foi comprometida. Usando esses dados, a proteção de identidade gera relatórios e alertas para que você possa investigar essas detecções de risco e tomar a ação apropriada de correção ou mitigação.

Azure Active Directory Identity Protection é mais do que uma ferramenta de monitoramento e relatório. Com base nas detecções de riscos, a proteção de identidade calcula um nível de risco do usuário para cada usuário, para que você possa configurar políticas baseadas em risco para proteger automaticamente as identidades da sua organização.

Essas políticas baseadas em risco, além de outros [controles de acesso condicional](../../active-directory/active-directory-conditional-access-azure-portal.md) fornecidos pelo Azure Active Directory e pelo [EMS](../../active-directory/active-directory-conditional-access-azure-portal.md), podem bloquear automaticamente ou oferecer ações de correção adaptável que incluem redefinições de senha e a imposição de autenticação multifator.

### <a name="identity-protection-capabilities"></a>Recursos de proteção de identidade

Azure Active Directory Identity Protection é mais do que uma ferramenta de monitoramento e relatório. Para proteger as identidades de sua organização, você pode configurar políticas baseadas em risco que respondem automaticamente a problemas detectados quando um nível de risco especificado é atingido. Essas políticas, além de outros controles de acesso condicional fornecidos pelo Azure Active Directory e pelo EMS, podem bloquear ou iniciar automaticamente as ações de correção adaptável, incluindo redefinições de senha e a imposição de autenticação multifator.

Exemplos de algumas das maneiras pelas quais a proteção de identidade do Azure pode ajudar a proteger suas contas e identidades incluem:

[Detectando detecções de risco e contas arriscadas](../../active-directory/identity-protection/overview.md)
-   Detecte seis tipos de detecção de risco usando regras heurísticas e aprendizado de máquina.
-   Calcule os níveis de risco do usuário.
-   Forneça recomendações personalizadas para melhorar a postura geral de segurança realçando vulnerabilidades.

[Investigando as detecções de risco](../../active-directory/identity-protection/overview.md)
-   Envie notificações para detecções de risco.
-   Investigue as detecções de risco usando informações relevantes e contextuais.
-   Forneça fluxos de trabalho básicos para controlar investigações.
-   Fornecer acesso fácil a ações de correção, como redefinição de senha.

[Políticas de acesso condicional e baseadas em risco](../../active-directory/identity-protection/overview.md)
-   Reduza entradas arriscadas bloqueando entradas ou exigindo desafios de autenticação multifator.
-   Bloquear ou proteger contas de usuário arriscadas.
-   Exigir que os usuários se registrem para autenticação multifator.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

Com o [PIM (Azure Active Directory Privileged Identity Management)](../../active-directory/privileged-identity-management/pim-configure.md), você pode gerenciar, controlar e monitorar o acesso em sua organização. Esse recurso inclui acesso a recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou Microsoft Intune.

![Diagrama de Azure AD Privileged Identity Management](./media/threat-detection/azure-threat-detection-fig2.png)

O PIM ajuda você a:

-   Obtenha alertas e relatórios sobre os administradores do Azure AD e o acesso administrativo just-in-time (JIT) ao Microsoft serviços online, como o Office 365 e o Intune.

-   Obtenha relatórios sobre o histórico de acesso de administrador e as alterações nas atribuições de administrador.

-   Obtenha alertas sobre o acesso a uma função com privilégios.

## <a name="azure-monitor-logs"></a>Registos do Azure Monitor

O [Azure monitor logs](../../azure-monitor/index.yml) é uma solução de gerenciamento de ti baseada em nuvem da Microsoft que ajuda você a gerenciar e proteger sua infraestrutura local e na nuvem. Como os logs de Azure Monitor são implementados como um serviço baseado em nuvem, você pode tê-los funcionando rapidamente com investimento mínimo em serviços de infraestrutura. Novos recursos de segurança são entregues automaticamente, economizando custos contínuos de manutenção e atualização.

Além de fornecer serviços valiosos por conta própria, Azure Monitor logs podem ser integrados a componentes do System Center, como o [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), para estender seus investimentos de gerenciamento de segurança existentes para a nuvem. Os logs do System Center e do Azure Monitor podem trabalhar juntos para fornecer uma experiência completa de gerenciamento híbrido.

### <a name="holistic-security-and-compliance-posture"></a>Postura holística de segurança e conformidade

O [painel de Segurança e Auditoria log Analytics](../../security-center/security-center-intro.md) fornece uma visão abrangente da postura de segurança de ti de sua organização, com consultas de pesquisa internas para problemas importantes que exigem atenção. O painel de Segurança e Auditoria é a tela inicial para tudo relacionado à segurança em logs de Azure Monitor. Fornece informações de alto nível sobre o estado de segurança dos computadores. Você também pode exibir todos os eventos das últimas 24 horas, 7 dias ou qualquer outro período personalizado.

Os logs de Azure Monitor ajudam a compreender com rapidez e facilidade a postura geral de segurança de qualquer ambiente, tudo no contexto das operações de ti, incluindo avaliação de atualização de software, avaliação antimalware e linhas de base de configuração. Os dados de log de segurança estão prontamente acessíveis para simplificar os processos de auditoria de segurança e conformidade.

![O painel Segurança e Auditoria do Log Analytics](./media/threat-detection/azure-threat-detection-fig3.jpg)

O painel de Segurança e Auditoria Log Analytics é organizado em quatro categorias principais:

-   **Domínios de segurança**: permite que você explore mais os registros de segurança ao longo do tempo; acessar avaliações de malware; avaliações de atualização; exibir informações de segurança de rede, identidade e acesso; Exibir computadores com eventos de segurança; e acesse rapidamente o painel da central de segurança do Azure.

-   **Problemas notáveis**: permite identificar rapidamente o número de problemas ativos e a gravidade dos problemas.

-   **Detecções (versão prévia)** : permite que você identifique os padrões de ataque exibindo alertas de segurança à medida que eles ocorrem em seus recursos.

-   **Inteligência contra ameaças**: permite que você identifique padrões de ataque exibindo o número total de servidores com tráfego IP de saída mal-intencionado, o tipo de ameaça mal-intencionada e um mapa dos locais de IPS.

-   **Consultas de segurança comuns**: lista as consultas de segurança mais comuns que você pode usar para monitorar seu ambiente. Quando você seleciona qualquer consulta, o painel Pesquisar é aberto e exibe os resultados dessa consulta.

### <a name="insight-and-analytics"></a>Insight e análise
No centro dos [logs de Azure monitor](../../log-analytics/log-analytics-queries.md) é o repositório, que é hospedado pelo Azure.

![Diagrama de Insight e análise](./media/threat-detection/azure-threat-detection-fig4.png)

Você coleta dados no repositório de fontes conectadas Configurando fontes de dados e adicionando soluções à sua assinatura.

![O painel de logs de Azure Monitor](./media/threat-detection/azure-threat-detection-fig5.png)

As fontes de dados e soluções criam tipos de registros separados com seu próprio conjunto de propriedades, mas você ainda pode analisá-los juntos em consultas ao repositório. Você pode usar as mesmas ferramentas e métodos para trabalhar com uma variedade de dados coletados por várias fontes.


A maior parte de sua interação com os logs de Azure Monitor é por meio do portal do Azure, que é executado em qualquer navegador e fornece acesso a definições de configuração e várias ferramentas para analisar e agir sobre os dados coletados. No portal, você pode usar:
* [Pesquisas de log](../../log-analytics/log-analytics-queries.md) nas quais você constrói consultas para analisar os dados coletados.
* [Painéis](../../azure-monitor/learn/tutorial-logs-dashboards.md), que você pode personalizar com exibições gráficas de suas pesquisas mais valiosas.
* [Soluções](../../monitoring/monitoring-solutions.md), que fornecem outras ferramentas de análise e funcionalidade.

![Ferramentas de análise](./media/threat-detection/azure-threat-detection-fig6.png)

As soluções adicionam funcionalidade a logs de Azure Monitor. Eles são executados principalmente na nuvem e fornecem análise dos dados coletados no repositório do log Analytics. As soluções também podem definir novos tipos de registro a serem coletados que podem ser analisados com pesquisas de log ou usando uma interface de usuário adicional que a solução fornece no painel do log Analytics.

O painel do Segurança e Auditoria é um exemplo desses tipos de soluções.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automação e controle: alerta sobre as descompassos de configuração de segurança

A automação do Azure automatiza processos administrativos com runbooks baseados no PowerShell e executados na nuvem. Os runbooks podem ser também executados no seu centro de dados local para gerir recursos locais. A automação do Azure fornece gerenciamento de configuração com a DSC (configuração de estado desejado) do PowerShell.

![Diagrama de automação do Azure](./media/threat-detection/azure-threat-detection-fig7.png)

Você pode criar e gerenciar recursos DSC hospedados no Azure e aplicá-los a sistemas locais e na nuvem. Ao fazer isso, você pode definir e impor automaticamente sua configuração ou obter relatórios sobre descompasso para ajudar a garantir que as configurações de segurança permaneçam dentro da política.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

A central de segurança do Azure ajuda a proteger seus recursos do Azure. Ele fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure. Dentro do serviço, você pode definir políticas em relação a suas assinaturas do Azure e [grupos de recursos](../../azure-resource-manager/management/manage-resources-portal.md) para maior granularidade.

![Diagrama da central de segurança do Azure](./media/threat-detection/azure-threat-detection-fig8.png)

Os investigadores de segurança da Microsoft estão constantemente atentos a ameaças. Estes têm acesso a um conjunto amplo de telemetria obtida através da presença global da Microsoft na nuvem e no local. Esta coleção abrangente e diversificada de conjuntos de dados permite à Microsoft descobrir novos padrões de ataque e tendências dos seus produtos de consumidor e empresariais no local, assim como os seus serviços online.

Assim, a central de segurança pode atualizar rapidamente seus algoritmos de detecção, pois os invasores lançam explorações novas e cada vez mais sofisticadas. Essa abordagem ajuda você a acompanhar o ritmo com um ambiente de ameaças de movimentação rápida.

![Detecção de ameaças da central de segurança](./media/threat-detection/azure-threat-detection-fig9.jpg)

A deteção de ameaças do Centro de Segurança funciona através da recolha automática de informações de segurança a partir dos seus recursos do Azure, da rede e das soluções de parceiros ligadas. Ele analisa essas informações, correlacionando informações de várias fontes, para identificar ameaças.

Os alertas de segurança são prioritários no Centro de Segurança, juntamente com recomendações sobre como remediar a ameaça.

O Centro de Segurança emprega análises de segurança avançadas, que ultrapassam as abordagens baseadas na assinatura. As inovações em tecnologias de Big Data e de [aprendizado de máquina](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) são usadas para avaliar eventos em toda a malha de nuvem. A análise avançada pode detectar ameaças que seriam impossíveis de identificar por meio de abordagens manuais e prevendo a evolução dos ataques. Esses tipos de análise de segurança são abordados nas próximas seções.

### <a name="threat-intelligence"></a>Informações sobre ameaças

A Microsoft tem acesso a uma grande quantidade de inteligência contra ameaças globais.

A telemetria flui de várias fontes, como o Azure, o Office 365, o Microsoft CRM Online, o Microsoft Dynamics AX, o outlook.com, o MSN.com, a unidade de crimes digitais da Microsoft (DCU) e o Microsoft Security Response Center (MSRC).

![Conclusões de inteligência contra ameaças](./media/threat-detection/azure-threat-detection-fig10.jpg)

Os pesquisadores também recebem informações de inteligência contra ameaças que são compartilhadas entre os principais provedores de serviços de nuvem e assinam os feeds de inteligência contra ameaças de terceiros. O Centro de Segurança do Azure pode utilizar estas informações para alertá-lo de ameaças de pessoas mal-intencionadas conhecidas. Alguns exemplos incluem:

-   **Aproveitando o poder do aprendizado de máquina**: a central de segurança do Azure tem acesso a uma vasta quantidade de dados sobre a atividade de rede na nuvem, que pode ser usada para detectar ameaças direcionadas a suas implantações do Azure.

-   **Detecção de força bruta**: o Machine Learning é usado para criar um padrão histórico de tentativas de acesso remoto, o que permite detectar ataques de força bruta contra Secure Shell (SSH), protocolo RDP (RDP) e portas do SQL.

-   **Detecção de DDoS e botnet de saída**: um objetivo comum de ataques que visam recursos de nuvem é usar o poder de computação desses recursos para executar outros ataques.

-   **Novos servidores e VMs de análise comportamental**: depois que um servidor ou uma máquina virtual é comprometida, os invasores empregam uma ampla variedade de técnicas para executar código mal-intencionado no sistema, ao mesmo tempo que evitam a detecção, a garantia de persistência e os controles de segurança dispensando.

-   **Detecção de ameaças do banco de dados SQL do Azure**: detecção de ameaças para o banco de dados SQL do Azure, que identifica atividades anormais de banco de dados que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

### <a name="behavioral-analytics"></a>Análise comportamental

A análise comportamental é uma técnica que analisa e compara os dados para uma coleção de padrões conhecidos. No entanto, estes padrões não são simples assinaturas. São determinados através de algoritmos complexos de machine learning aplicados a conjuntos de dados gigantescos.

![Conclusões de análise comportamental](./media/threat-detection/azure-threat-detection-fig11.jpg)

Os padrões também são determinados por meio de uma análise cuidadosa de comportamentos mal-intencionados por analistas especialistas. A central de segurança do Azure pode usar a análise comportamental para identificar recursos comprometidos com base na análise de logs de máquina virtual, logs de dispositivo de rede virtual, logs de malha, despejos de memória e outras fontes.

Além disso, os padrões são correlacionados a outros sinais para verificar a evidência de suporte de uma campanha generalizada. Esta correlação ajuda a identificar eventos que são consistentes com indicadores estabelecidos de comprometimento.

Alguns exemplos incluem:
-   **Execução do processo suspeito**: os atacantes empregam várias técnicas para executar o software malicioso sem deteção. Por exemplo, um invasor pode dar os mesmos nomes de malware que arquivos de sistema legítimos, mas colocar esses arquivos em um local alternativo, usar um nome semelhante ao de um arquivo benigno ou mascarar a extensão verdadeira do arquivo. A central de segurança modela comportamentos de processo e monitora as execuções de processos para detectar exceções como essas.

-   **Tentativas de malware e exploração ocultas**: malware sofisticado podem escapar dos produtos Antimalware tradicionais, nunca gravando em disco ou criptografando componentes de software armazenados em disco. No entanto, esse malware pode ser detectado usando a análise de memória, porque o malware deve deixar rastreamentos na memória para funcionar. Quando o software falha, uma informação de falha de sistema captura uma parte da memória no momento da falha. Analisando a memória no despejo de falha, a central de segurança do Azure pode detectar técnicas usadas para explorar vulnerabilidades no software, acessar dados confidenciais e maneira furtiva manter em um computador comprometido sem afetar o desempenho do seu Tradução.

-   **Movimentação lateral e reconhecimento interno**: para persistir em uma rede comprometida e localizar e coletar dados valiosos, os invasores geralmente tentam mover-se para outras tarde da máquina comprometida para outras dentro da mesma rede. A central de segurança monitora as atividades de processo e de logon para descobrir tentativas de expandir a parte do invasor dentro da rede, como execução de comando remoto, investigação de rede e enumeração de conta.

-   **Scripts do PowerShell mal-intencionados**: o PowerShell pode ser usado por invasores para executar código mal-intencionado em máquinas virtuais de destino para vários fins. O Centro da Segurança inspeciona a atividade do PowerShell quanto a provas de atividade suspeita.

-   **Ataques de saída**: os atacantes concentram-se muitas vezes nos recursos da nuvem com o objetivo de os utilizar para preparar ataques adicionais. As máquinas virtuais comprometidas, por exemplo, podem ser usadas para iniciar ataques de força bruta em relação a outras máquinas virtuais, enviar spam ou verificar portas abertas e outros dispositivos na Internet. Ao aplicar o machine learning ao tráfego de rede, o Centro de Segurança pode detetar quando as comunicações da rede de saída excedem o normal. Quando o spam é detectado, a central de segurança também correlaciona o tráfego de email incomum com inteligência do Office 365 para determinar se o email é provavelmente perigoso ou o resultado de uma campanha de email legítima.

### <a name="anomaly-detection"></a>Deteção de anomalias

O Centro de Segurança do Azure utiliza também a deteção de anomalias para identificar ameaças. Contrariamente à análise comportamental (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a deteção de anomalias é mais “personalizada” e concentra-se nas linhas de base específicas das suas implementações. O Machine Learning é aplicado para determinar a atividade normal para suas implantações e, em seguida, as regras são geradas para definir condições de exceção que podem representar um evento de segurança. Segue-se um exemplo:

-   **Ataques de força bruta de RDP/SSH de entrada**: suas implantações podem ter máquinas virtuais ocupadas com muitos logons por dia e outras máquinas virtuais que têm poucos logons, se houver. A central de segurança do Azure pode determinar a atividade de logon de linha de base para essas máquinas virtuais e usar o aprendizado de máquina para definir as atividades normais de logon. Se houver alguma discrepância com a linha de base definida para as características relacionadas ao logon, um alerta poderá ser gerado. Novamente, o machine learning determina o que é significativo.

### <a name="continuous-threat-intelligence-monitoring"></a>Monitorização de informações sobre ameaças contínua

A central de segurança do Azure opera com as equipes de pesquisa de segurança e ciência de dados em todo o mundo que monitoram continuamente as alterações no cenário de ameaças. Isto inclui as seguintes iniciativas:

-   **Monitoramento de inteligência contra ameaças**: a inteligência contra ameaças inclui mecanismos, indicadores, implicações e conselhos acionáveis sobre ameaças emergentes ou existentes. Essas informações são compartilhadas na Comunidade de segurança e a Microsoft monitora continuamente os feeds de inteligência contra ameaças de fontes internas e externas.

-   **Compartilhamento de sinal**: informações de equipes de segurança em todo o amplo portfólio da Microsoft de serviços de nuvem e locais, servidores e dispositivos de ponto de extremidade de cliente são compartilhados e analisados.

-   **Especialistas em segurança da Microsoft**: envolvimento contínuo com equipes na Microsoft que trabalham em campos de segurança especializados, como detecção de ataques da Web e forense.

-   **Ajuste de detecção**: os algoritmos são executados em conjuntos de dados reais do cliente e os pesquisadores de segurança trabalham com os clientes para validar os resultados. Os verdadeiros e falsos positivos são utilizados para refinar os algoritmos do machine learning.

Esses esforços combinados culminam em detecções novas e aprimoradas, que você pode aproveitar instantaneamente. Não há nenhuma ação a ser tomada.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Recursos de detecção avançada de ameaças: outros serviços do Azure

### <a name="virtual-machines-microsoft-antimalware"></a>Máquinas virtuais: Microsoft Antimalware

[O Microsoft Antimalware](antimalware.md) para Azure é uma solução de agente único para aplicativos e ambientes de locatário, projetada para ser executada em segundo plano sem intervenção humana. Você pode implantar a proteção com base nas necessidades de suas cargas de trabalho de aplicativo, com a configuração básica de segurança padrão ou personalizada avançada, incluindo o monitoramento antimalware. O antimalware do Azure é uma opção de segurança para máquinas virtuais do Azure que é instalada automaticamente em todas as máquinas virtuais de PaaS do Azure.

#### <a name="microsoft-antimalware-core-features"></a>Recursos principais do Microsoft Antimalware

Aqui estão os recursos do Azure que implantam e habilitam o Microsoft antimalware para seus aplicativos:

-   **Proteção em tempo real**: monitora a atividade em serviços de nuvem e em máquinas virtuais para detectar e bloquear a execução de malware.

-   **Verificação agendada**: executa periodicamente a verificação direcionada para detectar malware, incluindo programas ativamente em execução.

-   **Correção de malware**: atua automaticamente em malware detectado, como excluir ou colocar arquivos mal-intencionados em quarentena e limpar entradas de registro mal-intencionadas.

-   **Atualizações de assinatura**: o instala automaticamente as assinaturas de proteção mais recentes (definições de vírus) para garantir que a proteção esteja atualizada em uma frequência predeterminada.

-   **Atualizações de antimalware Engine**: atualiza automaticamente o Microsoft Antimalware Engine.

-   **Atualizações da plataforma Antimalware**: atualiza automaticamente a plataforma antimalware da Microsoft.

-   **Proteção ativa**: relata os metadados de telemetria sobre ameaças detectadas e recursos suspeitos para Microsoft Azure para garantir uma resposta rápida ao panorama de ameaças em constante evolução, permitindo a entrega de assinatura síncrona em tempo real por meio do Microsoft Active Protection System.

-   **Relatórios de exemplos**: fornece e relata exemplos para o serviço Microsoft antimalware para ajudar a refinar o serviço e a habilitar a solução de problemas.

-   **Exclusões**: permite que os administradores de aplicativos e serviços configurem determinados arquivos, processos e unidades para exclusão da proteção e verificação de desempenho e outros motivos.

-   **Coleção de eventos Antimalware**: registra a integridade do serviço Antimalware, as atividades suspeitas e as ações de correção executadas no log de eventos do sistema operacional e as coleta na conta de armazenamento do Azure do cliente.

### <a name="azure-sql-database-threat-detection"></a>Detecção de ameaças do banco de dados SQL do Azure

A [detecção de ameaças do banco de dados SQL do Azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) é um novo recurso de inteligência de segurança incorporado ao serviço de banco de dados SQL do Azure. Contornando o relógio para aprender, criar um perfil e detectar atividades anormais de banco de dados, a detecção de ameaças do Azure SQL Database identifica possíveis ameaças ao banco de dados.

Os responsáveis pela segurança ou outros administradores designados podem obter uma notificação imediata sobre atividades suspeitas do banco de dados conforme elas ocorrem. Cada notificação fornece detalhes da atividade suspeita e recomenda como investigar e atenuar a ameaça.

Atualmente, a detecção de ameaças do banco de dados SQL do Azure detecta possíveis vulnerabilidades e ataques de injeção de SQL e padrões de acesso de banco de dados anormais.

Após receber uma notificação por email de detecção de ameaças, os usuários poderão navegar e exibir os registros de auditoria relevantes por meio de um link profundo no email. O link abre um visualizador de auditoria ou um modelo de auditoria pré-configurado do Excel que mostra os registros de auditoria relevantes em vez do evento suspeito, de acordo com o seguinte:

-   Auditar o armazenamento para o banco de dados/servidor com as atividades anormais do banco de dados.

-   Tabela de armazenamento de auditoria relevante que foi usada no momento do evento para gravar o log de auditoria.

-   Registros de auditoria da hora imediatamente após a ocorrência do evento.

-   Registros de auditoria com uma ID de evento semelhante no momento do evento (opcional para alguns detectores).

Os detectores de ameaças do banco de dados SQL usam uma das seguintes metodologias de detecção:

-   **Detecção determinística**: detecta padrões suspeitos (baseados em regras) nas consultas do cliente SQL que correspondem a ataques conhecidos. Essa metodologia tem detecção alta e baixa de falsos positivos, mas cobertura limitada porque ela está dentro da categoria de "detecções atômicas".

-   **Detecção comportamental**: detecta a atividade anômala, que é um comportamento anormal no banco de dados que não foi visto durante os 30 dias mais recentes. Exemplos de atividade de anomalias do cliente SQL podem ser um pico de logons ou consultas com falha, um alto volume de dados sendo extraídos, consultas canônicas incomuns ou endereços IP desconhecidos usados para acessar o banco de dado.

### <a name="application-gateway-web-application-firewall"></a>Firewall do aplicativo Web do gateway de aplicativo

O [WAF (firewall do aplicativo Web)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) é um recurso do [Gateway de aplicativo Azure](../../application-gateway/application-gateway-web-application-firewall-overview.md) que fornece proteção para aplicativos Web que usam um gateway de aplicativo para funções de controle de entrega de [aplicativos](https://kemptechnologies.com/in/application-delivery-controllers) padrão. O Firewall do aplicativo Web faz isso protegendo-os contra a maioria das [10 principais vulnerabilidades da Web do OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Top_10_2010-Main).

![Diagrama de firewall do aplicativo Web do gateway de aplicativo](./media/threat-detection/azure-threat-detection-fig13.png)

As proteções incluem:

-   Proteção de injeção de SQL.

-   Proteção de scripts entre sites.

-   Proteção comum contra ataques da Web, como injeção de comandos, indesejada de solicitação HTTP, divisão de resposta HTTP e ataque de inclusão de arquivo remoto.

-   Proteção contra violações de protocolo HTTP.

-   Proteção contra anomalias de protocolo HTTP, como o agente de usuário de host ausente e os cabeçalhos de aceitação.

-   Prevenção contra bots, rastreadores e scanners.

-   Detecção de incorretas configurações de aplicativo comuns (isto é, Apache, IIS e assim por diante).

Configurar o WAF no seu gateway de aplicativo oferece os seguintes benefícios:

-   Protege seu aplicativo Web contra vulnerabilidades e ataques da Web sem a modificação do código de back-end.

-   Protege vários aplicativos Web ao mesmo tempo por trás de um gateway de aplicativo. Um gateway de aplicativo dá suporte à Hospedagem de até 20 sites.

-   Monitora aplicativos Web contra ataques usando relatórios em tempo real gerados por logs de WAF do gateway de aplicativo.

-   Ajuda a atender aos requisitos de conformidade. Determinados controles de conformidade exigem que todos os pontos de extremidade voltados para a Internet sejam protegidos por uma solução WAF.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>API de detecção de anomalias: compilada com Azure Machine Learning

A API de detecção de anomalias é uma API útil para detectar uma variedade de padrões anormais em seus dados de série temporal. A API atribui uma pontuação de anomalia a cada ponto de dados na série temporal, que pode ser usada para gerar alertas, monitorar por meio de painéis ou conectar-se com seus sistemas de tíquetes.

A [API de detecção de anomalias](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) pode detectar os seguintes tipos de anomalias em dados de série temporal:

-   **Picos e DIPs**: quando você está monitorando o número de falhas de logon em um serviço ou número de check-outs em um site de comércio eletrônico, picos ou DIPs incomuns podem indicar ataques de segurança ou interrupções de serviço.

-   **Tendências positivas e negativas**: quando você está monitorando o uso de memória na computação, reduzir o tamanho da memória livre indica um possível vazamento de memória. Para o monitoramento de comprimento da fila de serviço, uma tendência ascendente persistente pode indicar um problema de software subjacente.

-   **Alterações de nível e alterações no intervalo dinâmico de valores**: alterações de nível em latências de um serviço após uma atualização de serviço ou níveis inferiores de exceções após a atualização podem ser interessantes para monitorar.

A API baseada em aprendizado de máquina permite:

-   **Detecção flexível e robusta**: os modelos de detecção de anomalias permitem que os usuários definam configurações de sensibilidade e detectem anomalias entre conjuntos de dados sazonais e não sazonais. Os usuários podem ajustar o modelo de detecção de anomalias para tornar a API de detecção menos ou mais sensível de acordo com suas necessidades. Isso significaria detectar as anomalias menos ou mais visíveis nos dados com e sem padrões sazonais.

-   **Detecção escalonável e oportuna**: a maneira tradicional de monitorar com os limites presentes definidos pelo conhecimento de domínio dos especialistas é dispendiosa e não é escalonável para milhões de conjuntos de dados de alteração dinâmica. Os modelos de detecção de anomalias nessa API são aprendidos e os modelos são ajustados automaticamente de dados históricos e em tempo real.

-   **Detecção proativa e acionável**: a detecção de tendência lenta e de alteração de nível pode ser aplicada para detecção de anomalias inicial. Os sinais anormais iniciais detectados podem ser usados para direcionar os seres humanos a investigar e agir nas áreas problemáticas. Além disso, os modelos de análise de causa raiz e as ferramentas de alerta podem ser desenvolvidos sobre esse serviço de API de detecção de anomalias.

A API de detecção de anomalias é uma solução eficaz e eficiente para uma ampla gama de cenários, como integridade do serviço e monitoramento de KPI, IoT, monitoramento de desempenho e monitoramento de tráfego de rede. Aqui estão alguns cenários populares em que essa API pode ser útil:

- Os departamentos de ti precisam de ferramentas para acompanhar eventos, código de erro, log de uso e desempenho (CPU, memória e assim por diante) em tempo hábil.

-   Sites de comércio online desejam acompanhar atividades do cliente, exibições de página, cliques e assim por diante.

-   As empresas do utilitário desejam acompanhar o consumo de água, gás, eletricidade e outros recursos.

-   A instalação ou a criação de serviços de gerenciamento desejam monitorar a temperatura, a umidade, o tráfego e assim por diante.

-   IoT/fabricantes desejam usar dados de sensor na série temporal para monitorar o fluxo de trabalho, a qualidade e assim por diante.

-   Provedores de serviços, como call centers, precisam monitorar a tendência de demanda de serviço, o volume de incidentes, o comprimento da fila de espera e assim por diante.

-   Os grupos de análise de negócios desejam monitorar a movimentação anormal dos KPIs (como volume de vendas, opiniões do cliente ou preço) em tempo real.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) é um componente crítico da pilha de segurança do Microsoft Cloud. É uma solução abrangente que pode ajudar sua organização à medida que você se move para aproveitar ao máximo a promessa de aplicativos em nuvem. Ele mantém você no controle, por meio da visibilidade aprimorada da atividade. Também aumenta a proteção dos dados vitais nas várias aplicações em cloud.

Com ferramentas para ajudar a desvendar Shadow IT, avaliar o risco, aplicar políticas, investigar atividades e parar ameaças, a sua organização pode migrar com mais segurança para a cloud sem perder o controlo dos dados vitais.

| | |
|---|---|
| Descobrir | Desvende o TI sombra com o Cloud App Security. Obtenha visibilidade ao detetar aplicações, atividades, utilizadores, dados e ficheiros no seu ambiente na cloud. Detete aplicações de terceiros que estão ligadas à sua cloud.|
|Investigar | Investigue as suas aplicações na cloud com ferramentas forenses de cloud para ajudar a obter informações detalhadas sobre aplicações arriscadas, utilizadores e ficheiros específicos na sua rede. Descubra padrões nos dados recolhidos a partir da cloud. Gere relatórios para monitorizar a sua cloud. |
| Controlo | Reduza o risco através da definição de políticas e alertas para obter o máximo controlo sobre o tráfego de rede de cloud. Utilize o Cloud App Security para migrar os seus utilizadores para aplicações em nuvem alternativas seguras e aprovadas. |
| Proteger | Use Cloud App Security para aprovar ou proibir aplicativos, impor a prevenção de perda de dados, controlar permissões e compartilhamento e gerar relatórios e alertas personalizados. |
| Controlo | Reduza o risco através da definição de políticas e alertas para obter o máximo controlo sobre o tráfego de rede de cloud. Utilize o Cloud App Security para migrar os seus utilizadores para aplicações em nuvem alternativas seguras e aprovadas. |
| | |


![Diagrama de Cloud App Security](./media/threat-detection/azure-threat-detection-fig14.png)

O Cloud App Security integra a visibilidade à sua nuvem:

-   Usar Cloud Discovery para mapear e identificar seu ambiente de nuvem e os aplicativos de nuvem que sua organização está usando.

-   Aprovar e proibir aplicativos em sua nuvem.

-   Usando conectores de aplicativos fáceis de implantar que aproveitam as APIs do provedor, para visibilidade e governança de aplicativos aos quais você se conecta.

-   Ajudando você a ter controle contínuo Configurando e, em seguida, ajustar continuamente as políticas.

Ao coletar dados dessas fontes, Cloud App Security executa uma análise sofisticada sobre ela. Alerta-o imediatamente de atividades anómalas e dá-lhe mais visibilidade para o seu ambiente em cloud. Pode configurar uma política no Cloud App Security e utilizá-la para proteger tudo no seu ambiente em cloud.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Recursos de detecção avançada de ameaças de terceiros por meio do Azure Marketplace

### <a name="web-application-firewall"></a>Firewall de Aplicações Web

O Firewall do aplicativo Web inspeciona o tráfego da Web de entrada e bloqueia as injeções de SQL, scripts entre sites, carregamentos de malware, ataques de DDoS de aplicativo e outros ataques direcionados aos seus aplicativos Web. Ele também inspeciona as respostas dos servidores Web back-end para DLP (prevenção contra perda de dados). O mecanismo de controle de acesso integrado permite que os administradores criem políticas de controle de acesso granulares para autenticação, autorização e contabilidade (AAA), o que oferece às organizações uma autenticação forte e um controle de usuário.

O Firewall do aplicativo Web oferece os seguintes benefícios:

-   Detecta e bloqueia injeções de SQL, scripts entre sites, carregamentos de malware, DDoS de aplicativo ou qualquer outro ataque contra seu aplicativo.

-   Autenticação e controle de acesso.

-   Verifica o tráfego de saída para detectar dados confidenciais e pode mascarar ou bloquear a perda das informações.

-   Acelera a entrega de conteúdo de aplicativos Web, usando recursos como cache, compactação e outras otimizações de tráfego.

Para obter exemplos de firewalls do aplicativo Web que estão disponíveis no Azure Marketplace, consulte [Barracuda WAF, vWAF (firewall do aplicativo Web virtual) da Brocade, Imperva SecureSphere e o firewall de IP do ThreatSTOP](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Passos seguintes

- [Respondendo às ameaças atuais](../../security-center/security-center-alerts-overview.md#respond-threats): ajuda a identificar ameaças ativas direcionadas aos recursos do Azure e fornece as informações que você precisa responder rapidamente.

- [Detecção de ameaças do banco de dados SQL do Azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): ajuda a resolver suas preocupações sobre possíveis ameaças aos seus bancos de dados.
