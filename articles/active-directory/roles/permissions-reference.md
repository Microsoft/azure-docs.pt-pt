---
title: Funções incorporadas da Azure AD - Azure Ative Directory
description: Descreve o Azure Ative Directory funções e permissões incorporadas.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 03/29/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad8466dca6634b0e72ef4a65acb537006dba3bda
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108545"
---
# <a name="azure-ad-built-in-roles"></a>Funções incorporadas do Azure AD

No Azure Ative Directory (Azure AD), se outro administrador ou não administrador precisar de gerir os recursos Azure AD, atribua-lhes uma função AD Azure que fornece as permissões de que necessitam. Por exemplo, pode atribuir funções para permitir adicionar ou alterar utilizadores, redefinir palavras-passe do utilizador, gerir licenças de utilizador ou gerir nomes de domínio.

Este artigo lista as funções incorporadas AD AZure que pode atribuir para permitir a gestão dos recursos AZure AD. Para obter informações sobre como atribuir funções, consulte [atribuir funções AZure AD aos utilizadores](manage-roles-portal.md).

## <a name="all-roles"></a>Todos os papéis

> [!div class="mx-tableFixed"]
> | Função | Description | ID do Modelo |
> | --- | --- | --- |
> | [Administrador de Aplicações](#application-administrator) | Pode criar e gerir todos os aspetos dos registos de aplicações e aplicações empresariais. | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [Programador de Aplicações](#application-developer) | Pode criar registos de aplicações independentemente da definição de "Os Utilizadores podem registar as aplicações". | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [Autor de carga útil de ataque](#attack-payload-author) | Pode criar cargas de ataque que um administrador pode iniciar mais tarde. | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [Administrador de simulação de ataque](#attack-simulation-administrator) | Pode criar e gerir todos os aspetos das campanhas de simulação de ataque. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [Administrador de Autenticação](#authentication-administrator) | Pode aceder à visualização, definição e reposição de informações do método de autenticação para qualquer utilizador não administrado. | c4e39bd9-1100-46d3-8c65-fb160da00071f |
> | [Administrador de Política de Autenticação](#authentication-policy-administrator) | Pode criar e gerir todos os aspetos dos métodos de autenticação e das políticas de proteção de senhas. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Azure Ad ad ad dispositivo local administrador](#azure-ad-joined-device-local-administrator) | Os utilizadores destacados para esta função são adicionados ao grupo de administradores locais em dispositivos aderidos a Azure AD. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Administrador da Azure DevOps](#azure-devops-administrator) | Pode gerir a política e configurações da organização Azure DevOps. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Administrador de Proteção de Informação da Azure](#azure-information-protection-administrator) | Pode gerir todos os aspetos do produto Azure Information Protection. | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [Administrador do keyset B2C IEF](#b2c-ief-keyset-administrator) | Pode gerir segredos para a federação e encriptação no Quadro de Experiência de Identidade (IEF). | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [Administrador de Política B2C IEF](#b2c-ief-policy-administrator) | Pode criar e gerir políticas-quadro de confiança no Quadro de Experiência de Identidade (IEF). | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [Administrador de Faturação](#billing-administrator) | Pode executar tarefas comuns relacionadas com a faturação, como atualizar informações de pagamento. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Administrador de Aplicações na Cloud](#cloud-application-administrator) | Pode criar e gerir todos os aspetos dos registos de aplicações e aplicações empresariais, exceto App Proxy. | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [Administrador de dispositivos de nuvem](#cloud-device-administrator) | Acesso limitado a dispositivos de gestão em Azure AD. | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [Administrador de Conformidade](#compliance-administrator) | Pode ler e gerir a configuração e relatórios de conformidade em Azure AD e Microsoft 365. | 17315797-102d-40b4-93e0-432062caca18 |
> | [Administrador de Dados de Conformidade](#compliance-data-administrator) | Cria e gere o conteúdo de conformidade. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [Administrador de acesso condicional](#conditional-access-administrator) | Pode gerir as capacidades de Acesso Condicional. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [Aprovação de acesso ao cliente LockBox](#customer-lockbox-access-approver) | Pode aprovar pedidos de suporte da Microsoft para aceder a dados organizacionais do cliente. | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [Administrador de Análise de Desktop](#desktop-analytics-administrator) | Pode aceder e gerir ferramentas e serviços de gestão de desktop. | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [Leitores de Diretório](#directory-readers) | Pode ler informações básicas de diretório. Normalmente usado para conceder o acesso ao diretório de leitura a candidaturas e hóspedes. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [Contas de Sincronização do Diretório](#directory-synchronization-accounts) | Utilizado apenas pelo serviço Azure AD Connect. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [Escritores do Diretório](#directory-writers) | Pode ler e escrever informações básicas de diretório. Para a concessão de acesso a aplicações, não destinadas aos utilizadores. | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [Administrador de Nome de Domínio](#domain-name-administrator) | Pode gerir nomes de domínio em nuvem e no local. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Administrador dinâmico 365](#dynamics-365-administrator) | Pode gerir todos os aspetos do produto Dynamics 365. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Administrador de Intercâmbio](#exchange-administrator) | Pode gerir todos os aspetos do produto Exchange. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Administrador de fluxo de id externo](#external-id-user-flow-administrator) | Pode criar e gerir todos os aspetos dos fluxos de utilizadores. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [Administrador de atributo de fluxo de id do utilizador externo](#external-id-user-flow-attribute-administrator) | Pode criar e gerir o esquema de atributos disponível para todos os fluxos do utilizador. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [Administrador de fornecedor de identidade externa](#external-identity-provider-administrator) | Pode configurar fornecedores de identidade para uso na federação direta. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [Administrador Global](#global-administrator) | Pode gerir todos os aspetos dos serviços Azure AD e Microsoft que utilizam identidades AZure AD. | 62e90394-69f5-4237-9190-012177145e10 |
> | [Leitor Global](#global-reader) | Pode ler tudo o que um Administrador Global pode, mas não atualizar nada. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [Administrador de Grupos](#groups-administrator) | Os membros desta função podem criar/gerir grupos, criar/gerir configurações de grupos como políticas de nomeação e expiração, e ver relatórios de atividade e auditoria de grupos. | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [Convidado Convidado](#guest-inviter) | Pode convidar utilizadores convidados independentemente da definição de "membros podem convidar os hóspedes". | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [Administrador helpdesk](#helpdesk-administrator) | Pode redefinir palavras-passe para administradores não-administradores e administradores de helpdesk. | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [Administrador de Identidade Híbrida](#hybrid-identity-administrator) | Pode gerir o fornecimento de nuvem AD a azure, a ligação ad azure e as configurações da federação. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Administrador de Insights](#insights-administrator) | Tem acesso administrativo na aplicação Microsoft 365 Insights. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Insights Business Leader](#insights-business-leader) | Pode ver e partilhar dashboards e insights através da aplicação M365 Insights. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Administrador Intune](#intune-administrator) | Pode gerir todos os aspetos do produto Intune. | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Administrador kaizala](#kaizala-administrator) | Pode gerir as definições para o Microsoft Kaizala. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [Administrador de Conhecimento](#knowledge-administrator) | Pode configurar conhecimento, aprendizagem e outras características inteligentes. | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [Administrador de Licença](#license-administrator) | Pode gerir licenças de produtos em utilizadores e grupos. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [Leitor de Privacidade do Centro de Mensagens](#message-center-privacy-reader) | Pode ler mensagens de segurança e atualizações apenas no Office 365 Message Center. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [Leitor do Centro de Mensagens](#message-center-reader) | Pode ler mensagens e atualizações para a sua organização apenas no Office 365 Message Center. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Utilizador de Comércio Moderno](#modern-commerce-user) | Pode gerir compras comerciais para uma empresa, departamento ou equipa. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [Administrador de rede](#network-administrator) | Pode gerir as localizações da rede e rever insights de design de rede empresarial para o Microsoft 365 Software como aplicações de serviço. | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Administrador de Aplicações de Escritório](#office-apps-administrator) | Pode gerir os serviços de cloud de aplicações do Office, incluindo a gestão de políticas e definições, e gerir a capacidade de selecionar, desescolhê-lo e publicar conteúdo de funcionalidade "quais as novidades" para os dispositivos do utilizador final. | 2b745bdf-0803-4d80-aa65-822c493daac |
> | [Suporte parceiro Tier1](#partner-tier1-support) | Não utilizar - não destinado a uso geral. | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [Suporte parceiro Tier2](#partner-tier2-support) | Não utilizar - não destinado a uso geral. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [Administrador de password](#password-administrator) | Pode redefinir palavras-passe para administradores não-administradores e administradores de passwords. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Administrador de Bi de Energia](#power-bi-administrator) | Pode gerir todos os aspetos do produto Power BI. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Administrador da Plataforma de Energia](#power-platform-administrator) | Pode criar e gerir todos os aspetos do Microsoft Dynamics 365, PowerApps e Microsoft Flow. | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [Administrador de impressora](#printer-administrator) | Pode gerir todos os aspetos das impressoras e conectores de impressoras. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [Técnico de Impressora](#printer-technician) | Pode registar-se e não registar impressoras e atualizar o estado da impressora. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [Administrador de Autenticação Privilegiada](#privileged-authentication-administrator) | Pode aceder à visualização, definição e reposição de informações do método de autenticação para qualquer utilizador (administrador ou não administrador). | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [Administrador privilegiado](#privileged-role-administrator) | Pode gerir atribuições de funções em AD Azure, e todos os aspetos da Gestão de Identidade Privilegiada. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [Leitor de Relatórios](#reports-reader) | Pode ler relatórios de inscrição e auditoria. | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Administrador de Pesquisa](#search-administrator) | Pode criar e gerir todos os aspetos das definições de Pesquisa do Microsoft. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Editor de Pesquisa](#search-editor) | Pode criar e gerir os conteúdos editoriais tais como marcadores, Q e As, localizações, planta. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [Administrador de Segurança](#security-administrator) | Pode ler informações de segurança e relatórios e gerir a configuração em Azure AD e Office 365. | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [Operador de Segurança](#security-operator) | Cria e gere eventos de segurança. | 5f222b1-57c3-48ba-8ad5-d4759f1f1fde6f |
> | [Leitor de Segurança](#security-reader) | Pode ler informações de segurança e relatórios no Azure AD e no Office 365. | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [Administrador de Suporte de Serviço](#service-support-administrator) | Pode ler informações de saúde do serviço e gerir bilhetes de apoio. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [Administrador do SharePoint](#sharepoint-administrator) | Pode gerir todos os aspetos do serviço SharePoint. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Skype para Administrador de Negócios](#skype-for-business-administrator) | Pode gerir todos os aspetos do produto Skype for Business. | 75941009-915a-4869-abe7-691bff18279e |
> | [Administrador de Equipas](#teams-administrator) | Pode gerir o serviço Microsoft Teams. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Administrador de Comunicações de Equipas](#teams-communications-administrator) | Pode gerir funcionalidades de chamadas e reuniões dentro do serviço Microsoft Teams. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Engenheiro de Suporte de Comunicações de Equipas](#teams-communications-support-engineer) | Pode resolver problemas de comunicação dentro das equipas usando ferramentas avançadas. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Especialista em Apoio às Comunicações das Equipas](#teams-communications-support-specialist) | Pode resolver problemas de comunicação dentro das equipas usando ferramentas básicas. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Administrador de dispositivos de equipas](#teams-devices-administrator) | Pode executar tarefas relacionadas com a gestão em dispositivos certificados por Equipas. | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [Leitor de Relatórios de Resumo de Utilização](#usage-summary-reports-reader) | Pode ver apenas agregados de nível de inquilino no Microsoft 365 Usage Analytics e Productivity Score. | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [Administrador do Utilizador](#user-administrator) | Pode gerir todos os aspetos dos utilizadores e grupos, incluindo a reposição de palavras-passe para administradores limitados. | fe930be7-5e62-47db-91af-98c3a49a38b1 |

## <a name="application-administrator"></a>Administrador da Aplicação

Os utilizadores desta função podem criar e gerir todos os aspetos das aplicações empresariais, registos de aplicações e configurações de procuração de aplicações. Note que os utilizadores destacados para esta função não são adicionados como proprietários na criação de novos registos de aplicações ou aplicações empresariais.

Esta função também concede a capacidade de consentir para permissões delegadas e permissões de aplicação, com exceção das permissões de aplicação tanto para o Microsoft Graph como para o Azure AD Graph.

> [!IMPORTANT]
> Esta exceção significa que ainda pode consentir permissões de aplicações para _outras_ aplicações (por exemplo, aplicações não Microsoft ou aplicações que tenha registado). Ainda pode _solicitar_ estas permissões como parte do registo da app, mas _conceder_ (isto é, consentir) estas permissões requer um administrador mais privilegiado, como o Administrador Global.
>
>Esta função confere a capacidade de gerir as credenciais de aplicação. Os utilizadores designados para esta função podem adicionar credenciais a uma aplicação, e usar essas credenciais para personificar a identidade da aplicação. Se a identidade da aplicação tiver tido acesso a um recurso, como a capacidade de criar ou atualizar o Utilizador ou outros objetos, então um utilizador designado para esta função poderá executar essas ações enquanto se faz passar pela aplicação. Esta capacidade de personificar a identidade da aplicação pode ser uma elevação de privilégio sobre o que o utilizador pode fazer através das suas atribuições de funções. É importante entender que atribuir um utilizador à função de Administrador de Aplicação dá-lhes a capacidade de personificar a identidade de uma aplicação.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/aplicações/criar | Criar todos os tipos de aplicações |
> | microsoft.diretório/aplicações/delete | Eliminar todos os tipos de aplicações |
> | microsoft.diretório/aplicações/aplicaçãoProxy/read | Leia todas as propriedades de procuração de aplicações |
> | microsoft.diretório/aplicações/applicationProxy/update | Atualizar todas as propriedades de procuração de aplicações |
> | microsoft.diretório/aplicações/applicationProxyAuthentication/update | Atualizar propriedades de autenticação de procuração de aplicativos |
> | microsoft.diretório/aplicações/aplicaçãoProxySslCertificate/update | Atualizar domínios personalizados de procuração de aplicativos |
> | microsoft.diretório/aplicações/applicationProxyUrlSettings/update | Atualização de pedidos de proxy URLs internos e externos |
> | microsoft.diretório/aplicações/appRoles/update | Atualizar a propriedade appRoles em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/audiência/atualização | Atualizar a propriedade do público para aplicações |
> | microsoft.diretório/aplicações/autenticação/atualização | Atualizar a autenticação em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas para aplicações |
> | microsoft.diretório/aplicações/credenciais/atualização | Atualizar credenciais de aplicação |
> | microsoft.diretório/aplicações/proprietários/atualização | Atualizar os proprietários de aplicações |
> | microsoft.diretório/aplicações/permissões/atualização | Atualizar permissões expostas e permissões necessárias em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/políticas/atualização | Atualizar políticas de aplicações |
> | microsoft.diretório/aplicações/verificação/atualização | Atualizar propriedade de confirmação de aplicações |
> | microsoft.diretório/aplicações/sincronização/standard/read | Leia as definições de provisionamento associadas ao objeto de aplicação |
> | microsoft.diretório/aplicaçãoTemplates/instantiate | Aplicações instantâneas de galeria a partir de modelos de aplicação |
> | microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades em registos de auditoria, incluindo propriedades privilegiadas |
> | microsoft.diretório/conectores/criar | Criar conectores de procuração de aplicação |
> | microsoft.diretório/conectores/allProperties/read | Leia todas as propriedades dos conectores proxy de aplicação |
> | microsoft.diretório/conectorGroups/criar | Criar grupos de conector de procuração de aplicação |
> | microsoft.diretório/conectorGroups/delete | Eliminar grupos de conector de procuração de aplicação |
> | microsoft.diretório/conectorGroups/allProperties/read | Leia todas as propriedades dos grupos de conector de procuração de aplicação |
> | microsoft.diretório/conectorGroups/allProperties/update | Atualizar todas as propriedades dos grupos de conector de procuração de aplicação |
> | microsoft.diretório/oAuth2PermissionGrants/allProperties/allTasks | Criar e eliminar bolsas de permissão OAuth 2.0 e ler e atualizar todas as propriedades |
> | microsoft.diretório/aplicaçãoPolicies/create | Criar políticas de aplicação |
> | microsoft.diretório/aplicaçãoPolicies/delete | Eliminar políticas de aplicação |
> | microsoft.diretório/aplicaçãoPolicies/standard/read | Ler propriedades padrão das políticas de aplicação |
> | microsoft.diretório/aplicaçãoPolícias/proprietários/ler | Leia os proprietários sobre as políticas de aplicação |
> | microsoft.diretório/aplicaçãoPolicies/policyAppliedTo/read | Leia as políticas de aplicação aplicadas à lista de objetos |
> | microsoft.diretório/aplicaçãoPolicies/basic/update | Atualizar propriedades padrão das políticas de aplicação |
> | microsoft.diretório/aplicaçãoPolícias/proprietários/atualização | Atualizar a propriedade do proprietário das políticas de aplicação |
> | microsoft.diretório/provisioningLogs/allProperties/read | Leia todas as propriedades dos registos de provisionamento |
> | microsoft.diretório/serviçoPrincipals/criar | Criar principais de serviço |
> | microsoft.diretório/serviçoPrincipals/delete | Eliminar os principais de serviço |
> | microsoft.diretório/serviçoPrincipals/desativar | Diretores de serviço para desativação |
> | microsoft.diretório/serviçoPrincipals/enable | Ativar os principais de serviço |
> | microsoft.diretório/serviçoPrincipals/getPasswordSingleSignOnCredentials | Gerir credenciais de inscrição únicas de senha nos principais de serviço |
> | microsoft.diretório/serviçoPrincipals/synchronizationCredentiss/manage | Gerir a aplicação que prevê segredos e credenciais |
> | microsoft.diretório/serviçoPrincipals/sincronizaçãoJobs/gerir | Iniciar, reiniciar e interromper a aplicação de provisão de empregos de sincronização |
> | microsoft.diretório/serviçoPrincipals/sincronizaçãoSchema/gerir | Criar e gerir os trabalhos de sincronização e esquemas de sincronização de aplicações |
> | microsoft.diretório/serviçoPrincipals/managePasswordSingleSignOnCredentials | Leia credenciais de inscrição únicas na palavra-passe nos principais de serviço |
> | microsoft.diretório/serviçoPrincipals/managePermissionGrantsForAll.microsoft-application-admin | Conceder consentimento para permissões de aplicação e permissões delegadas em nome de qualquer utilizador ou de todos os utilizadores, exceto para permissões de aplicação para Microsoft Graph e Azure AD Graph |
> | microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update | Atualizar atribuições de funções principais de serviço |
> | microsoft.diretório/serviçoPrincipals/audience/update | Atualizar propriedades do público em diretores de serviço |
> | microsoft.diretório/serviçoPrincipals/autenticação/atualização | Atualizar propriedades de autenticação em principais de serviço |
> | microsoft.diretório/serviçoPrincipals/básico/atualização | Atualizar propriedades básicas em principais serviços |
> | microsoft.diretório/serviçoPrincipals/credenciais/atualização | Atualizar credenciais dos principais de serviços |
> | microsoft.diretório/serviçoPrincipals/proprietários/atualização | Atualizar os proprietários dos principados de serviços |
> | microsoft.diretório/serviçoPrincipals/permissões/atualização | Atualizar permissões dos principais serviços |
> | microsoft.diretório/serviçoPrincipals/políticas/atualização | Atualizar políticas dos principais serviços |
> | microsoft.diretório/serviçoPrincipals/tag/update | Atualizar a propriedade tag para os principados de serviço |
> | microsoft.diretório/serviçoPrincipals/sincronização/standard/read | Leia as definições de provisionamento associadas ao seu principal serviço |
> | microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades em relatórios de inscrição, incluindo propriedades privilegiadas |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="application-developer"></a>Programador de Aplicações

Os utilizadores desta função podem criar registos de aplicações quando a definição de "Utilizadores podem registar aplicações" é definida como Nº. Esta função também permite consentir em nome próprio quando a definição de "Utilizadores podem consentir em aplicações que acedam aos dados da empresa em seu nome". Os utilizadores destacados para esta função são adicionados como proprietários ao criar novos registos de aplicações ou aplicações empresariais.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/aplicações/createAsOwner | Crie todos os tipos de aplicações, e o criador é adicionado como o primeiro proprietário |
> | microsoft.diretório/appRoleAssignments/createAsOwner | Criar atribuições de funções de candidatura, com o criador como o primeiro proprietário |
> | microsoft.diretório/oAuth2PermissionGrants/createAsOwner | Criar bolsas de permissão OAuth 2.0, com o criador como o primeiro proprietário |
> | microsoft.diretório/serviçoPrincipals/createAsOwner | Criar diretores de serviço, com o criador como o primeiro proprietário |

## <a name="attack-payload-author"></a>Autor de carga útil de ataque

Os utilizadores desta função podem criar cargas de ataque, mas não realmente lançá-las ou programar. As cargas de ataque estão então disponíveis para todos os administradores do inquilino que podem usá-los para criar uma simulação.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Criar e gerir cargas de ataque no Simulador de Ataque |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Leia relatos de simulação de ataque, respostas e treino associado |

## <a name="attack-simulation-administrator"></a>Administrador de simulação de ataque

Os utilizadores desta função podem criar e gerir todos os aspetos da criação de simulação de ataque, lançamento/agendamento de uma simulação e revisão dos resultados da simulação. Os membros desta função têm este acesso a todas as simulações no arrendatário.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Criar e gerir cargas de ataque no Simulador de Ataque |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Leia relatos de simulação de ataque, respostas e treino associado |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Criar e gerir modelos de simulação de ataque no Simulador de Ataque |

## <a name="authentication-administrator"></a>Administrador de Autenticação

Os utilizadores com esta função podem definir ou redefinir qualquer método de autenticação (incluindo palavras-passe) para não administradores e algumas funções. Os administradores de autenticação podem exigir que os utilizadores que não sejam administradores ou que sejam designados para algumas funções se re-registem contra as credenciais não senhas existentes (por exemplo, MFA ou FIDO), podendo também revogar **o remember MFA no dispositivo**, que solicita mFA no próximo registo. Para obter uma lista das funções que um Administrador de Autenticação pode ler ou atualizar métodos de autenticação, consulte [permissões de reset de palavra-passe](#password-reset-permissions).

A [função de administrador de autenticação privilegiada](#privileged-authentication-administrator) tem permissão para forçar o reinscúlgi dos registos e a autenticação de vários fatores para todos os utilizadores.

A [função de administrador de política de autenticação](#authentication-policy-administrator) tem permissões para definir a política de método de autenticação do arrendatário que determina quais os métodos que cada utilizador pode registar e utilizar.

| Função | Gerir os métodos de auth do utilizador | Gerir por utilizador MFA | Gerir as definições de MFA | Gerir a política do método auth | Gerir a política de proteção de senhas |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Administrador de autenticação | Sim para alguns utilizadores (ver acima) | Sim para alguns utilizadores (ver acima) | No | No | No |
| Administrador de autenticação privilegiada| Sim para todos os utilizadores | Sim para todos os utilizadores | No | No | No |
| Administrador de política de autenticação | No |No | Yes | Yes | Yes |

> [!IMPORTANT]
> Os utilizadores com esta função podem alterar credenciais para pessoas que possam ter acesso a informações sensíveis ou privadas ou configuração crítica dentro e fora do Azure Ative Directory. Alterar as credenciais de um utilizador pode significar a capacidade de assumir a identidade e permissões desse utilizador. Por exemplo:
>
>* Os proprietários de Registo de Aplicações e Aplicações Empresariais, que podem gerir credenciais de apps que possuam. Essas aplicações podem ter permissões privilegiadas em Azure AD e em outros lugares não concedidos a Administradores de Autenticação. Por este caminho um Administrador de Autenticação pode assumir a identidade de titular de uma candidatura e, em seguida, assumir a identidade de uma aplicação privilegiada atualizando as credenciais para a aplicação.
>* Proprietários de subscrições Azure, que podem ter acesso a informações sensíveis ou privadas ou configuração crítica em Azure.
>* Security Group e Microsoft 365 proprietários do grupo, que podem gerir a adesão ao grupo. Esses grupos podem conceder acesso a informações sensíveis ou privadas ou configuração crítica em Azure AD e em outros lugares.
>* Administradores em outros serviços fora da Azure AD como Exchange Online, Office Security and Compliance Center, e sistemas de recursos humanos.
>* Não administradores como executivos, advogados e funcionários de recursos humanos que possam ter acesso a informações confidenciais ou privadas.

> [!IMPORTANT]
> Esta função não consegue gerir as definições de MFA no antigo portal de gestão de MFA ou fichas de OATH de Hardware. As mesmas funções podem ser executadas utilizando o módulo Azure AD Powershell do comando [Set-MsolUser.](/powershell/module/msonline/set-msoluser)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Forçar a aprovação invalidando tokens de atualização do utilizador |
> | microsoft.diretório/utilizadores/strongAuthentication/update | Atualizar a forte propriedade de autenticação para os utilizadores |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="authentication-policy-administrator"></a>Administrador de Política de Autenticação

Os utilizadores com esta função podem configurar a política de métodos de autenticação, as definições de MFA em todo o inquilino e a política de proteção de senhas. Esta função permite gerir as definições de Proteção de Palavras-Passe: configurações de bloqueio inteligente e atualização da lista de senhas proibidas personalizadas.

As funções [de administrador de autenticação](#authentication-administrator) autenticação e [de administrador de autenticação privilegiada](#privileged-authentication-administrator) têm permissão para gerir métodos de autenticação registados nos utilizadores e podem forçar o re-registo e a autenticação de vários fatores para todos os utilizadores.

| Função | Gerir os métodos de auth do utilizador | Gerir por utilizador MFA | Gerir as definições de MFA | Gerir a política do método auth | Gerir a política de proteção de senhas |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Administrador de autenticação | Sim para alguns utilizadores (ver acima) | Sim para alguns utilizadores (ver acima) | No | No | No |
| Administrador de autenticação privilegiada| Sim para todos os utilizadores | Sim para todos os utilizadores | No | No | No |
| Administrador de política de autenticação | No | No | Yes | Yes | Yes |

> [!IMPORTANT]
> Esta função não consegue gerir as definições de MFA no antigo portal de gestão de MFA ou fichas de OATH de Hardware. 

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/organização/strongAuthentication/update | Atualizar propriedades fortes de uma organização |
> | microsoft.diretório/userCredentialPolicies/create | Criar políticas de credencial para os utilizadores |
> | microsoft.diretório/userCredentialPolicies/delete | Eliminar políticas de credencial para os utilizadores |
> | microsoft.diretório/userCredentialPolicies/standard/read | Ler propriedades padrão das políticas de credencial para os utilizadores |
> | microsoft.diretório/userCredentialPolicies/owners/read | Ler os proprietários de políticas de credencial para utilizadores |
> | microsoft.diretório/userCredentialPolicies/policyAppliedTo/read | Ler política.aplica-se Para link de navegação |
> | microsoft.diretório/userCredentialPolicies/basic/update | Atualizar políticas básicas para utilizadores |
> | microsoft.diretório/userCredentialPolicies/owners/update | Atualizar os proprietários de políticas de credencial para os utilizadores |
> | microsoft.diretório/userCredentialPolicies/tenantDefault/update | Política de atualização.isOrganizaçãoDefault propriedade |

## <a name="azure-ad-joined-device-local-administrator"></a>Azure Ad ad ad dispositivo local administrador

Esta função está disponível para atribuição apenas como administrador local adicional nas [definições do Dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Os utilizadores com esta função tornam-se administradores de máquinas locais em todos os dispositivos windows 10 que se juntam ao Azure Ative Directory. Não têm a capacidade de gerir objetos de dispositivos no Azure Ative Directory.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/grupoSettings/standard/read | Ler propriedades básicas nas definições de grupo |
> | microsoft.diretório/grupoSettingTemplates/standard/read | Leia propriedades básicas em modelos de definição de grupo |

## <a name="azure-devops-administrator"></a>Administrador da Azure DevOps

Os utilizadores com esta função podem gerir a política Azure DevOps para restringir a criação de uma nova organização Azure DevOps a um conjunto de utilizadores ou grupos configuráveis. Os utilizadores desta função podem gerir esta política através de qualquer organização da Azure DevOps que seja apoiada pela organização Azure AD da empresa. Esta função não concede outras permissões específicas da Azure DevOps (por exemplo, Administradores de Cobrança de Projetos) dentro de qualquer uma das organizações Azure DevOps apoiadas pela organização Azure AD da empresa.

Todas as políticas da empresa Azure DevOps podem ser geridas pelos utilizadores neste papel.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.devOps/allEntities/allTasks | Ler e configurar Azure DevOps |

## <a name="azure-information-protection-administrator"></a>Administrador de Proteção de Informação da Azure

Os utilizadores com esta função têm todas as permissões no serviço de Proteção de Informação Azure. Esta função permite configurar rótulos para a política de proteção de informação Azure, gerir modelos de proteção e ativar a proteção. Esta função não concede quaisquer permissões no Centro de Proteção de Identidade, Gestão de Identidade Privilegiada, Monitor Microsoft 365 Service Health ou Office 365 Security & Compliance Center.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.informationProtection/allEntities/allTasks | Gerir todos os aspetos da Proteção de Informação Azure |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="b2c-ief-keyset-administrator"></a>Administrador do keyset B2C IEF

O utilizador pode criar e gerir chaves e segredos de política para encriptação simbólica, assinaturas simbólicas e alegar encriptação/desencriptação. Ao adicionar novas chaves aos recipientes-chave existentes, este administrador limitado pode reverter os segredos conforme necessário sem afetar as aplicações existentes. Este utilizador pode ver todo o conteúdo destes segredos e as datas de validade mesmo após a sua criação.

> [!IMPORTANT]
> Este é um papel sensível. A função de administrador do tecla deve ser cuidadosamente auditada e atribuída com cuidado durante a pré-produção e a produção.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.directy/b2cTrustFrameworkKeySet/allProperties/allTasks | Ler e atualizar todos os imóveis das políticas de autorização |

## <a name="b2c-ief-policy-administrator"></a>Administrador de Política B2C IEF

Os utilizadores desta função têm a capacidade de criar, ler, atualizar e eliminar todas as políticas personalizadas em Azure AD B2C e, portanto, ter total controlo sobre o Quadro de Experiência de Identidade na organização Azure AD B2C relevante. Ao editar políticas, este utilizador pode estabelecer uma federação direta com fornecedores de identidade externos, alterar o esquema de diretório, alterar todos os conteúdos voltados para o utilizador (HTML, CSS, JavaScript), alterar os requisitos para completar uma autenticação, criar novos utilizadores, enviar dados de utilizador para sistemas externos, incluindo migrações completas, e editar todas as informações do utilizador, incluindo campos sensíveis, como palavras-passe e números de telefone. Inversamente, este papel não pode alterar as chaves de encriptação ou editar os segredos usados para a federação na organização.

> [!IMPORTANT]
> O Administrador de Política B2 IEF é um papel altamente sensível que deve ser atribuído numa base muito limitada às organizações em produção. As atividades destes utilizadores devem ser auditadas de perto, especialmente para as organizações em produção.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.directy/b2cTrustFrameworkPolicy/allProperties/allTasks | Leia e configuure conjuntos de chaves no Azure Ative Directory B2C |

## <a name="billing-administrator"></a>Administrador de Faturação

Efetua compras, gere subscrições, gere pedidos de suporte e monitoriza o estado de funcionamento do serviço.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/organização/básico/atualização | Atualizar propriedades básicas na organização |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.commerce.billing/allEntities/allTasks | Gerir todos os aspetos da faturação do Office 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="cloud-application-administrator"></a>Administrador de Aplicações na Cloud

Os utilizadores nesta função têm as mesmas permissões que a função de Administrador de Aplicação, excluindo a capacidade de gerir o proxy da aplicação. Esta função confere a capacidade de criar e gerir todos os aspetos dos pedidos de empresa e dos registos de candidaturas. Os utilizadores destacados para esta função não são adicionados como proprietários ao criar novos registos de aplicações ou aplicações empresariais.

Esta função também concede a capacidade de consentir para permissões delegadas e permissões de aplicação, com exceção das permissões de aplicação tanto para o Microsoft Graph como para o Azure AD Graph.

> [!IMPORTANT]
> Esta exceção significa que ainda pode consentir permissões de aplicações para _outras_ aplicações (por exemplo, aplicações não Microsoft ou aplicações que tenha registado). Ainda pode _solicitar_ estas permissões como parte do registo da app, mas _conceder_ (isto é, consentir) estas permissões requer um administrador mais privilegiado, como o Administrador Global.
>
>Esta função confere a capacidade de gerir as credenciais de aplicação. Os utilizadores designados para esta função podem adicionar credenciais a uma aplicação, e usar essas credenciais para personificar a identidade da aplicação. Se a identidade da aplicação tiver tido acesso a um recurso, como a capacidade de criar ou atualizar o Utilizador ou outros objetos, então um utilizador designado para esta função poderá executar essas ações enquanto se faz passar pela aplicação. Esta capacidade de personificar a identidade da aplicação pode ser uma elevação de privilégio sobre o que o utilizador pode fazer através das suas atribuições de funções. É importante entender que atribuir um utilizador à função de Administrador de Aplicação dá-lhes a capacidade de personificar a identidade de uma aplicação.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/aplicações/criar | Criar todos os tipos de aplicações |
> | microsoft.diretório/aplicações/delete | Eliminar todos os tipos de aplicações |
> | microsoft.diretório/aplicações/appRoles/update | Atualizar a propriedade appRoles em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/audiência/atualização | Atualizar a propriedade do público para aplicações |
> | microsoft.diretório/aplicações/autenticação/atualização | Atualizar a autenticação em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas para aplicações |
> | microsoft.diretório/aplicações/credenciais/atualização | Atualizar credenciais de aplicação |
> | microsoft.diretório/aplicações/proprietários/atualização | Atualizar os proprietários de aplicações |
> | microsoft.diretório/aplicações/permissões/atualização | Atualizar permissões expostas e permissões necessárias em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/políticas/atualização | Atualizar políticas de aplicações |
> | microsoft.diretório/aplicações/verificação/atualização | Atualizar propriedade de confirmação de aplicações |
> | microsoft.diretório/aplicações/sincronização/standard/read | Leia as definições de provisionamento associadas ao objeto de aplicação |
> | microsoft.diretório/aplicaçãoTemplates/instantiate | Aplicações instantâneas de galeria a partir de modelos de aplicação |
> | microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades em registos de auditoria, incluindo propriedades privilegiadas |
> | microsoft.diretório/oAuth2PermissionGrants/allProperties/allTasks | Criar e eliminar bolsas de permissão OAuth 2.0 e ler e atualizar todas as propriedades |
> | microsoft.diretório/aplicaçãoPolicies/create | Criar políticas de aplicação |
> | microsoft.diretório/aplicaçãoPolicies/delete | Eliminar políticas de aplicação |
> | microsoft.diretório/aplicaçãoPolicies/standard/read | Ler propriedades padrão das políticas de aplicação |
> | microsoft.diretório/aplicaçãoPolícias/proprietários/ler | Leia os proprietários sobre as políticas de aplicação |
> | microsoft.diretório/aplicaçãoPolicies/policyAppliedTo/read | Leia as políticas de aplicação aplicadas à lista de objetos |
> | microsoft.diretório/aplicaçãoPolicies/basic/update | Atualizar propriedades padrão das políticas de aplicação |
> | microsoft.diretório/aplicaçãoPolícias/proprietários/atualização | Atualizar a propriedade do proprietário das políticas de aplicação |
> | microsoft.diretório/provisioningLogs/allProperties/read | Leia todas as propriedades dos registos de provisionamento |
> | microsoft.diretório/serviçoPrincipals/criar | Criar principais de serviço |
> | microsoft.diretório/serviçoPrincipals/delete | Eliminar os principais de serviço |
> | microsoft.diretório/serviçoPrincipals/desativar | Diretores de serviço para desativação |
> | microsoft.diretório/serviçoPrincipals/enable | Ativar os principais de serviço |
> | microsoft.diretório/serviçoPrincipals/getPasswordSingleSignOnCredentials | Gerir credenciais de inscrição únicas de senha nos principais de serviço |
> | microsoft.diretório/serviçoPrincipals/synchronizationCredentiss/manage | Gerir a aplicação que prevê segredos e credenciais |
> | microsoft.diretório/serviçoPrincipals/sincronizaçãoJobs/gerir | Iniciar, reiniciar e interromper a aplicação de provisão de empregos de sincronização |
> | microsoft.diretório/serviçoPrincipals/sincronizaçãoSchema/gerir | Criar e gerir os trabalhos de sincronização e esquemas de sincronização de aplicações |
> | microsoft.diretório/serviçoPrincipals/managePasswordSingleSignOnCredentials | Leia credenciais de inscrição únicas na palavra-passe nos principais de serviço |
> | microsoft.diretório/serviçoPrincipals/managePermissionGrantsForAll.microsoft-application-admin | Conceder consentimento para permissões de aplicação e permissões delegadas em nome de qualquer utilizador ou de todos os utilizadores, exceto para permissões de aplicação para Microsoft Graph e Azure AD Graph |
> | microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update | Atualizar atribuições de funções principais de serviço |
> | microsoft.diretório/serviçoPrincipals/audience/update | Atualizar propriedades do público em diretores de serviço |
> | microsoft.diretório/serviçoPrincipals/autenticação/atualização | Atualizar propriedades de autenticação em principais de serviço |
> | microsoft.diretório/serviçoPrincipals/básico/atualização | Atualizar propriedades básicas em principais serviços |
> | microsoft.diretório/serviçoPrincipals/credenciais/atualização | Atualizar credenciais dos principais de serviços |
> | microsoft.diretório/serviçoPrincipals/proprietários/atualização | Atualizar os proprietários dos principados de serviços |
> | microsoft.diretório/serviçoPrincipals/permissões/atualização | Atualizar permissões dos principais serviços |
> | microsoft.diretório/serviçoPrincipals/políticas/atualização | Atualizar políticas dos principais serviços |
> | microsoft.diretório/serviçoPrincipals/tag/update | Atualizar a propriedade tag para os principados de serviço |
> | microsoft.diretório/serviçoPrincipals/sincronização/standard/read | Leia as definições de provisionamento associadas ao seu principal serviço |
> | microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades em relatórios de inscrição, incluindo propriedades privilegiadas |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="cloud-device-administrator"></a>Administrador de dispositivos de nuvem

Os utilizadores desta função podem ativar, desativar e eliminar dispositivos em AZure AD e ler teclas BitLocker do Windows 10 (se presentes) no portal Azure. A função não concede permissões para gerir quaisquer outras propriedades no dispositivo.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades em registos de auditoria, incluindo propriedades privilegiadas |
> | microsoft.diretório/bitlockerKeys/chave/ler | Leia metadados bitlocker e chave em dispositivos |
> | microsoft.diretório/dispositivos/delete | Eliminar dispositivos do Azure AD |
> | microsoft.diretório/dispositivos/desativar | Desativar dispositivos em Azure AD |
> | microsoft.diretório/dispositivos/enable | Ativar dispositivos em Azure AD |
> | microsoft.diretório/dispositivos/extensãoAttributes/update | Atualizar todos os valores para dispositivos.extensionA propriedade atribuída |
> | microsoft.diretório/dispositivoManagementPolicies/standard/read | Leia propriedades padrão nas políticas de aplicação de gestão de dispositivos |
> | microsoft.diretório/dispositivoManagementPolicies/basic/update | Atualizar propriedades básicas sobre políticas de aplicação de gestão de dispositivos |
> | microsoft.diretório/dispositivoRegistrationPolicy/standard/read | Ler propriedades padrão nas políticas de registo de dispositivos |
> | microsoft.diretório/dispositivoRegistrationPolicy/basic/update | Atualizar propriedades básicas nas políticas de registo de dispositivos |
> | microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades em relatórios de inscrição, incluindo propriedades privilegiadas |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |

## <a name="compliance-administrator"></a>Administrador de Conformidade

Os utilizadores com esta função têm permissões para gerir funcionalidades relacionadas com a conformidade no centro de conformidade microsoft 365, Microsoft 365 admin center, Azure e Office 365 Security & Compliance Center. Os assignees também podem gerir todas as funcionalidades dentro do Centro de Administração Exchange e equipas & Skype para centros de administração de negócios e criar bilhetes de apoio para Azure e Microsoft 365. Mais informações estão disponíveis em [funções de administração da Microsoft 365.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

Em | Pode fazer
----- | ----------
[Microsoft 365 centro de conformidade](https://protection.office.com) | Proteja e gere os dados da sua organização através dos serviços microsoft 365<br>Gerir alertas de conformidade
[Gestor de Conformidade](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhe, atribua e verifique as atividades de conformidade regulamentar da sua organização
[Escritório 365 Centro de Conformidade & de Segurança](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerir a governação de dados<br>Realizar investigação legal e de dados<br>Gerir pedido de assunto de dados<br><br>Esta função tem as mesmas permissões que o [RoleGroup do Administrador de Conformidade](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) no Office 365 Security & Compliance Center controlo de acesso baseado em funções.
[Intune](/intune/role-based-access-control) | Ver todos os dados de auditoria do Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Tem permissões só de leitura e pode gerir alertas<br>Pode criar e modificar políticas de ficheiros e permitir ações de governação de ficheiros<br>Pode ver todos os relatórios incorporados sob a Gestão de Dados

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.diretório/direitoManagement/allProperties/read | Leia todos os imóveis na gestão de direitos AD da Azure |
> | microsoft.office365.complianceManager/allEntities/allTasks | Gerir todos os aspetos do Office 365 Compliance Manager |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="compliance-data-administrator"></a>Administrador de Dados de Conformidade

Os utilizadores com esta função têm permissões para rastrear dados no centro de conformidade Microsoft 365, Microsoft 365 admin center e Azure. Os utilizadores também podem rastrear dados de conformidade dentro do centro de administração Exchange, Compliance Manager e Teams & Skype for Business e criar bilhetes de suporte para Azure e Microsoft 365. [Esta documentação](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) tem detalhes sobre diferenças entre o Administrador de Conformidade e o Administrador de Dados de Conformidade.

Em | Pode fazer
----- | ----------
[Microsoft 365 centro de conformidade](https://protection.office.com) | Monitorize as políticas relacionadas com a conformidade nos serviços da Microsoft 365<br>Gerir alertas de conformidade
[Gestor de Conformidade](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhe, atribua e verifique as atividades de conformidade regulamentar da sua organização
[Escritório 365 Centro de Conformidade & de Segurança](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerir a governação de dados<br>Realizar investigação legal e de dados<br>Gerir pedido de assunto de dados<br><br>Esta função tem as mesmas permissões que o [RoleGroup do Administrador de Dados de Conformidade](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) no Office 365 Security & Control de acesso baseado em funções do Compliance Center.
[Intune](/intune/role-based-access-control) | Ver todos os dados de auditoria do Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Tem permissões só de leitura e pode gerir alertas<br>Pode criar e modificar políticas de ficheiros e permitir ações de governação de ficheiros<br>Pode ver todos os relatórios incorporados sob a Gestão de Dados

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.directy/cloudAppSecurity/allProperties/allTasks | Criar e eliminar todos os recursos e ler e atualizar propriedades padrão na Microsoft Cloud App Security |
> | microsoft.azure.informationProtection/allEntities/allTasks | Gerir todos os aspetos da Proteção de Informação Azure |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.complianceManager/allEntities/allTasks | Gerir todos os aspetos do Office 365 Compliance Manager |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="conditional-access-administrator"></a>Administrador de acesso condicional

Os utilizadores com esta função têm a capacidade de gerir as definições de Acesso Condicional do Diretório Ativo Azure.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/condicionalAccessPolicies/create | Criar políticas de acesso condicional |
> | microsoft.diretório/condicionalAccessPolicies/delete | Eliminar políticas de acesso condicional |
> | microsoft.diretório/condicionalAccessPolicies/standard/read | Ler políticas.conditionalAposa propriedade |
> | microsoft.diretório/condicionalAccessPolicies/owners/read | Ler políticas.conditionalAposa propriedade |
> | microsoft.diretório/condicionalAccessPolicies/policyAppliedTo/read | Ler políticas.conditionalAposa propriedade |
> | microsoft.diretório/condicionalAccessPolicies/basic/update | Atualizar propriedades básicas para políticas de acesso condicional |
> | microsoft.diretório/condicionalAccessPolicies/owners/update | Atualização de políticas.propriedade deacessoacis |
> | microsoft.diretório/condicionalAccessPolicies/tenantDefault/update | Atualização de políticas.propriedade deacessoacis |
> | microsoft.diretório/crossTenantAccessPolicies/create | Criar políticas de acesso ao inquilino transversal |
> | microsoft.diretório/crossTenantAccessPolicies/delete | Eliminar as políticas de acesso ao inquilino transversal |
> | microsoft.diretório/crossTenantAccessPolicies/standard/read | Ler propriedades básicas das políticas de acesso ao inquilino transversal |
> | microsoft.diretório/crossTenantAccessPolicies/owners/read | Leia os proprietários de políticas de acesso de inquilinos cruzados |
> | microsoft.diretório/crossTenantAccessPolicies/policyAppliedTo/read | Leia a políticaAppliedTo de propriedade de políticas de acesso de inquilinos cruzados |
> | microsoft.diretório/crossTenantAccessPolicies/basic/update | Atualizar propriedades básicas das políticas de acesso ao inquilino transversal |
> | microsoft.diretório/crossTenantAccessPolicies/owners/update | Atualizar os proprietários de políticas de acesso de inquilinos cruzados |
> | microsoft.diretório/crossTenantAccessPolicies/tenantDefault/update | Atualizar o inquilino predefinido para políticas de acesso de inquilinos cruzados |

## <a name="customer-lockbox-access-approver"></a>Aprovação de acesso ao cliente LockBox

Gere os [pedidos de Lockbox do Cliente](/office365/admin/manage/customer-lockbox-requests) na sua organização. Recebem notificações por e-mail para pedidos de Bloqueio de Clientes e podem aprovar e negar pedidos do centro de administração microsoft 365. Também podem ligar ou desligar a função 'Lockbox' do cliente. Apenas os Administradores Globais podem redefinir as palavras-passe das pessoas afetadas a esta função.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.office365.lockbox/allEntities/allTasks | Gerir todos os aspetos do Lockbox do Cliente |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="desktop-analytics-administrator"></a>Administrador de Análise de Desktop

Os utilizadores desta função podem gerir os serviços de Política de & de Personalização do Desktop Analytics e Office. Para desktop Analytics, isto inclui a capacidade de visualizar o inventário de ativos, criar planos de implementação, ver implementação e estado de saúde. Para o serviço de personalização do Office & Policy, esta função permite aos utilizadores gerir as políticas do Office.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerir todos os aspetos do Desktop Analytics |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="directory-readers"></a>Leitores de Diretório

Os utilizadores desta função podem ler informações básicas de diretório. Esta função deve ser utilizada para:

* Concedendo um conjunto específico de utilizadores convidados leia o acesso em vez de conceder a todos os utilizadores convidados.
* A concessão de um conjunto específico de utilizadores não administrativos ao portal Azure quando "Restringir o acesso ao portal AD Ad Azure apenas para administradores" está definido para "Sim".
* Conceder aos diretores de serviço acesso ao diretório onde o Diretório.Read.Tudo não é uma opção.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/administrativoSSunits/standard/read | Ler propriedades básicas em unidades administrativas |
> | microsoft.diretório/administrativoSIns/membros/ler | Ler membros das unidades administrativas |
> | microsoft.diretório/aplicações/standard/read | Ler propriedades padrão das aplicações |
> | microsoft.diretório/aplicações/proprietários/ler | Ler proprietários de candidaturas |
> | microsoft.diretório/aplicações/políticas/ler | Ler políticas de aplicações |
> | microsoft.diretório/contactos/standard/read | Ler propriedades básicas em contactos em Azure AD |
> | microsoft.diretório/contactos/memberOf/read | Leia a adesão ao grupo para todos os contactos em Azure AD |
> | microsoft.diretório/contratos/standard/read | Ler propriedades básicas em contratos de parceiros |
> | microsoft.diretório/dispositivos/standard/read | Ler propriedades básicas em dispositivos |
> | microsoft.diretório/dispositivos/memberOf/read | Leia os membros do dispositivo |
> | microsoft.diretório/dispositivos/registadosSowners/read | Ler os proprietários registados de dispositivos |
> | microsoft.diretório/dispositivos/registros/ler | Ler utilizadores registados de dispositivos |
> | microsoft.diretório/directórioRoles/standard/read | Atualizar propriedades básicas em funções AD Azure |
> | microsoft.diretório/directórioRoles/elegíveisMembers/read | Leia os membros elegíveis das funções de Azure AD |
> | microsoft.diretório/directórioRoles/membros/ler | Leia todos os membros dos papéis da AD Azure |
> | microsoft.diretório/domínios/standard/read | Ler propriedades básicas em domínios |
> | microsoft.diretório/grupos/standard/read | Ler propriedades básicas em grupos |
> | microsoft.diretório/grupos/appRoleAssignments/read | Ler atribuições de funções de candidatura de grupos |
> | microsoft.diretório/grupos/memberOf/read | Leia os grupos dos quais um grupo é membro em Azure AD |
> | microsoft.diretório/grupos/membros/ler | Ler membros de grupos |
> | microsoft.diretório/grupos/proprietários/ler | Ler proprietários de grupos |
> | microsoft.diretório/grupos/definições/ler | Ler configurações de grupos |
> | microsoft.diretório/grupoSettings/standard/read | Ler propriedades básicas nas definições de grupo |
> | microsoft.diretório/grupoSettingTemplates/standard/read | Leia propriedades básicas em modelos de definição de grupo |
> | microsoft.diretório/oAuth2PermissionGrants/standard/read | Ler propriedades básicas em bolsas de autorização OAuth 2.0 |
> | microsoft.diretório/organização/standard/read | Ler propriedades básicas em uma organização |
> | microsoft.diretório/organização/trustedCAsForPasslessAuth/read | Ler autoridades de certificados fidedignos para autenticação sem palavras-passe |
> | microsoft.diretório/aplicaçãoPolicies/standard/read | Ler propriedades padrão das políticas de aplicação |
> | microsoft.diretório/papelAssinhas/standard/read | Ler propriedades básicas em atribuições de funções |
> | microsoft.diretório/roleDefinitions/standard/read | Ler propriedades básicas sobre definições de funções |
> | microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/read | Ler atribuições principais de funções de serviço |
> | microsoft.diretório/serviçoPrincipals/appRoleAssignments/read | Ler atribuições de funções atribuídas a diretores de serviço |
> | microsoft.diretório/serviçoPrincipals/standard/read | Ler propriedades básicas dos principados de serviço |
> | microsoft.diretório/serviçoPrincipals/memberOf/read | Leia os membros do grupo em diretores de serviço |
> | microsoft.diretório/serviçoPrincipals/oAuth2PermissionGrants/read | Ler subsídios de autorização delegados em diretores de serviço |
> | microsoft.diretório/serviçoPrincipals/proprietários/ler | Ler os proprietários dos principais serviços |
> | microsoft.diretório/serviçoPrincipals/ownedObjects/read | Ler objetos de propriedade de diretores de serviço |
> | microsoft.diretório/serviçoPrincipals/políticas/ler | Ler políticas dos diretores de serviços |
> | microsoft.diretório/subscritoSkus/standard/read | Ler propriedades básicas em subscrições |
> | microsoft.diretório/utilizadores/standard/read | Ler propriedades básicas nos utilizadores |
> | microsoft.diretório/utilizadores/appRoleAssignments/read | Ler atribuições de funções de aplicação dos utilizadores |
> | microsoft.directy/users/directReports/read | Leia os relatórios diretos para os utilizadores |
> | microsoft.diretório/utilizadores/manager/read | Ler gestor de utilizadores |
> | microsoft.diretório/utilizadores/membroOf/read | Leia os membros do grupo dos utilizadores |
> | microsoft.diretório/utilizadores/oAuth2PermissionGrants/read | Ler subsídios de autorização delegados aos utilizadores |
> | microsoft.diretório/utilizadores/propriedadeDevices/ler | Ler dispositivos de propriedade dos utilizadores |
> | microsoft.diretório/utilizadores/ownedObjects/read | Ler objetos de propriedade dos utilizadores |
> | microsoft.diretório/utilizadores/registradoDevices/read | Ler dispositivos registados de utilizadores |

## <a name="directory-synchronization-accounts"></a>Contas de Sincronização do Diretório

Não utilizar. Esta função é automaticamente atribuída ao serviço Azure AD Connect e não se destina ou suporta qualquer outra utilização.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/aplicações/criar | Criar todos os tipos de aplicações |
> | microsoft.diretório/aplicações/delete | Eliminar todos os tipos de aplicações |
> | microsoft.diretório/aplicações/appRoles/update | Atualizar a propriedade appRoles em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/audiência/atualização | Atualizar a propriedade do público para aplicações |
> | microsoft.diretório/aplicações/autenticação/atualização | Atualizar a autenticação em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas para aplicações |
> | microsoft.diretório/aplicações/credenciais/atualização | Atualizar credenciais de aplicação |
> | microsoft.diretório/aplicações/proprietários/atualização | Atualizar os proprietários de aplicações |
> | microsoft.diretório/aplicações/permissões/atualização | Atualizar permissões expostas e permissões necessárias em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/políticas/atualização | Atualizar políticas de aplicações |
> | microsoft.diretório/organização/dirSync/update | Atualizar a propriedade de sincronização de diretórios de organização |
> | microsoft.diretório/políticas/criar | Criar políticas em Azure AD |
> | microsoft.diretório/políticas/excluir | Eliminar políticas em Azure AD |
> | microsoft.diretório/políticas/standard/read | Ler propriedades básicas sobre políticas |
> | microsoft.diretório/políticas/proprietários/ler | Ler os proprietários de políticas |
> | microsoft.diretório/políticas/policyAppliedTo/read | Ler políticas.policyAppliedTo propriedade |
> | microsoft.diretório/políticas/básico/atualização | Atualizar propriedades básicas em políticas |
> | microsoft.diretório/políticas/proprietários/atualização | Atualizar os proprietários de políticas |
> | microsoft.diretório/políticas/tenantDefault/update | Atualizar as políticas de organização padrão |
> | microsoft.diretório/serviçoPrincipals/criar | Criar principais de serviço |
> | microsoft.diretório/serviçoPrincipals/delete | Eliminar os principais de serviço |
> | microsoft.diretório/serviçoPrincipals/enable | Ativar os principais de serviço |
> | microsoft.diretório/serviçoPrincipals/desativar | Diretores de serviço para desativação |
> | microsoft.diretório/serviçoPrincipals/getPasswordSingleSignOnCredentials | Gerir credenciais de inscrição únicas de senha nos principais de serviço |
> | microsoft.diretório/serviçoPrincipals/managePasswordSingleSignOnCredentials | Leia credenciais de inscrição únicas na palavra-passe nos principais de serviço |
> | microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/read | Ler atribuições principais de funções de serviço |
> | microsoft.diretório/serviçoPrincipals/appRoleAssignments/read | Ler atribuições de funções atribuídas a diretores de serviço |
> | microsoft.diretório/serviçoPrincipals/standard/read | Ler propriedades básicas dos principados de serviço |
> | microsoft.diretório/serviçoPrincipals/memberOf/read | Leia os membros do grupo em diretores de serviço |
> | microsoft.diretório/serviçoPrincipals/oAuth2PermissionGrants/read | Ler subsídios de autorização delegados em diretores de serviço |
> | microsoft.diretório/serviçoPrincipals/proprietários/ler | Ler os proprietários dos principais serviços |
> | microsoft.diretório/serviçoPrincipals/ownedObjects/read | Ler objetos de propriedade de diretores de serviço |
> | microsoft.diretório/serviçoPrincipals/políticas/ler | Ler políticas dos diretores de serviços |
> | microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update | Atualizar atribuições de funções principais de serviço |
> | microsoft.diretório/serviçoPrincipals/audience/update | Atualizar propriedades do público em diretores de serviço |
> | microsoft.diretório/serviçoPrincipals/autenticação/atualização | Atualizar propriedades de autenticação em principais de serviço |
> | microsoft.diretório/serviçoPrincipals/básico/atualização | Atualizar propriedades básicas em principais serviços |
> | microsoft.diretório/serviçoPrincipals/credenciais/atualização | Atualizar credenciais dos principais de serviços |
> | microsoft.diretório/serviçoPrincipals/proprietários/atualização | Atualizar os proprietários dos principados de serviços |
> | microsoft.diretório/serviçoPrincipals/permissões/atualização | Atualizar permissões dos principais serviços |
> | microsoft.diretório/serviçoPrincipals/políticas/atualização | Atualizar políticas dos principais serviços |
> | microsoft.diretório/serviçoPrincipals/tag/update | Atualizar a propriedade tag para os principados de serviço |

## <a name="directory-writers"></a>Escritores do Diretório

Os utilizadores desta função podem ler e atualizar informações básicas de utilizadores, grupos e diretores de serviço. Atribua esta função apenas a aplicações que não suportem o [Quadro de Consentimento](../develop/quickstart-register-app.md). Não deve ser atribuído a nenhum utilizadores.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/grupos/assignLicense | Atribuir licenças de produtos a grupos para licenciamento baseado em grupo |
> | microsoft.diretório/grupos/criar | Criar grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/reprocessLicenseAssignment | Reprocessar as atribuições de licenças para licenciamento baseado em grupo |
> | microsoft.diretório/grupos/básico/atualização | Atualizar propriedades básicas em grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/classificação/atualização | Atualizar a propriedade de classificação de grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/dynamicMembershipRule/update | Atualizar regra de adesão dinâmica dos grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/grupoType/atualização | Atualizar a propriedade groupType para um grupo |
> | microsoft.diretório/grupos/membros/atualização | Atualizar membros de grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/onPremWriteBack/update | Atualizar os grupos AD da Azure a serem reensitados para as instalações |
> | microsoft.diretório/grupos/proprietários/atualização | Atualizar os proprietários de grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/definições/atualização | Configurações de atualização de grupos |
> | microsoft.diretório/grupos/visibilidade/atualização | Atualizar a propriedade de visibilidade dos grupos |
> | microsoft.diretório/grupoSettings/create | Criar definições de grupo |
> | microsoft.diretório/grupoSettings/delete | Eliminar definições do grupo |
> | microsoft.diretório/grupoSettings/basic/update | Atualizar propriedades básicas nas definições do grupo |
> | microsoft.diretório/oAuth2PermissionGrants/create | Criar bolsas de permissão OAuth 2.0 |
> | microsoft.diretório/oAuth2PermissionGrants/basic/update | Atualizar bolsas de autorização OAuth 2.0 |
> | microsoft.diretório/serviçoPrincipals/synchronizationCredentiss/manage | Gerir a aplicação que prevê segredos e credenciais |
> | microsoft.diretório/serviçoPrincipals/sincronizaçãoJobs/gerir | Iniciar, reiniciar e interromper a aplicação de provisão de empregos de sincronização |
> | microsoft.diretório/serviçoPrincipals/sincronizaçãoSchema/gerir | Criar e gerir os trabalhos de sincronização e esquemas de sincronização de aplicações |
> | microsoft.diretório/serviçoPrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Conceder um acesso direto pricípo ao serviço aos dados de um grupo |
> | microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update | Atualizar atribuições de funções principais de serviço |
> | microsoft.diretório/utilizadores/assignLicense | Gerir licenças de utilizador |
> | microsoft.diretório/utilizadores/criar | Adicionar utilizadores |
> | microsoft.diretório/utilizadores/desativar | Desativar utilizadores |
> | microsoft.diretório/utilizadores/enable | Ativar os utilizadores |
> | microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Forçar a aprovação invalidando tokens de atualização do utilizador |
> | microsoft.diretório/utilizadores/reprocessLicenseAssignment | Reprocessar as atribuições de licenças para utilizadores |
> | microsoft.diretório/utilizadores/básico/atualização | Atualizar propriedades básicas nos utilizadores |
> | microsoft.diretório/utilizadores/gestor/atualização | Gestor de atualização para utilizadores |
> | microsoft.diretório/utilizadores/userPrincipalName/update | Atualizar Nome Principal do Utilizador dos utilizadores |

## <a name="domain-name-administrator"></a>Administrador de Nome de Domínio

Os utilizadores com esta função podem gerir (ler, adicionar, verificar, atualizar e eliminar) nomes de domínio. Também podem ler informações sobre utilizadores, grupos e aplicações, uma vez que estes objetos possuem dependências de domínio. Para ambientes no local, os utilizadores com esta função podem configurar nomes de domínio para a federação para que os utilizadores associados sejam sempre autenticados no local. Estes utilizadores podem então assinar em serviços baseados em Azure com as suas senhas no local através de um único s-sign-on. As definições da Federação precisam de ser sincronizadas através do Azure AD Connect, pelo que os utilizadores também têm permissões para gerir o Azure AD Connect.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/domínios/allProperties/allTasks | Criar e eliminar domínios, e ler e atualizar todas as propriedades |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |

## <a name="dynamics-365-administrator"></a>Administrador dinâmico 365

Os utilizadores com esta função têm permissões globais dentro do Microsoft Dynamics 365 Online, quando o serviço está presente, bem como a capacidade de gerir bilhetes de suporte e monitorizar a saúde do serviço. Mais informações na [Utilização da função de administrador de serviço para gerir a sua organização AZure AD](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço Dinâmico 365". É "Administrador Dinâmico 365" no [portal Azure](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.dynamics365/allEntities/allTasks | Gerir todos os aspetos da Dinâmica 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="exchange-administrator"></a>Administrador de Intercâmbio

Os utilizadores com esta função têm permissões globais dentro do Microsoft Exchange Online, quando o serviço está presente. Também tem a capacidade de criar e gerir todos os grupos Microsoft 365, gerir bilhetes de suporte e monitorizar a saúde do serviço. Mais informações [sobre as funções de administração da Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Na Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço de Câmbio". É "Exchange Administrator" no [portal Azure](https://portal.azure.com). É "Exchange Online administrator" no [Centro de Administração Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/grupos/hiddenMembers/read | Leia membros escondidos de um grupo |
> | microsoft.diretório/grupos.unificado/criar | Criar grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.unificado/delete | Eliminar os grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.unificado/restaurar | Restaurar os grupos Microsoft 365 |
> | microsoft.diretório/grupos.unificado/básico/atualização | Atualizar propriedades básicas em grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.unificado/membros/atualização | Atualizar membros de grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.unificado/proprietários/atualização | Atualizar os proprietários de grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Gerir todos os aspetos do Exchange Online |
> | microsoft.office365.network/performance/allProperties/read | Leia todas as propriedades de desempenho da rede no centro de administração Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Ler relatórios de utilização do Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="external-id-user-flow-administrator"></a>Administrador de fluxo de id externo

Os utilizadores com esta função podem criar e gerir fluxos de utilizadores (também chamados políticas "incorporadas") no portal Azure. Estes utilizadores podem personalizar o conteúdo HTML/CSS/JavaScript, alterar os requisitos de MFA, selecionar reclamações no token, gerir conectores API e configurar as definições de sessão para todos os fluxos de utilizador na organização Azure AD. Por outro lado, esta função não inclui a capacidade de rever os dados dos utilizadores ou de fazer alterações nos atributos que estão incluídos no esquema da organização. As alterações às políticas do Quadro de Experiência de Identidade (também conhecidas como políticas personalizadas) também estão fora do âmbito desta função.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/b2cUserFlow/allProperties/allTasks | Leia e configuure os atributos do utilizador no Azure Ative Directory B2C |

## <a name="external-id-user-flow-attribute-administrator"></a>Administrador de atributo de fluxo de id do utilizador externo

Os utilizadores com esta função adicionam ou eliminam os atributos personalizados disponíveis para todos os fluxos de utilizador na organização AZure AD. Como tal, os utilizadores com esta função podem alterar ou adicionar novos elementos ao esquema do utilizador final e impactar o comportamento de todos os fluxos de utilizador e resultar indiretamente em alterações aos dados que podem ser solicitados aos utilizadores finais e, em última análise, enviados como reivindicações para as aplicações. Esta função não pode editar fluxos de utilizador.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/b2cUserAttribute/allProperties/allTasks | Leia e configuure políticas personalizadas no Azure Ative Directory B2C |

## <a name="external-identity-provider-administrator"></a>Administrador de fornecedor de identidade externa

Este administrador gere a federação entre organizações AD da Azure e fornecedores de identidade externa. Com esta função, os utilizadores podem adicionar novos fornecedores de identidade e configurar todas as definições disponíveis (por exemplo, percurso de autenticação, ID de serviço, recipientes-chave atribuídos). Este utilizador pode permitir que a organização Azure AD confie em autenticações de fornecedores de identidade externos. O impacto resultante nas experiências do utilizador final depende do tipo de organização:

* Azure AD organizações para colaboradores e parceiros: A adição de uma federação (por exemplo, com o Gmail) terá imediatamente impacto em todos os convites de hóspedes ainda não resgatados. Consulte [a adição do Google como um fornecedor de identidade para utilizadores convidados B2B](../external-identities/google-federation.md).
* Organizações Azure Ative Directory B2C: A adição de uma federação (por exemplo, com o Facebook, ou com outra organização AZure AD) não afeta imediatamente os fluxos de utilizador final até que o fornecedor de identidade seja adicionado como uma opção num fluxo de utilizador (também chamado de política incorporada). Consulte [configurar uma conta Microsoft como um fornecedor de identidade,](../../active-directory-b2c/identity-provider-microsoft-account.md) por exemplo. Para alterar os fluxos dos utilizadores, é necessária a função limitada de "Administrador de Fluxo de Utilizador B2C".

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/identidadeProviders/allProperties/allTasks | Ler e configurar fornecedores de identidade no Azure Ative Directory B2C |

## <a name="global-administrator"></a>Administrador Global

Os utilizadores com esta função têm acesso a todas as funcionalidades administrativas no Azure Ative Directory, bem como serviços que utilizam identidades do Azure Ative Directory como o Microsoft 365 security center, o Microsoft 365 compliance center, o Exchange Online, o SharePoint Online e o Skype para business online. Além disso, os Administradores Globais podem [elevar o seu acesso](../../role-based-access-control/elevate-access-global-admin.md) para gerir todas as subscrições e grupos de gestão da Azure. Isto permite que os Administradores Globais tenham acesso total a todos os recursos Azure usando o respetivo Azure AD Tenant. A pessoa que se inscreve na organização AZure AD torna-se administradora global. Pode haver mais do que um Administrador Global na sua empresa. Os Administradores Globais podem redefinir a palavra-passe para qualquer utilizador e todos os outros administradores.

> [!NOTE]
> Como uma boa prática, a Microsoft recomenda que atribua o papel de Administrador Global a menos de cinco pessoas na sua organização. Para mais informações, consulte [as melhores práticas para as funções de Ad Azure.](best-practices.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/acessoReviews/allProperties/allTasks | Criar e apagar comentários de acesso e ler e atualizar todas as propriedades de avaliações de acesso em Azure AD |
> | microsoft.diretório/administrativoSIns/allProperties/allTasks | Criar e gerir unidades administrativas (incluindo membros) |
> | microsoft.diretório/aplicações/allProperties/allTasks | Criar e apagar aplicações, ler e atualizar todas as propriedades |
> | microsoft.diretório/aplicações/sincronização/standard/read | Leia as definições de provisionamento associadas ao objeto de aplicação |
> | microsoft.diretório/aplicaçãoTemplates/instantiate | Aplicações instantâneas de galeria a partir de modelos de aplicação |
> | microsoft.diretório/appRoleAssignments/allProperties/allTasks | Criar e apagar apRoleAssignments e ler e atualizar todas as propriedades |
> | microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades em registos de auditoria, incluindo propriedades privilegiadas |
> | microsoft.diretório/autorizaçãoPolícia/allProperties/allTasks | Gerir todos os aspetos das políticas de autorização |
> | microsoft.diretório/bitlockerKeys/chave/ler | Leia metadados bitlocker e chave em dispositivos |
> | microsoft.directy/cloudAppSecurity/allProperties/allTasks | Criar e eliminar todos os recursos e ler e atualizar propriedades padrão na Microsoft Cloud App Security |
> | microsoft.diretório/conectores/criar | Criar conectores de procuração de aplicação |
> | microsoft.diretório/conectores/allProperties/read | Leia todas as propriedades dos conectores proxy de aplicação |
> | microsoft.diretório/conectorGroups/criar | Criar grupos de conector de procuração de aplicação |
> | microsoft.diretório/conectorGroups/delete | Eliminar grupos de conector de procuração de aplicação |
> | microsoft.diretório/conectorGroups/allProperties/read | Leia todas as propriedades dos grupos de conector de procuração de aplicação |
> | microsoft.diretório/conectorGroups/allProperties/update | Atualizar todas as propriedades dos grupos de conector de procuração de aplicação |
> | microsoft.diretório/contactos/allProperties/allTasks | Criar e apagar contactos e ler e atualizar todas as propriedades |
> | microsoft.diretório/contratos/allProperties/allTasks | Criar e apagar contratos de parceiros, e ler e atualizar todas as propriedades |
> | microsoft.diretório/dispositivos/allProperties/allTasks | Criar e eliminar dispositivos e ler e atualizar todas as propriedades |
> | microsoft.diretório/dispositivoManagementPolicies/standard/read | Leia propriedades padrão nas políticas de aplicação de gestão de dispositivos |
> | microsoft.diretório/dispositivoManagementPolicies/basic/update | Atualizar propriedades básicas sobre políticas de aplicação de gestão de dispositivos |
> | microsoft.diretório/dispositivoRegistrationPolicy/standard/read | Ler propriedades padrão nas políticas de registo de dispositivos |
> | microsoft.diretório/dispositivoRegistrationPolicy/basic/update | Atualizar propriedades básicas nas políticas de registo de dispositivos |
> | microsoft.diretório/directórioRoles/allProperties/allTasks | Criar e apagar funções de diretório, e ler e atualizar todas as propriedades |
> | microsoft.diretório/directórioRoleTemplates/allProperties/allTasks | Criar e eliminar modelos de funções AD Azure e ler e atualizar todas as propriedades |
> | microsoft.diretório/domínios/allProperties/allTasks | Criar e eliminar domínios, e ler e atualizar todas as propriedades |
> | microsoft.diretório/direitoManagement/allProperties/allTasks | Criar e apagar recursos, e ler e atualizar todos os imóveis na gestão de direitos AD Azure |
> | microsoft.diretório/grupos/allProperties/allTasks | Criar e eliminar grupos, ler e atualizar todas as propriedades |
> | microsoft.diretório/gruposAssignableToRoles/create | Criar grupos aos quais se pode atribuir funções |
> | microsoft.diretório/gruposAssignableToRoles/delete | Eliminar grupos atribuíveis por funções |
> | microsoft.diretório/gruposAssignableToRoles/restauro | Restaurar grupos atribuíveis por funções |
> | microsoft.diretório/gruposAssignableToRoles/allProperties/update | Atualizar grupos atribuíveis por funções |
> | microsoft.diretório/grupoSettings/allProperties/allTasks | Criar e eliminar definições de grupo e ler e atualizar todas as propriedades |
> | microsoft.diretório/grupoSettingTemplates/allProperties/allTasks | Criar e eliminar modelos de definição de grupo e ler e atualizar todas as propriedades |
> | microsoft.diretório/identidadeProtecção/todas as Ofertas/allTasks | Criar e eliminar todos os recursos e ler e atualizar propriedades padrão na Proteção de Identidade AZure AD |
> | microsoft.diretório/loginOrganizaçãoBranding/allProperties/allTasks | Criar e eliminar loginTenantBranding e ler e atualizar todas as propriedades |
> | microsoft.diretório/oAuth2PermissionGrants/allProperties/allTasks | Criar e eliminar bolsas de permissão OAuth 2.0 e ler e atualizar todas as propriedades |
> | microsoft.diretório/organização/allProperties/allTasks | Criar e excluir organizações, e ler e atualizar todas as propriedades |
> | microsoft.diretório/políticas/allProperties/allTasks | Criar e eliminar políticas, ler e atualizar todas as propriedades |
> | microsoft.diretório/condicionalAccessPolicies/allProperties/allTasks | Gerir todas as propriedades das políticas de acesso condicional |
> | microsoft.diretório/privilegiadoIdmentManagement/allProperties/read | Ler todos os recursos em Gestão de Identidade Privilegiada |
> | microsoft.diretório/provisioningLogs/allProperties/read | Leia todas as propriedades dos registos de provisionamento |
> | microsoft.diretório/papelAssignments/allProperties/allTasks | Criar e eliminar atribuições de funções e ler e atualizar todas as propriedades de atribuição de funções |
> | microsoft.diretório/roleDefinitions/allProperties/allTasks | Criar e eliminar definições de funções, e ler e atualizar todas as propriedades |
> | microsoft.diretório/scopedRoleMemberships/allProperties/allTasks | Criar e eliminar as empresas scopedRoleMemberberships e ler e atualizar todas as propriedades |
> | microsoft.diretório/serviçoAction/activateService | Pode realizar a ação de "ativar o serviço" para um serviço |
> | microsoft.directy/serviceAction/disableDirectDirectfature | Pode executar a ação de serviço de "desativação do diretório" |
> | microsoft.directy/serviceAction/enableDirectoryFeature | Pode executar a ação de serviço "enable directy feature" |
> | microsoft.diretório/serviçoAction/getAvailableExtentionProperties | Pode realizar a ação de serviço de propriedades de exteneridades disponíveis |
> | microsoft.diretório/serviçoPrincipals/allProperties/allTasks | Criar e apagar os principais de serviços e ler e atualizar todas as propriedades |
> | microsoft.diretório/serviçoPrincipals/managePermissionGrantsForAll.microsoft-company-admin | Conceder consentimento para qualquer permissão a qualquer pedido |
> | microsoft.diretório/serviçoPrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Conceder um acesso direto pricípo ao serviço aos dados de um grupo |
> | microsoft.diretório/serviçoPrincipals/sincronização/standard/read | Leia as definições de provisionamento associadas ao seu principal serviço |
> | microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades em relatórios de inscrição, incluindo propriedades privilegiadas |
> | microsoft.diretório/subscritoSkus/allProperties/allTasks | Comprar e gerir subscrições e apagar subscrições |
> | microsoft.diretório/utilizadores/allProperties/allTasks | Criar e eliminar utilizadores, e ler e atualizar todas as propriedades |
> | microsoft.diretório/permissãoSpolias/criar | Criar políticas de concessão de permissão |
> | microsoft.diretório/permissãoSpolíticas/excluir | Eliminar políticas de concessão de permissões |
> | microsoft.diretório/permissãoSpolíticas/standard/read | Ler propriedades padrão das políticas de concessão de permissão |
> | microsoft.diretório/permissãoSpolíticas/básico/atualização | Atualizar propriedades básicas das políticas de concessão de permissão |
> | microsoft.diretório/serviçoPrincipalCreationPolicies/create | Criar políticas de criação principal de serviços |
> | microsoft.diretório/serviçoPrincipalCreationPolicies/delete | Eliminar políticas de criação principal de serviços |
> | microsoft.diretório/serviçoPrincipalCreationPolicies/standard/read | Leia propriedades padrão das políticas principais de criação de serviços |
> | microsoft.diretório/serviçoPrincipalCreationPolicies/basic/update | Atualizar propriedades básicas das principais políticas de criação de serviços |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Gerir todos os aspetos da Azure Advanced Threat Protection |
> | microsoft.azure.informationProtection/allEntities/allTasks | Gerir todos os aspetos da Proteção de Informação Azure |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.commerce.billing/allEntities/allTasks | Gerir todos os aspetos da faturação do Office 365 |
> | microsoft.dynamics365/allEntities/allTasks | Gerir todos os aspetos da Dinâmica 365 |
> | microsoft.flow/allEntities/allTasks | Gerir todos os aspetos do Microsoft Power Automamate |
> | microsoft.intune/allEntities/allTasks | Gerir todos os aspetos da Microsoft Intune |
> | microsoft.office365.complianceManager/allEntities/allTasks | Gerir todos os aspetos do Office 365 Compliance Manager |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerir todos os aspetos do Desktop Analytics |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Gerir todos os aspetos do Exchange Online |
> | microsoft.office365.lockbox/allEntities/allTasks | Gerir todos os aspetos do Lockbox do Cliente |
> | microsoft.office365.messageCenter/messages/read | Leia mensagens no Centro de Mensagens no centro de administração microsoft 365, excluindo mensagens de segurança |
> | microsoft.office365.messageCenter/securityMessages/read | Leia mensagens de segurança no Centro de Mensagens no centro de administração microsoft 365 |
> | microsoft.office365.network/performance/allProperties/read | Leia todas as propriedades de desempenho da rede no centro de administração Microsoft 365 |
> | microsoft.office365.protectionCenter/allEntities/allProperties/allTasks | Gerir todos os aspetos dos centros de Segurança e Conformidade |
> | microsoft.office365.search/content/manage | Criar e eliminar conteúdo, e ler e atualizar todas as propriedades no Microsoft Search |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Criar e eliminar todos os recursos e ler e atualizar propriedades padrão no Microsoft 365 Security and Compliance Center |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.sharePoint/allEntities/allTasks | Criar e eliminar todos os recursos e ler e atualizar propriedades padrão no SharePoint |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerir todos os aspetos do Skype para business online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Ler relatórios de utilização do Office 365 |
> | microsoft.office365.userCommunication/allEntities/allTasks | Leia e atualize a visibilidade das novas mensagens |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |
> | microsoft.powerApps/allEntities/allTasks | Gerir todos os aspetos das Power Apps |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Gerir todos os aspetos do Power BI |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | Gerir todos os aspetos do Microsoft Defender para Endpoint |

## <a name="global-reader"></a>Leitor Global

Os utilizadores desta função podem ler definições e informações administrativas em todos os serviços da Microsoft 365, mas não podem tomar ações de gestão. O leitor global é a contrapartida apenas de leitura para o Administrador Global. Atribua o leitor global em vez de Administrador Global para planeamento, auditorias ou investigações. Use o leitor Global em combinação com outras funções de administração limitadas, como o Exchange Administrator, para facilitar o trabalho sem atribuir o papel de Administrador Global. O leitor global trabalha com o Microsoft 365 admin center, Exchange admin center, SharePoint admin center, Teams admin center, Security center, Compliance center, Azure AD admin center e Device Management admin center.

> [!NOTE]
> O papel de leitor global tem algumas limitações agora -
>
>- [Centro de administração OneDrive](https://admin.onedrive.com/) - O centro de administração OneDrive não suporta o papel de leitor Global
>- [Centro de administração M365](https://admin.microsoft.com/Adminportal/Home#/homepage) - O leitor global não consegue ler os pedidos do lockbox do cliente. Não encontrará o separador de **pedidos de bloqueio** do Cliente no **suporte** no painel esquerdo do M365 Admin Center.
>- [Office Security & Compliance Center](https://sip.protection.office.com/homepage) - O leitor global não consegue ler registos de auditoria SCC, fazer pesquisa de conteúdos ou ver Pontuação Segura.
>- [Teams admin center](https://admin.teams.microsoft.com) - O leitor global não consegue ler **o ciclo de vida das equipas,** **relatórios de & de análise,** **gestão de dispositivos ip e** catálogo de **aplicações.**
>- [A Gestão privilegiada de Acesso (PAM)](/office365/securitycompliance/privileged-access-management-overview) não suporta o papel de leitor global.
>- [Azure Information Protection](/azure/information-protection/what-is-information-protection) - O leitor global é apoiado apenas [para relatórios centrais,](/azure/information-protection/reports-aip) e quando a sua organização AZure AD não está na [plataforma de rotulagem unificada.](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
>
> Estas funcionalidades estão atualmente em desenvolvimento.
>

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/aplicações/aplicaçãoProxy/read | Leia todas as propriedades de procuração de aplicações |
> | microsoft.diretório/aplicações/sincronização/standard/read | Leia as definições de provisionamento associadas ao objeto de aplicação |
> | microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades em registos de auditoria, incluindo propriedades privilegiadas |
> | microsoft.diretório/bitlockerKeys/chave/ler | Leia metadados bitlocker e chave em dispositivos |
> | microsoft.diretório/conectores/allProperties/read | Leia todas as propriedades dos conectores proxy de aplicação |
> | microsoft.diretório/conectorGroups/allProperties/read | Leia todas as propriedades dos grupos de conector de procuração de aplicação |
> | microsoft.diretório/direitoManagement/allProperties/read | Leia todos os imóveis na gestão de direitos AD da Azure |
> | microsoft.diretório/dispositivoManagementPolicies/standard/read | Leia propriedades padrão nas políticas de aplicação de gestão de dispositivos |
> | microsoft.diretório/dispositivoRegistrationPolicy/standard/read | Ler propriedades padrão nas políticas de registo de dispositivos |
> | microsoft.diretório/grupos/hiddenMembers/read | Leia membros escondidos de um grupo |
> | microsoft.diretório/políticas/standard/read | Ler propriedades básicas sobre políticas |
> | microsoft.diretório/políticas/proprietários/ler | Ler os proprietários de políticas |
> | microsoft.diretório/políticas/policyAppliedTo/read | Ler políticas.policyAppliedTo propriedade |
> | microsoft.diretório/condicionalAccessPolicies/standard/read | Ler políticas.conditionalAposa propriedade |
> | microsoft.diretório/condicionalAccessPolicies/owners/read | Ler políticas.conditionalAposa propriedade |
> | microsoft.diretório/condicionalAccessPolicies/policyAppliedTo/read | Ler políticas.conditionalAposa propriedade |
> | microsoft.diretório/provisioningLogs/allProperties/read | Leia todas as propriedades dos registos de provisionamento |
> | microsoft.diretório/serviçoPrincipals/autenticação/leitura | Ler propriedades de autenticação em principais de serviço |
> | microsoft.diretório/serviçoPrincipals/sincronização/standard/read | Leia as definições de provisionamento associadas ao seu principal serviço |
> | microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades em relatórios de inscrição, incluindo propriedades privilegiadas |
> | microsoft.diretório/utilizadores/strongAuthentication/read | Leia a forte propriedade de autenticação para os utilizadores |
> | microsoft.commerce.billing/allEntities/read | Leia todos os recursos do Office 365 faturando |
> | microsoft.office365.exchange/allEntities/standard/read | Leia todos os recursos da Exchange Online |
> | microsoft.office365.messageCenter/messages/read | Leia mensagens no Centro de Mensagens no centro de administração microsoft 365, excluindo mensagens de segurança |
> | microsoft.office365.messageCenter/securityMessages/read | Leia mensagens de segurança no Centro de Mensagens no centro de administração microsoft 365 |
> | microsoft.office365.network/performance/allProperties/read | Leia todas as propriedades de desempenho da rede no centro de administração Microsoft 365 |
> | microsoft.office365.protectionCenter/allEntities/allProperties/read | Leia todos os imóveis nos centros de Segurança e Conformidade |
> | microsoft.office365.securityComplianceCenter/allEntities/read | Leia propriedades padrão no Microsoft 365 Security and Compliance Center |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Ler relatórios de utilização do Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="groups-administrator"></a>Administrador de Grupos

Os utilizadores desta função podem criar/gerir grupos e as suas definições como políticas de nomeação e expiração. É importante entender que atribuir um utilizador a este papel dá-lhes a capacidade de gerir todos os grupos da organização em várias cargas de trabalho como Teams, SharePoint, Yammer, além do Outlook. Também o utilizador será capaz de gerir as configurações de vários grupos em vários portais de administração como o Microsoft Admin Center, portal Azure, bem como os específicos da carga de trabalho, como Teams e SharePoint Admin Centers.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/grupos/assignLicense | Atribuir licenças de produtos a grupos para licenciamento baseado em grupo |
> | microsoft.diretório/grupos/criar | Criar grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/delete | Eliminar grupos, excluindo o grupo que atribui funções |
> | microsoft.diretório/grupos/hiddenMembers/read | Leia membros escondidos de um grupo |
> | microsoft.diretório/grupos/reprocessLicenseAssignment | Reprocessar as atribuições de licenças para licenciamento baseado em grupo |
> | microsoft.diretório/grupos/restaurar | Restaurar os grupos eliminados |
> | microsoft.diretório/grupos/básico/atualização | Atualizar propriedades básicas em grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/classificação/atualização | Atualizar a propriedade de classificação de grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/dynamicMembershipRule/update | Atualizar regra de adesão dinâmica dos grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/grupoType/atualização | Atualizar a propriedade groupType para um grupo |
> | microsoft.diretório/grupos/membros/atualização | Atualizar membros de grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/onPremWriteBack/update | Atualizar os grupos AD da Azure a serem reensitados para as instalações |
> | microsoft.diretório/grupos/proprietários/atualização | Atualizar os proprietários de grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/definições/atualização | Configurações de atualização de grupos |
> | microsoft.diretório/grupos/visibilidade/atualização | Atualizar a propriedade de visibilidade dos grupos |
> | microsoft.diretório/serviçoPrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Conceder um acesso direto pricípo ao serviço aos dados de um grupo |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="guest-inviter"></a>Convidado Convidado

Os utilizadores desta função podem gerir convites de utilizadores convidados Azure Ative Directory B2B quando os **Membros podem convidar** a definição do utilizador está definido para Nº. Mais informações sobre a colaboração B2B na [colaboração sobre a azure AD B2B](../external-identities/what-is-b2b.md). Não inclui quaisquer outras permissões.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/utilizadores/inviteGuest | Convidar utilizadores |
> | microsoft.diretório/utilizadores/standard/read | Ler propriedades básicas nos utilizadores |
> | microsoft.diretório/utilizadores/appRoleAssignments/read | Ler atribuições de funções de aplicação dos utilizadores |
> | microsoft.directy/users/directReports/read | Leia os relatórios diretos para os utilizadores |
> | microsoft.diretório/utilizadores/manager/read | Ler gestor de utilizadores |
> | microsoft.diretório/utilizadores/membroOf/read | Leia os membros do grupo dos utilizadores |
> | microsoft.diretório/utilizadores/oAuth2PermissionGrants/read | Ler subsídios de autorização delegados aos utilizadores |
> | microsoft.diretório/utilizadores/propriedadeDevices/ler | Ler dispositivos de propriedade dos utilizadores |
> | microsoft.diretório/utilizadores/ownedObjects/read | Ler objetos de propriedade dos utilizadores |
> | microsoft.diretório/utilizadores/registradoDevices/read | Ler dispositivos registados de utilizadores |

## <a name="helpdesk-administrator"></a>Administrador helpdesk

Os utilizadores com esta função podem alterar palavras-passe, invalidar fichas de atualização, gerir pedidos de serviço e monitorizar a saúde do serviço. Invalidar um token de atualização obriga o utilizador a iniciar novamente o sat. Se um Administrador helpdesk pode redefinir a palavra-passe de um utilizador e invalidar fichas de atualização depende da função que o utilizador é atribuído. Para obter uma lista das funções para as quais um administrador de helpdesk pode redefinir palavras-passe e invalidar fichas de atualização, consulte [permissões de reset de palavra-passe](#password-reset-permissions).

> [!IMPORTANT]
> Os utilizadores com esta função podem alterar palavras-passe para pessoas que possam ter acesso a informações sensíveis ou privadas ou configuração crítica dentro e fora do Azure Ative Directory. Alterar a palavra-passe de um utilizador pode significar a capacidade de assumir a identidade e permissões desse utilizador. Por exemplo:
>
>- Os proprietários de Registo de Aplicações e Aplicações Empresariais, que podem gerir credenciais de apps que possuam. Essas aplicações podem ter permissões privilegiadas em Azure AD e em outros lugares não concedidos a Administradores helpdesk. Por este caminho, um Administrador helpdesk poderá assumir a identidade de um titular da aplicação e, em seguida, assumir a identidade de um pedido privilegiado atualizando as credenciais para o pedido.
>- Proprietários de subscrições Azure, que podem ter acesso a informações confidenciais ou privadas ou configuração crítica em Azure.
>- Security Group e Microsoft 365 proprietários do grupo, que podem gerir a adesão ao grupo. Esses grupos podem conceder acesso a informações sensíveis ou privadas ou configuração crítica em Azure AD e em outros lugares.
>- Administradores em outros serviços fora da Azure AD como Exchange Online, Office Security and Compliance Center, e sistemas de recursos humanos.
>- Não administradores como executivos, advogados e funcionários de recursos humanos que possam ter acesso a informações confidenciais ou privadas.

Delegar permissões administrativas sobre subconjuntos de utilizadores e aplicar políticas a um subconjunto de utilizadores é possível com [unidades administrativas.](administrative-units.md)

Esta função foi anteriormente denominada "Administrador de Password" no [portal Azure](https://portal.azure.com/). O nome "Helpdesk Administrator" em Azure AD agora corresponde ao seu nome no Azure AD PowerShell e na Microsoft Graph API.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/bitlockerKeys/chave/ler | Leia metadados bitlocker e chave em dispositivos |
> | microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Forçar a aprovação invalidando tokens de atualização do utilizador |
> | microsoft.diretório/utilizadores/password/atualização | Redefinir palavras-passe para todos os utilizadores |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="hybrid-identity-administrator"></a>Administrador de Identidade Híbrida

Os utilizadores desta função podem criar, gerir e implementar a configuração de configuração de provisionamento de AD a AD AZure usando cloud Provisioning, bem como gerir as definições do Azure AD Connect e da federação. Os utilizadores também podem resolver problemas e monitorizar registos utilizando esta função.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/aplicações/criar | Criar todos os tipos de aplicações |
> | microsoft.diretório/aplicações/delete | Eliminar todos os tipos de aplicações |
> | microsoft.diretório/aplicações/appRoles/update | Atualizar a propriedade appRoles em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/audiência/atualização | Atualizar a propriedade do público para aplicações |
> | microsoft.diretório/aplicações/autenticação/atualização | Atualizar a autenticação em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas para aplicações |
> | microsoft.diretório/aplicações/credenciais/atualização | Atualizar credenciais de aplicação |
> | microsoft.diretório/aplicações/proprietários/atualização | Atualizar os proprietários de aplicações |
> | microsoft.diretório/aplicações/permissões/atualização | Atualizar permissões expostas e permissões necessárias em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/políticas/atualização | Atualizar políticas de aplicações |
> | microsoft.diretório/aplicações/sincronização/standard/read | Leia as definições de provisionamento associadas ao objeto de aplicação |
> | microsoft.diretório/aplicaçãoTemplates/instantiate | Aplicações instantâneas de galeria a partir de modelos de aplicação |
> | microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades em registos de auditoria, incluindo propriedades privilegiadas |
> | microsoft.directy/cloudProvisioning/allProperties/allTasks | Leia e configuure todas as propriedades do serviço Azure AD Cloud Provisioning. |
> | microsoft.diretório/domínios/allProperties/read | Leia todas as propriedades dos domínios |
> | microsoft.diretório/domínios/federação/atualização | Atualizar propriedade da federação de domínios |
> | microsoft.diretório/organização/dirSync/update | Atualizar a propriedade de sincronização de diretórios de organização |
> | microsoft.diretório/provisioningLogs/allProperties/read | Leia todas as propriedades dos registos de provisionamento |
> | microsoft.diretório/serviçoPrincipals/criar | Criar principais de serviço |
> | microsoft.diretório/serviçoPrincipals/delete | Eliminar os principais de serviço |
> | microsoft.diretório/serviçoPrincipals/desativar | Diretores de serviço para desativação |
> | microsoft.diretório/serviçoPrincipals/enable | Ativar os principais de serviço |
> | microsoft.diretório/serviçoPrincipals/synchronizationCredentiss/manage | Gerir a aplicação que prevê segredos e credenciais |
> | microsoft.diretório/serviçoPrincipals/sincronizaçãoJobs/gerir | Iniciar, reiniciar e interromper a aplicação de provisão de empregos de sincronização |
> | microsoft.diretório/serviçoPrincipals/sincronizaçãoSchema/gerir | Criar e gerir os trabalhos de sincronização e esquemas de sincronização de aplicações |
> | microsoft.diretório/serviçoPrincipals/audience/update | Atualizar propriedades do público em diretores de serviço |
> | microsoft.diretório/serviçoPrincipals/autenticação/atualização | Atualizar propriedades de autenticação em principais de serviço |
> | microsoft.diretório/serviçoPrincipals/básico/atualização | Atualizar propriedades básicas em principais serviços |
> | microsoft.diretório/serviçoPrincipals/credenciais/atualização | Atualizar credenciais dos principais de serviços |
> | microsoft.diretório/serviçoPrincipals/proprietários/atualização | Atualizar os proprietários dos principados de serviços |
> | microsoft.diretório/serviçoPrincipals/permissões/atualização | Atualizar permissões dos principais serviços |
> | microsoft.diretório/serviçoPrincipals/políticas/atualização | Atualizar políticas dos principais serviços |
> | microsoft.diretório/serviçoPrincipals/tag/update | Atualizar a propriedade tag para os principados de serviço |
> | microsoft.diretório/serviçoPrincipals/sincronização/standard/read | Leia as definições de provisionamento associadas ao seu principal serviço |
> | microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades em relatórios de inscrição, incluindo propriedades privilegiadas |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.messageCenter/messages/read | Leia mensagens no Centro de Mensagens no centro de administração microsoft 365, excluindo mensagens de segurança |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="insights-administrator"></a>Administrador de Insights

Os utilizadores desta função podem aceder a todo o conjunto de capacidades administrativas na [aplicação M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Esta função tem a capacidade de ler informações de diretórios, monitorizar a saúde do serviço, bilhetes de suporte de ficheiros e aceder aos aspetos de definições de administração do Insights.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.insights/allEntities/allTasks | Gerir todos os aspetos da app Insights |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="insights-business-leader"></a>Insights Business Leader

Os utilizadores desta função podem aceder a um conjunto de dashboards e insights através da [aplicação M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Isto inclui acesso total a todos os dashboards e apresenta insights e funcionalidade de exploração de dados. Os utilizadores desta função não têm acesso às definições de configuração do produto, que é da responsabilidade da função Insights Admin.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.insights/reports/read | Ver relatórios e dashboard na app Insights |
> | microsoft.insights/programas/update | Implementar e gerir programas na app Insights |

## <a name="intune-administrator"></a>Administrador Intune

Os utilizadores com esta função têm permissões globais dentro do Microsoft Intune Online, quando o serviço está presente. Além disso, esta função contém a capacidade de gerir utilizadores e dispositivos de forma a associar a política, bem como criar e gerir grupos. Mais informações no [Controlo de Administração baseado em Role (RBAC) com a Microsoft Intune](/intune/role-based-access-control).

Este papel pode criar e gerir todos os grupos de segurança. No entanto, a Intune Admin não tem direitos de administração sobre grupos de escritórios. Isto significa que o administrador não pode atualizar proprietários ou membros de todos os grupos do Office na organização. No entanto, pode gerir o grupo de Escritório que cria e que vem como parte dos seus privilégios de utilizador final. Assim, qualquer grupo de Escritório (não grupo de segurança) que cria deve ser contabilizado contra a sua quota de 250.

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço Intune". É "Administrador Intune" no [portal Azure](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/bitlockerKeys/chave/ler | Leia metadados bitlocker e chave em dispositivos |
> | microsoft.diretório/contactos/criar | Criar contactos |
> | microsoft.diretório/contactos/excluir | Eliminar contactos |
> | microsoft.diretório/contactos/básico/atualização | Atualizar propriedades básicas em contactos |
> | microsoft.diretório/dispositivos/criar | Criar dispositivos (matricular-se em Azure AD) |
> | microsoft.diretório/dispositivos/delete | Eliminar dispositivos do Azure AD |
> | microsoft.diretório/dispositivos/desativar | Desativar dispositivos em Azure AD |
> | microsoft.diretório/dispositivos/enable | Ativar dispositivos em Azure AD |
> | microsoft.diretório/dispositivos/básico/atualização | Atualizar propriedades básicas em dispositivos |
> | microsoft.diretório/dispositivos/extensãoAttributes/update | Atualizar todos os valores para dispositivos.extensionA propriedade atribuída |
> | microsoft.diretório/dispositivos/registadosSowners/update | Atualizar os proprietários registados de dispositivos |
> | microsoft.diretório/dispositivos/registros/update | Atualizar utilizadores registados de dispositivos |
> | microsoft.diretório/dispositivoManagementPolicies/standard/read | Leia propriedades padrão nas políticas de aplicação de gestão de dispositivos |
> | microsoft.diretório/dispositivoRegistrationPolicy/standard/read | Ler propriedades padrão nas políticas de registo de dispositivos |
> | microsoft.diretório/grupos/hiddenMembers/read | Leia membros escondidos de um grupo |
> | microsoft.diretório/grupos.security/create | Criar grupos de segurança com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.security/delete | Eliminar grupos de segurança com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.security/basic/update | Atualizar propriedades básicas em grupos de segurança com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.security/classification/update | Atualizar a propriedade de classificação dos grupos de segurança com a exclusão de grupos atribuíveis por funções |
> | microsoft.directy/groups.security/dynamicMembershipRule/update | Atualização dinâmicaMembershipRule propriedade dos grupos de segurança com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.security/members/update | Atualizar membros de grupos de segurança com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.security/owners/update | Atualizar os proprietários de grupos de segurança com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.security/visibility/update | Atualizar a propriedade de visibilidade dos grupos de segurança com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/utilizadores/básico/atualização | Atualizar propriedades básicas nos utilizadores |
> | microsoft.diretório/utilizadores/gestor/atualização | Gestor de atualização para utilizadores |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.intune/allEntities/allTasks | Gerir todos os aspetos da Microsoft Intune |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="kaizala-administrator"></a>Administrador kaizala

Os utilizadores com esta função têm permissões globais para gerir as definições dentro da Microsoft Kaizala, quando o serviço está presente, bem como a capacidade de gerir bilhetes de suporte e monitorizar a saúde do serviço. Além disso, o utilizador pode aceder a relatórios relacionados com a adoção & uso de Kaizala por membros da Organização e relatórios de negócios gerados através das ações de Kaizala.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="knowledge-administrator"></a>Administrador de Conhecimento

Os utilizadores desta função têm acesso total a todos os conhecimentos, aprendizagens e funcionalidades inteligentes no centro de administração microsoft 365. Têm uma compreensão geral do conjunto de produtos, licenciamentos e têm a responsabilidade de controlar o acesso. O administrador de conhecimento pode criar e gerir conteúdos, como tópicos, siglas e recursos de aprendizagem. Além disso, estes utilizadores podem criar centros de conteúdo, monitorizar a saúde do serviço e criar pedidos de serviço.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/grupos.security/create | Criar grupos de segurança com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.security/createAsOwner | Criar grupos de Segurança com a exclusão de grupos atribuíveis por funções e criador é adicionado como o primeiro proprietário |
> | microsoft.diretório/grupos.security/delete | Eliminar grupos de segurança com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.security/basic/update | Atualizar propriedades básicas em grupos de segurança com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.security/members/update | Atualizar membros de grupos de segurança com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.security/owners/update | Atualizar os proprietários de grupos de segurança com a exclusão de grupos atribuíveis por funções |
> | microsoft.office365.knowledge/contentUnderstanding/allProperties/allTasks | Leia e atualize todas as propriedades da compreensão de conteúdos no Microsoft 365 admin center |
> | microsoft.office365.knowledge/knowledgeNetwork/allProperties/allTasks | Leia e atualize todas as propriedades da rede de conhecimento no Microsoft 365 admin center |
> | microsoft.office365.protectionCenter/sensitivityLabels/allProperties/read | Leia etiquetas de sensibilidade nos centros de segurança e conformidade |
> | microsoft.office365.sharePoint/allEntities/allTasks | Criar e eliminar todos os recursos e ler e atualizar propriedades padrão no SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="license-administrator"></a>Administrador de Licença

Os utilizadores desta função podem adicionar, remover e atualizar as atribuições de licenças em utilizadores, grupos (usando licenças baseadas em grupo) e gerir a localização de utilização nos utilizadores. A função não concede a capacidade de comprar ou gerir subscrições, criar ou gerir grupos, ou criar ou gerir utilizadores para além da localização de utilização. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/grupos/assignLicense | Atribuir licenças de produtos a grupos para licenciamento baseado em grupo |
> | microsoft.diretório/grupos/reprocessLicenseAssignment | Reprocessar as atribuições de licenças para licenciamento baseado em grupo |
> | microsoft.diretório/utilizadores/assignLicense | Gerir licenças de utilizador |
> | microsoft.diretório/utilizadores/reprocessLicenseAssignment | Reprocessar as atribuições de licenças para utilizadores |
> | microsoft.diretório/utilizadores/usageLocation/update | Atualizar a localização de utilização dos utilizadores |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="message-center-privacy-reader"></a>Leitor de Privacidade do Centro de Mensagens

Os utilizadores desta função podem monitorizar todas as notificações no Centro de Mensagens, incluindo mensagens de privacidade de dados. Os Leitores de Privacidade do Centro de Mensagens recebem notificações de e-mail, incluindo as relacionadas com a privacidade dos dados e podem cancelar a subscrição através das Preferências do Centro de Mensagens. Apenas o Administrador Global e o Leitor de Privacidade do Centro de Mensagens podem ler mensagens de privacidade de dados. Além disso, esta função contém a capacidade de visualizar grupos, domínios e subscrições. Esta função não tem permissão para visualizar, criar ou gerir pedidos de serviço.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Leia mensagens no Centro de Mensagens no centro de administração microsoft 365, excluindo mensagens de segurança |
> | microsoft.office365.messageCenter/securityMessages/read | Leia mensagens de segurança no Centro de Mensagens no centro de administração microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="message-center-reader"></a>Leitor do Centro de Mensagens

Os utilizadores desta função podem monitorizar notificações e atualizações de saúde de aconselhamento no [Centro de Mensagens](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) para a sua organização em serviços configurados como Exchange, Intune e Microsoft Teams. Os Leitores do Centro de Mensagens recebem digestão semanal de mensagens, atualizações e podem partilhar posts de centro de mensagens no Microsoft 365. No Azure AD, os utilizadores designados para esta função terão apenas acesso de leitura apenas a serviços AD Azure, como utilizadores e grupos. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Leia mensagens no Centro de Mensagens no centro de administração microsoft 365, excluindo mensagens de segurança |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="modern-commerce-user"></a>Utilizador de Comércio Moderno

Não utilizar. Esta função é automaticamente atribuída ao Comércio, e não se destina ou é suportada para qualquer outra utilização. Veja os detalhes abaixo.

A função de Utilizador do Comércio Moderno dá a certos utilizadores permissão para aceder ao centro de administração microsoft 365 e ver as entradas de navegação esquerda para **Home**, **Billing** e **Support**. O conteúdo disponível nestas áreas é controlado por [funções específicas do comércio](../../cost-management-billing/manage/understand-mca-roles.md) atribuídas aos utilizadores para gerir produtos que compraram para si ou para a sua organização. Isto pode incluir tarefas como pagar contas ou acesso a contas de faturação e perfis de faturação.

Os utilizadores com a função de Utilizador de Comércio Moderno normalmente têm permissões administrativas em outros sistemas de compra da Microsoft, mas não têm funções de administrador global ou administrador de faturação usadas para aceder ao centro de administração.

**Quando é atribuída a função de Utilizador do Comércio Moderno?**

* **A compra de self-service no Microsoft 365 admin center** – A compra de self-service dá aos utilizadores a oportunidade de experimentar novos produtos comprando ou inscrevendo-se por conta própria. Estes produtos são geridos no centro de administração. Os utilizadores que fazem uma compra de self-service são atribuídos a um papel no sistema de comércio, e o papel de Utilizador do Comércio Moderno para que possam gerir as suas compras no centro de administração. Os administradores podem bloquear as compras de self-service (para Power BI, Power Apps, Power automat) através [do PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell). Para obter mais informações, veja [Self-service purchase FAQ](/microsoft-365/commerce/subscriptions/self-service-purchase-faq) (FAQ da compra personalizada).
* **Compras no mercado comercial da Microsoft** – Semelhante à compra de self-service, quando um utilizador compra um produto ou serviço ao Microsoft AppSource ou ao Azure Marketplace, a função de Utilizador de Comércio Moderno é atribuída se não tiver o papel de administrador global ou administrador de faturação. Em alguns casos, os utilizadores podem estar impedidos de fazer estas compras. Para mais informações, consulte [o mercado comercial da Microsoft.](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase)
* **Propostas da Microsoft** – Uma proposta é uma oferta formal da Microsoft para a sua organização comprar produtos e serviços da Microsoft. Quando a pessoa que está a aceitar a proposta não tem um papel de administrador global ou administrador de faturação no Ad AD Azure, é-lhes atribuído um papel específico do comércio para completar a proposta e o papel de Utilizador do Comércio Moderno para aceder ao centro de administração. Quando acedem ao centro de administração, só podem usar funcionalidades que são autorizadas pelo seu papel específico do comércio.
* **Funções específicas do comércio** – Alguns utilizadores têm funções específicas do comércio. Se um utilizador não for um administrador global ou de faturação, obtém o papel de Utilizador do Comércio Moderno para que possa aceder ao centro de administração.

Se a função de Utilizador do Comércio Moderno não for atribuído a um utilizador, perde o acesso ao centro de administração microsoft 365. Se eles estavam a gerir quaisquer produtos, para si ou para a sua organização, eles não serão capazes de geri-los. Isto pode incluir a atribuição de licenças, alteração de métodos de pagamento, pagamento de contas ou outras tarefas para a gestão de subscrições.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.commerce.billing/partners/read | Leia propriedade parceira da Microsoft 365 Billing |
> | microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | Gerir todos os aspetos do Centro de Serviços de Licenciamento de Volume |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="network-administrator"></a>Administrador de rede

Os utilizadores desta função podem rever recomendações de arquitetura de perímetro de rede da Microsoft que são baseadas em telemetria de rede a partir das suas localizações de utilizadores. O desempenho da rede para o Microsoft 365 baseia-se numa arquitetura cuidadosa do perímetro da rede de clientes da empresa, que é geralmente específica da localização do utilizador. Esta função permite a edição das localizações do utilizador descobertas e a configuração de parâmetros de rede para esses locais para facilitar a melhoria das medições de telemetria e recomendações de design

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.office365.network/locations/allProperties/allTasks | Gerir todos os aspetos das localizações da rede |
> | microsoft.office365.network/performance/allProperties/read | Leia todas as propriedades de desempenho da rede no centro de administração Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="office-apps-administrator"></a>Administrador de Aplicações de Escritório

Os utilizadores desta função podem gerir as definições de cloud das aplicações da Microsoft 365. Isto inclui gerir políticas de nuvem, gestão de descarregamento de autosserviço e a capacidade de visualizar relatório relacionado com aplicações do Office. Esta função também garante a capacidade de gerir os bilhetes de apoio e monitorizar a saúde do serviço dentro do principal centro de administração. Os utilizadores destacados para esta função também podem gerir a comunicação de novas funcionalidades em aplicações do Office.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.messageCenter/messages/read | Leia mensagens no Centro de Mensagens no centro de administração microsoft 365, excluindo mensagens de segurança |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.userCommunication/allEntities/allTasks | Leia e atualize a visibilidade das novas mensagens |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="partner-tier1-support"></a>Suporte parceiro Tier1

Não utilizar. Este papel foi depreciado e será removido da Azure AD no futuro. Esta função destina-se a ser utilizada por um pequeno número de parceiros de revenda da Microsoft, e não se destina a ser utilizada em geral.

> [!IMPORTANT]
> Esta função pode redefinir palavras-passe e invalidar fichas de atualização apenas para não administradores. Esta função não deve ser utilizada uma vez que é depreciada e deixará de ser devolvida na API.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/aplicações/appRoles/update | Atualizar a propriedade appRoles em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/audiência/atualização | Atualizar a propriedade do público para aplicações |
> | microsoft.diretório/aplicações/autenticação/atualização | Atualizar a autenticação em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas para aplicações |
> | microsoft.diretório/aplicações/credenciais/atualização | Atualizar credenciais de aplicação |
> | microsoft.diretório/aplicações/proprietários/atualização | Atualizar os proprietários de aplicações |
> | microsoft.diretório/aplicações/permissões/atualização | Atualizar permissões expostas e permissões necessárias em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/políticas/atualização | Atualizar políticas de aplicações |
> | microsoft.diretório/contactos/criar | Criar contactos |
> | microsoft.diretório/contactos/excluir | Eliminar contactos |
> | microsoft.diretório/contactos/básico/atualização | Atualizar propriedades básicas em contactos |
> | microsoft.diretório/grupos/criar | Criar grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/delete | Eliminar grupos, excluindo o grupo que atribui funções |
> | microsoft.diretório/grupos/restaurar | Restaurar os grupos eliminados |
> | microsoft.diretório/grupos/membros/atualização | Atualizar membros de grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/proprietários/atualização | Atualizar os proprietários de grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/oAuth2PermissionGrants/allProperties/allTasks | Criar e eliminar bolsas de permissão OAuth 2.0 e ler e atualizar todas as propriedades |
> | microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update | Atualizar atribuições de funções principais de serviço |
> | microsoft.diretório/utilizadores/assignLicense | Gerir licenças de utilizador |
> | microsoft.diretório/utilizadores/criar | Adicionar utilizadores |
> | microsoft.diretório/utilizadores/delete | Eliminar utilizadores |
> | microsoft.diretório/utilizadores/desativar | Desativar utilizadores |
> | microsoft.diretório/utilizadores/enable | Ativar os utilizadores |
> | microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Forçar a aprovação invalidando tokens de atualização do utilizador |
> | microsoft.diretório/utilizadores/restaurar | Restaurar utilizadores eliminados |
> | microsoft.diretório/utilizadores/básico/atualização | Atualizar propriedades básicas nos utilizadores |
> | microsoft.diretório/utilizadores/gestor/atualização | Gestor de atualização para utilizadores |
> | microsoft.diretório/utilizadores/password/atualização | Redefinir palavras-passe para todos os utilizadores |
> | microsoft.diretório/utilizadores/userPrincipalName/update | Atualizar Nome Principal do Utilizador dos utilizadores |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="partner-tier2-support"></a>Suporte parceiro Tier2

Não utilizar. Este papel foi depreciado e será removido da Azure AD no futuro. Esta função destina-se a ser utilizada por um pequeno número de parceiros de revenda da Microsoft, e não se destina a ser utilizada em geral.

> [!IMPORTANT]
> Esta função pode redefinir palavras-passe e invalidar fichas de atualização para todos os administradores e administradores não administradores (incluindo administradores globais). Esta função não deve ser utilizada uma vez que é depreciada e deixará de ser devolvida na API.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/aplicações/appRoles/update | Atualizar a propriedade appRoles em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/audiência/atualização | Atualizar a propriedade do público para aplicações |
> | microsoft.diretório/aplicações/autenticação/atualização | Atualizar a autenticação em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas para aplicações |
> | microsoft.diretório/aplicações/credenciais/atualização | Atualizar credenciais de aplicação |
> | microsoft.diretório/aplicações/proprietários/atualização | Atualizar os proprietários de aplicações |
> | microsoft.diretório/aplicações/permissões/atualização | Atualizar permissões expostas e permissões necessárias em todos os tipos de aplicações |
> | microsoft.diretório/aplicações/políticas/atualização | Atualizar políticas de aplicações |
> | microsoft.diretório/contactos/criar | Criar contactos |
> | microsoft.diretório/contactos/excluir | Eliminar contactos |
> | microsoft.diretório/contactos/básico/atualização | Atualizar propriedades básicas em contactos |
> | microsoft.diretório/domínios/básico/allTasks | Criar e eliminar domínios e ler e atualizar propriedades padrão |
> | microsoft.diretório/grupos/criar | Criar grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/delete | Eliminar grupos, excluindo o grupo que atribui funções |
> | microsoft.diretório/grupos/restaurar | Restaurar os grupos eliminados |
> | microsoft.diretório/grupos/membros/atualização | Atualizar membros de grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/proprietários/atualização | Atualizar os proprietários de grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/oAuth2PermissionGrants/allProperties/allTasks | Criar e eliminar bolsas de permissão OAuth 2.0 e ler e atualizar todas as propriedades |
> | microsoft.diretório/organização/básico/atualização | Atualizar propriedades básicas na organização |
> | microsoft.diretório/papelAssignments/allProperties/allTasks | Criar e eliminar atribuições de funções e ler e atualizar todas as propriedades de atribuição de funções |
> | microsoft.diretório/roleDefinitions/allProperties/allTasks | Criar e eliminar definições de funções, e ler e atualizar todas as propriedades |
> | microsoft.diretório/scopedRoleMemberships/allProperties/allTasks | Criar e eliminar as empresas scopedRoleMemberberships e ler e atualizar todas as propriedades |
> | microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update | Atualizar atribuições de funções principais de serviço |
> | microsoft.diretório/subscritoSkus/standard/read | Ler propriedades básicas em subscrições |
> | microsoft.diretório/utilizadores/assignLicense | Gerir licenças de utilizador |
> | microsoft.diretório/utilizadores/criar | Adicionar utilizadores |
> | microsoft.diretório/utilizadores/delete | Eliminar utilizadores |
> | microsoft.diretório/utilizadores/desativar | Desativar utilizadores |
> | microsoft.diretório/utilizadores/enable | Ativar os utilizadores |
> | microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Forçar a aprovação invalidando tokens de atualização do utilizador |
> | microsoft.diretório/utilizadores/restaurar | Restaurar utilizadores eliminados |
> | microsoft.diretório/utilizadores/básico/atualização | Atualizar propriedades básicas nos utilizadores |
> | microsoft.diretório/utilizadores/gestor/atualização | Gestor de atualização para utilizadores |
> | microsoft.diretório/utilizadores/password/atualização | Redefinir palavras-passe para todos os utilizadores |
> | microsoft.diretório/utilizadores/userPrincipalName/update | Atualizar Nome Principal do Utilizador dos utilizadores |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="password-administrator"></a>Administrador de password

Os utilizadores com esta função têm capacidade limitada para gerir senhas. Esta função não garante a capacidade de gerir pedidos de serviço ou monitorizar a saúde do serviço. Se um Administrador de Palavra-Passe pode redefinir a palavra-passe de um utilizador depende da função que o utilizador é atribuído. Para obter uma lista das funções para as quais um administrador de passwords pode redefinir as palavras-passe, consulte [permissões de reset de palavra-passe](#password-reset-permissions).

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/utilizadores/password/atualização | Redefinir palavras-passe para todos os utilizadores |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="power-bi-administrator"></a>Administrador de Bi de Energia

Os utilizadores com esta função têm permissões globais dentro do Microsoft Power BI, quando o serviço está presente, bem como a capacidade de gerir bilhetes de suporte e monitorizar a saúde do serviço. Mais informações na [Compreensão do papel de administrador do Power BI](/power-bi/service-admin-role).

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço de Power BI". É "Power BI Administrator" no [portal Azure](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Gerir todos os aspetos do Power BI |

## <a name="power-platform-administrator"></a>Administrador da Plataforma de Energia

Os utilizadores desta função podem criar e gerir todos os aspetos de ambientes, PowerApps, Flows, Políticas de Prevenção de Perda de Dados. Além disso, os utilizadores com esta função têm a capacidade de gerir bilhetes de apoio e monitorizar a saúde do serviço.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.dynamics365/allEntities/allTasks | Gerir todos os aspetos da Dinâmica 365 |
> | microsoft.flow/allEntities/allTasks | Gerir todos os aspetos do Microsoft Power Automamate |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |
> | microsoft.powerApps/allEntities/allTasks | Gerir todos os aspetos das Power Apps |

## <a name="printer-administrator"></a>Administrador de impressora

Os utilizadores desta função podem registar impressoras e gerir todos os aspetos de todas as configurações da impressora na solução Microsoft Universal Print, incluindo as definições do Conector de Impressão Universal. Podem consentir com todos os pedidos de autorização de impressão delegados. Os administradores da impressora também têm acesso a relatórios de impressão.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.print/allEntities/allProperties/allTasks | Criar e eliminar impressoras e conectores e ler e atualizar todas as propriedades no Microsoft Print |

## <a name="printer-technician"></a>Técnico de Impressora

Os utilizadores com esta função podem registar impressoras e gerir o estado da impressora na solução De Impressão Universal da Microsoft. Também podem ler todas as informações do conector. A tarefa chave que um Técnico de Impressora não pode fazer é definir permissões do utilizador em impressoras e partilhar impressoras.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.print/connectors/allProperties/read | Leia todas as propriedades dos conectores na Microsoft Print |
> | microsoft.azure.print/printers/allProperties/read | Leia todas as propriedades das impressoras na Microsoft Print |
> | microsoft.azure.print/printers/register | Registar impressoras na Microsoft Print |
> | microsoft.azure.print/printers/unregister | Impressoras não registadas na Microsoft Print |
> | microsoft.azure.print/printers/basic/update | Atualizar propriedades básicas de impressoras na Microsoft Print |

## <a name="privileged-authentication-administrator"></a>Administrador de Autenticação Privilegiada

Os utilizadores com esta função podem definir ou redefinir qualquer método de autenticação (incluindo palavras-passe) para qualquer utilizador, incluindo Administradores Globais. Os Administradores de Autenticação Privilegiada podem forçar os utilizadores a re-registarem-se contra a credencial não-senha existente (como MFA ou FIDO) e revogar "lembrem-se de MFA no dispositivo", solicitando para MFA na próxima entrada de todos os utilizadores.

A [função de administrador de autenticação](#authentication-administrator) tem permissão para forçar o reinscúlido e a autenticação de vários fatores para utilizadores padrão e utilizadores com algumas funções de administração.

A [função de administrador de política de autenticação](#authentication-policy-administrator) tem permissões para definir a política de método de autenticação do arrendatário que determina quais os métodos que cada utilizador pode registar e utilizar.

| Função | Gerir os métodos de auth do utilizador | Gerir por utilizador MFA | Gerir as definições de MFA | Gerir a política do método auth | Gerir a política de proteção de senhas |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Administrador de autenticação | Sim para alguns utilizadores (ver acima) | Sim para alguns utilizadores (ver acima) | No | No | No |
| Administrador de autenticação privilegiada| Sim para todos os utilizadores | Sim para todos os utilizadores | No | No | No |
| Administrador de política de autenticação | No | No | Yes | Yes | Yes |

> [!IMPORTANT]
> Os utilizadores com esta função podem alterar credenciais para pessoas que possam ter acesso a informações sensíveis ou privadas ou configuração crítica dentro e fora do Azure Ative Directory. Alterar as credenciais de um utilizador pode significar a capacidade de assumir a identidade e permissões desse utilizador. Por exemplo:
>
>* Os proprietários de Registo de Aplicações e Aplicações Empresariais, que podem gerir credenciais de apps que possuam. Essas aplicações podem ter permissões privilegiadas em Azure AD e em outros lugares não concedidos a Administradores de Autenticação. Por este caminho um Administrador de Autenticação pode assumir a identidade de titular de uma candidatura e, em seguida, assumir a identidade de uma aplicação privilegiada atualizando as credenciais para a aplicação.
>* Proprietários de subscrições Azure, que podem ter acesso a informações sensíveis ou privadas ou configuração crítica em Azure.
>* Security Group e Microsoft 365 proprietários do grupo, que podem gerir a adesão ao grupo. Esses grupos podem conceder acesso a informações sensíveis ou privadas ou configuração crítica em Azure AD e em outros lugares.
>* Administradores em outros serviços fora da Azure AD como Exchange Online, Office Security and Compliance Center, e sistemas de recursos humanos.
>* Não administradores como executivos, advogados e funcionários de recursos humanos que possam ter acesso a informações confidenciais ou privadas.


> [!IMPORTANT]
> Esta função não é atualmente capaz de gerir o MFA por utilizador no antigo portal de gestão de MFA. As mesmas funções podem ser executadas utilizando o módulo Azure AD Powershell do comando [Set-MsolUser.](/powershell/module/msonline/set-msoluser)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Forçar a aprovação invalidando tokens de atualização do utilizador |
> | microsoft.diretório/utilizadores/strongAuthentication/update | Atualizar a forte propriedade de autenticação para os utilizadores |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="privileged-role-administrator"></a>Administrador privilegiado

Os utilizadores com esta função podem gerir atribuições de funções no Azure Ative Directory, bem como dentro da Azure AD Privileged Identity Management. Podem criar e gerir grupos que podem ser atribuídos a funções AZure AD. Além disso, esta função permite a gestão de todos os aspetos da Gestão de Identidade Privilegiada e das unidades administrativas.

> [!IMPORTANT]
> Esta função confere a capacidade de gerir atribuições para todas as funções de AD Azure, incluindo o papel de Administrador Global. Esta função não inclui quaisquer outras habilidades privilegiadas no AD Azure, como criar ou atualizar utilizadores. No entanto, os utilizadores destacados para esta função podem conceder a si mesmos ou a outros privilégios adicionais atribuindo funções adicionais.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/administrativoSIns/allProperties/allTasks | Criar e gerir unidades administrativas (incluindo membros) |
> | microsoft.diretório/appRoleAssignments/allProperties/allTasks | Criar e apagar apRoleAssignments e ler e atualizar todas as propriedades |
> | microsoft.diretório/autorizaçãoPolícia/allProperties/allTasks | Gerir todos os aspetos das políticas de autorização |
> | microsoft.diretório/directórioRoles/allProperties/allTasks | Criar e apagar funções de diretório, e ler e atualizar todas as propriedades |
> | microsoft.diretório/gruposAssignableToRoles/create | Criar grupos aos quais se pode atribuir funções |
> | microsoft.diretório/gruposAssignableToRoles/delete | Eliminar grupos atribuíveis por funções |
> | microsoft.diretório/gruposAssignableToRoles/restauro | Restaurar grupos atribuíveis por funções |
> | microsoft.diretório/gruposAssignableToRoles/allProperties/update | Atualizar grupos atribuíveis por funções |
> | microsoft.diretório/oAuth2PermissionGrants/allProperties/allTasks | Criar e eliminar bolsas de permissão OAuth 2.0 e ler e atualizar todas as propriedades |
> | microsoft.diretório/privilegiadoIdmanagement/allProperties/allTasks | Criar e eliminar todos os recursos e ler e atualizar propriedades padrão na Gestão de Identidade Privilegiada |
> | microsoft.diretório/papelAssignments/allProperties/allTasks | Criar e eliminar atribuições de funções e ler e atualizar todas as propriedades de atribuição de funções |
> | microsoft.diretório/roleDefinitions/allProperties/allTasks | Criar e eliminar definições de funções, e ler e atualizar todas as propriedades |
> | microsoft.diretório/scopedRoleMemberships/allProperties/allTasks | Criar e eliminar as empresas scopedRoleMemberberships e ler e atualizar todas as propriedades |
> | microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update | Atualizar atribuições de funções principais de serviço |
> | microsoft.diretório/serviçoPrincipals/permissões/atualização | Atualizar permissões dos principais serviços |
> | microsoft.diretório/serviçoPrincipals/managePermissionGrantsForAll.microsoft-company-admin | Conceder consentimento para qualquer permissão a qualquer pedido |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="reports-reader"></a>Leitor de Relatórios

Os utilizadores com esta função podem visualizar dados de reporte de utilização e o painel de relatórios no centro de administração microsoft 365 e o pacote de contexto de adoção no Power BI. Além disso, a função fornece acesso a relatórios de inscrição e atividade em AD Azure e dados devolvidos pela API de relatório do Microsoft Graph. Um utilizador atribuído à função Reports Reader só pode aceder a métricas de utilização e adoção relevantes. Não têm permissões de administração para configurar definições ou aceder aos centros de administração específicos do produto, como o Exchange. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades em registos de auditoria, incluindo propriedades privilegiadas |
> | microsoft.diretório/provisioningLogs/allProperties/read | Leia todas as propriedades dos registos de provisionamento |
> | microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades em relatórios de inscrição, incluindo propriedades privilegiadas |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.office365.network/performance/allProperties/read | Leia todas as propriedades de desempenho da rede no centro de administração Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Ler relatórios de utilização do Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="search-administrator"></a>Administrador de Pesquisa

Os utilizadores desta função têm acesso total a todas as funcionalidades de gestão da Microsoft Search no centro de administração microsoft 365. Além disso, estes utilizadores podem ver o centro de mensagens, monitorizar a saúde do serviço e criar pedidos de serviço.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Leia mensagens no Centro de Mensagens no centro de administração microsoft 365, excluindo mensagens de segurança |
> | microsoft.office365.search/content/manage | Criar e eliminar conteúdo, e ler e atualizar todas as propriedades no Microsoft Search |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="search-editor"></a>Editor de Pesquisa

Os utilizadores desta função podem criar, gerir e eliminar conteúdo para o Microsoft Search no centro de administração microsoft 365, incluindo marcadores, Q&As e localizações.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Leia mensagens no Centro de Mensagens no centro de administração microsoft 365, excluindo mensagens de segurança |
> | microsoft.office365.search/content/manage | Criar e eliminar conteúdo, e ler e atualizar todas as propriedades no Microsoft Search |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="security-administrator"></a>Administrador de Segurança

Os utilizadores com esta função têm permissões para gerir funcionalidades relacionadas com a segurança no centro de segurança Microsoft 365, Azure Ative Directory Identity Protection, Azure Ative Directory Authentication, Azure Information Protection e Office 365 Security & Compliance Center. Mais informações sobre permissões do Office 365 estão disponíveis [em Permissões no Centro de Conformidade & de Segurança.](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)

Em | Pode fazer
--- | ---
[Centro de segurança Microsoft 365](https://protection.office.com) | Monitorize políticas relacionadas com a segurança em todos os serviços da Microsoft 365<br>Gerir ameaças e alertas de segurança<br>Ver relatórios
Centro de Proteção de Identidade | Todas as permissões do papel do Leitor de Segurança<br>Além disso, a capacidade de realizar todas as operações do Centro de Proteção de Identidade, exceto para repor palavras-passe
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Todas as permissões do papel do Leitor de Segurança<br>**Não é possível** gerir atribuições ou configurações de funções AZure AD
[Escritório 365 Centro de Conformidade & de Segurança](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Manage security policies (Gerir políticas de segurança)<br>Ver, investigar e responder a ameaças de segurança<br>Ver relatórios
Proteção Avançada Contra Ameaças do Azure | Monitorize e responda a atividades de segurança suspeitas
Windows Defender ATP e EDR | Atribuir funções<br>Gerir grupos de máquinas<br>Configure a deteção de ameaças de ponto final e a remediação automatizada<br>Ver, investigar e responder a alertas
[Intune](/intune/role-based-access-control) | Visualizações de informações do utilizador, dispositivo, inscrição, configuração e aplicação<br>Não é possível fazer alterações ao Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Adicionar administradores, adicionar políticas e configurações, carregar registos e executar ações de governação
[Centro de Segurança do Azure](../../key-vault/managed-hsm/built-in-roles.md) | Pode ver políticas de segurança, ver estados de segurança, editar políticas de segurança, ver alertas e recomendações, rejeitar alertas e recomendações
[Saúde do serviço Microsoft 365](/office365/enterprise/view-service-health) | Ver a saúde dos serviços microsoft 365
[Bloqueio inteligente](../authentication/howto-password-smart-lockout.md) | Defina o limiar e a duração dos bloqueios quando ocorrerem eventos de entrada falhados.
[Proteção de passwords](../authentication/concept-password-ban-bad.md) | Configurar a lista de senhas proibidas personalizadas ou a proteção de senhas no local.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/aplicações/políticas/atualização | Atualizar políticas de aplicações |
> | microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades em registos de auditoria, incluindo propriedades privilegiadas |
> | microsoft.diretório/bitlockerKeys/chave/ler | Leia metadados bitlocker e chave em dispositivos |
> | microsoft.diretório/direitoManagement/allProperties/read | Leia todos os imóveis na gestão de direitos AD da Azure |
> | microsoft.diretório/identidadeProteção/todas as ofertas/ler | Leia todos os recursos na Azure AD Identity Protection |
> | microsoft.diretório/identidadeProteção/todas as ofertas/atualização | Atualizar todos os recursos na Proteção de Identidade AZure AD |
> | microsoft.diretório/políticas/criar | Criar políticas em Azure AD |
> | microsoft.diretório/políticas/excluir | Eliminar políticas em Azure AD |
> | microsoft.diretório/políticas/básico/atualização | Atualizar propriedades básicas em políticas |
> | microsoft.diretório/políticas/proprietários/atualização | Atualizar os proprietários de políticas |
> | microsoft.diretório/políticas/tenantDefault/update | Atualizar as políticas de organização padrão |
> | microsoft.diretório/condicionalAccessPolicies/create | Criar políticas de acesso condicional |
> | microsoft.diretório/condicionalAccessPolicies/delete | Eliminar políticas de acesso condicional |
> | microsoft.diretório/condicionalAccessPolicies/standard/read | Ler políticas.conditionalAposa propriedade |
> | microsoft.diretório/condicionalAccessPolicies/owners/read | Ler políticas.conditionalAposa propriedade |
> | microsoft.diretório/condicionalAccessPolicies/policyAppliedTo/read | Ler políticas.conditionalAposa propriedade |
> | microsoft.diretório/condicionalAccessPolicies/basic/update | Atualizar propriedades básicas para políticas de acesso condicional |
> | microsoft.diretório/condicionalAccessPolicies/owners/update | Atualização de políticas.propriedade deacessoacis |
> | microsoft.diretório/condicionalAccessPolicies/tenantDefault/update | Atualização de políticas.propriedade deacessoacis |
> | microsoft.diretório/privilegiadoIdmentManagement/allProperties/read | Ler todos os recursos em Gestão de Identidade Privilegiada |
> | microsoft.diretório/provisioningLogs/allProperties/read | Leia todas as propriedades dos registos de provisionamento |
> | microsoft.diretório/serviçoPrincipals/políticas/atualização | Atualizar políticas dos principais serviços |
> | microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades em relatórios de inscrição, incluindo propriedades privilegiadas |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Ler propriedades padrão de todos os recursos nos centros de Segurança e Conformidade |
> | microsoft.office365.protectionCenter/allEntities/basic/update | Atualizar propriedades básicas de todos os recursos nos centros de Segurança e Conformidade |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Criar e gerir cargas de ataque no Simulador de Ataque |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Leia relatos de simulação de ataque, respostas e treino associado |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Criar e gerir modelos de simulação de ataque no Simulador de Ataque |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="security-operator"></a>Operador de Segurança

Os utilizadores com esta função podem gerir alertas e ter acesso global apenas de leitura sobre funcionalidades relacionadas com a segurança, incluindo todas as informações no centro de segurança Microsoft 365, Diretório Ativo Azure, Proteção de Identidade, Gestão de Identidade Privilegiada e Centro de Segurança & Compliance Center do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis [em Permissões no Centro de Conformidade & de Segurança.](/office365/securitycompliance/permissions-in-the-security-and-compliance-center)

Em | Pode fazer
--- | ---
[Centro de segurança Microsoft 365](https://protection.office.com) | Todas as permissões do papel do Leitor de Segurança<br>Ver, investigar e responder a alertas de ameaças à segurança
Azure AD Identity Protection | Todas as permissões do papel do Leitor de Segurança<br>Além disso, a capacidade de realizar todas as operações do Centro de Proteção de Identidade, exceto para redefinir palavras-passe e configurar e-mails de alerta.
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Todas as permissões do papel do Leitor de Segurança
[Escritório 365 Centro de Conformidade & de Segurança](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Todas as permissões do papel do Leitor de Segurança<br>Ver, investigar e responder aos alertas de segurança
Windows Defender ATP e EDR | Todas as permissões do papel do Leitor de Segurança<br>Ver, investigar e responder aos alertas de segurança
[Intune](/intune/role-based-access-control) | Todas as permissões do papel do Leitor de Segurança
[Cloud App Security](/cloud-app-security/manage-admins) | Todas as permissões do papel do Leitor de Segurança
[Saúde do serviço Microsoft 365](/office365/enterprise/view-service-health) | Ver a saúde dos serviços microsoft 365

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades em registos de auditoria, incluindo propriedades privilegiadas |
> | microsoft.directy/cloudAppSecurity/allProperties/allTasks | Criar e eliminar todos os recursos e ler e atualizar propriedades padrão na Microsoft Cloud App Security |
> | microsoft.diretório/identidadeProtecção/todas as Ofertas/allTasks | Criar e eliminar todos os recursos e ler e atualizar propriedades padrão na Proteção de Identidade AZure AD |
> | microsoft.diretório/privilegiadoIdmentManagement/allProperties/read | Ler todos os recursos em Gestão de Identidade Privilegiada |
> | microsoft.diretório/provisioningLogs/allProperties/read | Leia todas as propriedades dos registos de provisionamento |
> | microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades em relatórios de inscrição, incluindo propriedades privilegiadas |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Gerir todos os aspetos da Azure Advanced Threat Protection |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.intune/allEntities/read | Leia todos os recursos no Microsoft Intune |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Criar e eliminar todos os recursos e ler e atualizar propriedades padrão no Microsoft 365 Security and Compliance Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | Gerir todos os aspetos do Microsoft Defender para Endpoint |

## <a name="security-reader"></a>Leitor de Segurança

Os utilizadores com esta função têm acesso global apenas à leitura sobre funcionalidades relacionadas com a segurança, incluindo todas as informações no centro de segurança microsoft 365, Diretório Ativo Azure, Proteção de Identidade, Gestão de Identidade Privilegiada, bem como a capacidade de ler relatórios de login e registos de auditoria do Azure Ative Directory, e no Office 365 Security & Compliance Center. Mais informações sobre permissões do Office 365 estão disponíveis [em Permissões no Centro de Conformidade & de Segurança.](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)

Em | Pode fazer
--- | ---
[Centro de segurança Microsoft 365](https://protection.office.com) | Ver políticas relacionadas com segurança em todos os serviços da Microsoft 365<br>Ver ameaças de segurança e alertas<br>Ver relatórios
Centro de Proteção de Identidade | Leia todos os relatórios de segurança e definições de informações para funcionalidades de segurança<br><ul><li>Anti-correio publicitário não-correio publicitário não-correio publicitário não-<li>Encriptação<li>Prevenção de perda de dados<li>Anti-malware<li>Proteção avançada contra ameaças<li>Anti-phishing<li>Regras de fluxo de correio
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Tem acesso apenas de leitura a todas as informações surgidas na Azure AD Privileged Identity Management: Políticas e relatórios para atribuições de funções AD AZure e revisões de segurança.<br>**Não é possível** inscrever-se na Azure AD Privileged Identity Management ou fazer quaisquer alterações à sua. No portal de Gestão de Identidade Privilegiada ou via PowerShell, alguém nesta função pode ativar funções adicionais (por exemplo, Administrador Global ou Administrador de Função Privilegiada), se o utilizador for elegível para eles.
[Escritório 365 Centro de Conformidade & de Segurança](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Ver as políticas de segurança<br>Ver e investigar ameaças à segurança<br>Ver relatórios
Windows Defender ATP e EDR | Ver e investigar alertas. Quando liga o controlo de acesso baseado em funções no Windows Defender ATP, os utilizadores com permissões apenas de leitura, como a função de leitor de Segurança Ad Ad Azure, perdem acesso até serem atribuídos a uma função ATP do Windows Defender.
[Intune](/intune/role-based-access-control) | Visualiza informações do utilizador, dispositivo, inscrição, configuração e aplicação. Não pode fazer alterações ao Intune.
[Cloud App Security](/cloud-app-security/manage-admins) | Tem permissões só de leitura e pode gerir alertas
[Centro de Segurança do Azure](../../key-vault/managed-hsm/built-in-roles.md) | Pode ver recomendações e alertas, ver políticas de segurança, ver estados de segurança, mas não pode fazer alterações
[Saúde do serviço Microsoft 365](/office365/enterprise/view-service-health) | Ver a saúde dos serviços microsoft 365

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades em registos de auditoria, incluindo propriedades privilegiadas |
> | microsoft.diretório/bitlockerKeys/chave/ler | Leia metadados bitlocker e chave em dispositivos |
> | microsoft.diretório/direitoManagement/allProperties/read | Leia todos os imóveis na gestão de direitos AD da Azure |
> | microsoft.diretório/identidadeProteção/todas as ofertas/ler | Leia todos os recursos na Azure AD Identity Protection |
> | microsoft.diretório/políticas/standard/read | Ler propriedades básicas sobre políticas |
> | microsoft.diretório/políticas/proprietários/ler | Ler os proprietários de políticas |
> | microsoft.diretório/políticas/policyAppliedTo/read | Ler políticas.policyAppliedTo propriedade |
> | microsoft.diretório/condicionalAccessPolicies/standard/read | Ler políticas.conditionalAposa propriedade |
> | microsoft.diretório/condicionalAccessPolicies/owners/read | Ler políticas.conditionalAposa propriedade |
> | microsoft.diretório/condicionalAccessPolicies/policyAppliedTo/read | Ler políticas.conditionalAposa propriedade |
> | microsoft.diretório/privilegiadoIdmentManagement/allProperties/read | Ler todos os recursos em Gestão de Identidade Privilegiada |
> | microsoft.diretório/provisioningLogs/allProperties/read | Leia todas as propriedades dos registos de provisionamento |
> | microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades em relatórios de inscrição, incluindo propriedades privilegiadas |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Ler propriedades padrão de todos os recursos nos centros de Segurança e Conformidade |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/read | Leia todas as propriedades das cargas de ataque no Simulador de Ataque |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Leia relatos de simulação de ataque, respostas e treino associado |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/read | Leia todas as propriedades dos modelos de simulação de ataque no Simulador de Ataque |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="service-support-administrator"></a>Administrador de Suporte de Serviço

Os utilizadores com esta função podem abrir pedidos de suporte com os serviços da Microsoft para os serviços Azure e Microsoft 365, e visualizar o painel de serviço e o centro de mensagens no [portal Azure](https://portal.azure.com) e [no Microsoft 365.](https://admin.microsoft.com) Mais informações sobre [funções de administração.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> Anteriormente, esta função chamava-se "Administrador de Serviço" no [portal Azure](https://portal.azure.com) e [no Microsoft 365.](https://admin.microsoft.com) Rebatizámo-lo para "Administrador de Suporte de Serviço" para alinhar com o nome de exsiting na Microsoft Graph API, Azure AD Graph API e Azure AD PowerShell.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.network/performance/allProperties/read | Leia todas as propriedades de desempenho da rede no centro de administração Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="sharepoint-administrator"></a>Administrador do SharePoint

Os utilizadores com esta função têm permissões globais dentro do Microsoft SharePoint Online, quando o serviço está presente, bem como a capacidade de criar e gerir todos os grupos microsoft 365, gerir bilhetes de suporte e monitorizar a saúde do serviço. Mais informações sobre [funções de administração.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço SharePoint". É "SharePoint Administrator" no [portal Azure](https://portal.azure.com).

> [!NOTE]
> Esta função também concede permissões de âmbito à API do Microsoft Graph para o Microsoft Intune, permitindo a gestão e configuração de políticas relacionadas com os recursos sharePoint e OneDrive.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/grupos.unificado/criar | Criar grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.unificado/delete | Eliminar os grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.unificado/restaurar | Restaurar os grupos Microsoft 365 |
> | microsoft.diretório/grupos.unificado/básico/atualização | Atualizar propriedades básicas em grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.unificado/membros/atualização | Atualizar membros de grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.unificado/proprietários/atualização | Atualizar os proprietários de grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.network/performance/allProperties/read | Leia todas as propriedades de desempenho da rede no centro de administração Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.sharePoint/allEntities/allTasks | Criar e eliminar todos os recursos e ler e atualizar propriedades padrão no SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Ler relatórios de utilização do Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="skype-for-business-administrator"></a>Skype para Administrador de Negócios

Os utilizadores com esta função têm permissões globais dentro do Microsoft Skype para negócios, quando o serviço está presente, bem como gerir atributos de utilizador específicos do Skype no Azure Ative Directory. Além disso, esta função garante a capacidade de gerir bilhetes de apoio e monitorizar a saúde do serviço, e aceder às Equipas e Skype para o Business Admin Center. A conta também deve ser licenciada para equipas ou não pode executar as equipas PowerShell cmdlets. Mais informações sobre [o papel de administrador de negócios](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) do Skype para negócios e equipas que licenciam informações no Skype para licenciamento de [empresas e equipas da Microsoft Teams](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> Na Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço Lync". É "Skype for Business Administrator" no [portal Azure](https://portal.azure.com/).

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerir todos os aspetos do Skype para business online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Ler relatórios de utilização do Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="teams-administrator"></a>Administrador de Equipas

Os utilizadores desta função podem gerir todos os aspetos da carga de trabalho das Equipas Microsoft através das Equipas microsoft & Skype para centro de administração de negócios e os respetivos módulos PowerShell. Isto inclui, entre outras áreas, todas as ferramentas de gestão relacionadas com a telefonia, mensagens, reuniões e as próprias equipas. Esta função também oferece a capacidade de criar e gerir todos os grupos Microsoft 365, gerir bilhetes de suporte e monitorizar a saúde do serviço.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/grupos/hiddenMembers/read | Leia membros escondidos de um grupo |
> | microsoft.diretório/grupos.unificado/criar | Criar grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.unificado/delete | Eliminar os grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.unificado/restaurar | Restaurar os grupos Microsoft 365 |
> | microsoft.diretório/grupos.unificado/básico/atualização | Atualizar propriedades básicas em grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.unificado/membros/atualização | Atualizar membros de grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/grupos.unificado/proprietários/atualização | Atualizar os proprietários de grupos Microsoft 365 com a exclusão de grupos atribuíveis por funções |
> | microsoft.diretório/serviçoPrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Conceder um acesso direto pricípo ao serviço aos dados de um grupo |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.network/performance/allProperties/read | Leia todas as propriedades de desempenho da rede no centro de administração Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerir todos os aspetos do Skype para business online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Ler relatórios de utilização do Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |
> | microsoft.teams/allEntities/allProperties/allTasks | Gerir todos os recursos em Equipas |

## <a name="teams-communications-administrator"></a>Administrador de Comunicações de Equipas

Os utilizadores desta função podem gerir aspetos da carga de trabalho das Equipas Microsoft relacionadas com a & telefonia. Isto inclui as ferramentas de gestão para a atribuição de números de telefone, políticas de voz e reunião, e acesso total ao conjunto de ferramentas de análise de chamadas.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerir todos os aspetos do Skype para business online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Ler relatórios de utilização do Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |
> | microsoft.teams/callQuality/allProperties/read | Leia todos os dados no Painel de Qualidade de Chamada (CQD) |
> | microsoft.teams/meetings/allProperties/allTasks | Gerir reuniões, incluindo políticas de reuniões, configurações e pontes de conferências |
> | microsoft.teams/voice/allProperties/allTasks | Gerir políticas de voz, incluindo políticas de chamadas e inventário de números de telefone e atribuição |

## <a name="teams-communications-support-engineer"></a>Engenheiro de Suporte de Comunicações de Equipas

Os utilizadores desta função podem resolver problemas de comunicação dentro do Microsoft Teams & Skype for Business utilizando as ferramentas de resolução de problemas de chamadas de utilizador no Centro de Administração & Skype para empresas da Microsoft Teams. Os utilizadores desta função podem ver informações completas do registo de chamadas para todos os participantes envolvidos. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerir todos os aspetos do Skype para business online |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |
> | microsoft.teams/callQuality/allProperties/read | Leia todos os dados no Painel de Qualidade de Chamada (CQD) |

## <a name="teams-communications-support-specialist"></a>Especialista em Apoio às Comunicações das Equipas

Os utilizadores desta função podem resolver problemas de comunicação dentro do Microsoft Teams & Skype for Business utilizando as ferramentas de resolução de problemas de chamadas de utilizador no Centro de Administração & Skype para empresas da Microsoft Teams. Os utilizadores desta função só podem ver os detalhes do utilizador na chamada para o utilizador específico que procuraram. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerir todos os aspetos do Skype para business online |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |
> | microsoft.teams/callQuality/standard/read | Leia os dados básicos no Painel de Qualidade de Chamada (CQD) |

## <a name="teams-devices-administrator"></a>Administrador de dispositivos de equipas

Os utilizadores com esta função podem gerir [dispositivos certificados por Equipas](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) do Centro de Administração de Equipas. Esta função permite visualizar todos os dispositivos num só olhar, com capacidade de pesquisar e filtrar dispositivos. O utilizador pode verificar detalhes de cada dispositivo, incluindo conta iniciada, e modelo do dispositivo. O utilizador pode alterar as definições do dispositivo e atualizar as versões do software. Esta função não concede permissões para verificar a atividade das Equipas e chamar a qualidade do dispositivo.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |
> | microsoft.teams/devices/standard/read | Gerir todos os aspetos dos dispositivos certificados por Equipas, incluindo políticas de configuração |

## <a name="usage-summary-reports-reader"></a>Leitor de Relatórios de Resumo de Utilização

Os utilizadores com esta função podem aceder a dados agregados de nível de inquilino e insights associados no Microsoft 365 Admin Center for Usage and Productivity Score, mas não podem aceder a quaisquer detalhes ou insights do nível do utilizador. No Microsoft 365 Admin Center para os dois relatórios, diferenciamos entre dados agregados ao nível do inquilino e detalhes do nível de utilizador. Esta função confere uma camada extra de proteção sobre dados identificáveis individuais dos utilizadores, que foi solicitado tanto por clientes como por equipas legais.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.office365.network/performance/allProperties/read | Leia todas as propriedades de desempenho da rede no centro de administração Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/standard/read | Leia relatórios agregados de uso do Office 365 ao nível do inquilino |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="user-administrator"></a>Administrador de Utilizadores

Os utilizadores com esta função podem criar utilizadores e gerir todos os aspetos dos utilizadores com algumas restrições (ver tabela) e podem atualizar as políticas de validade da palavra-passe. Além disso, os utilizadores com esta função podem criar e gerir todos os grupos. Esta função inclui também a capacidade de criar e gerir as vistas dos utilizadores, gerir bilhetes de apoio e monitorizar a saúde do serviço. Os administradores do utilizador não têm permissão para gerir algumas propriedades do utilizador para os utilizadores na maioria das funções de administrador. O utilizador com esta função não tem permissões para gerir o MFA. As funções que são exceções a esta restrição estão listadas no quadro seguinte.

| Permissão do administrador do utilizador | Notas |
| --- | --- |
| Criar utilizadores e grupos<br/>Criar e gerir vistas de utilizador<br/>Gerir bilhetes de apoio ao Escritório<br/>Atualizar as políticas de expiração da palavra-passe |  |
| Gerir licenças<br/>Gerir todas as propriedades do utilizador, exceto o nome principal do utilizador | Aplica-se a todos os utilizadores, incluindo todos os administradores |
| Eliminar e restaurar<br/>Desativar e ativar<br/>Gerir todas as propriedades do utilizador, incluindo o nome principal do utilizador<br/>Teclas de dispositivo de atualização (FIDO) | Aplica-se a utilizadores que não sejam administradores ou em qualquer uma das seguintes funções:<ul><li>Administrador helpdesk</li><li>Utilizador sem papel</li><li>Administrador de Utilizadores</li></ul> |
| Tokens de atualização invalidado<br/>Repor palavra-passe | Para obter uma lista das funções para as quais um Administrador de Utilizador pode redefinir palavras-passe e invalidar fichas de atualização, consulte [permissões de reset de palavra-passe](#password-reset-permissions). |

> [!IMPORTANT]
> Os utilizadores com esta função podem alterar palavras-passe para pessoas que possam ter acesso a informações sensíveis ou privadas ou configuração crítica dentro e fora do Azure Ative Directory. Alterar a palavra-passe de um utilizador pode significar a capacidade de assumir a identidade e permissões desse utilizador. Por exemplo:
>
>- Os proprietários de Registo de Aplicações e Aplicações Empresariais, que podem gerir credenciais de apps que possuam. Essas aplicações podem ter permissões privilegiadas em Azure AD e em outros lugares não concedidos aos Administradores de Utilizadores. Por este caminho, um Administrador de Utilizador poderá assumir a identidade de um titular da aplicação e, em seguida, assumir a identidade de uma aplicação privilegiada atualizando as credenciais para a aplicação.
>- Proprietários de subscrições Azure, que podem ter acesso a informações sensíveis ou privadas ou configuração crítica em Azure.
>- Security Group e Microsoft 365 proprietários do grupo, que podem gerir a adesão ao grupo. Esses grupos podem conceder acesso a informações sensíveis ou privadas ou configuração crítica em Azure AD e em outros lugares.
>- Administradores em outros serviços fora da Azure AD como Exchange Online, Office Security and Compliance Center, e sistemas de recursos humanos.
>- Não administradores como executivos, advogados e funcionários de recursos humanos que possam ter acesso a informações confidenciais ou privadas.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | microsoft.diretório/appRoleAssignments/create | Criar atribuições de funções de candidatura |
> | microsoft.diretório/appRoleAssignments/delete | Eliminar atribuições de funções de aplicação |
> | microsoft.diretório/appRoleAssignments/basic/update | Atualizar propriedades básicas das atribuições de funções de aplicação |
> | microsoft.diretório/contactos/criar | Criar contactos |
> | microsoft.diretório/contactos/excluir | Eliminar contactos |
> | microsoft.diretório/contactos/básico/atualização | Atualizar propriedades básicas em contactos |
> | microsoft.diretório/direitoManagement/allProperties/allTasks | Criar e apagar recursos, e ler e atualizar todos os imóveis na gestão de direitos AD Azure |
> | microsoft.diretório/grupos/assignLicense | Atribuir licenças de produtos a grupos para licenciamento baseado em grupo |
> | microsoft.diretório/grupos/criar | Criar grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/delete | Eliminar grupos, excluindo o grupo que atribui funções |
> | microsoft.diretório/grupos/hiddenMembers/read | Leia membros escondidos de um grupo |
> | microsoft.diretório/grupos/reprocessLicenseAssignment | Reprocessar as atribuições de licenças para licenciamento baseado em grupo |
> | microsoft.diretório/grupos/restaurar | Restaurar os grupos eliminados |
> | microsoft.diretório/grupos/básico/atualização | Atualizar propriedades básicas em grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/classificação/atualização | Atualizar a propriedade de classificação de grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/dynamicMembershipRule/update | Atualizar regra de adesão dinâmica dos grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/grupoType/atualização | Atualizar a propriedade groupType para um grupo |
> | microsoft.diretório/grupos/membros/atualização | Atualizar membros de grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/onPremWriteBack/update | Atualizar os grupos AD da Azure a serem reensitados para as instalações |
> | microsoft.diretório/grupos/proprietários/atualização | Atualizar os proprietários de grupos, excluindo grupos atribuíveis por funções |
> | microsoft.diretório/grupos/definições/atualização | Configurações de atualização de grupos |
> | microsoft.diretório/grupos/visibilidade/atualização | Atualizar a propriedade de visibilidade dos grupos |
> | microsoft.diretório/oAuth2PermissionGrants/allProperties/allTasks | Criar e eliminar bolsas de permissão OAuth 2.0 e ler e atualizar todas as propriedades |
> | microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update | Atualizar atribuições de funções principais de serviço |
> | microsoft.diretório/utilizadores/assignLicense | Gerir licenças de utilizador |
> | microsoft.diretório/utilizadores/criar | Adicionar utilizadores |
> | microsoft.diretório/utilizadores/delete | Eliminar utilizadores |
> | microsoft.diretório/utilizadores/desativar | Desativar utilizadores |
> | microsoft.diretório/utilizadores/enable | Ativar os utilizadores |
> | microsoft.diretório/utilizadores/inviteGuest | Convidar utilizadores |
> | microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Forçar a aprovação invalidando tokens de atualização do utilizador |
> | microsoft.diretório/utilizadores/reprocessLicenseAssignment | Reprocessar as atribuições de licenças para utilizadores |
> | microsoft.diretório/utilizadores/restaurar | Restaurar utilizadores eliminados |
> | microsoft.diretório/utilizadores/básico/atualização | Atualizar propriedades básicas nos utilizadores |
> | microsoft.diretório/utilizadores/gestor/atualização | Gestor de atualização para utilizadores |
> | microsoft.diretório/utilizadores/password/atualização | Redefinir palavras-passe para todos os utilizadores |
> | microsoft.diretório/utilizadores/userPrincipalName/update | Atualizar Nome Principal do Utilizador dos utilizadores |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir bilhetes de apoio Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o serviço de saúde no centro de administração Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de serviço microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leia propriedades básicas em todos os recursos no centro de administração Microsoft 365 |

## <a name="how-to-understand-role-permissions"></a>Como compreender permissões de função

O esquema para permissões segue vagamente o formato REST do Microsoft Graph:

`<namespace>/<entity>/<propertySet>/<action>`

Por exemplo:

`microsoft.directory/applications/credentials/update`

| Elemento de permissão | Description |
| --- | --- |
| espaço de nomes | Produto ou serviço que expõe a tarefa e está preparado com `microsoft` . Por exemplo, todas as tarefas em Azure AD usam o `microsoft.directory` espaço de nome. |
| entidade | Característica lógica ou componente exposto pelo serviço no Microsoft Graph. Por exemplo, a Azure AD expõe o Utilizador e os Grupos, o OneNote expõe notas e a Exchange expõe caixas de correio e calendários. Existe uma `allEntities` palavra-chave especial para especificar todas as entidades num espaço de nome. Isto é frequentemente usado em papéis que concedem acesso a um produto inteiro. |
| conjunto de propriedades | Propriedades ou aspetos específicos da entidade para a qual o acesso está a ser concedido. Por exemplo, `microsoft.directory/applications/authentication/read` concede a capacidade de ler o URL de resposta, URL de logout e propriedade de fluxo implícito no objeto de aplicação em Azure AD.<ul><li>`allProperties` designa todas as propriedades da entidade, incluindo propriedades privilegiadas.</li><li>`standard` designa propriedades comuns, mas exclui as privilegiadas relacionadas com a `read` ação. Por exemplo, `microsoft.directory/user/standard/read` inclui a capacidade de ler propriedades padrão como número de telefone público e endereço de e-mail, mas não o número de telefone secundário privado ou endereço de e-mail usado para a autenticação de vários fatores.</li><li>`basic` designa propriedades comuns, mas exclui as privilegiadas relacionadas com a `update` ação. O conjunto de propriedades que pode ler pode ser diferente do que pode atualizar. É por isso que há `standard` `basic` e palavras-chave para refletir isso.</li></ul> |
| ação | Operação sendo concedida, a maioria normalmente criar, ler, atualizar ou apagar (CRUD). Existe uma `allTasks` palavra-chave especial para especificar todas as capacidades acima (criar, ler, atualizar e eliminar). |

## <a name="deprecated-roles"></a>Papéis precotados

As seguintes funções não devem ser utilizadas. Foram depreciadas e serão removidas da Azure AD no futuro.

* Administrador de Licença AdHoc
* Aderir ao dispositivo
* Gestores de Dispositivos
* Utilizadores de Dispositivos
* E-mail Criador de utilizadores verificado
* Administrador da caixa de correio
* Aderir ao dispositivo de trabalho

## <a name="roles-not-shown-in-the-portal"></a>Funções não mostradas no portal

Nem todos os papéis devolvidos pela PowerShell ou pela MS Graph API são visíveis no portal Azure. A tabela seguinte organiza essas diferenças.

Nome da API | Nome do portal Azure | Notas
-------- | ------------------- | -------------
Aderir ao dispositivo | Preterido | [Documentação de papéis precotados](#deprecated-roles)
Gestores de Dispositivos | Preterido | [Documentação de papéis precotados](#deprecated-roles)
Utilizadores de Dispositivos | Preterido | [Documentação de papéis precotados](#deprecated-roles)
Contas de Sincronização do Diretório | Não mostrado porque não deve ser usado | [Documentação de Contas de Sincronização do Diretório](#directory-synchronization-accounts)
Utilizador Convidado | Não mostrado porque não pode ser usado | ND
Suporte de Nível 1 do Parceiro | Não mostrado porque não deve ser usado | [Documentação de suporte do Parceiro Tier1](#partner-tier1-support)
Suporte parceiro tier 2 | Não mostrado porque não deve ser usado | [Documentação de suporte de parceiro Tier2](#partner-tier2-support)
Utilizador restrito de hóspedes | Não mostrado porque não pode ser usado | ND
User | Não mostrado porque não pode ser usado | ND
Aderir ao dispositivo de trabalho | Preterido | [Documentação de papéis precotados](#deprecated-roles)

## <a name="password-reset-permissions"></a>Permissões de reset de palavra-passe

As rubricas da coluna representam as funções que podem redefinir as palavras-passe. As linhas de mesa contêm as funções para as quais a sua palavra-passe pode ser reposta.

A palavra-passe pode ser reposta | Administrador de password | Administração Helpdesk | Administrador de Autenticação | Administrador de Utilizador | Administrador de Autenticação Privilegiada | Admin Global
------ | ------ | ------ | ------ | ------ | ------ | ------
Administrador de Autenticação | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Leitores de Diretório | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Admin Global | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Admin grupos | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Convidado Convidado | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administração Helpdesk | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Leitor do Centro de Mensagens | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrador de password | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrador de Autenticação Privilegiada | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Administrador privilegiado | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Leitor de Relatórios | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Utilizador (sem função de administrador) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrador de Utilizador | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Leitor de Relatórios de Resumo de Utilização | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* Um Administrador Global não pode remover a sua própria atribuição de Administrador Global. Isto é para prevenir uma situação em que uma organização tem 0 Administradores Globais.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de Ad AZure a grupos](groups-assign-role.md)
- [Compreender as diferentes funções](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Atribuir um utilizador como administrador de uma subscrição do Azure](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
