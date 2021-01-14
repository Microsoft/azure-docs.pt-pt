---
title: Linha de segurança Azure para aplicações geridas azure
description: A linha de base de segurança das aplicações geridas Azure fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: managed-applications
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ca49e7c171d0b8903beccf0b1ca803f5b359951b
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201710"
---
# <a name="azure-security-baseline-for-azure-managed-applications"></a>Linha de segurança Azure para aplicações geridas azure

Esta linha de base de segurança aplica orientações da [versão 2.0 do Azure Security Benchmark](../../security/benchmarks/overview.md) para aplicações geridas aZure. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos controlos de **segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável às Aplicações Geridas Azure. Foram excluídos **os controlos** não aplicáveis às Aplicações Geridas Azure.

Para ver como as aplicações geridas pelo Azure mapeiam completamente o Benchmark de Segurança Azure, consulte o ficheiro completo de [mapeamento de base de base de aplicações geridas Azure.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: Simplificar as regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtual Azure para definir controlos de acesso à rede em grupos de segurança de rede ou firewall Azure configurados para os seus recursos de aplicação gerido azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (Por exemplo: AzureResourceManager) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Utilize tags de serviço do Gestor de Recursos Azure](../../virtual-network/service-tags-overview.md#available-service-tags)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gestão de Identidades

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Identidades](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Uniformizar o Azure Active Directory como o sistema central de identidade e autenticação

**Orientação**: As aplicações geridas azure utilizam o Azure Ative Directory (Azure AD) como o serviço de gestão de identidade e acesso padrão. Padronize na Azure AD para governar a gestão de identidade e acesso da sua organização.

A Azure fornece as seguintes funções incorporadas do Azure para autorizar o acesso a Aplicações Geridas utilizando Azure AD e OAuth:

- Função de contribuinte de aplicação gerida: Permite a criação de recursos de aplicação geridos.

- Função de operador de aplicação gerida: Permite-lhe ler e executar ações sobre recursos de aplicação gerida

- Leitor de Aplicações Geridas: Permite-lhe ler recursos numa aplicação gerida e solicitar acesso ao JIT.

Para obter mais informações, veja as seguintes referências:

- [Papel de contribuinte de aplicação gerido](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Papel do operador de aplicação gerido](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Leitor de aplicações geridos](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Inquilinos no Azure Active Directory](../../active-directory/develop/single-and-multi-tenant-apps.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gerir identidades de aplicação de forma segura e automática

**Orientação**: Utilize identidades geridas pelo Azure para conceder permissões às suas Aplicações Geridas. Recomenda-se a utilização de funcionalidades de identidade geridas pelo Azure em vez de criar uma conta humana mais poderosa para aceder ou executar os seus recursos para limitar a necessidade de gerir credenciais adicionais. O serviço de Aplicações Geridas Azure também pode ser atribuído a uma identidade gerida para autenticar de forma nativa outros serviços/recursos Azure que suportem a autenticação AZURE AD. Isto pode ser útil para permitir um fácil acesso a partir das aplicações geridas a Azure para a Azure Key Vault ao recuperar segredos. Ao utilizar identidades geridas, a identidade é gerida pela plataforma Azure e não requer que forneça ou rode quaisquer segredos.

As Aplicações Geridas Azure suportam que a sua aplicação seja concedida dois tipos de identidades:

- Uma identidade atribuída ao sistema está ligada ao seu recurso de configuração. É eliminado se o seu recurso de configuração for eliminado. Um recurso de configuração só pode ter uma identidade atribuída ao sistema.

- Uma identidade atribuída ao utilizador é um recurso autónomo da Azure que pode ser atribuído ao seu recurso de configuração. Um recurso de configuração pode ter várias identidades atribuídas pelo utilizador.

Quando as identidades geridas não puderem ser alavancadas, crie um principal de serviço com permissões restritas ao nível de recursos da Aplicação Gerida Azure. Configure estes princípios de serviço com credenciais de certificado e apenas recue para os segredos dos clientes. Em ambos os casos, o Azure Key Vault pode ser usado em conjunto com identidades geridas aZure, de modo que o ambiente de tempo de execução (por exemplo, uma função Azure) pode recuperar a credencial do cofre da chave.

- [Como utilizar identidades geridas para aplicações geridas azure](publish-managed-identity.md)

- [Identidades geridas por Azure](../../active-directory/managed-identities-azure-resources/overview.md)

- [Principal de serviço do Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Use o cofre da chave Azure para o registo principal de segurança](../../key-vault/general/authentication.md#app-identity-and-security-principals)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Utilizar o início de sessão único (SSO) do Azure AD para acesso a aplicações

**Orientação**: As aplicações geridas pela Azure utilizam o Azure Ative Directory para fornecer gestão de identidade e acesso aos recursos Azure, aplicações em nuvem e aplicações no local. Essas identidades incluem identidades empresariais, como colaboradores, bem como identidades externas, como parceiros, fornecedores e distribuidores. Com isto, o início de sessão único (SSO) pode gerir e proteger o acesso aos dados e recursos da sua organização, tanto no ambiente no local, como na cloud. Ligue todos os seus utilizadores, aplicações e dispositivos ao Azure AD para beneficiar de acesso seguro e ininterrupto e de mais visibilidade e controlo.

- [Compreender o SSO de Aplicações com o Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilizar controlos de autenticação fortes para todos os acessos baseados no Azure Active Directory

**Orientação**: As aplicações geridas azure utilizam o Azure Ative Directory que suporta controlos de autenticação forte através da autenticação multifactor e métodos fortes sem palavras-passe.
- Autenticação multifactor - Ativar a autenticação multifactor Azure AD e seguir as recomendações do Azure Security Center Identity and Access Management para algumas boas práticas na configuração de autenticação multifactor. A autenticação multifactor pode ser aplicada em todos, selecione utilizadores ou ao nível por utilizador com base em condições de inscrição e fatores de risco.
- Autenticação sem palavra-passe - estão disponíveis três opções de autenticação sem palavra-passe: Windows Hello para Empresas, aplicação Microsoft Authenticator e métodos de autenticação no local, como cartões inteligentes.

Para administradores e utilizadores privilegiados, certifique-se de que é utilizado o mais alto nível de autenticação forte.

- [Como permitir a autenticação multifactor em Azure](../../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introdução às opções de autenticação sem palavra-passe para o Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorizar e alertar para anomalias em contas

**Orientação**: As aplicações geridas azure são integradas com o Azure Ative Directory no qual fornece as seguintes fontes de dados:
- Inícios de sessão - o relatório de inícios de sessão faculta informações sobre a utilização de aplicações geridas e das atividades de início de sessão dos utilizadores.
- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas.
- Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.
- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

Estas fontes de dados podem ser integradas com sistemas Azure Monitor, Azure Sentinel ou siem de terceiros.

O Centro de Segurança do Azure também pode alertar para determinadas atividades suspeitas, como tentativas excessivas de autenticações mal-sucedidas ou contas descontinuadas na subscrição.

A Proteção Avançada Contra Ameaças (ATP) do Azure é uma solução de segurança que pode utilizar sinais do Active Directory para identificar, detetar e investigar ameaças avançadas, identidades comprometidas e ações internas maliciosas.

- [Relatórios de atividades de auditoria no Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como ver os inícios de sessão de risco do Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../../security-center/security-center-identity-access.md)

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso aos recursos do Azure com base em condições

**Orientação**: As Aplicações Geridas Azure suportam o acesso condicional do Azure AD a um controlo de acesso mais granular baseado em condições definidas pelo utilizador, como os logins do utilizador de determinadas gamas IP terão de utilizar a autenticação multifactor para o login. A política de gestão de sessão de autenticação granular também pode ser utilizada para diferentes casos de utilização.

- [Descrição geral do acesso condicional do Azure](../../active-directory/conditional-access/overview.md) 

- [Políticas de acesso condicional comuns](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurar a gestão da sessão de autenticação com o acesso condicional](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para obter mais informações, veja [Referência de Segurança do Azure: Acesso Privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Proteger e limitar utilizadores com muitos privilégios

**Orientação**: As aplicações geridas azure utilizam o Azure Ative Directory (Azure AD) para identidade e acesso. As funções mais críticas incorporadas são a Azure AD são o Administrador Global e o Administrador de Função Privilegiado, uma vez que os utilizadores designados para estas duas funções podem delegar funções de administrador:
- Administrador Global / Administrador da Empresa: Os utilizadores com esta função têm acesso a todas as funcionalidades administrativas em Azure AD, bem como serviços que utilizam identidades AZure AD.
- Administrador privilegiado: Os utilizadores com esta função podem gerir atribuições de funções em Azure AD, bem como dentro da Azure AD Privileged Identity Management (PIM). Além disso, esta função permite a gestão de todos os aspetos da PIM e das unidades administrativas.

Nota: Pode ter outras funções críticas que precisam de ser regidas se utilizar funções personalizadas com determinadas permissões privilegiadas atribuídas. E também pode querer aplicar controlos semelhantes à conta de administrador de ativos empresariais críticos.

Pode ativar o acesso privilegiado just-in-time (JIT) nos recursos do Azure e no Azure AD com o Azure AD Privileged Identity Management (PIM). O JIT concede permissões temporárias para realizar tarefas privilegiadas apenas quando os utilizadores precisam. O PIM também pode gerar alertas de segurança em caso de atividades suspeitas ou inseguras na sua organização do Azure AD.

- [Permissões das funções de administrador no Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Utilizar os alertas de segurança do Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos da empresa

**Orientação**: As aplicações geridas azure usam o Azure RBAC para isolar o acesso a sistemas críticos de negócio, limitando quais as contas que têm acesso privilegiado às subscrições e grupos de gestão em que se encontram.

Certifique-se de que também restringe o acesso aos sistemas de gestão, identidade e segurança que tenham acesso administrativo ao seu negócio de acesso crítico, tais como Controladores de Domínio de Diretório Ativo (DCs), ferramentas de segurança e ferramentas de gestão do sistema com agentes instalados em sistemas críticos de negócio. Os atacantes que comprometem estes sistemas de gestão e segurança podem imediatamente armar-los para comprometer ativos críticos do negócio.

Todos os tipos de controlos de acesso devem ser alinhados com a sua estratégia de segmentação da empresa para garantir um controlo de acesso consistente.

- [Componentes Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Acesso ao Grupo de Gestão](../../governance/management-groups/overview.md#management-group-access)

- [Administradores de subscrição da Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Rever e reconciliar o acesso dos utilizadores regularmente

**Orientação**: Rever regularmente as contas dos utilizadores e a atribuição de acesso para garantir que as contas e o seu nível de acesso são válidos.

As Aplicações Geridas Azure utilizam contas Azure Ative (AAD) para gerir os seus recursos, rever as contas dos utilizadores e aceder regularmente à atribuição de acesso para garantir que as contas e o seu acesso são válidos. Você pode usar comentários de acesso Azure AD para rever membros do grupo, acesso a aplicações empresariais e atribuições de funções. O relatório AD AD do Azure pode fornecer registos para ajudar a descobrir contas velhas. Também pode utilizar a Azure AD Privileged Identity Management para criar o fluxo de trabalho do relatório de revisão de acessos para facilitar o processo de revisão.

Além disso, a Azure Privileged Identity Management também pode ser configurada para alertar quando um número excessivo de contas de administrador é criado, e para identificar contas de administrador que estão incompras ou configuradas indevidamente.

Nota: Alguns serviços da Azure apoiam utilizadores locais e funções que não foram geridas através do Azure AD. Terá de gerir estes utilizadores separadamente.

- [Criar uma revisão de acesso das funções de recursos da Azure na Gestão de Identidade Privilegiada (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Como utilizar as revisões de identidades e acessos do Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configurar o acesso de emergência no AAD

**Orientação**: A Azure Managed Applications usa o Azure Ative Directory para gerir os seus recursos. Para evitar que seja acidentalmente bloqueado fora da sua organização Azure AD, crie uma conta de acesso de emergência para acesso quando não puder ser utilizada uma conta administrativa normal. As contas de acesso de emergência são, normalmente, altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a cenários de emergência ou de “interrupção de emergência”, em que as contas administrativas normais não podem ser utilizadas.

Deve garantir que as credenciais (por exemplo, palavra-passe, certificado ou smart card) das contas de acesso de emergência são mantidas em segurança e só são conhecidas por indivíduos que estão autorizados a utilizá-las apenas para uma emergência.

- [Gerir contas de acesso de emergência no AAD](../../active-directory/roles/security-emergency-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Gestão de direitos de automatização 

**Orientação**: As Aplicações Geridas aZURE estão integradas com o Azure Ative Directory para gerir os seus recursos. Utilize funcionalidades de gestão de direitos Azure AD para automatizar fluxos de trabalho de pedidos de acesso, incluindo atribuições de acesso, revisões e expiração. A aprovação dual ou multi-fase também é apoiada.

- [O que são avaliações de acesso AZure AD](../../active-directory/governance/access-reviews-overview.md)

- [O que é a gestão de direitos da AD Azure](../../active-directory/governance/entitlement-management-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Utilizar estações de trabalho privilegiadas

**Orientação**: As estações de trabalho seguras e isoladas são de importância crítica para a segurança de funções confidenciais, por exemplo, administradores, programadores e operadores de serviço de importância crítica. Utilize estações de trabalho de utilizador altamente seguras e/ou Bastião Azure para tarefas administrativas relacionadas com as suas Aplicações Geridas. Utilize o Azure Active Directory, a Proteção Avançada Contra Ameaças do Microsoft Defender (ATP) e/ou o Microsoft Intune para implementar uma estação de trabalho de utilizador gerida e segura para tarefas administrativas. As estações de trabalho seguras podem ser geridas centralmente para impor uma configuração segura, incluindo uma autenticação forte e linhas de base de software e hardware e acesso de rede e lógico restrito.

- [Compreender estações de trabalho de acesso privilegiada](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Implementar uma estação de trabalho de acesso privilegiado](../../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Seguir a abordagem de Administração Suficiente (princípio do privilégio mínimo) 

**Orientação**: As aplicações geridas pelo Azure estão integradas no controlo de acesso baseado em funções (RBAC) para gerir os seus recursos. O RBAC do Azure permite-lhe gerir o acesso aos recursos do Azure através da atribuição de funções. Pode atribuir estas funções aos utilizadores, grupos de diretores de serviço e identidades geridas. Existem funções incorporadas predefinidas para determinados recursos, que podem ser inventariadas ou consultadas com ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure. Os privilégios que atribuir aos recursos através do RBAC do Azure devem estar sempre limitados àquilo que as funções requerem. Esta abordagem complementa a abordagem just-in-time (JIT) do Azure AD Privileged Identity Management (PIM) e deve ser revista periodicamente.

Sempre que possível, utilize funções incorporadas para alocar a permissão e apenas crie um papel personalizado quando necessário.

A Azure fornece as seguintes funções incorporadas do Azure para autorizar o acesso a Aplicações Geridas utilizando Azure AD e OAuth:

- Função de contribuinte de aplicação gerida: Permite a criação de recursos de aplicação geridos.

- Função de operador de aplicação gerida: Permite-lhe ler e executar ações sobre recursos de aplicação gerida

- Leitor de Aplicações Geridas: Permite-lhe ler recursos numa aplicação gerida e solicitar acesso ao JIT.

Para obter mais informações, veja as seguintes referências:

- [Papel de contribuinte de aplicação gerido](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Papel do operador de aplicação gerido](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Leitor de aplicações geridos](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [O que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Escolha o processo de aprovação para suporte da Microsoft  

**Orientação**: Implementar um processo de aprovação organizacional para cenários de suporte onde a Microsoft possa precisar de acesso aos seus dados de Aplicação Gerida aZure. O Lockbox do cliente não está atualmente disponível para cenários de Aplicação Gerida.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteger dados confidenciais

**Orientação**: Para trazer encriptação com as suas próprias teclas, pode utilizar a sua própria conta de armazenamento para o armazenamento dos ficheiros de configuração da Aplicação Gerida.

- [Proteção de dados de ficheiros de configuração geridos usando o seu próprio armazenamento](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Encripte os dados confidenciais inativos

**Orientação**: Definições de aplicação geridas que definem a sua aplicação e os seus recursos podem ser armazenadas nas suas próprias contas de armazenamento, que podem ser configuradas com chaves de encriptação geridas pelo cliente.

Para cenários em que não queira trazer o seu próprio armazenamento para definições de Aplicação Gerida, o Azure fornece dados em repouso encriptação por padrão.

- [Traga o seu próprio armazenamento para definições de Aplicação Gerida](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Compreender a encriptação de dados inativos no Azure](../../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Como configurar chaves de encriptação geridas pelo cliente](/azure/storage/common/storage-encryption-keys-portal)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

## <a name="asset-management"></a>Gestão de Recursos

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Ativos](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Certifique-se de que a equipa de segurança tem visibilidade para os riscos dos ativos

**Orientação**: Confirme que as equipas de segurança recebem as permissões Leitor de Segurança no seu inquilino e nas suas subscrições do Azure, para que possam monitorizar os riscos de segurança com o Centro de Segurança do Azure. 

Dependendo da forma como as responsabilidades destas equipas são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Dito isto, as informações e os riscos de segurança têm de ser sempre agregados centralmente nas organizações. 

As permissões Leitor de Segurança podem ser aplicadas de um modo amplo em todo um inquilino (Grupo de Gestão Raiz) ou dentro de âmbitos, como grupos de gestão ou subscrições específicas. 

Nota: para obter visibilidade para cargas de trabalho e serviços, poderão ser necessárias permissões adicionais. 

- [Descrição Geral da Função de Leitor de Segurança](../../role-based-access-control/built-in-roles.md#security-reader)

- [Descrição Geral dos Grupos de Gestão do Azure](../../governance/management-groups/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Garantir que a equipa de segurança tem acesso aos metadados e inventário dos recursos

**Orientação**: Aplique etiquetas nos seus recursos Azure, grupos de recursos e subscrições para organizá-las logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

As etiquetas que são fornecidas no momento da criação da aplicação gerida também são aplicadas ao Grupo de Recursos Geridos. O editor da sua aplicação pode fornecer a sua própria marcação adicional dos recursos geridos após a implantação.

- [Tags por recurso Gerido Elemento UI Applicaiton](microsoft-common-tagsbyresource.md)

- [Como criar consultas com o Explorador do Azure Resource Graph](../../governance/resource-graph/first-query-portal.md) 

- [Para obter mais informações sobre a marcação de ativos, consulte o guia de decisão de nomeação e marcação de recursos](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Utilizar apenas os serviços do Azure aprovados

**Orientação**: As aplicações geridas azure suportam implementações baseadas em recursos Azure e aplicação de configuração utilizando a Política Azure. Utilize o Azure Policy para auditar e restringir que serviços os utilizadores podem aprovisionar no ambiente. Utilize o Azure Resource Graph para consultar e detetar recursos dentro das subscrições. Também pode utilizar o Azure Monitor para criar regras para acionar alertas quando um serviço não aprovado for detetado.

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../../governance/policy/samples/built-in-policies.md#general)

- [Como criar consultas com o Explorador do Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garanta a segurança da gestão do ciclo de vida dos ativos

**Orientação**: Os recursos de aplicação geridos e o seu grupo de recursos geridos conectados podem ser eliminados eliminando o recurso de aplicação gerido. Quando um recurso de aplicação gerido for eliminado, também eliminará o grupo de recursos gerido e o seu conteúdo. As capacidades adicionais do ciclo de vida são especificadas pelo editor da aplicação, onde podem conferido ao consumidor direitos adicionais sobre o ciclo de vida dos recursos geridos subjacentes através de ações permitidas. Consulte o editor da sua aplicação gerida para que recursos são geridos pelo consumidor.

- [Limpar recursos de aplicação gerida](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider?tabs=azurecli-interactive#clean-up-resources)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Registos e Deteção de Ameaças

*Para obter mais informações, veja [Referência de Segurança do Azure: Registos e Deteção de Ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Ative a deteção de ameaças para a gestão de identidades e acessos do Azure

**Orientação**: A Azure AD fornece os seguintes registos de utilizador que podem ser visualizados em relatórios AZure AD ou integrados com Azure Monitor, Azure Sentinel ou outras ferramentas de monitorização/monitorização do SIEM/para casos de monitorização e análise mais sofisticados: Logins – O relatório de login fornece informações sobre a utilização de aplicações geridas e atividades de login do utilizador.
Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas.
Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.
Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

O Centro de Segurança do Azure também pode alertar para determinadas atividades suspeitas, como tentativas excessivas de autenticações mal-sucedidas ou contas descontinuadas na subscrição. Além da monitorização básica da higiene de segurança, o módulo Proteção Contra Ameaças do Centro de Segurança do Azure também pode recolher alertas de segurança mais profundos de recursos de computação do Azure individuais (máquinas virtuais, contentores, serviços de aplicações), recursos de dados (DB SQL e armazenamento) e camadas de serviços do Azure. Esta capacidade dá-lhe visibilidade sobre anomalias de conta dentro dos recursos individuais.

- [Relatórios de atividades de auditoria no Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Ativar o Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Proteção contra ameaças no Centro de Segurança do Azure](/azure/security-center/threat-protection)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Ativar o registo dos recursos do Azure

**Orientação**: Os registos de atividade, que estão automaticamente disponíveis, contêm todas as operações de escrita (PUT, POST, DELETE) para os seus recursos de aplicação geridos, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro na resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md) 

- [Compreenda o registo e diferentes tipos de registo em Azure](../../azure-monitor/platform/platform-logs-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a análise e gestão do registo de segurança

**Orientação**: Centralizar o registo, armazenamento e análise para permitir a correlação. Para cada fonte de registo, certifique-se de que atribuiu um titular de dados, orientação de acesso, localização de armazenamento, que ferramentas são usadas para processar e aceder aos dados, e requisitos de retenção de dados.
Certifique-se de que está a integrar os registos de atividade do Azure na sua sessão central. Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Além disso, ative e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados frequentemente e o Azure Storage para dados "frios" que são usados com menos frequência.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md)

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Configurar a retenção de armazenamento dos registos

**Orientação**: Certifique-se de que quaisquer contas de armazenamento ou espaços de trabalho do Log Analytics utilizados para armazenar registos criados pelos recursos de aplicação geridos tem o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização.
No Azure Monitor, pode definir o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize o espaço de trabalho Azure Storage, Data Lake ou Log Analytics para armazenamento de longo prazo e arquivo.

- [Como configurar o período de retenção do espaço de trabalho do Log Analytics](../../azure-monitor/platform/manage-cost-storage.md)

- [Armazenar registos de recursos numa conta de armazenamento Azure](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

**Orientação**: Certifique-se de que a sua organização tem processos para responder a incidentes de segurança, que atualizou esses processos para o Azure e que os pratica regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparação – configurar a notificação de incidentes

**Orientação**: Configure as informações de contacto dos incidentes de segurança no Centro de Segurança do Azure. A Microsoft utiliza estas informações de contacto para o contactar caso o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos de forma ilícita ou não autorizada. Também tem opções para personalizar os alertas e as notificações de incidentes em diferentes serviços do Azure de acordo com as suas necessidades de resposta aos incidentes. 

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes com base em alertas de alta qualidade

**Orientação**: garanta que tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Desta forma, pode aprender lições com os incidentes passados e priorizar os alertas para os analistas, para que estes não percam tempo a lidar com falsos positivos. 

Podem ser desenvolvidos alertas de alta qualidade com base na experiência de incidentes passados, origens da comunidade validadas e ferramentas designadas para gerar e limpar alertas através da fusão e correlação de origens de sinais diversas. 

O Azure Security Center fornece alertas de alta qualidade em muitos ativos da Azure. Pode utilizar o conector de dados ASC para transmitir os alertas para o Azure Sentinel. O Azure Sentinel permite-lhe criar regras de alertas avançadas para gerar incidentes automaticamente para investigações. 

Exporte os alertas e as recomendações do Centro de Segurança do Azure com a funcionalidade de exportação para ajudar a identificar riscos para os recursos do Azure. Exporte os alertas e as recomendações manualmente ou de forma contínua.

- [Como configurar a exportação](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

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

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

**Orientação**: Dê contexto aos analistas quanto aos incidentes em que se devem concentrar primeiro tendo por base a gravidade dos alertas e a sensibilidade dos ativos. 

O Centro de Segurança do Azure atribui uma gravidade a cada alerta para o ajudar a priorizar os que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque os recursos com etiquetas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais. É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o processamento dos incidentes

**Orientação**: Automatize tarefas manuais repetitivas para acelerar o tempo de resposta e reduzir a carga dos analistas. As tarefas manuais são mais morosas e atrasam todos os incidentes, reduzindo o número daqueles com que um analista pode lidar. Estas tarefas também aumentam a fadiga dos analistas, o que aumenta o risco de erro humanos e provoca atrasos, bem como degrada a capacidade de aqueles se concentrarem em tarefas complexas. 

Utilize as funcionalidades de automatização de fluxos de trabalho do Centro de Segurança do Azure e do Azure Sentinel para acionar automaticamente ações ou executar um manual de procedimentos para responder aos alertas de segurança recebidos. O manual de procedimentos efetua ações, como o envio de notificações, a desativação de contas e o isolamento de redes problemáticas. 

- [Configurar a automatização dos fluxos de trabalho no Centro de Segurança](../../security-center/workflow-automation.md)

- [Configurar respostas automatizadas a ameaças no Centro de Segurança do Azure](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas automatizadas a ameaças no Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gestão da Postura e da Vulnerabilidade

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão da Postura e da Vulnerabilidade](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Estabelecer configurações seguras para os serviços do Azure 

**Orientação**: Defina os seguranças para infraestruturas e equipas de DevOps, facilitando a configuração segura dos serviços Azure que utilizam.

Configure A política de azure para auditar e impor configurações dos seus recursos relacionados com as implementações das suas Aplicações Geridas.

Você pode usar Azure Blueprints para automatizar a implementação e configuração de ambientes de serviços e aplicações, incluindo modelos de Gestor de Recursos Azure, atribuições de RBAC Azure e atribuições de Política Azure, numa única definição de projeto.

- [Zona de aterragem em escala de adoção em nuvem](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Criar e gerir políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Suportar configurações seguras para os serviços do Azure

**Orientação**: Utilize o Azure Security Center para monitorizar os seus recursos relacionados com aplicações geridas a Azure e utilize efeitos da Política Azure [negar] e [implementar se não existir] para sustentar configurações seguras.
- [Compreender os efeitos da Política Azure](../../governance/policy/concepts/effects.md)

- [Criar e gerir políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulações de ataques regulares

**Orientação**: Conforme necessário, faça testes de penetração ou atividades de "equipa de ataque" nos seus recursos do Azure e assegure a remediação de todas as descobertas de segurança críticas.
Para ter a certeza de que os seus testes de penetração não infringem as políticas da Microsoft, siga as Regras de Interação para Testes de Penetração da Microsoft Cloud. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.

- [Testes de Penetração no Azure](../../security/fundamentals/pen-testing.md)

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="backup-and-recovery"></a>Cópia de Segurança e Recuperação

*Para obter mais informações, veja [Referência de Segurança do Azure: Cópia de Segurança e Recuperação](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Ao armazenar as definições de Aplicação Gerida na sua própria conta de armazenamento, certifique-se de que pode restaurar quaisquer chaves geridas pelo cliente associadas que sejam utilizadas para a encriptação dessa conta que são armazenadas no Cofre da Chave Azure.

- [Traga o seu próprio armazenamento para definições de Aplicação Gerida](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Como restaurar chaves do Cofre chave em Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Mitigar o risco de perda de chaves

**Orientação**: Se estiver a trazer o seu próprio armazenamento para as definições de Aplicação Gerida, certifique-se de que dispõe de medidas para prevenir e recuperar da perda de chaves utilizadas para encriptar as suas definições. Ativar a proteção de eliminação e purga suave no Cofre da Chave Azure, que armazena as chaves geridas pelo cliente para proteger as chaves contra a eliminação acidental ou maliciosa.  

- [Traga o seu próprio armazenamento para definições de Aplicação Gerida](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Como ativar a eliminação recuperável e a proteção contra remoção no Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

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
- [Recomendação de Arquitetura de Segurança do Azure - Armazenamento, dados e encriptação](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Noções Básicas da Segurança do Azure - Segurança, encriptação e armazenamento de dados do Azure](../../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Melhores práticas de segurança e encriptação de dados do Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Referência de Segurança do Azure - Gestão de ativos](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Referência de Segurança do Azure - Proteção de dados](/azure/security/benchmarks/security-controls-v2-data-protection)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definir a estratégia de segmentação da empresa 

**Orientação**: Estabeleça uma estratégia ao nível de toda a empresa para segmentar o acesso aos ativos através de uma combinação de controlos de identidade, rede, aplicações, subscrições, grupos de gestão, entre outros.

Equilibre cuidadosamente a necessidade de separação da segurança com a necessidade de permitir o funcionamento diário dos sistemas que têm de comunicar entre si e aceder a dados.

Certifique-se de que a estratégia de segmentação está implementada de forma consistente em todos os tipos de controlos, incluindo segurança de rede, modelos de identidade e acesso e modelos de permissão/acesso a aplicações e controlos de processos humanos.

- [Orientação para a estratégia de segmentação no Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Orientação para a estratégia de segmentação no Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definir a estratégia de gestão da postura de segurança

**Orientação**: Meça e mitigue continuamente os riscos para os seus ativos individuais e para o ambiente em que estão alojados. Dê prioridade aos ativos de valor alto e a superfícies de ataque muito expostas, como aplicações publicadas, pontos de entrada e saída de rede, pontos finais de utilizador e administrador, etc.

- [Referência de Segurança do Azure - Gestão da postura e das vulnerabilidades](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alinhar as funções, as responsabilidades e as responsabilizações na organização

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para as funções e responsabilidades na sua organização de segurança. Estabeleça prioridades ao indicar de forma clara a responsabilização quanto às decisões de segurança, explicando o modelo de responsabilização partilhada a todos e explicando às equipas técnicas a tecnologia para proteger a cloud.

- [Melhor Prática de Segurança do Azure 1 – Pessoas: Explicar às Equipas o Percurso da Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Melhor Prática de Segurança do Azure 2 – Pessoas: Explicar às Equipas a Tecnologia de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Melhor Prática de Segurança do Azure 3 – Processo: Atribuir Responsabilização Quanto às Decisões de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

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

- [Referência de Segurança do Azure - Segurança de Rede](/azure/security/benchmarks/security-controls-v2-network-security)

- [Descrição geral da segurança de rede do Azure](../../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura da rede empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definir a estratégia de identidades e acessos privilegiados

**Orientação**: Crie uma abordagem às identidades e aos acessos privilegiados do Azure como parte da estratégia de controlo de acesso de segurança geral da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Um sistema de identidades e autenticação centralizado e respetiva interconectividade com outros sistemas de identidades internas e externas

-   Métodos de autenticação fortes em diferentes casos de utilização e condições

-   Proteção de utilizadores com muitos privilégios

-   Monitorização e processamento de atividades anómalas de utilizadores  

-   Processo de revisão e reconciliação de identidades e acessos dos utilizadores

Para obter mais informações, veja as seguintes referências:

- [Referência de Segurança do Azure - Gestão de identidades](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Referência de Segurança do Azure - Acesso privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Descrição geral da segurança da gestão de identidades do Azure](../../security/fundamentals/identity-management-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

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

- [Referência de Segurança do Azure - Registos e deteção de ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Referência de Segurança do Azure - Resposta a incidentes](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Melhor Prática de Segurança do Azure 4 – Processo. Atualizar os Processos de Resposta a Incidentes para a Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, registos e guia de decisão de relatórios](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gestão e monitorização empresarial do Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
