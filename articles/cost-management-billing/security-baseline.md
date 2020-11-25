---
title: Linha de base de segurança do Azure para Cost Management
description: A linha de base de segurança do Cost Management disponibiliza orientações procedimentais e recursos para implementar as recomendações de segurança especificadas na Referência de Segurança do Azure.
author: msmbaldwin
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3270dae1fd37913ba51ca1da63bbb44d715e7d31
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021784"
---
# <a name="azure-security-baseline-for-cost-management"></a>Linha de base de segurança do Azure para Cost Management

Esta linha de base de segurança aplica as orientações da [versão 2.0 da Referência de Segurança do Azure](../security/benchmarks/overview.md) no Azure Cost Management. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. Os conteúdos são agrupados pelos **controlos de segurança** definidos pela Referência de Segurança do Azure e pelas orientações associadas aplicáveis ao Cost Management. Os **controlos** não aplicáveis ao Cost Management foram excluídos.

Para ver de que forma é que o Cost Management é completamente mapeado para a Referência de Segurança do Azure, veja o [ficheiro de mapeamento completo da linha de base de segurança do Cost Management](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Gestão de Identidades

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Identidades](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Uniformizar o Azure Active Directory como o sistema central de identidade e autenticação

**Orientação**: O Azure Cost Management está integrado no Azure Active Directory (Azure AD) que é o serviço predefinido de gestão de identidades e acessos do Azure. Deverá uniformizar a utilização do Azure AD para reger a gestão de identidades e acessos da sua organização em:

- serviços cloud da Microsoft, como o portal do Azure, o Armazenamento do Azure, as Máquinas Virtuais do Azure (Linux e Windows), o Azure Key Vault e aplicações PaaS e SaaS.

- recursos da sua organização, como as aplicações no Azure, ou os recursos da rede empresarial.

A proteção do Azure AD deve ser prioritária na prática de segurança da cloud para a sua organização. O Azure AD atribui uma pontuação de segurança de identidade para o ajudar a avaliar a postura de segurança de identidade face às recomendações de melhores práticas da Microsoft. Utilize a pontuação para determinar até que ponto é que a sua configuração corresponde às recomendações de melhores práticas e para fazer melhorias à postura de segurança.

Nota: o Azure AD suporta fornecedores de identidade externos, o que permite que utilizadores sem contas Microsoft iniciem sessão nas respetivas aplicações e recursos com as identidades externas.

- [Inquilinos no Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Definir inquilinos do Azure AD](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [Utilizar fornecedores de identidade externos em aplicações](/azure/active-directory/b2b/identity-providers)

- [O que é a pontuação de segurança de identidade no Azure AD](../active-directory/fundamentals/identity-secure-score.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Utilizar o início de sessão único (SSO) do Azure AD para acesso a aplicações

**Orientação**: O Azure Cost Management utiliza o Azure Active Directory para proporcionar a gestão de identidades e acessos a recursos, aplicações cloud e aplicações no local do Azure. Essas identidades incluem identidades empresariais, como colaboradores, bem como identidades externas, como parceiros, fornecedores e distribuidores. Com isto, o início de sessão único (SSO) pode gerir e proteger o acesso aos dados e recursos da sua organização, tanto no ambiente no local, como na cloud. Ligue todos os seus utilizadores, aplicações e dispositivos ao Azure AD para beneficiar de acesso seguro e ininterrupto e de mais visibilidade e controlo.

- [Compreender o SSO de Aplicações com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilizar controlos de autenticação fortes para todos os acessos baseados no Azure Active Directory

**Orientação**: O Azure Cost Management está integrado no Azure AD, o qual suporta controlos de autenticação fortes através da autenticação multifator (MFA) e métodos sem palavra-passe fortes.

- Autenticação multifator - ative o MFA do Azure AD e siga as recomendações de Gestão de Identidades e Acessos do Centro de Segurança do Azure para obter algumas melhores práticas na configuração do MFA. O MFA pode ser imposto em todos os utilizadores, utilizadores específicos ou utilizador a utilizador com base em condições de início de sessão e fatores de risco.

- Autenticação sem palavra-passe - estão disponíveis três opções de autenticação sem palavra-passe: Windows Hello para Empresas, aplicação Microsoft Authenticator e métodos de autenticação no local, como cartões inteligentes.

Para administradores e utilizadores com privilégios, confirme que é utilizado o nível mais alto do método de autenticação forte, seguido da implementação da política de autenticação forte adequada para os restantes utilizadores.

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introdução às opções de autenticação sem palavra-passe para o Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Política de palavras-passe predefinidas do Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminar palavras-passe fracas com a Proteção de Palavras-passe do Azure AD](../active-directory/authentication/concept-password-ban-bad.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorizar e alertar para anomalias em contas

**Orientação**: O Azure Cost Management está integrado no Azure Active Directory, no qual disponibiliza as seguintes origens de dados:

- Inícios de sessão - o relatório de inícios de sessão faculta informações sobre a utilização de aplicações geridas e das atividades de início de sessão dos utilizadores.

- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas.

- Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

Estas origens de dados podem ser integradas no Azure Monitor, no Azure Sentinel ou noutros sistemas SIEM de terceiros.

O Centro de Segurança do Azure também pode alertar para determinadas atividades suspeitas, como tentativas excessivas de autenticações mal-sucedidas ou contas descontinuadas na subscrição.

A Proteção Avançada Contra Ameaças (ATP) do Azure é uma solução de segurança que pode utilizar sinais do Active Directory para identificar, detetar e investigar ameaças avançadas, identidades comprometidas e ações internas maliciosas.

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Como ver os inícios de sessão de risco do Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../security-center/security-center-identity-access.md) 

- [Alertas no módulo de proteção de informações sobre ameaças do Centro de Segurança do Azure](../security-center/alerts-reference.md) 

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para obter mais informações, veja [Referência de Segurança do Azure: Acesso Privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Proteger e limitar utilizadores com muitos privilégios

**Orientação**: O Cost Management para Contratos Enterprise (EA) tem a conta com muitos privilégios abaixo.

- Administrador do Enterprise (EA)

Recomenda-se rever de forma rotineira os utilizadores atribuídos a funções no seu Contrato Enterprise (EA).

Também se recomenda, como regra, que limite o número de contas ou funções com muitos privilégios e que proteja essas contas a um nível elevado, porque os utilizadores com esses privilégios pode ler e modificar, direta ou indiretamente, todos os recursos no seu ambiente do Azure.

Pode ativar o acesso privilegiado just-in-time (JIT) nos recursos do Azure e no Azure AD com o Azure AD Privileged Identity Management (PIM). O JIT concede permissões temporárias para realizar tarefas privilegiadas apenas quando os utilizadores precisam. O PIM também pode gerar alertas de segurança em caso de atividades suspeitas ou inseguras na sua organização do Azure AD.

- [Gerir as funções Enterprise do Azure](manage/understand-ea-roles.md) 

- [Permissões das funções de administrador no Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [Utilizar os alertas de segurança do Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Rever e reconciliar o acesso dos utilizadores regularmente

**Orientação**: O Azure Cost Management depende do acesso adequado para ver e gerir os dados de custos organizacionais. O acesso é controlado com o controlo de acesso baseado em funções do Azure (RBAC do Azure) ao nível da subscrição e através de funções administrativas com Contratos Enterprise (EA) ou Contratos de Cliente Microsoft (MCA) para âmbitos de faturação. Audite e reveja o acesso concedido regularmente para garantir que os utilizadores ou grupos têm o acesso necessário.

- [Gerir o acesso às informações de faturação do Azure](manage/manage-billing-access.md)

- [Atribuir acesso aos dados da Gestão de Custos](costs/assign-access-acm-data.md)

- [Gerir as funções Enterprise do Azure](manage/understand-ea-roles.md)

- [Compreender as funções administrativas do Contrato de Cliente da Microsoft no Azure](manage/understand-mca-roles.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Seguir a abordagem de Administração Suficiente (princípio do privilégio mínimo) 

**Orientação**: O Azure Cost Management está integrado no controlo de acesso baseado em funções do Azure (RBAC) para gerir recursos (por exemplo, orçamentos, relatórios guardados, etc.). O RBAC do Azure permite-lhe gerir o acesso aos recursos do Azure através da atribuição de funções. Pode atribuir essas funções a utilizadores, grupos e principais de serviço. Existem funções incorporadas predefinidas para determinados recursos, que podem ser inventariadas ou consultadas com ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure. Os privilégios que atribuir aos recursos através do RBAC do Azure devem estar sempre limitados àquilo que as funções requerem. Esta abordagem complementa a abordagem just-in-time (JIT) do Azure AD Privileged Identity Management (PIM) e deve ser revista periodicamente.

Utilize as funções incorporadas para alocar as permissões e crie funções personalizadas apenas quando for necessário.

O Azure Cost Management oferece funções incorporadas, leitores e contribuidores.

- [Leitor do Azure Cost Management](../role-based-access-control/built-in-roles.md#cost-management-reader)

- [Contribuidor do Azure Cost Management](../role-based-access-control/built-in-roles.md#cost-management-contributor)

O que é o controlo de acesso baseado em funções do Azure (RBAC do Azure) ../role-based-access-control/overview.md 

- [Como configurar o RBAC no Azure](../role-based-access-control/role-assignments-portal.md) 

- [Como utilizar as revisões de identidades e acessos do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Descobrir, classificar e etiquetar dados confidenciais

**Orientação**: Recomenda-se descobrir, classificar e etiquetar os seus dados confidenciais para que possa criar os controlos adequados e garantir que as informações sensíveis são armazenadas, processadas e transmitidas de forma segura pelos sistemas tecnológicos da organização.

Utilize o Azure Information Protection (e a ferramenta de análise associada do mesmo) para informações confidenciais em documentos do Office no Azure, em ambientes no local, no Office 365 e noutras localizações.

Pode utilizar o Azure SQL Information Protection para ajudar na classificação e etiquetagem das informações armazenadas nas bases de dados da Base de Dados SQL do Azure.

- [Etiquetar informações confidenciais com o Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Como implementar a Descoberta de Dados do SQL do Azure](/azure/sql-database/sql-database-data-discovery-and-classification)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteger dados confidenciais

**Orientação**: Recomenda-se proteger os dados confidenciais ao limitar o acesso com o Controlo de Acesso Baseado em Funções do Azure (RBAC do Azure), controlos de acesso baseados em rede e controlos específicos nos serviços do Azure (como a encriptação no SQL e noutras bases de dados).

Para garantir um controlo de acesso consistente, todos os tipos de controlo de acesso devem estar alinhados com a estratégia de segmentação da sua empresa. A estratégia de segmentação da empresa também deve ter conhecimento da localização dos dados confidenciais e dos sistemas críticos para a empresa.

Quanto à plataforma subjacente, que é gerida pela Microsoft, a Microsoft encara todos os conteúdos dos clientes como confidenciais e protege-os da perda e exposição dos dados de cliente. Para assegurar que os dados permanecem seguros no Azure, a Microsoft implementou alguns controlos e capacidades de proteção de dados predefinidos.

- [Atribuir acesso aos dados da Gestão de Custos](costs/assign-access-acm-data.md)

- [Controlo de Acesso Baseado em Funções do Azure (RBAC)](../role-based-access-control/overview.md) 

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Monitorizar a transferência não autorizada de dados confidenciais

**Orientação**: Monitorize a transferência não autorizada de dados para localizações fora da visibilidade e do controlo da empresa. Tipicamente, esta orientação envolve a monitorização de atividades anómalas (transferências grandes ou incomuns) que podem indicar a exfiltração não autorizada de dados.

A Proteção Avançada Contra Ameaças (ATP) do Azure e o ATP do SQL do Azure podem alertar para transferências anómalas de informações, as quais podem indicar transferências não autorizadas de informações confidenciais.

O Azure Information Protection (AIP) proporciona capacidades de monitorização de informações que tenham sido classificadas e etiquetadas.

Se necessário para a conformidade em termos da prevenção contra perda de dados (Data Loss Prevention, DLP), pode utilizar uma solução de DLP baseada em anfitrião para impor controlos de deteção e/ou prevenção para evitar a exfiltração de dados.

- [Ativar o ATP do SQL do Azure](../azure-sql/database/threat-detection-overview.md) 

- [Ativar o ATP do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Encripte os dados confidenciais inativos

**Orientação**: Para complementar os controlos de acesso, a funcionalidade Exportações do Azure Cost Management suporta a encriptação do Armazenamento do Azure de dados inativos para proteger contra ataques "fora de banda" (como aceder ao armazenamento subjacente) mediante a utilização de chaves de encriptação geridas pela Microsoft. Estas predefinições ajudam a garantir que os atacantes não conseguem ler ou modificar os dados facilmente.

Para obter mais informações, veja:

- [Azure Storage encryption for data at rest](../storage/common/storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)

- [Compreender a encriptação de dados inativos no Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="asset-management"></a>Gestão de Recursos

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Ativos](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Certifique-se de que a equipa de segurança tem visibilidade para os riscos dos ativos

**Orientação**: Confirme que as equipas de segurança recebem as permissões Leitor de Segurança no seu inquilino e nas suas subscrições do Azure, para que possam monitorizar os riscos de segurança com o Centro de Segurança do Azure. 

Dependendo da forma como as responsabilidades destas equipas são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Dito isto, as informações e os riscos de segurança têm de ser sempre agregados centralmente nas organizações. 

As permissões Leitor de Segurança podem ser aplicadas de um modo amplo em todo um inquilino (Grupo de Gestão Raiz) ou dentro de âmbitos, como grupos de gestão ou subscrições específicas. 

Nota: para obter visibilidade para cargas de trabalho e serviços, poderão ser necessárias permissões adicionais. 

- [Descrição Geral da Função de Leitor de Segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Descrição Geral dos Grupos de Gestão do Azure](../governance/management-groups/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garanta a segurança da gestão do ciclo de vida dos ativos

**Orientação**: Crie um atualize as políticas de segurança que visam os processos de gestão do ciclo de vida dos ativos para modificações com um potencial grande impacto. Essas modificações incluem alterações a fornecedores de identidade e acesso, confidencialidade dos dados, configuração de rede e atribuição de privilégios administrativos.

Quando já não precisar dos recursos do Azure, remova-os.

- [Eliminar grupos de recursos e recursos do Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Registos e Deteção de Ameaças

*Para obter mais informações, veja [Referência de Segurança do Azure: Registos e Deteção de Ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Ative a deteção de ameaças para a gestão de identidades e acessos do Azure

**Orientação**: O Azure AD disponibiliza os seguintes registos de utilizador, os quais podem ser vistos nos relatórios do Azure AD ou integrados no Azure Monitor, no Azure Sentinel ou noutras ferramentas de SIEM/monitorização para casos de utilização de monitorização e análise mais sofisticados:

- Inícios de sessão – o relatório de inícios de sessão faculta informações sobre a utilização de aplicações geridas e das atividades de início de sessão dos utilizadores.

- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas.

- Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

O Centro de Segurança do Azure também pode alertar para determinadas atividades suspeitas, como tentativas excessivas de autenticações mal-sucedidas ou contas descontinuadas na subscrição. Além da monitorização básica da higiene de segurança, o módulo Proteção Contra Ameaças do Centro de Segurança do Azure também pode recolher alertas de segurança mais profundos de recursos de computação do Azure individuais (máquinas virtuais, contentores, serviços de aplicações), recursos de dados (DB SQL e armazenamento) e camadas de serviços do Azure. Com esta capacidade, tem visibilidade para anomalias da conta dentro dos recursos individuais.

Também se recomenda rever de forma rotineira os utilizadores atribuídos a funções no seu Contrato Enterprise (EA). 

- [Gerir as funções Enterprise do Azure](manage/understand-ea-roles.md)

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Ativar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Proteção contra ameaças no Centro de Segurança do Azure](/azure/security-center/threat-protection)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

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

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes com base em alertas de alta qualidade

**Orientação**: Certifique-se de que tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Desta forma, pode aprender lições com os incidentes passados e priorizar os alertas para os analistas, para que estes não percam tempo a lidar com falsos positivos. 

Os alertas de alta qualidade podem ser criados a partir da experiência com incidentes anteriores, de origens validadas pela comunidade e de ferramentas desenhadas para gerar e limpar alertas ao combinar e correlacionar diversas origens de sinais. 

O Centro de Segurança do Azure proporciona alertas de alta qualidade em muitos ativos do Azure. Pode utilizar o conector de dados ASC para transmitir os alertas para o Azure Sentinel. O Azure Sentinel permite-lhe criar regras de alertas avançadas para gerar incidentes automaticamente para investigações. 

Exporte os alertas e as recomendações do Centro de Segurança do Azure com a funcionalidade de exportação para ajudar a identificar riscos para os recursos do Azure. Exporte os alertas e as recomendações manualmente ou de forma contínua.

- [Como configurar a exportação](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

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

- [Criar um instantâneo de um disco de uma máquina Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Criar um instantâneo de um disco de uma máquina Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informações de diagnóstico e recolha da captura da memória do Suporte do Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigar incidentes com o Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

**Orientação**: Dê contexto aos analistas quanto aos incidentes em que se devem concentrar primeiro tendo por base a gravidade dos alertas e a sensibilidade dos ativos. 

O Centro de Segurança do Azure atribui uma gravidade a cada alerta para o ajudar a priorizar os que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança tem na descoberta ou na análise utilizada para emitir o alerta, bem como no nível de confiança em que havia uma intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque os recursos com etiquetas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais.  É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o processamento dos incidentes

**Orientação**: Automatize tarefas manuais repetitivas para acelerar o tempo de resposta e reduzir a carga dos analistas. As tarefas manuais são mais morosas e atrasam todos os incidentes, reduzindo o número daqueles com que um analista pode lidar. Estas tarefas também aumentam a fadiga dos analistas, o que aumenta o risco de erro humanos e provoca atrasos, bem como degrada a capacidade de aqueles se concentrarem em tarefas complexas. Utilize as funcionalidades de automatização de fluxos de trabalho do Centro de Segurança do Azure e do Azure Sentinel para acionar automaticamente ações ou executar um manual de procedimentos para responder aos alertas de segurança recebidos. O manual de procedimentos efetua ações, como o envio de notificações, a desativação de contas e o isolamento de redes problemáticas. 

- [Configurar a automatização dos fluxos de trabalho no Centro de Segurança](../security-center/workflow-automation.md)

- [Configurar respostas automatizadas a ameaças no Centro de Segurança do Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas automatizadas a ameaças no Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gestão da Postura e da Vulnerabilidade

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão da Postura e da Vulnerabilidade](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Remedeie as vulnerabilidades do software de forma rápida e automática

**Orientação**: Implemente rapidamente atualizações de software para remediar vulnerabilidades do software em sistemas operativos e aplicações.

A priorização utiliza um programa de classificação de risco comum (por exemplo, o Common Vulnerability Scoring System) ou as classificações de risco predefinidas disponibilizadas pela sua ferramenta de análise de terceiros e ajusta ao seu ambiente através do contexto das aplicações que apresentam um risco de segurança alto e as que exigem um tempo de atividade elevado.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulações de ataques regulares

**Orientação**: Conforme necessário, faça testes de penetração ou atividades de "equipa de ataque" nos seus recursos do Azure e assegure a remediação de todas as descobertas de segurança críticas.
Para ter a certeza de que os seus testes de penetração não infringem as políticas da Microsoft, siga as Regras de Interação para Testes de Penetração da Microsoft Cloud. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.

- [Testes de Penetração no Azure](../security/fundamentals/pen-testing.md)

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

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

-   Utilização das capacidades de proteção de dados nativas do Azure e de terceiros

-   Requisitos de encriptação de dados para casos de utilização de dados em trânsito e inativos

-   Normas criptográficas adequadas

Para obter mais informações, veja as seguintes referências:
- [Recomendação de Arquitetura de Segurança do Azure - Armazenamento, dados e encriptação](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Noções Básicas da Segurança do Azure - Segurança, encriptação e armazenamento de dados do Azure](../security/fundamentals/encryption-overview.md)

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

- [Descrição geral da segurança de rede do Azure](../security/fundamentals/network-overview.md)

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

- [Descrição geral da segurança da gestão de identidades do Azure](../security/fundamentals/identity-management-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta a ameaças e registos

**Orientação**: Crie uma estratégia de resposta a ameaças e registos para detetar e remediar rapidamente as ameaças e cumprir, em paralelo, os requisitos de conformidade. Estabeleça prioridades ao disponibilizar aos analistas alertas de alta qualidade e experiências práticas para que se possam concentrar nas ameaças e não na integração e em passos manuais. 

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
