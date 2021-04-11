---
title: Linha de segurança Azure para O Diretório Ativo Azure
description: A linha de base de segurança do Azure Ative Directory fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: active-directory
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d4203b7b5d7742bf198778864fa4f42b7423596
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107286020"
---
# <a name="azure-security-baseline-for-azure-active-directory"></a>Linha de segurança Azure para O Diretório Ativo Azure

Esta linha de base de segurança aplica orientações da [versão 2.0 do Azure Security Benchmark](../../security/benchmarks/overview.md) para o Azure Ative Directory. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Azure Ative Directory. 

> [!NOTE]
> **Os controlos** não aplicáveis ao Azure Ative Directory, ou para os quais a responsabilidade é da Microsoft, foram excluídos desta linha de base. Para ver como o Azure Ative Directory mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de base de segurança Azure Ative.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: Simplificar as regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtual Azure para definir controlos de acesso à rede em grupos de segurança de rede ou firewall Azure configurados para os seus recursos do Azure Ative Directory. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço, como 'AzureActiveDirectory' no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam. 

- [Compreender e utilizar tags de serviço](../../virtual-network/service-tags-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-management"></a>Gestão de Identidades

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Identidades](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Uniformizar o Azure Active Directory como o sistema central de identidade e autenticação

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como o serviço de gestão de identidade e acesso padrão. Deve normalizar a Azure AD para governar a gestão de identidade e acesso da sua organização em: 
- Recursos da Microsoft Cloud, como o portal Azure, Azure Storage, Azure Virtual Machine (Linux e Windows), Azure Key Vault, PaaS e SaaS. 
- recursos da sua organização, como as aplicações no Azure, ou os recursos da rede empresarial. 
 

A proteção do Azure AD deve ser prioritária na prática de segurança da cloud para a sua organização. O AAD proporciona uma pontuação de segurança de identidade para o ajudar a avaliar a postura de segurança de identidade relativamente às recomendações de melhores práticas da Microsoft. Utilize a pontuação para determinar até que ponto é que a sua configuração corresponde às recomendações de melhores práticas e para fazer melhorias à postura de segurança. 

O Azure AD suporta identidade externa que permite aos utilizadores sem conta da Microsoft iniciar súm na sua aplicação e recursos com a sua identidade externa. 

- [Inquilinos no Azure Active Directory](../develop/single-and-multi-tenant-apps.md)

- [Como criar e configurar instâncias do Azure AD](active-directory-access-create-new-tenant.md)

- [Utilizar fornecedores de identidade externos para a aplicação](/azure/active-directory/b2b/identity-providers)

- [O que é a pontuação de segurança de identidade no Azure Active Directory?](identity-secure-score.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gerir identidades de aplicação de forma segura e automática

**Orientação**: Utilize identidade gerida para recursos azure para contas não humanas, como serviços ou automação, é aconselhável utilizar a funcionalidade de identidade gerida pelo Azure em vez de criar uma conta humana mais poderosa para aceder ou executar os seus recursos. Pode autenticar de forma nativa os serviços/recursos da Azure que suportam a autenticação do Azure Ative Directory (Azure AD) através da regra de concessão de acesso pré-definida sem utilizar o código de credencial codificado em código fonte ou ficheiros de configuração. Não é possível atribuir identidades geridas ao Azure AD, mas a Azure AD é o mecanismo para autenticar com identidades geridas atribuídas aos recursos de outros serviços.

- [Identidade gerida para recursos Azure](../managed-identities-azure-resources/overview.md)

- [Serviços que suportam identidade gerida para recursos azure](../managed-identities-azure-resources/services-support-managed-identities.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Utilizar o início de sessão único (SSO) do Azure AD para acesso a aplicações

**Orientação**: Utilize o Azure Ative Directory para fornecer gestão de identidade e acesso aos recursos Azure, aplicações em nuvem e aplicações no local. Essas identidades incluem identidades empresariais, como colaboradores, bem como identidades externas, como parceiros, fornecedores e distribuidores. Com isto, o início de sessão único (SSO) pode gerir e proteger o acesso aos dados e recursos da sua organização, tanto no ambiente no local, como na cloud. Ligue todos os seus utilizadores, aplicações e dispositivos ao Azure AD para beneficiar de acesso seguro e ininterrupto e de mais visibilidade e controlo.

 
- [Compreender o SSO de Aplicações com o Azure AD](../manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilizar controlos de autenticação fortes para todos os acessos baseados no Azure Active Directory

**Orientação**: Utilize o Azure Ative Directory para suportar controlos de autenticação forte através da autenticação de vários fatores (MFA) e métodos fortes sem palavras-passe.
- Autenticação multifator - ative o MFA do Azure AD e siga as recomendações de Gestão de Identidades e Acessos do Centro de Segurança do Azure para obter algumas melhores práticas na configuração do MFA. O MFA pode ser imposto em todos os utilizadores, utilizadores específicos ou utilizador a utilizador com base em condições de início de sessão e fatores de risco.
- Autenticação sem palavras-passe - Estão disponíveis três opções de autenticação sem palavras-passe: Windows Hello for Business, Microsoft Authenticator e métodos de autenticação no local, como cartões inteligentes.

Para os utilizadores de administrador e privilegiados, certifique-se de que é utilizado o mais alto nível do método de autenticação forte, seguindo-se a aplicação da política de autenticação forte adequada a outros utilizadores.

O Azure AD suporta a autenticação baseada em passwords legacy, como contas apenas cloud (contas de utilizador criadas diretamente no AZure AD) que têm uma política de senha de base ou contas Híbridas (contas de utilizador que vêm de Ative Directory) que seguirão as políticas de senha no local. Ao utilizar a autenticação baseada em palavras-passe, o Azure AD fornece uma capacidade de proteção de palavras-passe que impede os utilizadores de definir palavras-passe que são fáceis de adivinhar. A Microsoft fornece uma lista global de senhas proibidas que é atualizada com base na telemetria, e os clientes podem aumentar a lista com base nas suas necessidades (por exemplo, branding, referências culturais, etc.). Esta proteção de palavra-passe pode ser utilizada para contas exclusivamente em nuvem e híbridas.

A autenticação baseada apenas nas credenciais de senha é suscetível a métodos de ataque populares. Para uma maior segurança, utilize uma autenticação forte, como mFA e uma política de senha forte. Para aplicações de terceiros e serviços de marketplace que possam ter senhas padrão, deve alterá-las após a configuração inicial do serviço.

 
- [Como implantar O AZure AD MFA](../authentication/howto-mfa-getstarted.md) 

 

 
- [Introdução às opções de autenticação sem palavra-passe para o Azure Active Directory](../authentication/concept-authentication-passwordless.md) 

 

 
- [Política de palavras-passe predefinidas do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminar palavras-passe fracas com a Proteção de Palavras-passe do Azure AD](../authentication/concept-password-ban-bad.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorizar e alertar para anomalias em contas

**Orientação**: O Azure Ative Directory fornece as seguintes fontes de dados:

 
- Inícios de sessão - o relatório de inícios de sessão faculta informações sobre a utilização de aplicações geridas e das atividades de início de sessão dos utilizadores.

 
- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas.

 
- Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

 
- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

 

Estas fontes de dados podem ser integradas com sistemas Azure Monitor, Azure Sentinel ou siem de terceiros.

 

 
O Centro de Segurança do Azure também pode alertar para determinadas atividades suspeitas, como tentativas excessivas de autenticações mal-sucedidas ou contas descontinuadas na subscrição.

 

 
A Proteção Avançada Contra Ameaças (ATP) do Azure é uma solução de segurança que pode utilizar sinais do Active Directory para identificar, detetar e investigar ameaças avançadas, identidades comprometidas e ações internas maliciosas.

 

 
- [Relatórios de atividades de auditoria no Azure Active Directory](../reports-monitoring/concept-audit-logs.md) 

 

 
- [Como ver os inícios de sessão de risco do Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

 

 
- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

 

 
- [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../../security-center/security-center-identity-access.md) 

 

 
- [Alertas no módulo de proteção de informações sobre ameaças do Centro de Segurança do Azure](../../security-center/alerts-reference.md) 

 

 
- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso aos recursos do Azure com base em condições

**Orientação**: Utilize o Azure Ative Directy (Azure AD) Acesso condicional para um controlo de acesso mais granular baseado em condições definidas pelo utilizador, como logins de utilizadores de determinadas gamas IP, terá de utilizar MFA para login. A política de gestão de sessão de autenticação granular também pode ser utilizada para diferentes casos de utilização.

 
- [Visão geral do acesso condicional da Azure AD](../conditional-access/overview.md) 

 

 
- [Políticas comuns de acesso condicional](../conditional-access/concept-conditional-access-policy-common.md) 

 

 
- [Configurar a gestão de sessões de autenticação com o Acesso Condicional](../conditional-access/howto-conditional-access-session-lifetime.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: Acesso seguro do utilizador a aplicações antigas

**Orientação**: Certifique-se de que tem modernos controlos de acesso e monitorização de sessão para aplicações antigas e os dados que armazenam e processam. Embora as VPNs sejam geralmente usadas para aceder a aplicações antigas, muitas vezes têm apenas controlo básico de acesso e monitorização limitada da sessão.

 
O Azure AD Application Proxy permite-lhe publicar aplicações antigas no local para utilizadores remotos com SSO, ao mesmo tempo que valida explicitamente a fiabilidade tanto dos utilizadores remotos como dos dispositivos com acesso condicional Azure AD.

 

 
Em alternativa, o Microsoft Cloud App Security é um serviço de Cloud Access Security Broker (CASB) que pode fornecer controlos para monitorizar as sessões de aplicações do utilizador e ações de bloqueio (tanto para aplicações antigas no local como software em nuvem como aplicações de serviço (SaaS).

 

 
- [Azure Ative Directory Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#what-is-application-proxy) 

 

 
- [Microsoft Cloud App Security as melhores práticas](/cloud-app-security/best-practices)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="privileged-access"></a>Acesso Privilegiado

*Para obter mais informações, veja [Referência de Segurança do Azure: Acesso Privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Proteger e limitar utilizadores com muitos privilégios

**Orientação**: As funções mais críticas embutidas são a Azure AD são o Administrador Global e o Administrador privilegiado, uma vez que os utilizadores designados para estas duas funções podem delegar funções de administrador:

- Administrador Global: Os utilizadores com esta função têm acesso a todas as funcionalidades administrativas em Azure AD, bem como serviços que utilizam identidades AZure AD.

- Administrador privilegiado: Os utilizadores com esta função podem gerir atribuições de funções em Azure AD, bem como dentro da Azure AD Privileged Identity Management (PIM). Além disso, esta função permite a gestão de todos os aspetos da PIM e das unidades administrativas.

Pode ter outros papéis críticos que precisam de ser governados se utilizar funções personalizadas com determinadas permissões privilegiadas atribuídas. E também pode querer aplicar controlos semelhantes à conta de administrador de ativos empresariais críticos.

A Azure AD tem contas altamente privilegiadas: os utilizadores e os principais de serviços que são direta ou indiretamente atribuídos ou elegíveis para as funções de Administrador Global ou Administrador privilegiado, e outras funções altamente privilegiadas em Ad e Azure.

Limitar o número de contas altamente privilegiadas e proteger estas contas a um nível elevado, porque os utilizadores com este privilégio podem ler e modificar diretamente ou indiretamente todos os recursos do seu ambiente Azure.

Pode ativar o acesso privilegiado just-in-time (JIT) nos recursos do Azure e no Azure AD com o Azure AD Privileged Identity Management (PIM). O JIT concede permissões temporárias para realizar tarefas privilegiadas apenas quando os utilizadores precisam. O PIM também pode gerar alertas de segurança em caso de atividades suspeitas ou inseguras na sua organização do Azure AD.

- [Permissões das funções de administrador no Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [Utilizar os alertas de segurança do Azure Privileged Identity Management](../privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos da empresa

**Orientação**: Utilize a Azure Ative Directory Gestão de Identidade Privilegiada e a autenticação de vários fatores para restringir o acesso administrativo a sistemas críticos empresariais.

- [Aprovação privilegiada da Gestão de Identidade dos pedidos de ativação de funções](../privileged-identity-management/azure-ad-pim-approval-workflow.md)

- [Autenticação multi-factor e acesso condicional](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Rever e reconciliar o acesso dos utilizadores regularmente

**Orientação**: Rever regularmente as atribuições de acesso à conta de utilizador para garantir que as contas e o seu acesso são válidos, especialmente focados nas funções altamente privilegiadas, incluindo Administrador Global e Administrador privilegiado. Você pode usar a Azure Ative Directory (Azure AD) avaliações de acesso para rever membros do grupo, acesso a aplicações empresariais e atribuições de funções, tanto para funções AD AZure como para funções Azure. O relatório AD AD do Azure pode fornecer registos para ajudar a descobrir contas velhas. Também pode utilizar a Azure AD Privileged Identity Management para criar o fluxo de trabalho do relatório de revisão de acessos para facilitar o processo de revisão.

Além disso, a Azure Privileged Identity Management também pode ser configurada para alertar quando um número excessivo de contas de administrador é criado, e para identificar contas de administrador que estão incompras ou configuradas indevidamente.

- [Criar uma revisão de acesso das funções AZURE AD em Gestão de Identidade Privilegiada (PIM)](../privileged-identity-management/pim-how-to-start-security-review.md)

- [Criar uma revisão de acesso das funções de recursos Azure em Gestão de Identidade Privilegiada (PIM)](../privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Como utilizar as revisões de identidades e acessos do Azure AD](../governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configurar o acesso de emergência no AAD

**Orientação**: Para evitar que seja acidentalmente bloqueado fora da sua organização Azure AD, crie uma conta de acesso de emergência para acesso quando não puder ser utilizadas contas administrativas normais. As contas de acesso de emergência são, normalmente, altamente privilegiadas e não devem ser atribuídas a indivíduos específicos.
As contas de acesso de emergência são limitadas a cenários de emergência ou de “interrupção de emergência”, em que as contas administrativas normais não podem ser utilizadas.

Deve garantir que as credenciais (por exemplo, palavra-passe, certificado ou smart card) das contas de acesso de emergência são mantidas em segurança e só são conhecidas por indivíduos que estão autorizados a utilizá-las apenas para uma emergência.

- [Gerir contas de acesso de emergência no AAD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Gestão de direitos de automatização 

**Orientação**: Utilize funcionalidades de gestão de direitos Ad Azure para automatizar fluxos de trabalho de pedidos de acesso, incluindo atribuições de acesso, revisões e expiração. A aprovação dual ou multi-fase também é apoiada.

- [O que é a gestão de direitos da AD Azure](../governance/entitlement-management-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Utilizar estações de trabalho privilegiadas

**Orientação**: As estações de trabalho seguras e isoladas são de importância crítica para a segurança de funções confidenciais, por exemplo, administradores, programadores e operadores de serviço de importância crítica. Utilize estações de trabalho de utilizador altamente seguras e/ou Bastião Azure para tarefas administrativas. Utilize o Azure Active Directory, a Proteção Avançada Contra Ameaças do Microsoft Defender (ATP) e/ou o Microsoft Intune para implementar uma estação de trabalho de utilizador gerida e segura para tarefas administrativas. As estações de trabalho seguras podem ser geridas centralmente para impor uma configuração segura, incluindo uma autenticação forte e linhas de base de software e hardware e acesso de rede e lógico restrito.

- [Dispositivos de segurança como parte do acesso privilegiado](/security/compass/privileged-access-devices)

- [Implementação de acesso privilegiado](/security/compass/privileged-access-deployment)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Seguir a abordagem de Administração Suficiente (princípio do privilégio mínimo) 

**Orientação**: Os clientes podem configurar a sua implementação Azure Ative Directory (Azure AD) para obter o mínimo privilégio, atribuindo aos utilizadores as funções com as permissões mínimas necessárias para que os utilizadores completem as suas tarefas administrativas.

- [Permissões das funções de administrador no Azure AD](../roles/permissions-reference.md)

- [Atribuir funções administrativas na Azure AD](../roles/manage-roles-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Escolha o processo de aprovação para suporte da Microsoft  

**Orientação**: O Azure Ative Directory não suporta o cofre do cliente. A Microsoft pode trabalhar com os clientes através de métodos não-lockbox para aprovação para aceder aos dados dos clientes.

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteger dados confidenciais

**Orientação**: Considere as seguintes orientações para a implementação da proteção dos seus dados sensíveis:

- **Isolamento:** Um diretório é o limite de dados pelo qual o Azure Ative Directory (Azure AD) armazena e processa dados para um cliente. Os clientes devem determinar onde querem que a maioria dos seus Dados de Cliente Azure AD residam definindo a propriedade Country no seu diretório.

- **Segmentação:** O papel do administrador global tem o controlo total de todos os dados do diretório, e as regras que regem as instruções de acesso e processamento. Um diretório pode ser segmentado em unidades administrativas, e a provisionado com utilizadores e grupos a serem geridos por administradores dessas unidades, os administradores globais podem delegar a responsabilidade de outros princípios na sua organização, atribuindo-os a funções pré-definidas ou funções personalizadas que possam criar.

 
- **Acesso:** As permissões podem ser aplicadas num utilizador, grupo, função, aplicação ou dispositivo. A atribuição pode ser permanente ou temporal por configuração de Gestão de Identidade Privilegiada. 
  
- **Encriptação:** A Azure AD encripta todos os dados em repouso ou em trânsito. A oferta não permite que os clientes encriptem dados de diretório com a sua própria chave de encriptação. 

Para determinar como o país selecionado mapeia para a localização física do seu diretório consulte o artigo 'Onde está o artigo localizado pelos seus dados'.

- [Onde os seus dados estão localizados artigo](https://www.microsoft.com/trust-center/privacy/data-location)

Como o cliente utiliza várias ferramentas, funcionalidades e aplicações AD Azure que interagem com o seu diretório, utilize o artigo Azure Ative Directory – Onde estão os seus dados localizados?

- [Onde está o painel de instrumentos localizado](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)

- [Documentação sobre funções do Azure AD](/azure/active-directory/roles/)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Encriptar informações confidenciais em trânsito

**Orientação**: Para complementar os controlos de acesso, os dados em trânsito devem ser protegidos contra ataques "fora de banda" (por exemplo, captura de tráfego) utilizando encriptação para garantir que os atacantes não podem ler ou modificar facilmente os dados.

A Azure AD suporta encriptação de dados em trânsito com TLS v1.2 ou superior.

Embora isto seja facultativo para o tráfego em redes privadas, isto é fundamental para o tráfego em redes externas e públicas. Para o tráfego HTTP, certifique-se de que qualquer cliente que se conecte aos seus recursos Azure pode negociar TLS v1.2 ou maior. Para uma gestão remota, utilize SSH (para Linux) ou RDP/TLS (para Windows) em vez de um protocolo não encriptado. As versões e protocolos obsoletos de SSL, TLS e SSH, e as cifras fracas devem ser desativadas.

Por padrão, o Azure fornece encriptação para dados em trânsito entre centros de dados Azure.

- [Compreender a encriptação em trânsito com Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) 

- [Informações sobre a Segurança TLS](/security/engineering/solving-tls1-problem) 

- [Dupla encriptação para dados do Azure em trânsito](https://docs.microsoft.com/azure/security/fundamentals/double-encryption#data-in-transit)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="asset-management"></a>Gestão de Recursos

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Ativos](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Certifique-se de que a equipa de segurança tem visibilidade para os riscos dos ativos

**Orientação**: Confirme que as equipas de segurança recebem as permissões Leitor de Segurança no seu inquilino e nas suas subscrições do Azure, para que possam monitorizar os riscos de segurança com o Centro de Segurança do Azure. 

Dependendo da forma como as responsabilidades destas equipas são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Dito isto, as informações e os riscos de segurança têm de ser sempre agregados centralmente nas organizações. 

As permissões Leitor de Segurança podem ser aplicadas de um modo amplo em todo um inquilino (Grupo de Gestão Raiz) ou dentro de âmbitos, como grupos de gestão ou subscrições específicas. 

Podem ser necessárias permissões adicionais para obter visibilidade em cargas de trabalho e serviços. 

- [Descrição Geral da Função de Leitor de Segurança](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Descrição Geral dos Grupos de Gestão do Azure](../../governance/management-groups/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Azure Ative Directy (Azure AD) Acesso Condicional para limitar a capacidade dos utilizadores de interagirem com a AZure AD através do portal Azure, configurando o "Acesso ao Bloco" para a App "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-threat-detection"></a>Registos e Deteção de Ameaças

*Para obter mais informações, veja [Referência de Segurança do Azure: Registos e Deteção de Ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Permitir a deteção de ameaças para recursos Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) Capacidade de deteção de ameaças incorporada para os seus recursos AD Azure. 
 
 
 

 
A Azure AD produz registos de atividade que são frequentemente usados para deteção de ameaças e caça a ameaças. Os registos de login da AD Azure fornecem um registo de atividade de autenticação e autorização para utilizadores, serviços e aplicações. Os registos de auditoria da AD AZure acompanham as alterações feitas a um inquilino AZure AD, incluindo alterações que melhoram ou diminuem a postura de segurança.

- [O que é o Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)

- [Ligue os dados de Proteção de Identidade Azure AD ao Azure Sentinel](../../sentinel/connect-azure-ad-identity-protection.md)

- [Ligue os dados do Diretório Ativo Azure ao Azure Sentinel](../../sentinel/connect-azure-active-directory.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Ative a deteção de ameaças para a gestão de identidades e acessos do Azure

**Orientação**: O Azure Ative Directory (Azure AD) fornece os seguintes registos de utilizador que podem ser vistos em relatórios AD Azure ou integrados com Azure Monitor, Azure Sentinel ou outras ferramentas SIEM/monitorização para casos de monitorização e utilização analíticos mais sofisticados: 
- Inícios de sessão – o relatório de inícios de sessão faculta informações sobre a utilização de aplicações geridas e das atividades de início de sessão dos utilizadores. 
- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas. 
- Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. 
- Utilizadores arriscados - Um utilizador arriscado é um indicador para uma conta de utilizador que pode ter sido comprometida. 

As deteções de riscos de proteção de identidade são ativadas por padrão e não requerem nenhum processo de embarque. Os dados de granularidade ou risco são determinados pela licença SKU. 

- [Relatórios de atividades de auditoria no Azure Active Directory](../reports-monitoring/concept-audit-logs.md)  

- [Ativar o Azure Identity Protection](../identity-protection/overview-identity-protection.md)  

- [Proteção contra ameaças no Centro de Segurança do Azure](/azure/security-center/threat-protection)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Ativar o registo dos recursos do Azure

**Orientação**: O Azure Ative Directory (Azure AD) produz registos de atividade. Ao contrário de alguns serviços Azure, a Azure AD não faz uma distinção entre atividade e registos de recursos. Os registos de atividade estão automaticamente disponíveis na secção AD Azure do portal Azure, e podem ser exportados para Azure Monitor, Azure Event Hubs, Azure Storage, SIEMs e outros locais.
 
- Inícios de sessão – o relatório de inícios de sessão faculta informações sobre a utilização de aplicações geridas e das atividades de início de sessão dos utilizadores.  
 
- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas.  
 
 

O Azure AD também fornece registos relacionados com segurança que podem ser vistos em relatórios AZure AD ou integrados com Azure Monitor, Azure Sentinel ou outras ferramentas siem/monitorização para casos de monitorização e utilização de análises mais sofisticados: 
- Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.
- Utilizadores arriscados - Um utilizador arriscado é um indicador para uma conta de utilizador que pode ter sido comprometida. 

As deteções de riscos de proteção de identidade são ativadas por padrão e não requerem nenhum processo de embarque. Os dados de granularidade ou risco são determinados pela licença SKU. 

 
- [Relatórios de atividade e segurança no Azure Ative Directory](../reports-monitoring/overview-reports.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a análise e gestão do registo de segurança

**Orientação**: Recomendamos as seguintes orientações quando os clientes pretendem centralizar os seus registos de segurança para uma análise mais fácil da caça de ameaças e da postura de segurança:
 
- Centralizar o armazenamento e análise de registo para permitir a correlação. Para cada fonte de registo dentro da Azure AD, certifique-se de que atribuiu um titular de dados, orientação de acesso, localização de armazenamento, que ferramentas são usadas para processar e aceder aos dados, e requisitos de retenção de dados.  
 
- Certifique-se de que está a integrar os registos de atividade do Azure na sua sessão central. Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.  
 
- Além disso, ative e a bordo dados para Azure Sentinel ou um SIEM de terceiros.  
 

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados frequentemente e o Azure Storage para dados "frios" que são usados com menos frequência.  
 

- [Como recolher registos e métricas da plataforma com o Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)   
 

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Configurar a retenção de armazenamento dos registos

**Orientação**: Certifique-se de que quaisquer contas de armazenamento ou espaços de trabalho do Log Analytics utilizados para armazenar registos de registos de diretórios ativos do Azure, registos de auditoria e registos de dados de risco tem o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização.

No Azure Monitor, pode definir o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize o espaço de trabalho Azure Storage, Data Lake ou Log Analytics para armazenamento de longo prazo e arquivo.

- [Como configurar o período de retenção do espaço de trabalho do Log Analytics](/azure/azure-monitor/platform/manage-cost-storage)  

- [Armazenar registos de recursos numa conta de armazenamento Azure](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

**Orientação**: Certifique-se de que a sua organização tem processos para responder a incidentes de segurança, que atualizou esses processos para o Azure e que os pratica regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparação – configurar a notificação de incidentes

**Orientação**: Configure as informações de contacto dos incidentes de segurança no Centro de Segurança do Azure. A Microsoft utiliza estas informações de contacto para o contactar caso o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos de forma ilícita ou não autorizada. Também tem opções para personalizar os alertas e as notificações de incidentes em diferentes serviços do Azure de acordo com as suas necessidades de resposta aos incidentes. 

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes baseados em alertas de alta qualidade 

**Orientação**: garanta que tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Desta forma, pode aprender lições com os incidentes passados e priorizar os alertas para os analistas, para que estes não percam tempo a lidar com falsos positivos. 

Podem ser desenvolvidos alertas de alta qualidade com base na experiência de incidentes passados, origens da comunidade validadas e ferramentas designadas para gerar e limpar alertas através da fusão e correlação de origens de sinais diversas. 

O Azure Security Center fornece alertas de alta qualidade em muitos ativos da Azure. Pode utilizar o conector de dados ASC para transmitir os alertas para o Azure Sentinel. O Azure Sentinel permite-lhe criar regras de alertas avançadas para gerar incidentes automaticamente para investigações. 

Exporte os alertas e as recomendações do Centro de Segurança do Azure com a funcionalidade de exportação para ajudar a identificar riscos para os recursos do Azure. Exporte os alertas e as recomendações manualmente ou de forma contínua.

- [Como configurar a exportação](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Deteção e análise – investigar incidentes

**Orientação**: Certifique-se de que os analistas podem consultar e utilizar várias origens de dados para investigar potenciais incidentes e ter uma perspetiva geral do que aconteceu. Para monitorizar as atividades de um potencial atacante em toda a rede do ataque e evitar ângulos mortos, devem ser recolhidos diversos registos.  Também deve assegurar que são apreendidas informações e aprendizagens para outros analistas e para informação histórica futura.  

As origens de dados para investigação incluem as origens de registos centralizadas que já estão a ser recolhidas pelos serviços dentro do âmbito e pelos sistemas em execução, mas podem também incluir:

- Dados de rede – utilize os registos de fluxos dos grupos de segurança de rede, o Observador de Rede do Azure e o Azure Monitor para capturar os registos de fluxos de rede e outras informações analíticas. 

- Instantâneos dos sistemas em execução: 

    - Utilize a capacidade de instantâneos das Máquinas Virtuais do Azure para criar um instantâneo do disco do sistema em execução. 

    - Utilize a capacidade de captura da memória nativa do sistema operativo para criar um instantâneo da memória do sistema operativo.

    - Utilize a funcionalidade de instantâneos dos serviços do Azure ou a capacidade do seu próprio software para criar instantâneos dos sistemas em execução.

O Azure Sentinel disponibiliza uma grande quantidade de análises de dados em praticamente qualquer origem de registos e um portal de gestão de casos para gerir o ciclo de vida completo dos incidentes. As informações de inteligência durante uma investigação podem ser associadas a um incidente para fins de monitorização e reporte. 

- [Criar um instantâneo de um disco de uma máquina Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Criar um instantâneo de um disco de uma máquina Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informações de diagnóstico e recolha da captura da memória do Suporte do Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigar incidentes com o Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

**Orientação**: Dê contexto aos analistas quanto aos incidentes em que se devem concentrar primeiro tendo por base a gravidade dos alertas e a sensibilidade dos ativos. 

O Centro de Segurança do Azure atribui uma gravidade a cada alerta para o ajudar a priorizar os que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque os recursos com etiquetas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais.  É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o processamento dos incidentes

**Orientação**: Automatize tarefas manuais repetitivas para acelerar o tempo de resposta e reduzir a carga dos analistas. As tarefas manuais são mais morosas e atrasam todos os incidentes, reduzindo o número daqueles com que um analista pode lidar. Estas tarefas também aumentam a fadiga dos analistas, o que aumenta o risco de erro humanos e provoca atrasos, bem como degrada a capacidade de aqueles se concentrarem em tarefas complexas. Utilize as funcionalidades de automatização de fluxos de trabalho do Centro de Segurança do Azure e do Azure Sentinel para acionar automaticamente ações ou executar um manual de procedimentos para responder aos alertas de segurança recebidos. O manual de procedimentos efetua ações, como o envio de notificações, a desativação de contas e o isolamento de redes problemáticas. 

- [Configurar a automatização dos fluxos de trabalho no Centro de Segurança](../../security-center/workflow-automation.md)

- [Configurar respostas automatizadas a ameaças no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Configurar respostas automatizadas a ameaças no Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="posture-and-vulnerability-management"></a>Gestão da Postura e da Vulnerabilidade

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão da Postura e da Vulnerabilidade](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Estabelecer configurações seguras para os serviços do Azure 

**Orientação**: As soluções de gestão de identidade e acesso da Microsoft ajudam a proteger o acesso a aplicações e recursos em todos os locais e na nuvem. É importante que as organizações sigam as melhores práticas de segurança para garantir que a sua implementação de identidade e gestão de acessos seja segura e mais resistente aos ataques. 

Com base na sua estratégia de implementação de identidade e gestão de acesso, a sua organização deve seguir as orientações de boas práticas da Microsoft para garantir a sua infraestrutura de identidade. 

As organizações que colaboram com parceiros externos devem ainda avaliar e implementar configurações adequadas de governação, segurança e conformidade para reduzir o risco de segurança e proteger recursos sensíveis.

- [Melhores práticas de segurança de controlo de acesso e Gestão de Identidades do Azure](../../security/fundamentals/identity-management-best-practices.md)

- [Cinco passos para garantir a sua infraestrutura de identidade](../../security/fundamentals/steps-secure-identity.md)

- [Assegurar a colaboração externa no Azure Ative Directory e microsoft 365](secure-external-access-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Suportar configurações seguras para os serviços do Azure

**Orientação**: O Microsoft Secure Score fornece às organizações uma medição da sua postura de segurança e recomendações que podem ajudar a proteger as organizações de ameaças. Recomenda-se que as organizações revejam rotineiramente a sua Pontuação Segura para ações de melhoria sugeridas para melhorar a sua postura de segurança de identidade. 

- [Qual é a pontuação de identidade segura no Azure Ative Directory?](identity-secure-score.md)

- [Classificação de Segurança da Microsoft](/microsoft-365/security/mtp/microsoft-secure-score)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulações de ataques regulares

**Orientação**: Conforme necessário, faça testes de penetração ou atividades de "equipa de ataque" nos seus recursos do Azure e assegure a remediação de todas as descobertas de segurança críticas.
Para ter a certeza de que os seus testes de penetração não infringem as políticas da Microsoft, siga as Regras de Interação para Testes de Penetração da Microsoft Cloud. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.

- [Testes de Penetração no Azure](../../security/fundamentals/pen-testing.md)

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="governance-and-strategy"></a>Governação e Estratégia

*Para obter mais informações, veja [Referência de Segurança do Azure: Governação e Estratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definir a gestão dos ativos e a estratégia de proteção de dados 

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para a monitorização e proteção contínua dos sistemas e dos dados. Priorize a descoberta, a avaliação, a proteção e a monitorização de dados e sistemas críticos para a empresa. 
 

 
Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 
 
-   Norma para a classificação de dados, de acordo com os riscos da atividade
 
-   Visibilidade da organização de segurança para os riscos e inventário de ativos 
 
-   Aprovação da organização de segurança dos serviços do Azure a utilizar 
 
-   Segurança dos ativos ao longo do ciclo de vida
 
-   Estratégia de controlo de acesso obrigatória, de acordo com a classificação dos dados organizacionais
 
-   Utilização de capacidades de proteção de dados de terceiros e nativas do Azure
 
-   Requisitos de encriptação de dados para casos de utilização de dados em trânsito e inativos
 
-   Normas criptográficas adequadas
 

 
Para obter mais informações, veja as seguintes referências: 
- [Recomendação de Arquitetura de Segurança do Azure - Armazenamento, dados e encriptação](/azure/architecture/framework/security/storage-data-encryption)
 

 
- [Noções Básicas da Segurança do Azure - Segurança, encriptação e armazenamento de dados do Azure](../../security/fundamentals/encryption-overview.md) 

 
- [Cloud Adoption Framework - Melhores práticas de segurança e encriptação de dados do Azure](../../security/fundamentals/data-encryption-best-practices.md) 

 
- [Referência de Segurança do Azure - Gestão de ativos](/azure/security/benchmarks/security-benchmark-v2-asset-management) 

 
- [Referência de Segurança do Azure - Proteção de dados](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definir a estratégia de segmentação da empresa 

**Orientação**: Estabeleça uma estratégia ao nível de toda a empresa para segmentar o acesso aos ativos através de uma combinação de controlos de identidade, rede, aplicações, subscrições, grupos de gestão, entre outros.

Equilibre cuidadosamente a necessidade de separação da segurança com a necessidade de permitir o funcionamento diário dos sistemas que têm de comunicar entre si e aceder a dados.

Certifique-se de que a estratégia de segmentação está implementada de forma consistente em todos os tipos de controlos, incluindo segurança de rede, modelos de identidade e acesso e modelos de permissão/acesso a aplicações e controlos de processos humanos.

- [Orientação para a estratégia de segmentação no Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Orientação para a estratégia de segmentação no Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definir a estratégia de gestão da postura de segurança

**Orientação**: Meça e mitigue continuamente os riscos para os seus ativos individuais e para o ambiente em que estão alojados. Dê prioridade aos ativos de valor alto e a superfícies de ataque muito expostas, como aplicações publicadas, pontos de entrada e saída de rede, pontos finais de utilizador e administrador, etc.

- [Referência de Segurança do Azure - Gestão da postura e das vulnerabilidades](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alinhar as funções, as responsabilidades e as responsabilizações na organização

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para as funções e responsabilidades na sua organização de segurança. Estabeleça prioridades ao indicar de forma clara a responsabilização quanto às decisões de segurança, explicando o modelo de responsabilização partilhada a todos e explicando às equipas técnicas a tecnologia para proteger a cloud.

- [Melhor Prática de Segurança do Azure 1 – Pessoas: Explicar às Equipas o Percurso da Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Melhor Prática de Segurança do Azure 2 – Pessoas: Explicar às Equipas a Tecnologia de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Melhor Prática de Segurança do Azure 3 – Processo: Atribuir Responsabilização Quanto às Decisões de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definir uma estratégia de segurança de rede

**Orientação**: Crie uma abordagem à segurança de rede do Azure como parte da estratégia de controlo de acesso de segurança geral da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Gestão centralizada da rede e responsabilidade quanto à segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação empresarial

-   Estratégia de remediação em diferentes cenários de ameaças e ataques

-   Estratégia de entrada e saída do edge da Internet

-   Estratégia de interconectividade entre a cloud híbrida e o ambiente no local

-   Artefactos de segurança de rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para obter mais informações, veja as seguintes referências:
- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Referência de Segurança do Azure - Segurança de Rede](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Descrição geral da segurança de rede do Azure](../../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura da rede empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definir a estratégia de identidades e acessos privilegiados

**Orientação**: Crie uma abordagem às identidades e aos acessos privilegiados do Azure como parte da estratégia de controlo de acesso de segurança geral da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Um sistema de identidades e autenticação centralizado e respetiva interconectividade com outros sistemas de identidades internas e externas

-   Métodos de autenticação fortes em diferentes casos de utilização e condições

-   Proteção de utilizadores com muitos privilégios

-   Monitorização e processamento de atividades anómalas de utilizadores  

-   Processo de revisão e reconciliação de identidades e acessos dos utilizadores

Para obter mais informações, veja as seguintes referências:

- [Referência de Segurança do Azure - Gestão de identidades](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Referência de Segurança do Azure - Acesso privilegiado](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Descrição geral da segurança da gestão de identidades do Azure](../../security/fundamentals/identity-management-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta a ameaças e registos

**Orientação**: Crie uma estratégia de resposta a ameaças e registos para detetar e remediar rapidamente as ameaças e cumprir, em paralelo, os requisitos de conformidade. Dê prioridade a proporcionar aos analistas alertas de alta qualidade e experiências totalmente integradas, para que se possam focar nas ameaças em vez de se focarem na integração e passos manuais. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   As funções e as responsabilidades da organização quanto às operações de segurança (SecOps) 

-   Processo de resposta a incidentes bem definido e alinhado com as normas da agência norte-americana NIST ou outro framework da indústria 

-   Captura e retenção de registos para suportar a deteção de ameaças, a resposta a incidentes e as necessidades de conformidade

-   Visibilidade centralizada e correlação de informações sobre ameaças, mediante a utilização de SIEM, das capacidades nativas do Azure e de outras origens 

-   Plano de comunicação e notificação com os seus clientes, fornecedores e partes interessadas públicas

-   Utilização de plataformas nativas do Azure e de terceiros para processamento de incidentes, como registo e deteção de ameaças, análises forenses e remediação e erradicação de ataques

-   Processos para lidar com incidentes e atividades pós-incidentes, como lições aprendidas e retenção de provas

Para obter mais informações, veja as seguintes referências:

- [Referência de Segurança do Azure - Registos e deteção de ameaças](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Referência de Segurança do Azure - Resposta a incidentes](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Melhor Prática de Segurança do Azure 4 – Processo. Atualizar os Processos de Resposta a Incidentes para a Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, registos e guia de decisão de relatórios](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gestão e monitorização empresarial do Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
