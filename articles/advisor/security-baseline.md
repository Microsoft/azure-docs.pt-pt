---
title: Linha de segurança Azure para Azure Advisor
description: A linha de base de segurança do Conselheiro Azure fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: advisor
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9ca8fbfb7f339c18f32379c64b40bc86e2bc47a6
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94811068"
---
# <a name="azure-security-baseline-for-azure-advisor"></a>Linha de segurança Azure para Azure Advisor

Esta linha de base de segurança aplica orientações da [versão 2.0 do Azure Security Benchmark](../security/benchmarks/overview.md) para O Advisor. O Azure Security Benchmark fornece recomendações sobre como pode proteger as suas soluções em nuvem no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Azure Advisor. Foram excluídos **os controlos** não aplicáveis ao Azure Advisor.

Para ver como o Azure Advisor mapeia completamente o Benchmark de Segurança Azure, consulte o ficheiro completo de [mapeamento de base de base de segurança do Azure Advisor](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Gestão de Identidades

*Para mais informações, consulte o [Azure Security Benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Normalizar o Azure Ative Directory como o sistema central de identidade e autenticação

**Orientação**: O Azure Advisor utiliza o Azure Ative Directory (Azure AD) como o serviço de gestão de identidade e acesso padrão. Padronize a Azure AD para governar a gestão de identidade e acesso da sua organização em:

- Recursos da Microsoft Cloud, como o portal Azure, Azure Storage, Azure Virtual Machine (Linux e Windows), Azure Key Vault, PaaS e Aplicações SaaS
- Os recursos da sua organização, tais como aplicações no Azure ou os seus recursos de rede corporativa

Certifique-se de que a Azure AD é uma alta prioridade na prática de segurança na nuvem da sua organização. O Azure AD também fornece uma pontuação segura de identidade para ajudá-lo a avaliar a postura de segurança de identidade em relação às recomendações de boas práticas da Microsoft. Use a pontuação para avaliar o quão perto a sua configuração corresponde às recomendações de boas práticas e para fazer melhorias na sua postura de segurança.

Note que o Azure AD suporta identidades externas, que permitem aos utilizadores sem conta da Microsoft iniciar súm na sua aplicação e recursos com a sua identidade externa.

- [Arrendamento em Diretório Ativo Azure](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Como criar e configurar uma instância AD Azure](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Utilizar fornecedores de identidade externos para aplicação](/azure/active-directory/b2b/identity-providers) 

- [Qual é a pontuação de segurança de identidade no Azure Ative Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Use a Azure AD single sign-on (SSO) para acesso à aplicação

**Orientação**: O Azure Advisor utiliza o Azure Ative Directory (Azure AD) para fornecer gestão de identidade e acesso aos recursos Azure, aplicações em nuvem e aplicações no local. Isto inclui identidades empresariais como funcionários, bem como identidades externas, como parceiros, fornecedores e fornecedores. 

Utilize um único sinal para gerir e garantir o acesso aos dados e recursos da sua organização no local e na nuvem. Ligue todos os seus utilizadores, aplicações e dispositivos ao AD Azure para um acesso sem emenda, acesso seguro e maior visibilidade e controlo.

- [Compreender SSO de aplicação com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilize controlos de autenticação forte para todo o acesso baseado no Azure Ative Directory

**Orientação**: O Azure Advisor utiliza o Azure Ative Directory (Azure AD), que suporta controlos de autenticação forte através da autenticação multifactor e métodos fortes sem palavras-passe.
- Autenticação multifactor - Ativar a autenticação multifactor Azure AD e seguir as recomendações de Gestão de Identidade e Acesso do Azure Security Center para algumas boas práticas na configuração de autenticação multifactor. A autenticação multifactor pode ser aplicada em todos, selecione utilizadores ou ao nível por utilizador com base no sinal em condições e fatores de risco.
- Autenticação sem palavras-passe – Estão disponíveis três opções de autenticação sem palavra-passe. São, Windows Hello for Business, Microsoft Authenticator app e métodos de autenticação no local, como cartões inteligentes.

Para os utilizadores de administrador e privilegiados, certifique-se de que o mais alto nível do método de autenticação forte é utilizado, seguindo-se a aplicação da política de autenticação forte adequada a outros utilizadores.

- [Como permitir o MFA em Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introdução a opções de autenticação sem palavras-passe para O Diretório Ativo Azure](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitor e alerta sobre anomalias de conta

**Orientação**: O Azure Advisor está integrado com o Azure Ative Directory (Azure AD) no qual fornece as seguintes fontes de dados:
- Iniciar saúde - O sinal no relatório fornece informações sobre a utilização de aplicações geridas e sinal de utilizador nas atividades.
- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Exemplos de registos de auditoria incluem alterações feitas a quaisquer recursos dentro do AD Azure, como adicionar ou remover utilizadores, apps, grupos, papéis e políticas.
- Sinal de risco - Um sinal de risco é um indicador para um sinal na tentativa que pode ter sido realizado por alguém que não é o legítimo proprietário de uma conta de utilizador.
- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

Utilize estas fontes de dados para integrar com sistemas Azure Monitor, Azure Sentinel ou siem de terceiros. Alertas de configuração no Azure Security Center para certas atividades suspeitas, tais como um número excessivo de tentativas de autenticação falhadas, contas precíídas na subscrição.

A Azure Advanced Threat Protection (ATP) é uma solução de segurança que pode usar sinais de Ative Directory para identificar, detetar e investigar ameaças avançadas, identidades comprometidas e ações de insider maliciosas.

- [Relatórios de atividades de auditoria no Diretório Ativo do Azure](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure](../security-center/security-center-identity-access.md) 

- [Alertas no módulo de proteção de ameaças do Centro de Segurança Azure](../security-center/alerts-reference.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso a recurso Azure com base em condições

**Orientação**: O Azure Advisor suporta a funcionalidade de acesso condicional (Azure AD) do Azure Ative Directory para um controlo de acesso mais granular baseado em condições definidas pelo utilizador. Por exemplo, os logins dos utilizadores de certas gamas IP terão de utilizar a autenticação de vários fatores para iniciar sessão. A política de gestão da sessão de autenticação granular também pode ser usada para diferentes casos de utilização.

- [Visão geral do acesso condicional do Azure](../active-directory/conditional-access/overview.md) 

- [Políticas comuns de acesso condicional](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurar gestão de sessão de autenticação com acesso condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para mais informações, consulte o [Azure Security Benchmark: Acesso Privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos de negócios

**Orientação**: O Azure Advisor utiliza o controlo de acesso baseado em funções (Azure RBAC) para isolar o acesso a sistemas críticos de negócio, limitando quais as contas que têm acesso privilegiado às subscrições e grupos de gestão a que pertencem.

Restringir o acesso aos sistemas de gestão, identidade e segurança que tenham acesso administrativo ao seu negócio de acesso crítico, tais como Controladores de Domínio de Diretório Ativo (DCs), ferramentas de segurança e ferramentas de gestão de sistemas com agentes instalados em sistemas críticos de negócio. Os atacantes que comprometem estes sistemas de gestão e segurança podem imediatamente armar-los para comprometer ativos críticos do negócio.

Todos os tipos de controlos de acesso devem ser alinhados com a sua estratégia de segmentação da empresa para garantir um controlo de acesso consistente.

- [Componentes Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Acesso ao Grupo de Gestão](../governance/management-groups/overview.md#management-group-access)

- [Administradores de subscrição da Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Rever e conciliar o acesso dos utilizadores regularmente

**Orientação**: O Azure Advisor utiliza contas Azure Ative (Azure AD) para gerir os seus recursos, rever as contas dos utilizadores e aceder regularmente para garantir que as contas e o seu acesso são válidos. Implementar revisões de acesso a Azure para rever membros do grupo, acesso a aplicações empresariais e atribuições de funções. O relatório AD AD do Azure pode fornecer registos para ajudar a descobrir contas velhas. 

Além disso, utilize as funcionalidades de Gestão de Identidade Privilegiada da Azure AD para criar o fluxo de trabalho do relatório de revisão de acesso para facilitar o processo de revisão. A Gestão de Identidade Privilegiada também pode ser configurada para alertar quando um número excessivo de contas de administrador é criado, e para identificar contas de administrador que estão em situação de estagnação ou configuração indevida.

Note que alguns serviços da Azure suportam utilizadores locais e funções que não são geridas através do Azure AD. Os clientes terão de gerir estes utilizadores separadamente.

- [Criar uma revisão de acesso das funções de recursos Azure em Gestão de Identidade Privilegiada (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Como utilizar a identidade AD do Azure e as avaliações de acesso](/azure/active-directory/governance/access-reviews-overvie)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Utilize postos de trabalho privilegiados de acesso

**Orientação**: Estações de trabalho seguras e isoladas são de importância crucial para a segurança de funções sensíveis como administradores, desenvolvedores e operadores de serviços críticos. 

Utilize estações de trabalho de utilizador altamente seguras e/ou Bastião Azure para tarefas administrativas. Escolha O Azure Ative Directory (Azure AD), Microsoft Defender Advanced Threat Protection (ATP), incluindo o Microsoft Intune para implementar uma estação de trabalho segura e gerida para tarefas administrativas. 

Gerir centralmente as estações de trabalho seguras para impor a configuração segura, incluindo bases de autenticação forte, software e hardware, restritos acessos lógicos e de rede.

- [Compreender estações de trabalho de acesso privilegiada](../active-directory/devices/concept-azure-managed-workstation.md) 
 
- [Implementar uma estação de trabalho de acesso privilegiada](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Siga a administração suficiente (princípio de mínimo privilégio) 

**Orientação**: O Azure Advisor está integrado no controlo de acesso baseado em funções da Azure (Azure RBAC) para gerir os seus recursos. Use o RBAC Azure para gerir o acesso a recursos Azure através de atribuições de funções. 

Atribuir funções aos utilizadores, grupos de diretores de serviço e identidades geridas. Existem funções incorporadas pré-definidas para determinados recursos, que podem ser inventariadas ou consultadas através de ferramentas como Azure CLI, Azure PowerShell ou o portal Azure. Os privilégios atribuídos aos recursos através do Azure RBAC devem estar sempre limitados ao que é exigido pelas funções. Isto complementa a abordagem just in time (JIT) da Azure AD Privileged Identity Management (PIM) e deve ser revisto periodicamente.

Utilize funções incorporadas para alocar a permissão e apenas crie um papel personalizado quando necessário.

O que é o controlo de acesso baseado em funções Azure (Azure RBAC) .. /fun-based-access-control/overview.md 

- [Como configurar o RBAC em Azure](../role-based-access-control/role-assignments-portal.md) 

- [Como utilizar a identidade AD do Azure e as avaliações de acesso](/azure/active-directory/governance/access-reviews-overview)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="asset-management"></a>Gestão de Recursos

*Para mais informações, consulte o [Azure Security Benchmark: Gestão de Ativos](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Garantir que a equipa de segurança tem visibilidade em riscos para os ativos

**Orientação**: Certifique-se de que as equipas de segurança recebem permissões de Leitor de Segurança no seu inquilino Estaure e subscrições para que possam monitorizar riscos de segurança utilizando o Centro de Segurança Azure. 

Dependendo da forma como as responsabilidades das equipas de segurança são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Dito isto, os conhecimentos de segurança e os riscos devem ser sempre agregados centralmente dentro de uma organização. 

As permissões do Security Reader podem ser aplicadas amplamente a um inquilino inteiro (Root Management Group) ou a grupos de gestão ou subscrições específicas. 

Nota: Podem ser necessárias permissões adicionais para obter visibilidade em cargas de trabalho e serviços. 

- [Visão geral do papel do leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Visão geral dos Grupos de Gestão Azure](../governance/management-groups/overview.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: O Azure Advisor utiliza o Azure Ative Directory (Azure AD) para identidade e autenticação, enquanto o portal Azure e o Gestor de Recursos Azure são utilizados para gerir o Advisor. 

Utilize o Acesso Condicional Azure para limitar a capacidade de um utilizador interagir com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a App "Microsoft Azure Management", conforme exigido com base nos requisitos de negócio. 

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Registo e Deteção de Ameaças

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e Deteção de Ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Permitir a exploração de madeira para recursos Azure

**Orientação**: Os registos de atividade estão automaticamente disponíveis e contêm todas as operações de escrita (PUT, POST, DELETE) para os seus recursos do Azure Advisor, exceto operações de leitura (GET). 

Os registos de atividade podem ser utilizados para encontrar um erro na resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso.

- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a gestão e análise de registos de segurança

**Orientação**: Centralizar o armazenamento e análise de registos para permitir a correlação. Para cada fonte de registo, certifique-se de que atribuiu um titular de dados, orientação de acesso, localização de armazenamento, que ferramentas são usadas para processar e aceder aos dados, e requisitos de retenção de dados.

Certifique-se de que está a integrar os registos de atividade do Azure na sua sessão central. Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Além disso, ative e a bordo dados para Azure Sentinel ou um SIEM de terceiros. Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados frequentemente e o Azure Storage para dados "frios" que são usados com menos frequência.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Retenção de armazenamento de madeira configurada

**Orientação**: Certifique-se de que quaisquer contas de armazenamento ou espaços de trabalho do Log Analytics utilizados para armazenar registos do Azure Advisor têm o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização.
No Azure Monitor, pode definir o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize o espaço de trabalho Azure Storage, Data Lake ou Log Analytics para armazenamento de longo prazo e arquivo.

- [Como configurar o período de retenção do espaço de trabalho do Log Analytics](../azure-monitor/platform/manage-cost-storage.md) 

- [Armazenar registos de recursos numa conta de armazenamento Azure](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Incident Response](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

**Orientação**: Certifique-se de que a sua organização tem processos para responder a incidentes de segurança, atualizou estes processos para o Azure, e está regularmente a exercê-los para garantir a prontidão.

- [Implementar segurança em todo o ambiente da empresa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência de resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparação – notificação de incidente de configuração

**Orientação**: Configurar informações de contacto de incidentes de segurança no Centro de Segurança Azure. Estas informações de contacto são utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Também tem opções para personalizar alerta de incidentes e notificação em diferentes serviços Azure com base nas suas necessidades de resposta a incidentes. 

- [Como definir o contacto de segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes baseados em alertas de alta qualidade

**Orientação**: Certifique-se de que tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Isto permite-lhe aprender lições de incidentes passados e priorizar alertas para analistas, para que não percam tempo com falsos positivos. 

Alertas de alta qualidade podem ser construídos com base na experiência de incidentes passados, fontes comunitárias validadas e ferramentas projetadas para gerar e limpar alertas fundindo e correlacionando diversas fontes de sinal. 

O Azure Security Center fornece alertas de alta qualidade em muitos ativos da Azure. Pode utilizar o conector de dados ASC para transmitir os alertas ao Azure Sentinel. O Azure Sentinel permite criar regras avançadas de alerta para gerar incidentes automaticamente para uma investigação. 

Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de exportação para ajudar a identificar riscos para os recursos da Azure. Alertas e recomendações de exportação, manualmente ou de forma contínua e contínua.

- [Como configurar a exportação](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Deteção e análise – investigue um incidente

**Orientação**: Certifique-se de que os analistas podem consultar e usar diversas fontes de dados à medida que investigam potenciais incidentes, para construir uma visão completa do que aconteceu. Devem ser recolhidos registos diversos para rastrear as atividades de um potencial atacante através da cadeia de morte para evitar pontos cegos.  Deve também garantir que os insights e aprendizagens são capturados para outros analistas e para referência histórica futura.  

As fontes de dados para investigação incluem as fontes centralizadas de registo que já estão a ser recolhidas dos serviços de âmbito e dos sistemas de funcionamento, mas também podem incluir:

- Dados da rede – utilize os registos de fluxo dos grupos de segurança da rede, o Azure Network Watcher e o Azure Monitor para capturar registos de fluxo de rede e outras informações analíticas. 

- Instantâneos dos sistemas de funcionamento: 

    - Utilize a capacidade de instantâneo da máquina virtual Azure para criar uma imagem do disco do sistema de funcionamento. 

    - Utilize a capacidade de despejo de memória nativa do sistema operativo para criar uma imagem da memória do sistema de funcionamento.

    - Utilize a funcionalidade instantânea dos serviços Azure ou a capacidade do seu próprio software para criar instantâneos dos sistemas de execução.

O Azure Sentinel fornece uma análise extensiva de dados em praticamente qualquer fonte de registo e um portal de gestão de casos para gerir todo o ciclo de vida dos incidentes. Informações de inteligência durante uma investigação podem ser associadas a um incidente para rastrear e reportar propósitos. 

- [Snapshot um disco de máquina do Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snapshot um disco de máquina Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure Suporta informações de diagnóstico e recolha de despejo de memória](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigue incidentes com Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

**Orientação**: Fornecer contexto aos analistas sobre quais incidentes se concentrar em primeiro lugar com base na gravidade do alerta e na sensibilidade do ativo. 

O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque recursos usando tags e crie um sistema de nomeação para identificar e categorizar recursos Azure, especialmente aqueles que processam dados sensíveis.  É da sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o tratamento do incidente

**Orientação**: Automatizar tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir o fardo sobre os analistas. As tarefas manuais demoram mais tempo a ser executadas, abrandando cada incidente e reduzindo quantos incidentes um analista pode lidar. As tarefas manuais também aumentam a fadiga dos analistas, o que aumenta o risco de erro humano que causa atrasos, e degrada a capacidade dos analistas de se concentrarem eficazmente em tarefas complexas. Utilize funcionalidades de automatização de fluxos de trabalho no Azure Security Center e no Azure Sentinel para desencadear automaticamente ações ou executar um livro de jogadas para responder aos alertas de segurança que chegam. O livro de jogadas toma medidas, tais como o envio de notificações, a desativação de contas e a isolação de redes problemáticas. 

- [Configurar automatização de fluxos de trabalho no Centro de Segurança](../security-center/workflow-automation.md)

- [Criar respostas automáticas de ameaças no Centro de Segurança Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas automáticas de ameaças em Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gestão de Postura e Vulnerabilidade

*Para mais informações, consulte o [Azure Security Benchmark: Posture and Vulnerability Management](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulação regular de ataque

**Orientação**: Conforme necessário, realize testes de penetração ou atividades de equipa vermelha nos seus recursos Azure e garanta a reparação de todos os resultados críticos de segurança.
Siga as Regras de Teste de Penetração na Nuvem da Microsoft para garantir que os seus testes de penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

- [Testes de penetração em Azure](../security/fundamentals/pen-testing.md)

- [Regras de teste de penetração de compromisso](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="governance-and-strategy"></a>Governação e Estratégia

*Para mais informações, consulte o [Benchmark de Segurança Azure: Governação e Estratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definir estratégia de gestão de ativos e proteção de dados 

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara de monitorização e proteção contínua de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o acompanhamento de dados e sistemas críticos do negócio. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Norma de classificação de dados de acordo com os riscos empresariais

-   Visibilidade da organização de segurança em riscos e inventário de ativos 

-   Aprovação da organização de segurança dos serviços da Azure para utilização 

-   Segurança dos bens através do seu ciclo de vida

-   Estratégia de controlo de acesso exigida de acordo com a classificação de dados organizacionais

-   Utilização de capacidades de proteção de dados nativas e de terceiros da Azure

-   Requisitos de encriptação de dados para casos de utilização em trânsito e em repouso

-   Padrões criptográficos adequados

Para mais informações, consulte as seguintes referências:
- [Recomendação de arquitetura de segurança Azure - Armazenamento, dados e encriptação](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Fundamentos de Segurança Azure - Segurança de dados Azure, encriptação e armazenamento](../security/fundamentals/encryption-overview.md)

- [Cloud Adopt Framework - Segurança de dados Azure e melhores práticas de encriptação](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark - Gestão de ativos](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Benchmark de segurança Azure - Proteção de Dados](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definir estratégia de segmentação empresarial 

**Orientação**: Estabeleça uma estratégia em toda a empresa para segmentar o acesso a ativos utilizando uma combinação de identidade, rede, aplicação, subscrição, grupo de gestão, entre outros controlos.

Equilibra cuidadosamente a necessidade de separação de segurança com a necessidade de permitir o funcionamento diário dos sistemas que precisam de comunicar entre si e aceder aos dados.

Certifique-se de que a estratégia de segmentação é implementada de forma consistente em todos os tipos de controlo, incluindo modelos de segurança de rede, identidade e acesso, e permissões de aplicações ou modelos de acesso, e controlos de processos humanos.

- [Orientação sobre estratégia de segmentação em Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Orientação sobre estratégia de segmentação em Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação da rede com a estratégia de segmentação empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definir estratégia de gestão da postura de segurança

**Orientação:** Medir e mitigar continuamente os riscos para os seus ativos individuais e para o ambiente em que estão hospedados. Priorize ativos de alto valor e superfícies de ataque altamente expostas, tais como aplicações publicadas, entradas de rede e pontos de saída, pontos finais de utilizador e administrador, etc.

- [Azure Security Benchmark - Postura e gestão de vulnerabilidades](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alinhar papéis de organização, responsabilidades e responsabilidades

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para funções e responsabilidades na sua organização de segurança. Priorizar a prestação de contas claras às decisões de segurança, educar todos no modelo de responsabilidade partilhada e educar as equipas técnicas em tecnologia para garantir a nuvem.

- [Azure Security Best Practice 1 - Pessoas: Educar equipas na jornada de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Security Best Practice 2 - Pessoas: Educar equipas sobre tecnologia de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security Best Practice 3 - Processo: Atribuir responsabilidade para decisões de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definir estratégia de segurança de rede

**Orientação**: Estabeleça uma abordagem de segurança da rede Azure como parte da estratégia global de controlo de acesso à segurança da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Responsabilidade centralizada de gestão de redes e segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação da empresa

-   Estratégia de remediação em diferentes cenários de ameaça e ataque

-   Internet borda e estratégia de entrada e saída

-   Estratégia híbrida de interconectividade em nuvem e no local

-   Artefactos de segurança da rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para mais informações, consulte as seguintes referências:
- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Benchmark de segurança Azure - Segurança de rede](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Visão geral da segurança da rede Azure](../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura de rede empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definir estratégia de identidade e acesso privilegiado

**Orientação**: Estabeleça uma abordagem de identidade Azure e acesso privilegiado como parte da estratégia global de controlo de acesso à empresa da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Um sistema centralizado de identidade e autenticação e a sua interconectividade com outros sistemas de identidade interna e externa

-   Métodos de autenticação forte em diferentes casos e condições de utilização

-   Proteção de utilizadores altamente privilegiados

-   Monitorização e manuseamento de atividades de utilizadores de anomalias  

-   Revisão e reconciliação da identidade do utilizador e acesso

Consulte os links referenciados para mais informações.

- [Azure Security Benchmark - Gestão de identidade](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Benchmark de Segurança Azure - Acesso privilegiado](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Visão geral da segurança da gestão de identidade Azure](../security/fundamentals/identity-management-overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta ao abate e à ameaça

**Orientação**: Estabeleça uma estratégia de resposta à exploração madeireira e a ameaças para detetar e remediar rapidamente as ameaças, cumprindo os requisitos de conformidade. Priorize fornecer aos analistas alertas de alta qualidade e experiências perfeitas para que se concentrem em ameaças em vez de integração e passos manuais. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   O papel e as responsabilidades da organização das operações de segurança (SecOps) 

-   Um processo de resposta a incidentes bem definido, alinhado com o NIST ou outro quadro da indústria 

-   Registar captura e retenção para apoiar a deteção de ameaças, resposta a incidentes e necessidades de conformidade

-   Visibilidade centralizada e correlação de informações sobre ameaças, utilizando siem, capacidades nativas de Azure, e outras fontes 

-   Plano de comunicação e notificação com os seus clientes, fornecedores e partes públicas de interesse

-   Utilização de plataformas nativas e de terceiros da Azure para o tratamento de incidentes, tais como deteção de registos e ameaças, perícia e remediação e erradicação de ataques

-   Processos de tratamento de incidentes e atividades pós-incidente, tais como lições aprendidas e retenção de provas

Para mais informações, consulte as seguintes referências:

- [Azure Security Benchmark - Registo e deteção de ameaças](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark - Resposta a incidentes](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 - Processo. Atualizar processos de resposta a incidentes para cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Quadro de adoção Azure, registo e guia de decisão de reporte](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gestão e monitorização da empresa Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Consulte a [visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança da Azure](/azure/security/benchmarks/security-baselines-overview)
