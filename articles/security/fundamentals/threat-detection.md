---
title: Deteção avançada de ameaças do Azure Microsoft Docs
description: Conheça a Proteção de Identidade Azure AD e as suas capacidades.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981469"
---
# <a name="azure-advanced-threat-detection"></a>Deteção avançada de ameaças do Azure

O Azure oferece funcionalidades avançadas de deteção de ameaças através de serviços como o Azure Ative Directory (Azure AD), os registos do Azure Monitor e o Azure Security Center. Esta coleção de serviços e capacidades de segurança fornece uma forma simples e rápida de entender o que está a acontecer dentro das suas implementações azure.

O Azure fornece um vasto leque de opções para configurar e personalizar a segurança para satisfazer os requisitos das implementações da sua aplicação. Este artigo discute como satisfazer estes requisitos.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[A Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) é uma funcionalidade de edição [Azure Ative Directory Premium P2](../../active-directory/active-directory-whatis.md) que fornece uma visão geral das deteções de risco e potenciais vulnerabilidades que podem afetar as identidades da sua organização. A Proteção de Identidade utiliza as capacidades de deteção de anomalias da AD Azure existentes que estão disponíveis através de Relatórios de [Atividade AD AD](../../active-directory/active-directory-reporting-azure-portal.md)Do Azure, e introduz novos tipos de deteção de risco que podem detetar anomalias em tempo real.

![Diagrama de Proteção de Identidade Azure AD](./media/threat-detection/azure-threat-detection-fig1.png)

A Identity Protection utiliza algoritmos adaptáveis de aprendizagem automática e heurística para detetar anomalias e deteções de risco que possam indicar que uma identidade foi comprometida. Utilizando estes dados, a Proteção de Identidade gera relatórios e alertas para que possa investigar estas deteções de risco e tomar medidas adequadas de reparação ou mitigação.

A Proteção de Identidade do Diretório Ativo Azure é mais do que uma ferramenta de monitorização e reporte. Com base em deteções de risco, a Proteção de Identidade calcula um nível de risco do utilizador para cada utilizador, para que possa configurar políticas baseadas em riscos para proteger automaticamente as identidades da sua organização.

Estas políticas baseadas no risco, para além de outros [controlos](../../active-directory/active-directory-conditional-access-azure-portal.md) de Acesso Condicional fornecidos pelo Azure Ative Directory e [pela EMS,](../../active-directory/active-directory-conditional-access-azure-portal.md)podem bloquear ou oferecer automaticamente ações de remediação adaptativas que incluam resets de palavras-passe e aplicação da autenticação de vários fatores.

### <a name="identity-protection-capabilities"></a>Capacidades de Proteção de Identidade

A Proteção de Identidade do Diretório Ativo Azure é mais do que uma ferramenta de monitorização e reporte. Para proteger as identidades da sua organização, pode configurar políticas baseadas no risco que respondem automaticamente a problemas detetados quando um nível de risco especificado foi atingido. Estas políticas, para além de outros controlos de acesso condicional fornecidos pelo Azure Ative Directory e pela EMS, podem bloquear ou iniciar automaticamente ações de reparação adaptativas, incluindo resets de palavras-passe e aplicação de autenticação de vários fatores.

Exemplos de algumas das formas como a Proteção de Identidade Azure pode ajudar a garantir as suas contas e identidades incluem:

[Deteção de deteções de risco e contas de risco](../../active-directory/identity-protection/overview.md)
-   Detete seis tipos de deteção de risco utilizando machine learning e regras heurísticas.
-   Calcular os níveis de risco do utilizador.
-   Forneça recomendações personalizadas para melhorar a postura de segurança global, destacando vulnerabilidades.

[Investigar deteções de risco](../../active-directory/identity-protection/overview.md)
-   Envie notificações para deteções de risco.
-   Investigar deteções de risco utilizando informações relevantes e contextuais.
-   Fornecer fluxos básicos de trabalho para acompanhar as investigações.
-   Proporcionar fácil acesso a ações de reparação, tais como reset de palavra-passe.

[Políticas de acesso condicional e baseadas no risco](../../active-directory/identity-protection/overview.md)
-   Mitigar inscrições arriscadas bloqueando inscrições ou exigindo desafios de autenticação de vários fatores.
-   Bloqueie ou proteja as contas de utilizador arriscadas.
-   Exigir que os utilizadores se registem para autenticação de vários fatores.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

Com a [Azure Ative Directory Privileged Identity Management (PIM),](../../active-directory/privileged-identity-management/pim-configure.md)pode gerir, controlar e monitorizar o acesso dentro da sua organização. Esta funcionalidade inclui acesso a recursos em Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

![Diagrama de Gestão de Identidade Privilegiada azure AD](./media/threat-detection/azure-threat-detection-fig2.png)

PiM ajuda-o:

-   Receba alertas e relatórios sobre administradores da AD Azure e acesso administrativo just-in-time (JIT) aos serviços online da Microsoft, como o Office 365 e o Intune.

-   Obtenha relatórios sobre o histórico de acesso do administrador e alterações nas atribuições de administradores.

-   Receba alertas sobre o acesso a um papel privilegiado.

## <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Os registos do Azure Monitor](../../azure-monitor/index.yml) são uma solução de gestão de TI baseada na nuvem da Microsoft que o ajuda a gerir e proteger as suas infraestruturas no local e na nuvem. Como os registos do Azure Monitor são implementados como um serviço baseado na nuvem, você pode tê-lo em funcionamento rapidamente com o mínimo de investimento em serviços de infraestrutura. As novas funcionalidades de segurança são entregues automaticamente, poupando custos de manutenção e atualização em curso.

Além de fornecer serviços valiosos por si só, os registos do Azure Monitor podem integrar-se com componentes do System Center, como [o System Center Operations Manager,](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/)para alargar os seus investimentos de gestão de segurança existentes na nuvem. Os registos do System Center e do Azure Monitor podem trabalhar em conjunto para proporcionar uma experiência de gestão híbrida completa.

### <a name="holistic-security-and-compliance-posture"></a>Postura holística de segurança e conformidade

O [dashboard de Segurança e Auditoria log analytics](../../security-center/security-center-intro.md) fornece uma visão abrangente da postura de segurança de TI da sua organização, com consultas de pesquisa incorporadas para questões notáveis que requerem a sua atenção. O dashboard de Segurança e Auditoria é o ecrã principal de tudo o que está relacionado com a segurança nos registos do Monitor Azure. Fornece informações de alto nível sobre o estado de segurança dos computadores. Você também pode ver todos os eventos das últimas 24 horas, 7 dias, ou qualquer outro prazo personalizado.

Os registos do Azure Monitor ajudam-no a compreender de forma rápida e fácil a postura de segurança global de qualquer ambiente, tudo no contexto das Operações de TI, incluindo avaliação de atualização de software, avaliação antimalware e linhas de base de configuração. Os dados do registo de segurança são facilmente acessíveis para simplificar os processos de auditoria de segurança e conformidade.

![O dashboard de segurança e auditoria de Log Analytics](./media/threat-detection/azure-threat-detection-fig3.jpg)

O dashboard de Segurança e Auditoria log analytics está organizado em quatro categorias principais:

-   **Domínios de Segurança**: Permite-lhe explorar ainda mais os registos de segurança ao longo do tempo; aceder a avaliações de malware; avaliações de atualização; ver informações sobre segurança, identidade e acesso da rede; ver computadores com eventos de segurança; e aceda rapidamente ao painel do Centro de Segurança Azure.

-   **Questões Notáveis**: Permite-lhe identificar rapidamente o número de questões ativas e a gravidade das questões.

-   **Deteções (Pré-visualização)**: Permite identificar padrões de ataque exibindo alertas de segurança à medida que ocorrem contra os seus recursos.

-   **Inteligência de Ameaça**: Permite identificar padrões de ataque exibindo o número total de servidores com tráfego IP malicioso de saída, o tipo de ameaça maliciosa e um mapa das localizações dos IPs.

-   Consultas de **segurança comuns**: Lista as consultas de segurança mais comuns que pode utilizar para monitorizar o seu ambiente. Quando selecionar qualquer consulta, o painel de pesquisa abre e exibe os resultados para essa consulta.

### <a name="insight-and-analytics"></a>Insight e análise
No centro dos registos do [Monitor Azure](../../log-analytics/log-analytics-queries.md) está o repositório, que é hospedado pelo Azure.

![Diagrama de insights e análises](./media/threat-detection/azure-threat-detection-fig4.png)

Recolhe dados no repositório a partir de fontes conectadas, configurando fontes de dados e adicionando soluções à sua subscrição.

![O painel de logs do Monitor Azure](./media/threat-detection/azure-threat-detection-fig5.png)

Fontes e soluções de dados criam tipos de registo separados com o seu próprio conjunto de propriedades, mas ainda pode analisá-los juntos em consultas ao repositório. Pode utilizar as mesmas ferramentas e métodos para trabalhar com uma variedade de dados que são recolhidos por várias fontes.


A maior parte da sua interação com os registos do Monitor Azur é através do portal Azure, que funciona em qualquer navegador e lhe fornece acesso a configurações de configuração e múltiplas ferramentas para analisar e atuar em dados recolhidos. A partir do portal, pode usar:
* [Faça log pesquisas](../../log-analytics/log-analytics-queries.md) onde constrói consultas para analisar dados recolhidos.
* [Dashboards](../../azure-monitor/learn/tutorial-logs-dashboards.md), que pode personalizar com vistas gráficas das suas pesquisas mais valiosas.
* [Soluções](../../monitoring/monitoring-solutions.md), que fornecem ferramentas adicionais de funcionalidade e análise.

![Ferramentas de análise](./media/threat-detection/azure-threat-detection-fig6.png)

As soluções adicionam funcionalidade aos registos do Monitor Azure. Eles correm principalmente na nuvem e fornecem a análise de dados que são recolhidos no repositório de análise de registo. As soluções também podem definir novos tipos de registo a serem recolhidos que podem ser analisados com pesquisas de registo ou utilizando uma interface adicional de utilizador que a solução fornece no painel de análise de registo.

O dashboard de Segurança e Auditoria é um exemplo deste tipo de soluções.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automação e controlo: Alerta sobre derivas de configuração de segurança

A Azure Automation automatiza processos administrativos com livros de execução baseados na PowerShell e funcionam na nuvem. Os runbooks podem ser também executados no seu centro de dados local para gerir recursos locais. A Azure Automation fornece uma gestão de configuração com a Configuração do Estado Desejado pela PowerShell (DSC).

![Diagrama de Automação Azure](./media/threat-detection/azure-threat-detection-fig7.png)

Você pode criar e gerir recursos DSC que estão hospedados em Azure e aplicá-los em sistemas de nuvem e no local. Ao fazê-lo, pode definir e aplicar automaticamente a sua configuração ou obter relatórios sobre deriva para ajudar a garantir que as configurações de segurança permanecem dentro da política.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

O Azure Security Center ajuda a proteger os seus recursos Azure. Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições do Azure. Dentro do serviço, pode definir polícias contra as suas assinaturas Azure e [grupos de recursos](../../azure-resource-manager/management/manage-resources-portal.md) para maior granularidade.

![Diagrama do Centro de Segurança Azure](./media/threat-detection/azure-threat-detection-fig8.png)

Os investigadores de segurança da Microsoft estão constantemente atentos a ameaças. Estes têm acesso a um conjunto amplo de telemetria obtida através da presença global da Microsoft na nuvem e no local. Esta coleção abrangente e diversificada de conjuntos de dados permite à Microsoft descobrir novos padrões de ataque e tendências dos seus produtos de consumidor e empresariais no local, assim como os seus serviços online.

Assim, o Security Center pode atualizar rapidamente os seus algoritmos de deteção à medida que os atacantes lançam novas e cada vez mais sofisticadas explorações. Esta abordagem ajuda-o a manter o ritmo com um ambiente de ameaça em movimento rápido.

![Deteção de ameaças do Centro de Segurança](./media/threat-detection/azure-threat-detection-fig9.jpg)

A deteção de ameaças do Centro de Segurança funciona através da recolha automática de informações de segurança a partir dos seus recursos do Azure, da rede e das soluções de parceiros ligadas. Analisa esta informação, correlando informações de várias fontes, para identificar ameaças.

Os alertas de segurança são prioritários no Centro de Segurança, juntamente com recomendações sobre como remediar a ameaça.

O Centro de Segurança emprega análises de segurança avançadas, que ultrapassam as abordagens baseadas na assinatura. Descobertas em tecnologias de big data e [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) são usadas para avaliar eventos em todo o tecido nuvem. Análises avançadas podem detetar ameaças que seriam impossíveis de identificar através de abordagens manuais e prever a evolução dos ataques. Estes tipos de análise de segurança estão cobertos nas próximas secções.

### <a name="threat-intelligence"></a>Informações sobre ameaças

A Microsoft tem acesso a uma enorme quantidade de inteligência global de ameaças.

A telemetria flui de várias fontes, tais como Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, a Microsoft Digital Crimes Unit (DCU) e o Microsoft Security Response Center (MSRC).

![Descobertas de inteligência de ameaça](./media/threat-detection/azure-threat-detection-fig10.jpg)

Os investigadores também recebem informações de inteligência de ameaças que são partilhadas entre os principais fornecedores de serviços na nuvem, e subscrevem feeds de inteligência de ameaças de terceiros. O Centro de Segurança do Azure pode utilizar estas informações para alertá-lo de ameaças de pessoas mal-intencionadas conhecidas. Alguns exemplos incluem:

-   **Aproveitando o poder do machine learning**: O Azure Security Center tem acesso a uma grande quantidade de dados sobre a atividade da rede cloud, que podem ser usados para detetar ameaças direcionadas às suas implementações Azure.

-   **Deteção de força bruta**: A aprendizagem automática é usada para criar um padrão histórico de tentativas de acesso remoto, o que lhe permite detetar ataques de força bruta contra portas Secure Shell (SSH), Remote Desktop Protocol (RDP) e SQL.

-   **DDoS de saída e deteção**de botnets : Um objetivo comum de ataques que visam os recursos da nuvem é usar o poder computacional destes recursos para executar outros ataques.

-   **Novos servidores de análise comportamental e VMs**: Depois de um servidor ou máquina virtual estar comprometido, os atacantes empregam uma grande variedade de técnicas para executar código malicioso nesse sistema, evitando a deteção, garantindo a persistência e obviando os controlos de segurança.

-   Deteção de ameaças de base de **dados Azure SQL**: Deteção de ameaças para base de dados Azure SQL, que identifica atividades anómalas de base de dados que indicam tentativas incomuns e potencialmente prejudiciais de acesso ou exploração de bases de dados.

### <a name="behavioral-analytics"></a>Análise comportamental

A análise comportamental é uma técnica que analisa e compara os dados para uma coleção de padrões conhecidos. No entanto, estes padrões não são simples assinaturas. São determinados através de algoritmos complexos de machine learning aplicados a conjuntos de dados gigantescos.

![Resultados de análise comportamental](./media/threat-detection/azure-threat-detection-fig11.jpg)

Os padrões também são determinados através de uma análise cuidadosa de comportamentos maliciosos por analistas especializados. O Azure Security Center pode usar análises comportamentais para identificar recursos comprometidos com base na análise de registos de máquinas virtuais, registos de dispositivos de rede virtual, troncos de tecido, lixeiras de colisão e outras fontes.

Além disso, os padrões estão correlacionados com outros sinais para verificar se há provas de apoio de uma campanha generalizada. Esta correlação ajuda a identificar eventos que são consistentes com indicadores estabelecidos de comprometimento.

Alguns exemplos incluem:
-   **Execução do processo suspeito**: os atacantes empregam várias técnicas para executar o software malicioso sem deteção. Por exemplo, um intruso pode dar ao malware os mesmos nomes que os ficheiros legítimos do sistema, mas colocar estes ficheiros num local alternativo, usar um nome semelhante ao de um ficheiro benigno ou mascarar a verdadeira extensão do ficheiro. Os modelos do Security Center processam comportamentos e monitorizam execuções de processos para detetar execuções mais distantes como estes.

-   **Tentativas ocultas**de malware e exploração : Malware sofisticado pode escapar aos produtos antimalware tradicionais, nunca escrevendo para disco ou encriptando componentes de software armazenados em disco. No entanto, este malware pode ser detetado através da análise da memória, porque o malware deve deixar vestígios na memória para funcionar. Quando o software falha, uma informação de falha de sistema captura uma parte da memória no momento da falha. Ao analisar a memória no depósito de colisão, o Azure Security Center pode detetar técnicas usadas para explorar vulnerabilidades em software, aceder a dados confidenciais e persistir subrepticiamente dentro de uma máquina comprometida sem afetar o desempenho da sua máquina.

-   **Movimento lateral e reconhecimento interno**: Para persistir numa rede comprometida e localizar e recolher dados valiosos, os atacantes muitas vezes tentam mover-se lateralmente da máquina comprometida para outros dentro da mesma rede. O Security Center monitoriza o processo e as atividades de login para descobrir tentativas de expandir a posição de um intruso dentro da rede, tais como execução de comando remoto, sondagem de rede e enumeração de conta.

-   **Scripts PowerShell maliciosos**: PowerShell pode ser usado por atacantes para executar código malicioso em máquinas virtuais alvo para vários fins. O Centro da Segurança inspeciona a atividade do PowerShell quanto a provas de atividade suspeita.

-   **Ataques de saída**: os atacantes concentram-se muitas vezes nos recursos da nuvem com o objetivo de os utilizar para preparar ataques adicionais. Máquinas virtuais comprometidas, por exemplo, podem ser usadas para lançar ataques de força bruta contra outras máquinas virtuais, enviar spam ou digitalizar portas abertas e outros dispositivos na internet. Ao aplicar o machine learning ao tráfego de rede, o Centro de Segurança pode detetar quando as comunicações da rede de saída excedem o normal. Quando o spam é detetado, o Security Center também correlaciona o tráfego de e-mail incomum com a inteligência do Office 365 para determinar se o correio é provavelmente nefasto ou o resultado de uma campanha legítima de e-mail.

### <a name="anomaly-detection"></a>Deteção de anomalias

O Centro de Segurança do Azure utiliza também a deteção de anomalias para identificar ameaças. Contrariamente à análise comportamental (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a deteção de anomalias é mais “personalizada” e concentra-se nas linhas de base específicas das suas implementações. A aprendizagem automática é aplicada para determinar a atividade normal para as suas implementações, e em seguida, são geradas regras para definir condições mais distantes que podem representar um evento de segurança. Segue-se um exemplo:

-   Ataques de **força bruta RDP/SSH de entrada**: As suas implantações podem ter máquinas virtuais ocupadas com muitos logins todos os dias e outras máquinas virtuais que têm poucos, se houver, logins. O Azure Security Center pode determinar a atividade de login de base para estas máquinas virtuais e usar machine learning para definir em torno das atividades normais de login. Se houver alguma discrepância com a linha de base definida para as características relacionadas com o login, pode ser gerado um alerta. Novamente, o machine learning determina o que é significativo.

### <a name="continuous-threat-intelligence-monitoring"></a>Monitorização de informações sobre ameaças contínua

O Azure Security Center opera com equipas de pesquisa de segurança e ciência de dados em todo o mundo que monitorizam continuamente as mudanças no panorama das ameaças. Isto inclui as seguintes iniciativas:

-   **Monitorização da inteligência de ameaças**: A inteligência das ameaças inclui mecanismos, indicadores, implicações e conselhos atuais sobre ameaças existentes ou emergentes. Esta informação é partilhada na comunidade de segurança, e a Microsoft monitoriza continuamente os feeds de inteligência de ameaças de fontes internas e externas.

-   **Partilha de sinais**: São partilhadas e analisadas as informações das equipas de segurança através do vasto portfólio da Microsoft de serviços, servidores e dispositivos de ponto final de cliente.

-   **Especialistas em segurança**da Microsoft : Envolvimento contínuo com equipas em toda a Microsoft que trabalham em campos de segurança especializados, tais como forenses e deteção de ataques web.

-   **Afinação de deteção**: Os algoritmos são executados contra conjuntos reais de dados de clientes, e os investigadores de segurança trabalham com os clientes para validar os resultados. Os verdadeiros e falsos positivos são utilizados para refinar os algoritmos do machine learning.

Estes esforços combinados culminam em novas e melhoradas deteções, das quais pode beneficiar instantaneamente. Não há ação a tomar.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Funcionalidades avançadas de deteção de ameaças: Outros serviços Azure

### <a name="virtual-machines-microsoft-antimalware"></a>Máquinas virtuais: Antimalware da Microsoft

O [antimalware](antimalware.md) da Microsoft para o Azure é uma solução de agente único para aplicações e ambientes de inquilinos, projetado para funcionar em segundo plano sem intervenção humana. Pode implementar proteção com base nas necessidades das cargas de trabalho da sua aplicação, com configuração personalizada básica de segurança por padrão ou avançada, incluindo monitorização antimalware. O antimalware Azure é uma opção de segurança para máquinas virtuais Azure que é instalada automaticamente em todas as máquinas virtuais Azure PaaS.

#### <a name="microsoft-antimalware-core-features"></a>Funcionalidades centrais antimalware da Microsoft

Estas são as funcionalidades do Azure que implementam e permitem o antimalware da Microsoft para as suas aplicações:

-   **Proteção em tempo real**: Monitoriza a atividade em serviços na nuvem e em máquinas virtuais para detetar e bloquear a execução de malware.

-   **Digitalização programada**: Periodicamente realiza digitalização direcionada para detetar malware, incluindo programas de execução ativa.

-   **Reparação de malware**: Atua automaticamente em malware detetado, como eliminar ou pôr em quarentena ficheiros maliciosos e limpar entradas de registo maliciosas.

-   **Atualizações**de assinaturas : Instala automaticamente as mais recentes assinaturas de proteção (definições de vírus) para garantir que a proteção está atualizada numa frequência pré-determinada.

-   **Atualizações do motor antimalware**: Atualiza automaticamente o Motor Antimalware da Microsoft.

-   **Atualizações**da plataforma antimalware : Atualiza automaticamente a plataforma antimalware da Microsoft.

-   **Proteção ativa**: Relatórios de metadados de telemetria sobre ameaças detetadas e recursos suspeitos para o Microsoft Azure para garantir uma resposta rápida ao cenário de ameaça em evolução, permitindo a entrega de assinaturas sincronizadas em tempo real através do sistema de proteção ativa da Microsoft.

-   **Relatórios de amostras**: Fornece e reporta amostras ao serviço antimalware da Microsoft para ajudar a refinar o serviço e permitir a resolução de problemas.

-   **Exclusões**: Permite que os administradores de aplicação e de serviço configurem certos ficheiros, processos e unidades para exclusão da proteção e digitalização por razões de desempenho e outros motivos.

-   Recolha de **eventos antimalware**: Regista as ações de saúde do serviço antimalware, atividades suspeitas e remediação tomadas no registo de eventos do sistema operativo e recolhe-as na conta de armazenamento azure do cliente.

### <a name="azure-sql-database-threat-detection"></a>Deteção de ameaças de base de dados Azure SQL

A Deteção de Ameaças de Base de [Dados Azure SQL](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) é uma nova funcionalidade de inteligência de segurança incorporada no serviço de base de dados Azure SQL. Trabalhando 24 horas por dia para aprender, perfil e detetar atividades anómalas de base de dados, a Deteção de Ameaças de Base de Dados Azure SQL identifica potenciais ameaças à base de dados.

Os oficiais de segurança ou outros administradores designados podem receber uma notificação imediata sobre atividades suspeitas de bases de dados à medida que ocorrem. Cada notificação fornece detalhes sobre a atividade suspeita e recomenda como investigar e mitigar a ameaça.

Atualmente, a Deteção de Ameaças de Base de Dados Azure SQL deteta potenciais vulnerabilidades e ataques de injeção SQL e padrões anómalos de acesso à base de dados.

Ao receber uma notificação de e-mail de deteção de ameaças, os utilizadores são capazes de navegar e ver os registos de auditoria relevantes através de um link profundo no correio. O link abre um espectador de auditoria ou um modelo de Excel de auditoria preconfigurado que mostra os registos de auditoria relevantes na altura do evento suspeito, de acordo com o seguinte:

-   Armazenamento de auditoria para a base de dados/servidor com as atividades anómalas da base de dados.

-   Quadro de armazenamento de auditoria relevante que foi usado no momento do evento para escrever o registo de auditoria.

-   Registos de auditoria da hora imediatamente a seguir à ocorrência do evento.

-   Registos de auditoria com um ID de evento semelhante no momento do evento (opcional para alguns detetores).

Os detetores de ameaças da Base de Dados SQL utilizam uma das seguintes metodologias de deteção:

-   **Deteção determinística**: Deteta padrões suspeitos (regras baseadas) nas consultas de clientes SQL que coincidem com os ataques conhecidos. Esta metodologia tem alta deteção e baixa cobertura falsa, mas limitada, porque se enquadra na categoria de "deteções atómicas".

-   **Deteção comportamental**: Deteta atividade anómala, que é um comportamento anormal na base de dados que não foi visto nos últimos 30 dias. Exemplos de atividade anómala do cliente SQL podem ser um pico de logins ou consultas falhadas, um grande volume de dados a ser extraído, consultas canónicas incomuns ou endereços IP desconhecidos usados para aceder à base de dados.

### <a name="application-gateway-web-application-firewall"></a>Application Gateway Web Application Firewall

[Firewall de aplicação web (WAF)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) é uma característica do Portal de [Aplicações Azure](../../application-gateway/application-gateway-web-application-firewall-overview.md) que fornece proteção às aplicações web que utilizam um gateway de aplicação para funções padrão de controlo de [aplicação.](https://kemptechnologies.com/in/application-delivery-controllers) A Firewall de aplicação web faz isso protegendo-os contra a maioria do Projeto de Segurança de [Aplicações Abertas web (OWASP) top 10 vulnerabilidades comuns web](https://www.owasp.org/index.php/Top_10_2010-Main).

![Diagrama de firewall de aplicação web de aplicação de gateway de aplicação de aplicação](./media/threat-detection/azure-threat-detection-fig13.png)

As proteções incluem:

-   Proteção contra injeção SQL.

-   Proteção de scripts de cross site.

-   Proteção de Ataques Web Comuns, tais como injeção de comando, http solicitar contrabando, divisão de resposta HTTP e ataque de inclusão de ficheiros remotos.

-   Proteção contra violações do protocolo HTTP.

-   Proteção contra anomalias do protocolo HTTP, tais como agente-utilizador hospedeiro desaparecido e cabeçalhos aceite.

-   Prevenção contra bots, rastejantes e scanners.

-   Deteção de configurações de aplicações comuns (isto é, Apache, IIS, e assim por diante).

Configurar o WAF na sua porta de entrada de aplicações fornece os seguintes benefícios:

-   Protege a sua aplicação web de vulnerabilidades e ataques web sem modificação do código de back-end.

-   Protege várias aplicações web ao mesmo tempo atrás de um gateway de aplicação. Um gateway de aplicação suporta hospedagem até 20 websites.

-   Monitoriza aplicações web contra ataques utilizando relatórios em tempo real que são gerados por registos WAF de gateway de aplicação.

-   Ajuda a cumprir os requisitos de conformidade. Certos controlos de conformidade exigem que todos os pontos finais virados para a Internet sejam protegidos por uma solução WAF.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>API de deteção de anomalias: Construído com Aprendizagem automática Azure

A API de Deteção de Anomalias é uma API que é útil para detetar uma variedade de padrões anómalos nos dados da série de tempo. A API atribui uma pontuação de anomalia a cada ponto de dados da série de tempo, que pode ser usada para gerar alertas, monitorização através de dashboards ou ligação com os seus sistemas de bilhética.

A API de deteção de [anomalias](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) pode detetar os seguintes tipos de anomalias nos dados da série time:

-   **Picos e mergulhos**: Quando estiver a monitorizar o número de falhas de login num serviço ou número de check-outs num site de e-commerce, picos ou mergulhos incomuns podem indicar ataques de segurança ou interrupções de serviço.

-   **Tendências positivas e negativas**: Quando está a monitorizar o uso da memória na computação, a redução do tamanho da memória livre indica uma fuga de memória potencial. Para a monitorização do comprimento da fila de serviço, uma tendência ascendente persistente pode indicar um problema de software subjacente.

-   **Alterações de nível e alterações na gama dinâmica de valores**: Alterações de nível nas latenciências de um serviço após uma atualização do serviço ou níveis mais baixos de exceções após a atualização podem ser interessantes de monitorizar.

A API baseada em machine learning permite:

-   **Deteção flexível e robusta:** Os modelos de deteção de anomalias permitem aos utilizadores configurar definições de sensibilidade e detetar anomalias entre conjuntos de dados sazonais e não sazonais. Os utilizadores podem ajustar o modelo de deteção de anomalias para tornar a API de deteção menos ou mais sensível de acordo com as suas necessidades. Isto significaria detetar as anomalias menos ou mais visíveis em dados com e sem padrões sazonais.

-   **Deteção escalável e oportuna**: A forma tradicional de monitorização com os atuais limiares estabelecidos pelos conhecimentos de domínio dos peritos são dispendiosas e não escaláveis para milhões de conjuntos de dados em mudança dinâmica. Os modelos de deteção de anomalias nesta API são aprendidos, e os modelos são sintonizados automaticamente a partir de dados históricos e em tempo real.

-   **Deteção proactiva e acionável:** A tendência lenta e a deteção de alterações de nível podem ser aplicadas para deteção precoce de anomalias. Os primeiros sinais anormais detetados podem ser usados para direcionar os humanos para investigar e agir nas áreas problemáticas. Além disso, os modelos de análise de causas de raiz e ferramentas de alerta podem ser desenvolvidos em cima deste serviço de Deteção de Anomalias API.

A API de deteção de anomalias é uma solução eficaz e eficiente para uma vasta gama de cenários, tais como a saúde dos serviços e monitorização do KPI, IoT, monitorização do desempenho e monitorização do tráfego de rede. Aqui estão alguns cenários populares onde esta API pode ser útil:

- Os departamentos de TI precisam de ferramentas para rastrear eventos, código de erro, registo de uso e desempenho (CPU, memória, e assim por diante) em tempo oportuno.

-   Os sites de comércio online querem acompanhar as atividades dos clientes, visualizações de páginas, cliques, e assim por diante.

-   As empresas de serviços públicos querem controlar o consumo de água, gás, eletricidade e outros recursos.

-   Os serviços de gestão de instalações ou edifícios querem monitorizar a temperatura, humidade, tráfego, e assim por diante.

-   A IoT/fabricantes quer utilizar dados de sensores em séries temporais para monitorizar o fluxo de trabalho, a qualidade, e assim por diante.

-   Os prestadores de serviços, como os call centers, precisam monitorizar a tendência da procura de serviços, o volume de incidentes, o comprimento da fila de espera, e assim por diante.

-   Os grupos de análise de negócios querem monitorizar o movimento anormal dos KPIs (como o volume de vendas, os sentimentos dos clientes ou os preços) em tempo real.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) é um componente crítico da pilha de Segurança cloud da Microsoft. É uma solução abrangente que pode ajudar a sua organização à medida que se move para tirar o máximo partido da promessa de aplicações na nuvem. Mantém-te no controlo, através de uma melhor visibilidade para a atividade. Também aumenta a proteção dos dados vitais nas várias aplicações em cloud.

Com ferramentas para ajudar a desvendar Shadow IT, avaliar o risco, aplicar políticas, investigar atividades e parar ameaças, a sua organização pode migrar com mais segurança para a cloud sem perder o controlo dos dados vitais.

| | |
|---|---|
| Detetar | Desvende o TI sombra com o Cloud App Security. Obtenha visibilidade ao detetar aplicações, atividades, utilizadores, dados e ficheiros no seu ambiente na cloud. Detete aplicações de terceiros que estão ligadas à sua cloud.|
|Investigar | Investigue as suas aplicações na cloud com ferramentas forenses de cloud para ajudar a obter informações detalhadas sobre aplicações arriscadas, utilizadores e ficheiros específicos na sua rede. Descubra padrões nos dados recolhidos a partir da cloud. Gere relatórios para monitorizar a sua cloud. |
| Controlo | Reduza o risco através da definição de políticas e alertas para obter o máximo controlo sobre o tráfego de rede de cloud. Utilize o Cloud App Security para migrar os seus utilizadores para aplicações em cloud alternativas seguras e aprovadas. |
| Proteger | Use cloud App Security para sancionar ou proibir aplicações, impor a prevenção de perdas de dados, controlar permissões e partilha, e gerar relatórios e alertas personalizados. |
| Controlo | Reduza o risco através da definição de políticas e alertas para obter o máximo controlo sobre o tráfego de rede de cloud. Utilize o Cloud App Security para migrar os seus utilizadores para aplicações em cloud alternativas seguras e aprovadas. |
| | |


![Diagrama de segurança de aplicativo de nuvem](./media/threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integra visibilidade com a sua nuvem por:

-   Usando o Cloud Discovery para mapear e identificar o seu ambiente em nuvem e as aplicações em nuvem que a sua organização está a usar.

-   Sancionando e proibindo aplicações na sua nuvem.

-   Utilizando conectores de aplicações fáceis de implementar que aproveitem as APIs do fornecedor, para visibilidade e governação de apps a que se conecta.

-   Ajudando-o a ter um controlo contínuo, definindo e, em seguida, continuamente afinando as políticas.

Ao recolher dados destas fontes, a Cloud App Security executa uma análise sofisticada sobre o mesmo. Alerta-o imediatamente de atividades anómalas e dá-lhe mais visibilidade para o seu ambiente em cloud. Pode configurar uma política no Cloud App Security e utilizá-la para proteger tudo no seu ambiente em cloud.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Capacidades avançadas de deteção de ameaças de terceiros através do Mercado Azure

### <a name="web-application-firewall"></a>Firewall de Aplicação Web

Aplicação Web Firewall inspeciona tráfego web de entrada e bloqueia injeções SQL, scripts cross-site, uploads de malware, ataques dDoS da aplicação e outros ataques direcionados para as suas aplicações web. Também inspeciona as respostas dos servidores web back-end para prevenção de perdas de dados (DLP). O motor integrado de controlo de acesso permite aos administradores criar políticas de controlo de acesso granular para autenticação, autorização e contabilidade (AAA), o que confere às organizações uma autenticação forte e controlo do utilizador.

Firewall de aplicação web fornece os seguintes benefícios:

-   Deteta e bloqueia injeções SQL, Scripting Cross-Site, uploads de malware, DDoS da aplicação ou quaisquer outros ataques contra a sua aplicação.

-   Autenticação e controlo de acesso.

-   Digitaliza o tráfego de saída para detetar dados sensíveis e pode mascarar ou bloquear a fuga de informação.

-   Acelera a entrega de conteúdos de aplicações web, utilizando capacidades como cache, compressão e outras otimizações de tráfego.

Por exemplo, as firewalls de aplicações web que estão disponíveis no Mercado Azure, ver [Barracuda WAF, firewall de aplicação web virtual Brocade (vWAF), Imperva SecureSphere e firewall IP ThreatSTOP](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Passos seguintes

- [Responder às ameaças de hoje](../../security-center/security-center-alerts-overview.md#respond-threats): Ajuda a identificar ameaças ativas que visam os seus recursos Azure e fornece os conhecimentos necessários para responder rapidamente.

- Deteção de ameaças de base de [dados Azure SQL](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): Ajuda a resolver as suas preocupações sobre potenciais ameaças às suas bases de dados.
