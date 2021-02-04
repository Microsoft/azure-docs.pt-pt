---
title: Proteção de ameaças Azure | Microsoft Docs
description: Saiba mais sobre a funcionalidade de proteção de ameaças incorporada para o Azure, como o serviço de Proteção de Identidade AZure AD.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: eb8332bda2105c3f83c0c1cc28fb7db4b1ca0102
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549954"
---
# <a name="azure-threat-protection"></a>Proteção de ameaças Azure

A Azure oferece funcionalidades de proteção de ameaças construídas através de serviços como Azure Ative Directory (Azure AD), registos Azure Monitor e Azure Security Center. Esta coleção de serviços e capacidades de segurança fornece uma maneira simples e rápida de entender o que está acontecendo dentro das suas implementações Azure.

O Azure oferece um vasto leque de opções para configurar e personalizar a segurança para satisfazer os requisitos das suas implementações de aplicações. Este artigo discute como satisfazer estes requisitos.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[A Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) é uma funcionalidade de edição [P2 do Azure Ative Directory Premium](../../active-directory/fundamentals/active-directory-whatis.md) que fornece uma visão geral das deteções de riscos e potenciais vulnerabilidades que podem afetar as identidades da sua organização. A Proteção de Identidade utiliza as capacidades de deteção de anomalias Azure AD existentes que estão disponíveis através de [Relatórios de Atividades Anómalas AD Azure](../../active-directory/reports-monitoring/overview-reports.md), e introduz novos tipos de deteção de riscos que podem detetar anomalias em tempo real.

![Diagrama de Proteção de Identidade Azure AD](./media/threat-detection/azure-threat-detection-fig1.png)

A Proteção de Identidade utiliza algoritmos de aprendizagem automática adaptativos e heurística para detetar anomalias e deteções de riscos que podem indicar que uma identidade foi comprometida. Utilizando estes dados, a Proteção de Identidade gera relatórios e alertas para que possa investigar estas deteções de riscos e tomar medidas de reparação ou mitigação adequadas.

A Azure Ative Directory Identity Protection é mais do que uma ferramenta de monitorização e relatório. Com base em deteções de risco, a Proteção de Identidade calcula um nível de risco do utilizador para cada utilizador, para que possa configurar políticas baseadas no risco para proteger automaticamente as identidades da sua organização.

Estas políticas baseadas no risco, para além de outros [controlos de Acesso Condicional](../../active-directory/conditional-access/overview.md) que são fornecidos pelo Azure Ative Directory e [pela EMS,](../../active-directory/conditional-access/overview.md)podem bloquear automaticamente ou oferecer ações de remediação adaptativa que incluem resets de palavra-passe e aplicação de autenticação de vários fatores.

### <a name="identity-protection-capabilities"></a>Capacidades de Proteção de Identidade

A Azure Ative Directory Identity Protection é mais do que uma ferramenta de monitorização e relatório. Para proteger as identidades da sua organização, pode configurar políticas baseadas no risco que respondam automaticamente a problemas detetados quando um nível de risco especificado foi atingido. Estas políticas, para além de outros controlos de Acesso Condicional fornecidos pela Azure Ative Directory e pela EMS, podem bloquear automaticamente ou iniciar ações de remediação adaptativa, incluindo resets de palavra-passe e aplicação de autenticação de vários fatores.

Exemplos de algumas das formas que a Azure Identity Protection pode ajudar a proteger as suas contas e identidades incluem:

[Deteção de deteções de riscos e contas de risco](../../active-directory/identity-protection/overview-identity-protection.md)
-   Detetar seis tipos de deteção de risco utilizando máquinas de aprendizagem e regras heurísticas.
-   Calcular os níveis de risco do utilizador.
-   Forneça recomendações personalizadas para melhorar a postura geral de segurança, destacando as vulnerabilidades.

[Investigação de deteções de riscos](../../active-directory/identity-protection/overview-identity-protection.md)
-   Envie notificações para deteções de riscos.
-   Investigar deteções de riscos utilizando informações relevantes e contextuais.
-   Fornecer fluxos de trabalho básicos para acompanhar as investigações.
-   Fornecer fácil acesso a ações de remediação, como o reset da palavra-passe.

[Políticas de acesso condicional e baseadas em riscos](../../active-directory/identity-protection/overview-identity-protection.md)
-   Atenuar as entradas de risco bloqueando as entradas ou exigindo desafios de autenticação de vários fatores.
-   Bloqueie ou proteja as contas de utilizador arriscadas.
-   Exija que os utilizadores se registem para a autenticação de vários fatores.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

Com [a Azure Ative Directory Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md)pode gerir, controlar e monitorizar o acesso dentro da sua organização. Esta funcionalidade inclui acesso a recursos em AD Azure e outros serviços online da Microsoft, como o Microsoft 365 ou o Microsoft Intune.

![Diagrama de Gestão de Identidade Privilegiada Azure AD](./media/threat-detection/azure-threat-detection-fig2.png)

A PIM ajuda-o:

-   Receba alertas e relatórios sobre administradores da AD Azure e acesso administrativo just-in-time (JIT) aos serviços online da Microsoft, como o Microsoft 365 e o Intune.

-   Obtenha relatórios sobre o histórico de acesso do administrador e alterações nas atribuições de administrador.

-   Receba alertas sobre o acesso a um papel privilegiado.

## <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Os registos do Azure Monitor](../../azure-monitor/index.yml) são uma solução de gestão de TI baseada na nuvem da Microsoft que o ajuda a gerir e proteger as suas infraestruturas de nuvem e instalações. Como os registos do Azure Monitor são implementados como um serviço baseado na nuvem, você pode tê-lo em funcionamento e rapidamente com o mínimo de investimento em serviços de infraestrutura. As novas funcionalidades de segurança são entregues automaticamente, poupando custos de manutenção e atualização em curso.

Além de fornecer serviços valiosos por si só, os registos do Azure Monitor podem integrar-se com componentes do System Center, como [o System Center Operations Manager,](/archive/blogs/cbernier/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started)para alargar os investimentos existentes na gestão de segurança na nuvem. Os registos system center e Azure Monitor podem trabalhar em conjunto para proporcionar uma experiência de gestão híbrida completa.

### <a name="holistic-security-and-compliance-posture"></a>Postura holística de segurança e conformidade

[O Azure Security Center](../../security-center/security-center-introduction.md) oferece uma visão abrangente da postura de segurança de TI da sua organização, com consultas de pesquisa incorporadas para questões notáveis que requerem a sua atenção. Fornece informações de alto nível sobre o estado de segurança dos computadores. Você também pode ver todos os eventos das últimas 24 horas, 7 dias, ou qualquer outro time-frame personalizado.

Os registos do Azure Monitor ajudam-no a compreender de forma rápida e fácil a postura de segurança geral de qualquer ambiente, tudo no contexto das Operações de TI, incluindo a avaliação da atualização de software, a avaliação antimalware e as linhas de base de configuração. Os dados de registo de segurança são facilmente acessíveis para simplificar os processos de auditoria de segurança e conformidade.

### <a name="insight-and-analytics"></a>Insight e análise
No centro dos registos do [Monitor Azure](../../azure-monitor/log-query/log-query-overview.md) está o repositório, que é hospedado pela Azure.

![Diagrama de insight e analítica](./media/threat-detection/azure-threat-detection-fig4.png)

Recolhe dados no repositório a partir de fontes conectadas, configurando fontes de dados e adicionando soluções à sua subscrição.

Fontes de dados e soluções cada uma criam tipos de registo separados com o seu próprio conjunto de propriedades, mas ainda pode analisá-los em conjunto em consultas ao repositório. Pode utilizar as mesmas ferramentas e métodos para trabalhar com uma variedade de dados que são recolhidos por várias fontes.

A maior parte da sua interação com os registos do Azure Monitor é através do portal Azure, que funciona em qualquer navegador e fornece-lhe acesso a configurações de configuração e múltiplas ferramentas para analisar e agir em dados recolhidos. A partir do portal, pode utilizar:
* [Faça pesquisas](../../azure-monitor/log-query/log-query-overview.md) de registo onde constrói consultas para analisar dados recolhidos.
* [Dashboards,](../../azure-monitor/learn/tutorial-logs-dashboards.md)que pode personalizar com vistas gráficas das suas pesquisas mais valiosas.
* [Soluções](../../azure-monitor/insights/solutions.md), que fornecem ferramentas adicionais de funcionalidade e análise.

As soluções adicionam funcionalidade aos registos do Monitor Azure. Eles correm principalmente na nuvem e fornecem a análise de dados recolhidos no repositório de analítica de log. As soluções também podem definir novos tipos de registo a serem recolhidos que podem ser analisados com pesquisas de registo ou utilizando uma interface adicional de utilizador que a solução fornece no painel de análise de registos.

O Centro de Segurança é um exemplo deste tipo de soluções.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automação e controlo: Alerta sobre derivas de configuração de segurança

A Azure Automation automatiza processos administrativos com livros que são baseados no PowerShell e funcionam na nuvem. Os runbooks podem ser também executados no seu centro de dados local para gerir recursos locais. A Azure Automation fornece gestão de configuração com configuração de estado desejada powerShell (DSC).

![Diagrama de Automação Azure](./media/threat-detection/azure-threat-detection-fig7.png)

Você pode criar e gerir recursos DSC que estão hospedados em Azure e aplicá-los em sistemas de nuvem e no local. Ao fazê-lo, pode definir e impor automaticamente a sua configuração ou obter relatórios sobre deriva para ajudar a garantir que as configurações de segurança permanecem dentro da política.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

O Centro de Segurança Azure ajuda a proteger o seu ambiente de nuvem híbrida. Ao realizar avaliações contínuas de segurança dos seus recursos conectados, é capaz de fornecer recomendações de segurança detalhadas para as vulnerabilidades descobertas.

As recomendações do Security Center baseiam-se no Benchmark de [Segurança Azure](../benchmarks/introduction.md) - o conjunto de diretrizes específicas da Microsoft para a segurança e conformidade das melhores práticas com base em quadros comuns de conformidade. Este referencial amplamente respeitado baseia-se nos controlos do [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e do Instituto Nacional de [Normalização e Tecnologia (NIST)](https://www.nist.gov/) com foco na segurança centrada na nuvem.

A plataforma integrada de proteção da carga de trabalho em nuvem (CWPP), **Azure Defender,** traz uma proteção avançada, inteligente, dos seus recursos e cargas híbridas e azure. Ativar o Azure Defender traz uma gama de funcionalidades de segurança adicionais (ver [Introdução ao Azure Defender).](../../security-center/azure-defender.md) O painel Azure Defender no Security Center proporciona visibilidade e controlo das funcionalidades CWP para o seu ambiente:

:::image type="content" source="../../security-center/media/azure-defender/sample-defender-dashboard.png" alt-text="Um exemplo do painel do Azure Defender" lightbox="../../security-center/media/azure-defender/sample-defender-dashboard.png":::

Os investigadores de segurança da Microsoft estão constantemente atentos a ameaças. Estes têm acesso a um conjunto amplo de telemetria obtida através da presença global da Microsoft na nuvem e no local. Esta coleção abrangente e diversificada de conjuntos de dados permite à Microsoft descobrir novos padrões de ataque e tendências dos seus produtos de consumidor e empresariais no local, assim como os seus serviços online.

Assim, o Security Center pode atualizar rapidamente os seus algoritmos de deteção à medida que os atacantes lançam novas e cada vez mais sofisticadas explorações. Esta abordagem ajuda-o a acompanhar um ambiente de ameaça em rápida mudança.

:::image type="content" source="../../security-center/media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lista de alertas de segurança do Azure Security Center":::

O Azure Defender recolhe automaticamente informações de segurança dos seus recursos, da rede e das soluções de parceiros conectados. Analisa esta informação, correlacionando informações de várias fontes, para identificar ameaças.

Os alertas do Azure Defender são priorizados no Centro de Segurança, juntamente com recomendações sobre como remediar as ameaças.

O Centro de Segurança emprega análises de segurança avançadas, que ultrapassam as abordagens baseadas na assinatura. Os avanços nas tecnologias de big data e [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) são usados para avaliar eventos em toda a nuvem. Análises avançadas podem detetar ameaças que seriam impossíveis de identificar através de abordagens manuais e prever a evolução dos ataques. Estes tipos de análise de segurança estão cobertos nas próximas secções.

### <a name="threat-intelligence"></a>Informações sobre ameaças

A Microsoft tem acesso a uma enorme quantidade de inteligência global de ameaças.

A telemetria flui de várias fontes, tais como Azure, Microsoft 365, Microsoft CRM on-line, Microsoft Dynamics AX, outlook.com, MSN.com, a Microsoft Digital Crimes Unit (DCU) e o Microsoft Security Response Center (MSRC).

![Descobertas de inteligência de ameaça](./media/threat-detection/azure-threat-detection-fig10.jpg)

Os investigadores também recebem informações de inteligência de ameaça que são partilhadas entre os principais fornecedores de serviços na nuvem, e subscrevem feeds de inteligência de ameaças de terceiros. O Centro de Segurança do Azure pode utilizar estas informações para alertá-lo de ameaças de pessoas mal-intencionadas conhecidas. Alguns exemplos incluem:

-   **Aproveitando o poder da aprendizagem automática**: O Azure Security Center tem acesso a uma grande quantidade de dados sobre a atividade da rede em nuvem, que podem ser usados para detetar ameaças direcionadas para as suas implementações do Azure.

-   **Deteção de força bruta**: A aprendizagem automática é usada para criar um padrão histórico de tentativas de acesso remoto, que lhe permite detetar ataques de força bruta contra secure shell (SSH), Remote Desktop Protocol (RDP) e portas SQL.

-   **Deteção de DDoS e botnets** de saída : Um objetivo comum de ataques que visam recursos em nuvem é usar o poder de computação destes recursos para executar outros ataques.

-   **Novos servidores de análise comportamental e VMs**: Após o compromisso de um servidor ou máquina virtual, os atacantes empregam uma grande variedade de técnicas para executar código malicioso nesse sistema, evitando a deteção, garantindo a persistência e obviando os controlos de segurança.

-   **Deteção de ameaças de base de dados Azure SQL**: Deteção de ameaças para base de dados Azure SQL, que identifica atividades anómalas de bases de dados que indicam tentativas incomuns e potencialmente prejudiciais de aceder ou explorar bases de dados.

### <a name="behavioral-analytics"></a>Análise comportamental

A análise comportamental é uma técnica que analisa e compara os dados para uma coleção de padrões conhecidos. No entanto, estes padrões não são simples assinaturas. São determinados através de algoritmos complexos de machine learning aplicados a conjuntos de dados gigantescos.

![Resultados da análise comportamental](./media/threat-detection/azure-threat-detection-fig11.jpg)

Os padrões também são determinados através de uma análise cuidadosa de comportamentos maliciosos por analistas especialistas. O Azure Security Center pode usar análises comportamentais para identificar recursos comprometidos com base na análise de registos de máquinas virtuais, registos de dispositivos de rede virtuais, registos de tecidos, depósitos de falhas e outras fontes.

Além disso, os padrões estão correlacionados com outros sinais para verificar se há provas de apoio de uma campanha generalizada. Esta correlação ajuda a identificar eventos que são consistentes com indicadores estabelecidos de comprometimento.

Alguns exemplos incluem:
-   **Execução do processo suspeito**: os atacantes empregam várias técnicas para executar o software malicioso sem deteção. Por exemplo, um intruso pode dar ao malware os mesmos nomes que os ficheiros legítimos do sistema, mas colocar estes ficheiros numa localização alternativa, usar um nome semelhante ao de um ficheiro benigno, ou mascarar a verdadeira extensão do ficheiro. Os modelos do Centro de Segurança processam comportamentos e monitorizam execuções de processos para detetar outliers como estes.

-   **Tentativas ocultas de malware e exploração**: Malware sofisticado pode escapar aos produtos antimalware tradicionais, ou nunca escrevendo para o disco ou encriptando componentes de software armazenados em disco. No entanto, tal malware pode ser detetado através da análise de memória, porque o malware deve deixar vestígios na memória para funcionar. Quando o software falha, uma informação de falha de sistema captura uma parte da memória no momento da falha. Ao analisar a memória no depósito de falhas, o Azure Security Center pode detetar técnicas usadas para explorar vulnerabilidades em software, aceder a dados confidenciais e persistir subrepticiamente dentro de uma máquina comprometida sem afetar o desempenho da sua máquina.

-   **Movimento lateral e reconhecimento interno**: Para persistir numa rede comprometida e localizar e recolher dados valiosos, os atacantes tentam muitas vezes mover-se lateralmente da máquina comprometida para outros dentro da mesma rede. O Security Center monitoriza as atividades de processo e login para descobrir tentativas de expandir a posição de um intruso dentro da rede, tais como a execução remota de comandos, sondagem de rede e enumeração de conta.

-   **Scripts Malicious PowerShell**: PowerShell pode ser usado por atacantes para executar código malicioso em máquinas virtuais alvo para vários fins. O Centro da Segurança inspeciona a atividade do PowerShell quanto a provas de atividade suspeita.

-   **Ataques de saída**: os atacantes concentram-se muitas vezes nos recursos da nuvem com o objetivo de os utilizar para preparar ataques adicionais. Máquinas virtuais comprometidas, por exemplo, podem ser usadas para lançar ataques de força bruta contra outras máquinas virtuais, enviar spam ou digitalizar portas abertas e outros dispositivos na internet. Ao aplicar o machine learning ao tráfego de rede, o Centro de Segurança pode detetar quando as comunicações da rede de saída excedem o normal. Quando o spam é detetado, o Security Center também correlaciona tráfego de e-mail incomum com informações da Microsoft 365 para determinar se o correio é provavelmente nefasto ou o resultado de uma campanha de e-mail legítima.

### <a name="anomaly-detection"></a>Deteção de anomalias

O Centro de Segurança do Azure utiliza também a deteção de anomalias para identificar ameaças. Contrariamente à análise comportamental (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a deteção de anomalias é mais “personalizada” e concentra-se nas linhas de base específicas das suas implementações. O machine learning é aplicado para determinar a atividade normal para as suas implementações, e então as regras são geradas para definir condições mais distantes que podem representar um evento de segurança. Segue-se um exemplo:

-   **Ataques de força bruta de entrada RDP/SSH**: As suas implementações podem ter máquinas virtuais ocupadas com muitos logins todos os dias e outras máquinas virtuais que têm poucos, se houver, logins. O Azure Security Center pode determinar a atividade de login de base para estas máquinas virtuais e usar machine learning para definir em torno das atividades normais de login. Se houver alguma discrepância com a linha de base definida para as características relacionadas com o login, pode ser gerado um alerta. Novamente, o machine learning determina o que é significativo.

### <a name="continuous-threat-intelligence-monitoring"></a>Monitorização de informações sobre ameaças contínua

O Azure Security Center opera com equipas de investigação de segurança e de dados em todo o mundo que monitorizam continuamente as mudanças no panorama das ameaças. Isto inclui as seguintes iniciativas:

-   **Monitorização de informações por ameaça**: A inteligência de ameaças inclui mecanismos, indicadores, implicações e conselhos acccídeos sobre ameaças existentes ou emergentes. Esta informação é partilhada na comunidade de segurança, e a Microsoft monitoriza continuamente os feeds de inteligência de ameaças de fontes internas e externas.

-   **Partilha de sinais**: São partilhadas e analisadas informações de equipas de segurança em todo o vasto portfólio da Microsoft de serviços de cloud e no local, servidores e dispositivos de ponto final do cliente.

-   **Especialistas em segurança da Microsoft**: Envolvimento contínuo com equipas em toda a Microsoft que trabalham em áreas de segurança especializadas, tais como forenses e deteção de ataques web.

-   **Afinação de deteção**: Os algoritmos são executados contra conjuntos reais de dados do cliente, e os investigadores de segurança trabalham com os clientes para validar os resultados. Os verdadeiros e falsos positivos são utilizados para refinar os algoritmos do machine learning.

Estes esforços combinados culminam em novas e melhoradas deteções, das quais pode beneficiar instantaneamente. Não há ação para tomares.

## <a name="threat-protection-features-other-azure-services"></a>Características de proteção contra ameaças: Outros serviços Azure

### <a name="virtual-machines-microsoft-antimalware"></a>Máquinas virtuais: Antimalware da Microsoft

[O antimalware da Microsoft](antimalware.md) para o Azure é uma solução de agente único para aplicações e ambientes de inquilinos, projetado para funcionar em segundo plano sem intervenção humana. Pode implementar proteção com base nas necessidades das cargas de trabalho da sua aplicação, com configuração personalizada básica de segurança por defeito ou avançada, incluindo monitorização antimalware. O antimalware Azure é uma opção de segurança para máquinas virtuais Azure que é automaticamente instalada em todas as máquinas virtuais Azure PaaS.

#### <a name="microsoft-antimalware-core-features"></a>Funcionalidades do núcleo de antimalware da Microsoft

Aqui estão as funcionalidades do Azure que implementam e permitem o antimalware da Microsoft para as suas aplicações:

-   **Proteção em tempo real**: Monitoriza a atividade nos serviços na nuvem e em máquinas virtuais para detetar e bloquear a execução de malware.

-   **Digitalização programada**: Periodicamente executa a digitalização direcionada para detetar malware, incluindo programas de execução ativa.

-   **Remediação de malware**: Atua automaticamente em malware detetado, como eliminar ou quarentenaizar ficheiros maliciosos e limpar entradas de registo maliciosos.

-   **Atualizações de assinaturas**: Instala automaticamente as assinaturas de proteção mais recentes (definições de vírus) para garantir que a proteção está atualizada numa frequência pré-determinada.

-   **Atualizações do motor antimalware**: Atualiza automaticamente o motor antimalware da Microsoft.

-   **Atualizações da plataforma antimalware**: Atualiza automaticamente a plataforma antimalware da Microsoft.

-   **Proteção ativa**: Relata metadados de telemetria sobre ameaças detetadas e recursos suspeitos para o Microsoft Azure para garantir uma resposta rápida ao cenário de ameaças em evolução, permitindo a entrega de assinatura sincronizada em tempo real através do sistema de proteção ativa da Microsoft.

-   **Relatórios de amostras**: Fornece e reporta amostras ao serviço antimalware da Microsoft para ajudar a refinar o serviço e permitir a resolução de problemas.

-   **Exclusões**: Permite que os administradores de aplicações e serviços configurem certos ficheiros, processos e impulsos para exclusão da proteção e digitalização por desempenho e outros motivos.

-   **Coleção de eventos antimalware**: Regista a saúde do serviço antimalware, atividades suspeitas e ações de reparação tomadas no registo de eventos do sistema operativo e recolhe-as na conta de armazenamento Azure do cliente.

### <a name="azure-sql-database-threat-detection"></a>Deteção de ameaças de base de dados Azure SQL

[A Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) é uma nova funcionalidade de inteligência de segurança incorporada no serviço de base de dados Azure SQL. Trabalhando 24 horas por dia para aprender, perfilar e detetar atividades anómalas de bases de dados, a Azure SQL Database Threat Detection identifica potenciais ameaças à base de dados.

Os oficiais de segurança ou outros administradores designados podem receber uma notificação imediata sobre atividades de base de dados suspeitas à medida que ocorrem. Cada notificação fornece detalhes da atividade suspeita e recomenda como investigar e mitigar a ameaça.

Atualmente, a Azure SQL Database Threat Detection deteta potenciais vulnerabilidades e ataques de injeção de SQL e padrões de acesso a bases de dados anómalas.

Ao receber uma notificação de e-mail de deteção de ameaças, os utilizadores são capazes de navegar e ver os registos de auditoria relevantes através de um link profundo no correio. O link abre um espectador de auditoria ou um modelo de auditoria pré-configurado do Excel que mostra os registos de auditoria relevantes na altura do evento suspeito, de acordo com o seguinte:

-   Armazenamento de auditoria para a base de dados/servidor com as atividades anómalas da base de dados.

-   Tabela de armazenamento de auditoria relevante que foi usada no momento do evento para escrever o registo de auditoria.

-   Registos de auditoria da hora imediatamente a seguir à ocorrência do evento.

-   Registos de auditoria com identificação de evento semelhante no momento do evento (opcional para alguns detetores).

Os detetores de ameaças da base de dados SQL utilizam uma das seguintes metodologias de deteção:

-   **Deteção determinística**: Deteta padrões suspeitos (baseados em regras) nas consultas de clientes SQL que correspondam a ataques conhecidos. Esta metodologia tem alta deteção e baixa falsa cobertura, mas limitada, porque se enquadra na categoria de "deteções atómicas".

-   **Deteção comportamental**: Deteta atividade anómala, que é um comportamento anormal na base de dados que não foi visto durante os últimos 30 dias. Exemplos de atividade anómala do cliente SQL podem ser um pico de logins ou consultas falhadas, um grande volume de dados sendo extraídos, consultas canónicas incomuns, ou endereços IP desconhecidos usados para aceder à base de dados.

### <a name="application-gateway-web-application-firewall"></a>Application Gateway Web Application Firewall

[Web Application Firewall (WAF)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) é uma característica do [Azure Application Gateway](../../web-application-firewall/ag/ag-overview.md) que fornece proteção a aplicações web que usam um gateway de aplicação para funções padrão [de controlo de entrega de aplicações.](https://kemptechnologies.com/in/application-delivery-controllers) O Firewall de aplicações web faz isso protegendo-os contra a maioria das [10 vulnerabilidades comuns do Open Web Application Security Project (OWASP) no top 10 das vulnerabilidades comuns da web.](https://owasp.org/www-project-top-ten/)

![Diagrama de firewall de aplicação gateway web](./media/threat-detection/azure-threat-detection-fig13.png)

As proteções incluem:

-   Proteção contra injeção SQL.

-   Proteção de scripts de site transversal.

-   Proteção comum de ataques web, como injeção de comando, pedido HTTP contrabando, divisão de resposta HTTP e ataque de inclusão de ficheiros remotos.

-   Proteção contra violações do protocolo HTTP.

-   Proteção contra anomalias do protocolo HTTP, tais como falta de agente de utilizador do hospedeiro e aceita cabeçalhos.

-   Prevenção contra bots, rastejantes e scanners.

-   Deteção de configurações comuns de aplicações (isto é, Apache, IIS, e assim por diante).

Configurar a WAF no seu gateway de aplicações fornece os seguintes benefícios:

-   Protege a sua aplicação web contra vulnerabilidades e ataques da web sem modificar o código back-end.

-   Protege várias aplicações web ao mesmo tempo atrás de um gateway de aplicações. Um gateway de aplicações suporta hospedar até 20 websites.

-   Monitoriza as aplicações web contra ataques utilizando relatórios em tempo real que são gerados por registos WAF de gateway de aplicação.

-   Ajuda a cumprir os requisitos de conformidade. Certos controlos de conformidade exigem que todos os pontos finais virados para a Internet sejam protegidos por uma solução WAF.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>API de Deteção de Anomalias: Construído com aprendizagem de máquinas Azure

A API de Deteção de Anomalias é uma API que é útil para detetar uma variedade de padrões anómalos nos dados da sua série de tempo. A API atribui uma pontuação de anomalia a cada ponto de dados da série de tempo, que pode ser usada para gerar alertas, monitorizar através de dashboards ou ligar-se aos seus sistemas de bilhética.

A [API de Deteção de Anomalias](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) pode detetar os seguintes tipos de anomalias nos dados da série de tempo:

-   **Picos e mergulhos**: Quando estiver a monitorizar o número de falhas de login num serviço ou num número de check-outs num site de e-commerce, picos ou mergulhos incomuns podem indicar ataques de segurança ou interrupções de serviço.

-   **Tendências positivas e negativas**: Quando está a monitorizar o uso da memória na computação, a redução do tamanho da memória livre indica uma possível fuga de memória. Para a monitorização do comprimento da fila de serviço, uma tendência persistente de subida pode indicar um problema de software subjacente.

-   **Alterações de nível e alterações na gama dinâmica de valores**: As alterações de nível nas latências de um serviço após uma atualização de serviço ou níveis mais baixos de exceções após a atualização podem ser interessantes de monitorizar.

A API baseada em aprendizagem automática permite:

-   **Deteção flexível e robusta**: Os modelos de deteção de anomalias permitem aos utilizadores configurar definições de sensibilidade e detetar anomalias entre conjuntos de dados sazonais e não sazonais. Os utilizadores podem ajustar o modelo de deteção de anomalias para tornar a API de deteção menos ou mais sensível de acordo com as suas necessidades. Isto significaria detetar as anomalias menos ou mais visíveis nos dados com e sem padrões sazonais.

-   **Deteção escalável e oportuna**: A forma tradicional de monitorização com os limiares atuais definidos pelos conhecimentos de domínio dos peritos é dispendiosa e não escalável para milhões de conjuntos de dados que mudam de forma dinâmica. Os modelos de deteção de anomalias nesta API são aprendidos, e os modelos são sintonizados automaticamente a partir de dados históricos e em tempo real.

-   **Deteção proactiva e accível:** A deteção de tendências lentas e de mudança de nível pode ser aplicada para deteção precoce de anomalias. Os primeiros sinais anormais detetados podem ser usados para direcionar os seres humanos para investigar e agir nas áreas problemáticas. Além disso, os modelos de análise de causa de raiz e ferramentas de alerta podem ser desenvolvidos em cima deste serviço de API de deteção de anomalias.

A API de deteção de anomalias é uma solução eficaz e eficiente para uma vasta gama de cenários, tais como a saúde do serviço e a monitorização do KPI, ioT, monitorização de desempenho e monitorização do tráfego de rede. Aqui estão alguns cenários populares onde esta API pode ser útil:

- Os departamentos de TI precisam de ferramentas para rastrear eventos, código de erro, registo de utilização e desempenho (CPU, memória, e assim por diante) em tempo útil.

-   Os sites de comércio online querem acompanhar as atividades do cliente, visualizações de páginas, cliques e assim por diante.

-   As empresas de serviços públicos querem acompanhar o consumo de água, gás, eletricidade e outros recursos.

-   As instalações ou os serviços de gestão de edifícios querem monitorizar a temperatura, humidade, tráfego, e assim por diante.

-   IoT/fabricantes querem usar dados de sensores em séries temporquim para monitorizar o fluxo de trabalho, a qualidade, e assim por diante.

-   Os prestadores de serviços, como call centers, precisam monitorizar a tendência da procura de serviços, o volume de incidentes, o comprimento da fila de espera, e assim por diante.

-   Os grupos de análise de negócios querem monitorizar o movimento anormal de KPI's de negócios (como volume de vendas, sentimentos de clientes ou preços) em tempo real.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](/cloud-app-security/what-is-cloud-app-security) é um componente crítico da pilha de Segurança da Cloud Microsoft. É uma solução abrangente que pode ajudar a sua organização à medida que se move para tirar o máximo partido da promessa de aplicações em nuvem. Mantém-te no controlo, através de uma maior visibilidade na atividade. Também aumenta a proteção dos dados vitais nas várias aplicações em cloud.

Com ferramentas para ajudar a desvendar Shadow IT, avaliar o risco, aplicar políticas, investigar atividades e parar ameaças, a sua organização pode migrar com mais segurança para a cloud sem perder o controlo dos dados vitais.

| Categoria | Descrição |
| -------- | ----------- |
| Detetar | Desvende o TI sombra com o Cloud App Security. Obtenha visibilidade ao detetar aplicações, atividades, utilizadores, dados e ficheiros no seu ambiente na cloud. Detete aplicações de terceiros que estão ligadas à sua cloud.|
|Investigar | Investigue as suas aplicações na cloud com ferramentas forenses de cloud para ajudar a obter informações detalhadas sobre aplicações arriscadas, utilizadores e ficheiros específicos na sua rede. Descubra padrões nos dados recolhidos a partir da cloud. Gere relatórios para monitorizar a sua cloud. |
| Controlar | Reduza o risco através da definição de políticas e alertas para obter o máximo controlo sobre o tráfego de rede de cloud. Utilize o Cloud App Security para migrar os seus utilizadores para aplicações em cloud alternativas seguras e aprovadas. |
| Proteger | Use a Cloud App Security para sancionar ou proibir aplicações, impor a prevenção de perdas de dados, controlar permissões e partilhas e gerar relatórios e alertas personalizados. |
| Controlar | Reduza o risco através da definição de políticas e alertas para obter o máximo controlo sobre o tráfego de rede de cloud. Utilize o Cloud App Security para migrar os seus utilizadores para aplicações em cloud alternativas seguras e aprovadas. |


![Diagrama de segurança de aplicativos em nuvem](./media/threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integra visibilidade com a sua nuvem por:

-   Usando o Cloud Discovery para mapear e identificar o seu ambiente em nuvem e as aplicações na nuvem que a sua organização está a usar.

-   Sancionar e proibir aplicativos na sua nuvem.

-   Utilizando conectores de aplicação fáceis de implementar que tirem partido das APIs do fornecedor, para visibilidade e governação das aplicações a que se conecta.

-   Ajudando-o a ter controlo contínuo, definindo e, em seguida, continuamente afinando as políticas.

Ao recolher dados destas fontes, a Cloud App Security executa uma análise sofisticada dos mesmos. Alerta-o imediatamente de atividades anómalas e dá-lhe mais visibilidade para o seu ambiente em cloud. Pode configurar uma política no Cloud App Security e utilizá-la para proteger tudo no seu ambiente em cloud.

## <a name="third-party-threat-protection-capabilities-through-the-azure-marketplace"></a>Capacidades de proteção contra ameaças de terceiros através do Azure Marketplace

### <a name="web-application-firewall"></a>Firewall de Aplicações Web

Web Application Firewall inspeciona o tráfego web de entrada e bloqueia injeções DE SQL, scripts de sites cruzados, uploads de malware, ataques DDoS de aplicações e outros ataques direcionados para as suas aplicações web. Também inspeciona as respostas dos servidores web de back-end para a prevenção da perda de dados (DLP). O motor integrado de controlo de acesso permite aos administradores criar políticas de controlo de acesso granular para a autenticação, autorização e contabilidade (AAA), o que confere às organizações uma forte autenticação e controlo do utilizador.

Web Application Firewall fornece os seguintes benefícios:

-   Deteta e bloqueia injeções DE SQL, Scripting cross-site, uploads de malware, DDoS de aplicação ou quaisquer outros ataques contra a sua aplicação.

-   Controlo de autenticação e acesso.

-   Verifica o tráfego de saída para detetar dados sensíveis e pode mascarar ou impedir que a informação seja divulgada.

-   Acelera a entrega de conteúdos de aplicações web, utilizando capacidades como caching, compressão e outras otimizações de tráfego.

Para exemplos de firewalls de aplicações web que estão disponíveis no Azure Marketplace, consulte [Barracuda WAF, Brocade virtual web application firewall (vWAF), Imperva SecureSphere, e a firewall IP ThreatSTOP](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Passos seguintes

- [Respondendo às ameaças de hoje](../../security-center/security-center-alerts-overview.md#respond-threats): Ajuda a identificar ameaças ativas que visam os seus recursos Azure e fornece as informações necessárias para responder rapidamente.

- [Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): Ajuda a resolver as suas preocupações sobre potenciais ameaças às suas bases de dados.