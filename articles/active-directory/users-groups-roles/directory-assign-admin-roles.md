---
title: Descrições de funções de administrador e permissões - Azure Active Directory | Documentos da Microsoft
description: Uma função de administrador pode adicionar utilizadores, atribuir funções administrativas, repor palavras-passe do utilizador, gerir licenças de utilizador ou gerir domínios.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5044567396d832d3c3b2b46e3c3e90e053834595
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417892"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Permissões de função de administrador no Azure Active Directory

Utilizar o Azure Active Directory (Azure AD), pode designar administradores limitados para gerir as tarefas de identidade em funções com menos privilégios. Os administradores podem ser atribuídos para esses efeitos como adicionar ou alterar os utilizadores, atribuir funções administrativas, repor palavras-passe do utilizador, gerir licenças de utilizador e gerir nomes de domínio. As permissões de utilizador padrão podem ser alteradas apenas nas definições de utilizador no Azure AD.

O Administrador Global tenha acesso a todas as funcionalidades administrativas. Por predefinição, a pessoa que se inscreve para uma subscrição do Azure é atribuída a função de Administrador Global do diretório. Apenas os administradores globais e administradores de função com privilégios, podem delegar funções de administrador. Para reduzir o risco para o seu negócio, que recomendamos que atribua esta função para apenas algumas pessoas na sua empresa.


## <a name="assign-or-remove-administrator-roles"></a>Atribuir ou remover funções de administrador

Para saber como atribuir funções administrativas a um utilizador no Azure Active Directory, veja [modo de exibição e atribuir funções de administrador no Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Funções disponíveis

As seguintes funções de administrador estão disponíveis:

* **[Administrador da aplicação](#application-administrator)** : Os utilizadores nesta função podem criar e gerir todos os aspetos de aplicações empresariais, registos de aplicação e as definições de proxy da aplicação. Esta função também concede a capacidade de dar consentimento a permissões delegadas e permissões de aplicação, excluindo o Microsoft Graph e o Azure AD Graph. Os utilizadores atribuídos a esta função não são adicionados como proprietários durante a criação de novos registos de aplicação ou aplicações da empresa.

  <b>Importante</b>: Esta função concede a capacidade de gerir as credenciais do aplicativo. Utilizadores atribuídos esta função podem adicionar as credenciais para uma aplicação e usar as credencias para representar a identidade da aplicação. Se a identidade da aplicação tenha sido concedida acesso ao Azure Active Directory, como a capacidade de criar ou atualizar o utilizador ou outros objetos, um utilizador atribuído a esta função foi execute essas ações quando for representar o aplicativo. Esta capacidade para representar a identidade da aplicação pode ser uma elevação do privilégio ao longo do que o utilizador pode fazer por meio de suas atribuições de funções no Azure AD. É importante compreender que atribuir um utilizador à função de administrador da aplicação fornece a capacidade para representar a identidade de um aplicativo.

* **[Desenvolvedor de aplicativos](#application-developer)** : Os utilizadores nesta função podem criar registos de aplicações quando o "Os utilizadores podem registar aplicações" definição está definida como não. Esta função também concede permissão para consentir em nome da própria quando o "Os utilizadores podem dar consentimento a aplicações acedam aos dados da empresa em nome deles" definição está definida como não. Os utilizadores atribuídos a esta função são adicionados como proprietários durante a criação de novos registos de aplicação ou aplicações da empresa.

* **[Administrador de autenticação](#authentication-administrator)** : Os utilizadores com esta função podem definir ou repor as credenciais de palavra-passe. Os administradores de autenticação podem exigir que os utilizadores voltar a registar contra credenciais de palavra-passe existente (por exemplo, MFA ou FIDO) e revogar **Lembre-se a MFA no dispositivo**, que pede para a MFA no próximo início de sessão de utilizadores que são não-administradores ou atribuída apenas as seguintes funções:
  * Administrador de autenticação
  * Leitores de diretório
  * Autor de convites
  * Leitor do Centro de mensagens
  * Leitor de relatórios

  A função de administrador de autenticação está atualmente em pré-visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

  <b>Importante</b>: Os utilizadores com esta função podem alterar as credenciais para as pessoas que podem ter acesso a informações confidenciais ou privadas ou críticos de configuração dentro e fora do Azure Active Directory. Alterar as credenciais de um utilizador pode significar que a capacidade de assumir a identidade e permissões do usuário. Por exemplo:

  * Registo de aplicação e aplicação empresarial proprietários, que podem gerir as credenciais de aplicações que possuem. Esses aplicativos podem ter privilegiado permissões no Azure AD e noutro local não é concedido a administradores de autenticação. Através deste caminho um administrador de autenticação pode ser capaz de assumir a identidade de um proprietário da aplicação e, em seguida, mais assumir a identidade de um aplicativo com privilégios ao atualizar as credenciais para a aplicação.
  * Proprietários de subscrições do Azure, que poderão ter acesso a informações confidenciais ou privadas ou críticos de configuração no Azure.
  * Grupo de segurança e de grupo do Office 365 proprietários, que podem gerir a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou críticos de configuração no Azure AD e em outros locais.
  * Os administradores em outros serviços fora do Azure AD, como sistemas de Exchange Online, segurança do Office e Centro de conformidade e de recursos humanos.
  * Os não administradores como executivos, assessores jurídicos e funcionários de recursos humanos que podem ter acesso a informações confidenciais ou privadas.

* **[Administrador do Azure Information Protection](#azure-information-protection-administrator)** : Os utilizadores com esta função têm todas as permissões no serviço do Azure Information Protection. Esta função permite configurar etiquetas para a política do Azure Information Protection, gestão de modelos de proteção e ativar a proteção. Esta função não concede quaisquer permissões no Centro de proteção de identidade, o Privileged Identity Management, o Monitor Office 365 Service Health, ou o segurança do Office 365 e o Centro de conformidade.

* **[Administrador de fluxo de utilizador do B2C](#b2c-user-flow-administrator)** : Os utilizadores com esta função podem criar e gerir B2C fluxos de utilizador (também conhecido como "internas" políticas) no Portal do Azure. Ao criar ou editar fluxos de utilizador, estes utilizadores podem alterar o conteúdo de html, CSS e javascript da experiência do usuário, alterar os requisitos de MFA por fluxo de utilizador, altere afirmações no token e ajustar as configurações de sessão para todas as políticas no inquilino. Por outro lado, esta função não incluem a capacidade de examinar os dados de utilizador ou fazer alterações aos atributos que estão incluídos no esquema do inquilino. Altera a arquitetura de experiências de identidade (também conhecido como personalizado) políticas também está fora do âmbito desta função.

* **[Administrador de atributo do fluxo de utilizador do B2C](#b2c-user-flow-attribute-administrator)** : Os utilizadores com esta função adicionar ou eliminar os atributos personalizados disponíveis para todos os fluxos de utilizador no inquilino. Como tal, os utilizadores com esta função podem alterar ou adicionar novos elementos ao esquema do utilizador final e afetar o comportamento de todos os fluxos de utilizador e indiretamente resultem em alterações a dados que podem ser mais frequentes dos utilizadores finais e enviados, por fim, como afirmações para aplicações. Esta função não é possível editar os fluxos de utilizador.

* **[Administrador de conjunto de chaves do B2C IEF](#b2c-ief-keyset-administrator)** :    Utilizador pode criar e gerir políticas de chaves e segredos para encriptação de tokens, assinaturas de token e afirmação encriptação/desencriptação. Adicionando novas chaves para os contentores de chaves existentes, este administrador limitado pode segredos de rollover conforme necessário, sem afetar os aplicativos existentes. Este utilizador pode ver o conteúdo completo destes segredos e as respetivas datas de expiração, mesmo após a sua criação.
    
  <b>Importante:</b> esta é uma função confidencial. A função de administrador de conjunto de chaves deve ser cuidadosamente auditada e atribuída com cuidado durante a produção e pré-produção.

* **[Administrador de diretiva do B2C IEF](#b2c-ief-policy-administrator)** : Os utilizadores nesta função têm a capacidade de criar, ler, atualizar e eliminar todas as políticas personalizadas no Azure AD B2C e, por conseguinte, tem controlo total sobre a arquitetura de experiências de identidade no inquilino do Azure AD B2C relevante. Ao editar as políticas, este utilizador pode estabelecer Federação direta com fornecedores de identidade externos, alterar o esquema de diretório, alterar o conteúdo de todos os utilizadores com acesso à (HTML, CSS, JavaScript), alterar os requisitos para concluir uma autenticação, criar novos utilizadores, enviar dados de utilizador a sistemas externos, incluindo completa de migrações e editar todas as informações de utilizador, incluindo campos confidenciais como palavras-passe e números de telefone. Por outro lado, esta função não é possível alterar as chaves de encriptação ou editar os segredos utilizados para a Federação no inquilino.

  <b>Importante:</b> O administrador de diretiva de IEF B2 é uma função altamente confidencial que deve ser atribuída de forma muito limitada para inquilinos na produção. Atividades por estes utilizadores devem ser estreitamente auditadas, especialmente para os inquilinos na produção.

* **[Administrador de faturação](#billing-administrator)** : Efetua compras, gere subscrições, gere pedidos de suporte e monitoriza o estado de funcionamento do serviço.

* **[Administrador da aplicação cloud](#cloud-application-administrator)** : Os utilizadores nesta função têm as mesmas permissões que a função de administrador da aplicação, excluindo a capacidade de gerir o proxy de aplicações. Esta função concede a capacidade de criar e gerir todos os aspetos de aplicações empresariais e registos de aplicação. Esta função também concede a capacidade de dar consentimento a permissões delegadas e permissões de aplicação, excluindo o Microsoft Graph e o Azure AD Graph. Os utilizadores atribuídos a esta função não são adicionados como proprietários durante a criação de novos registos de aplicação ou aplicações da empresa.

  <b>Importante</b>: Esta função concede a capacidade de gerir as credenciais do aplicativo. Utilizadores atribuídos esta função podem adicionar as credenciais para uma aplicação e usar as credencias para representar a identidade da aplicação. Se a identidade da aplicação tenha sido concedida acesso ao Azure Active Directory, como a capacidade de criar ou atualizar o utilizador ou outros objetos, um utilizador atribuído a esta função foi execute essas ações quando for representar o aplicativo. Esta capacidade para representar a identidade da aplicação pode ser uma elevação do privilégio ao longo do que o utilizador pode fazer por meio de suas atribuições de funções no Azure AD. É importante compreender que atribuir um utilizador à função de administrador da aplicação Cloud fornece a capacidade para representar a identidade de um aplicativo.

* **[Administrador de dispositivos na cloud](#cloud-device-administrator)** : Os utilizadores nesta função podem ativar, desativar e eliminar dispositivos no Azure AD e ler as chaves do BitLocker do Windows 10 (caso exista) no portal do Azure. A função não concede permissões para gerir todas as propriedades no dispositivo.

* **[Administrador de conformidade](#compliance-administrator)** : Os utilizadores com esta função têm permissões para gerir as funcionalidades relacionadas com a conformidade no Centro de conformidade do Microsoft 365, o Centro de administração do Microsoft 365, Azure e segurança do Office 365 e Centro de conformidade. Os utilizadores também podem gerir todas as funcionalidades dentro do Centro de administração do Exchange e Equipes & Skype para o Centro de administração de negócios e criar pedidos de suporte para o Azure e o Microsoft 365. Estão disponíveis em mais informações [funções de administrador sobre o Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  Em | Pode fazer
  ----- | ----------
  [Centro de conformidade do Microsoft 365](https://protection.office.com) | Proteger e gerir os dados da sua organização em todos os serviços do Microsoft 365<br>Gerir alertas de conformidade
  [Gestor de conformidade](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Controlar, atribuir e verificar suas atividades da organização a conformidade a normas
  [Centro de conformidade e segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerir a governação de dados<br>Executar dados de e legais da investigação<br>Gerir pedido do requerente de dados
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Ver todos os dados de auditoria do Intune
  [O cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões só de leitura e pode gerir alertas<br>Pode criar e modificar as políticas de ficheiros e permitir ações de governação de ficheiros<br> Pode ver todos os relatórios incorporados sob a gestão de dados

<!--* **[Compliance Data Administrator](#compliance-data-administrator)**: Users with this role have permissions to protect and track data in the Microsoft 365 compliance center, Microsoft 365 admin center, and Azure. Users can also manage all features within the Exchange admin center, Compliance Manager, and Teams & Skype for Business admin center and create support tickets for Azure and Microsoft 365.

  In | Can do
  ----- | ----------
  [Microsoft 365 compliance center](https://protection.office.com) | Monitor compliance-related policies across Microsoft 365 services<br>Manage compliance alerts
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Track, assign, and verify your organization's regulatory compliance activities
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Manage data governance<br>Perform legal and data investigation<br>Manage Data Subject Request
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | View all Intune audit data
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Has read-only permissions and can manage alerts<br>Can create and modify file policies and allow file governance actions<br> Can view all the built-in reports under Data Management
-->
* **[Administrador de acesso condicional](#conditional-access-administrator)** : Os utilizadores com esta função têm a capacidade para gerir as definições de acesso condicional do Azure Active Directory.
  > [!NOTE]
  > Para implementar a política de acesso condicional do Exchange ActiveSync no Azure, o utilizador também tem de ser um Administrador Global.
  
* **[Aprovador de acesso do Cofre de cliente](#customer-lockbox-access-approver)** : Gere [pedidos de cliente cofre](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) na sua organização. Receber notificações por e-mail para pedidos de Cofre de cliente e podem aprovar e negar pedidos a partir do Centro de administração do Microsoft 365. Também pode ativar a funcionalidade de Cofre de cliente ou desativar. Apenas os administradores globais podem repor as palavras-passe de pessoas atribuídas a esta função.
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[Administradores de dispositivos](#device-administrators)** : Esta função está disponível para atribuição apenas como um administrador local adicional na [definições do dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Os utilizadores com esta função tornam-se os administradores do computador local em todos os dispositivos Windows 10 que estão associados ao Azure Active Directory. Não têm a capacidade de gerir objetos de dispositivos no Azure Active Directory. 

* **[Leitores de diretório](#directory-readers)** : Esta é uma função que deve ser atribuída a aplicativos herdados apenas para que não suportam o [consentimento Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Não atribua aos utilizadores.

* **[Contas de sincronização de diretórios](#directory-synchronization-accounts)** : Não utilize. Esta função é atribuída automaticamente ao serviço Azure AD Connect e não é a finalidade ou suportada para qualquer outro uso.

* **[Gravadores de diretórios](#directory-writers)** : Esta é uma função legada que está a ser atribuída às aplicações que não suportam o [consentimento Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Não deve ser atribuído a quaisquer utilizadores.

* **[Administrador do Dynamics 365 / administrador de CRM](#crm-service-administrator)** : Os utilizadores com esta função possuem permissões globais no Microsoft Dynamics 365 Online, quando o serviço está presente, bem como a capacidade para gerir pedidos de suporte e monitorizar o estado de funcionamento do serviço. Mais informações em [utilizar a função de administrador de serviço para gerir o seu inquilino](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > No Microsoft Graph API, o Azure AD Graph API e o Azure AD PowerShell, esta função é identificada como "Administrador de serviços do Dynamics 365". É "Administrador do Dynamics 365" no [portal do Azure](https://portal.azure.com).

* **[Administrador do Exchange](#exchange-service-administrator)** : Os utilizadores com esta função possuem permissões globais dentro do Microsoft Exchange Online, quando o serviço está presente. Também tem a capacidade de criar e gerir todos os grupos do Office 365, gerir pedidos de suporte e monitorizar o estado de funcionamento do serviço. Mais informações em [funções de administrador sobre o Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > No Microsoft Graph API, o Azure AD Graph API e o Azure AD PowerShell, esta função é identificada como "administrador de serviço Exchange". É "Administrador do Exchange" no [portal do Azure](https://portal.azure.com). É "Administrador Online do Exchange" no [Centro de administração do Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144). 

* **[Administrador do fornecedor de identidade externo](#external-identity-provider-administrator)** : Este administrador gerencia o Federação entre inquilinos do Azure Active Directory e fornecedores de identidade externo. Com esta função, os utilizadores podem adicionar novos fornecedores de identidade e configurar todas as definições disponíveis (por exemplo, caminho de autenticação, id de serviço, atribuído contentores de chaves). Este utilizador pode ativar o inquilino confie as autenticações de fornecedores de identidade externo. O impacto resultante em experiências de usuário final depende do tipo de inquilino:
  * Inquilinos do Active Directory do Azure para os funcionários e parceiros: A adição de uma federação (por exemplo, com o Gmail) imediatamente irá afetar todos os convites de convidados ainda não foi resgatados. Ver [adicionando Google como fornecedor de identidade para utilizadores convidados B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
  * Inquilinos do Active Directory B2C do Azure: A adição de uma federação (por exemplo, com o Facebook ou com outro Azure Active Directory) não afeta os fluxos de utilizador final imediatamente, até que o fornecedor de identidade é adicionado como uma opção num fluxo de utilizador (também conhecido como política incorporada). Ver [configurar uma conta Microsoft como fornecedor de identidade](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) para obter um exemplo. Para alterar os fluxos de utilizador, a função limitada do "Administrador de fluxo de utilizador do B2C" é necessária.

* **[Administrador global / administrador de empresa](#company-administrator)** : Os utilizadores com esta função têm acesso a todas as funcionalidades administrativas no Azure Active Directory, bem como serviços que utilizam identidades do Azure Active Directory, como o Centro de segurança do Microsoft 365, Microsoft 365 Centro de conformidade, Exchange Online, SharePoint Online, e Skype para empresas Online. A pessoa que se inscreve no inquilino do Azure Active Directory torna-se um administrador global. Apenas os administradores globais podem atribuir outras funções de administrador. Podem existir mais do que um administrador global na sua empresa. Os administradores globais podem redefinir a palavra-passe para qualquer utilizador e a todos os outros administradores.

  > [!NOTE]
  > No Microsoft Graph API, o Azure AD Graph API e o Azure AD PowerShell, esta função é identificada como "Administrador de empresa". É "Administrador Global" no [portal do Azure](https://portal.azure.com).
  >
  >

* **[Autor de convites](#guest-inviter)** : Os utilizadores nesta função podem gerir convites de utilizadores do Azure Active Directory B2B quando os **os membros podem convidar** definição de utilizador está definida como não. Obter mais informações sobre a colaboração B2B ao [colaboração sobre o Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Não inclui quaisquer outras permissões.

* **[Administrador do Intune](#intune-service-administrator)** : Os utilizadores com esta função possuem permissões globais no Microsoft Intune Online, quando o serviço está presente. Além disso, esta função contém a capacidade de gerir utilizadores e dispositivos para associar a política, bem como criar e gerir grupos. Mais informações em [o controlo de administração baseada em funções (RBAC) com o Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > No Microsoft Graph API, o Azure AD Graph API e o Azure AD PowerShell, esta função é identificada como "administrador do serviço Intune". É "Administrador do Intune" no [portal do Azure](https://portal.azure.com).
  
 * **[Administrador de Kaizala](#kaizala-administrator)** : Os utilizadores com esta função têm permissões globais para gerir as definições no Microsoft Kaizala, quando o serviço está presente, bem como a capacidade para gerir pedidos de suporte e monitorizar o estado de funcionamento do serviço.
Além disso, o usuário pode acessar relatórios relacionados com a adoção e a utilização das Kaizala por membros da organização e os relatórios de negócio gerados usando as ações de Kaizala. 

* **[Administrador de licenças](#license-administrator)** : Os utilizadores nesta função podem adicionar, remover e atualizar as atribuições de utilizadores, grupos (com licenciamento baseado em grupo) de licença e gerir a localização de utilização sobre os usuários. A função não concede a capacidade de comprar ou gerir as subscrições, criar ou gerir grupos, ou criar ou gerir utilizadores além da localização de utilização. Esta função não tem acesso para ver, criar ou gerir pedidos de suporte.

* **[Leitor de privacidade do Centro de mensagens](#message-center-privacy-reader)** : Os utilizadores nesta função podem monitorizar todas as notificações no Centro de mensagens, incluindo mensagens de privacidade de dados. Os leitores de privacidade do Centro de mensagens receber notificações de e-mail, incluindo as relacionadas com a privacidade dos dados e podem anular a subscrição usando as preferências do Centro de mensagens. Apenas o Administrador Global e o leitor de privacidade do Centro de mensagens podem ler mensagens de privacidade de dados. Além disso, esta função contém a capacidade de ver grupos, domínios e subscrições. Esta função não tem permissão para ver, criar ou gerir pedidos de serviço.

* **[Leitor do Centro de mensagens](#message-center-reader)** : Os utilizadores nesta função podem monitorizar notificações e atualizações de estado de funcionamento de consultadoria [Centro de mensagens do Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) para a sua organização em serviços configurados como o Exchange, o Intune e o Microsoft Teams. Os leitores de centro de mensagens recebem resumos de e-mail semanal de postagens, atualizações e podem partilhar publicações do Centro de mensagens no Office 365. No Azure AD, os utilizadores atribuídos a esta função só terão acesso só de leitura nos serviços do Azure AD, como utilizadores e grupos. Esta função não tem acesso para ver, criar ou gerir pedidos de suporte.

* **[Suporte de escalão 1 de parceiros](#partner-tier1-support)** : Não utilize. Esta função foi preterida e será removida do Azure AD no futuro. Esta função é destinada à utilização por um pequeno número de parceiros de revenda da Microsoft e não se destina a utilização geral.

* **[Suporte de escalão 2 de parceiros](#partner-tier2-support)** : Não utilize. Esta função foi preterida e será removida do Azure AD no futuro. Esta função é destinada à utilização por um pequeno número de parceiros de revenda da Microsoft e não se destina a utilização geral.

* **[Administrador de suporte técnico (palavra-passe)](#helpdesk-administrator)** : Os utilizadores com esta função podem alterar palavras-passe, invalidar os tokens de atualização, gerir pedidos de serviço e monitorizar o estado de funcionamento do serviço. Invalidar um token de atualização força o utilizador iniciar sessão novamente. Os administradores de suporte técnico podem repor palavras-passe e invalidar os tokens de atualização de outros utilizadores que são não-administradores ou atribuída apenas as seguintes funções:
  * Leitores de diretório
  * Autor de convites
  * Administrador de suporte técnico
  * Leitor do Centro de mensagens
  * Leitor de relatórios
  
  <b>Importante</b>: Os utilizadores com esta função podem alterar as palavras-passe para as pessoas que podem ter acesso a informações confidenciais ou privadas ou críticos de configuração dentro e fora do Azure Active Directory. Alterar a palavra-passe de um utilizador pode significar que a capacidade de assumir a identidade e permissões do usuário. Por exemplo:
  * Registo de aplicação e aplicação empresarial proprietários, que podem gerir as credenciais de aplicações que possuem. Esses aplicativos podem ter privilegiado permissões no Azure AD e noutro local não é concedido a administradores de suporte técnico. Através deste caminho um administrador de suporte técnico poderá assumir a identidade de um proprietário da aplicação e, em seguida, mais assumir a identidade de um aplicativo com privilégios ao atualizar as credenciais para a aplicação.
  * Proprietários de subscrições do Azure, que poderão ter acesso a informações confidenciais ou privadas ou críticos de configuração no Azure.
  * Grupo de segurança e de grupo do Office 365 proprietários, que podem gerir a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou críticos de configuração no Azure AD e em outros locais.
  * Os administradores em outros serviços fora do Azure AD, como sistemas de Exchange Online, segurança do Office e Centro de conformidade e de recursos humanos.
  * Os não administradores como executivos, assessores jurídicos e funcionários de recursos humanos que podem ter acesso a informações confidenciais ou privadas.


  > [!NOTE]
  > Delegar permissões administrativas ao longo de subconjuntos de utilizadores e aplicar políticas para um subconjunto de utilizadores, é possível com o [unidades administrativas (pré-visualização)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).


  > [!NOTE]
  > Esta função anteriormente chamada "administrador de palavras-passe" [portal do Azure](https://portal.azure.com/). Estamos a alterar o nome para "Administrador de suporte técnico" de acordo com o respetivo nome no Azure AD PowerShell, o Azure AD Graph API e o Microsoft Graph API. Para um curto período de tempo, iremos alterar o nome para "Administrador de suporte técnico (palavra-passe)" no portal do Azure antes da alteração para o "Administrador de suporte técnico".


* **[Administrador do Power BI](#power-bi-service-administrator)** : Os utilizadores com esta função possuem permissões globais dentro do Microsoft Power BI, quando o serviço está presente, bem como a capacidade para gerir pedidos de suporte e monitorizar o estado de funcionamento do serviço. Mais informações em [compreender a função de administrador do Power BI](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > No Microsoft Graph API, o Azure AD Graph API e o Azure AD PowerShell, esta função é identificada como "administrador do Power BI". Se "administrador do Power BI" a [portal do Azure](https://portal.azure.com).

* **[Com privilégios administrador de autenticação](#privileged-authentication-administrator)** : Os utilizadores com esta função podem definir ou repor as credenciais de palavra-passe para todos os utilizadores, incluindo os administradores globais. Os administradores com privilégios de autenticação pode forçar os utilizadores para voltar a registar contra credenciais de palavra-passe existente (por exemplo, MFA, FIDO) e revogar "memorizar MFA no dispositivo", solicitar a MFA no próximo início de sessão de todos os utilizadores. Os administradores com privilégios de autenticação pode:
  * Forçar os usuários para voltar a registar contra credenciais de palavra-passe existente (por exemplo, MFA, FIDO)
  * Revogar "memorizar MFA no dispositivo", solicitar a MFA no próximo início de sessão

* **[Com privilégios administrador de função](#privileged-role-administrator)** : Os utilizadores com esta função podem gerir atribuições de funções no Azure Active Directory, assim como no Azure AD Privileged Identity Management. Além disso, esta função permite a gestão de todos os aspetos do Privileged Identity Management e unidades administrativas.

  <b>Importante</b>: Esta função concede a capacidade de gerenciar atribuições para todas as funções do Azure AD, incluindo a função de Administrador Global. Esta função não inclui quaisquer outras capacidades com privilégios no Azure AD, como a criação ou atualização dos utilizadores. No entanto, os utilizadores atribuídos a esta função podem conceder próprios ou outros privilégios adicionais através da atribuição de funções adicionais.

* **[Leitor de relatórios](#reports-reader)** : Os utilizadores com esta função podem ver os dados de relatórios de utilização e o dashboard de relatórios no Centro de administração do Microsoft 365 e o contexto de adoção do pacote no Power BI. Além disso, a função fornece acesso de início de sessão relatórios e a atividade no Azure AD e a API de relatórios de dados devolvidos pelo Microsoft Graph. Pode aceder a um utilizador atribuído à função do leitor de relatórios apenas utilização relevante e métricas de adoção. Eles não tem quaisquer permissões de administrador para configurar as definições ou o acesso que do Centro de administração da específicos de produtos como o Exchange. Esta função não tem acesso para ver, criar ou gerir pedidos de suporte.

* **[Administrador de pesquisa](#search-administrator)** : Os utilizadores nesta função têm acesso total a todas as funcionalidades de gestão do Microsoft Search no Centro de administração do Microsoft 365. Os administradores de pesquisa podem delegar os administradores de pesquisa e funções de Editor de pesquisa para os utilizadores e criar e gerir conteúdo, como marcadores, P & como e localizações. Além disso, estes utilizadores podem ver o Centro de mensagens, monitorizar o estado de funcionamento do serviço e criar pedidos de serviço.

* **[Editor de pesquisa](#search-editor)** : Os utilizadores nesta função podem criar, gerir e eliminar conteúdo para o Microsoft Search no Centro de administração do Microsoft 365, incluindo marcadores, P & como e localizações.

* **[Administrador de segurança](#security-administrator)** : Os utilizadores com esta função têm permissões para gerir as funcionalidades relacionadas com a segurança no Centro de segurança do Microsoft 365, Azure Active Directory Identity Protection, Azure Information Protection e segurança do Office 365 e Centro de conformidade. Obter mais informações sobre as permissões do Office 365 estão disponíveis em [permissões no Centro de conformidade de segurança do Office 365 e](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  Em | Pode fazer
  --- | ---
  [Centro de segurança do Microsoft 365](https://protection.office.com) | Monitorizar políticas relacionadas com segurança em todos os serviços do Microsoft 365<br>Gerir ameaças de segurança e alertas<br>Ver relatórios
  Centro de proteção de identidade | Todas as permissões da função do leitor de segurança<br>Além disso, a capacidade de executar todas as operações do Centro de proteção de identidade, exceto para repor palavras-passe
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Todas as permissões da função do leitor de segurança<br>**Não é possível** gerir atribuições de funções do Azure AD ou definições
  [Centro de conformidade e segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerir políticas de segurança<br>Ver, investigar e responder a ameaças de segurança<br>Ver relatórios
  Proteção Avançada Contra Ameaças do Azure | Monitorizar e responder à atividade suspeita de segurança
  Windows Defender ATP e EDR | Atribuir funções<br>Gerir grupos de máquinas<br>Configurar a deteção de ameaças do ponto final e a remediação automática<br>Ver, investigar e responder a alertas
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Utilizador de vistas, dispositivo, inscrição, configuração e informações sobre a aplicação<br>Não é possível efetuar alterações ao Intune
  [O cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Adicionar administradores, adicionar políticas e definições, carregar registos e desempenhar ações de governação
  [Centro de Segurança do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Pode ver as políticas de segurança, visualizar os Estados de segurança, editar as políticas de segurança, ver alertas e recomendações, dispensar alertas e recomendações
  [Estado de funcionamento do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Ver o estado de funcionamento dos serviços do Office 365

<!--* **[Security operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management, as well as the ability to read Azure Active Directory sign-in reports and audit logs, and in Office 365 Security & Compliance Center.

  In | Can do
  --- | ---
  [Microsoft 365 security center](https://protection.office.com) | All permissions of the Security Reader role<br>View, investigate, and respond to security threats alerts
  Identity Protection Center | All permissions of the Security Reader role<br>Additionally, the ability to perform all Identity Protection Center operations except for resetting passwords
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | All permissions of the Security Reader role
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  Windows Defender ATP and EDR | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | All permissions of the Security Reader role
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | All permissions of the Security Reader role
  [Office 365 service health](https://docs.microsoft.com/office365/enterprise/view-service-health) | View the health of Office 365 services
-->
* **[Leitor de segurança](#security-reader)** : Os utilizadores com esta função têm acesso só de leitura global na funcionalidade relacionados à segurança, incluindo todas as informações no Microsoft 365 Centro de segurança, Azure Active Directory, Identity Protection, Privileged Identity Management, bem como a capacidade de leitura do Active Directory do Azure Relatórios do início de sessão e registos de auditoria e no Centro de conformidade e segurança do Office 365. Obter mais informações sobre as permissões do Office 365 estão disponíveis em [permissões no Centro de conformidade de segurança do Office 365 e](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  Em | Pode fazer
  --- | ---
  [Centro de segurança do Microsoft 365](https://protection.office.com) | Ver políticas relacionadas com segurança em todos os serviços do Microsoft 365<br>Ameaças de segurança de View e alertas<br>Ver relatórios
  Centro de proteção de identidade | Ler todos os relatórios de segurança e informações das definições de funcionalidades de segurança<br><ul><li>Anti-spam<li>Encriptação<li>Prevenção de perda de dados<li>Antimalware<li>Proteção avançada contra ameaças<li>Anti-phishing<li>Regras de fluxo de mensagens
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Tem acesso só de leitura para todas as informações apresentadas no PIM do Azure AD: As políticas e relatórios para atribuições de funções do Azure AD, o security analisa e acesso aos dados de política e relatórios para cenários, além de atribuição de função do Azure AD de leitura no futuro.<br>**Não é possível** inscrever-se no PIM do Azure AD ou fazer alterações ao mesmo. No portal do PIM ou através do PowerShell, alguém nesta função pode ativar as funções adicionais (por exemplo, Administrador Global ou administrador com função privilegiada), se o utilizador é elegível para eles.
  [Centro de conformidade e segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Ver as políticas de segurança<br>Ver e investigar ameaças de segurança<br>Ver relatórios
  Windows Defender ATP e EDR | Ver e investigar alertas
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Utilizador de vistas, dispositivo, inscrição, configuração e informações sobre a aplicação. Não é possível efetuar alterações ao Intune.
  [O cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões só de leitura e pode gerir alertas
  [Centro de Segurança do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Pode ver recomendações e alertas, políticas de segurança, visualizar os Estados de segurança, mas não pode fazer alterações de vista
  [Estado de funcionamento do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Ver o estado de funcionamento dos serviços do Office 365

* **[Administrador de assistência técnica](#service-support-administrator)** : Os utilizadores com esta função podem abrir pedidos de suporte com a Microsoft para serviços do Azure e do Office 365 e vistas do dashboard de serviço e a mensagem center no [portal do Azure](https://portal.azure.com) e [Centro de administração do Microsoft 365](https://admin.microsoft.com). Mais informações em [funções de administrador sobre o Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > No Microsoft Graph API, o Azure AD Graph API e o Azure AD PowerShell, esta função é identificada como "administrador de assistência técnica." É "Administrador de serviço" no [portal do Azure](https://portal.azure.com), o [Centro de administração do Microsoft 365](https://admin.microsoft.com)e o portal do Intune.

* **[Administrador do SharePoint](#sharepoint-service-administrator)** : Os utilizadores com esta função possuem permissões globais dentro do Microsoft SharePoint Online, quando o serviço está presente, bem como a capacidade de criar e gerir todos os grupos do Office 365, gerir pedidos de suporte e monitorizar o estado de funcionamento do serviço. Mais informações em [funções de administrador sobre o Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > No Microsoft Graph API, o Azure AD Graph API e o Azure AD PowerShell, esta função é identificada como "administrador do serviço SharePoint." É "Administrador do SharePoint" na [portal do Azure](https://portal.azure.com).

* **[Skype para empresas / administrador Lync](#lync-service-administrator)** : Os utilizadores com esta função têm permissões globais no Microsoft Skype para empresas, quando o serviço está presente, bem como gerir os atributos de utilizador do Skype específicos no Azure Active Directory. Além disso, esta função concede a capacidade para gerir pedidos de suporte e monitorizar o estado de funcionamento do serviço e para acessar as Equipes e o Skype para empresas do Centro de administração. A conta também têm de estar licenciada para equipas ou não é possível executar cmdlets do PowerShell de Equipes. Mais informações em [sobre o Skype para a função de administrador de negócios](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) e as equipas de informações de licenciamento em [Skype para empresas e o Microsoft Teams suplemento licenciamento](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > No Microsoft Graph API, o Azure AD Graph API e o Azure AD PowerShell, esta função é identificada como "Administrador do serviço Lync". É "Administrador de Skype para empresas" no [portal do Azure](https://portal.azure.com/).

* **[As equipes de administrador](#teams-service-administrator)** : Os utilizadores nesta função podem gerir todos os aspetos da carga de trabalho Microsoft Teams através do Skype e Microsoft Teams para o Centro de administração de negócios e os respectivos módulos do PowerShell. Isto inclui, entre outras áreas, todas as ferramentas de gestão relacionados com a telefonia, mensagens, reuniões e as equipes propriamente ditas. Esta função concede, além disso, a capacidade de criar e gerir todos os grupos do Office 365, gerir pedidos de suporte e monitorizar o estado de funcionamento do serviço.
  > [!NOTE]
  > No Microsoft Graph API, o Azure AD Graph API e o Azure AD PowerShell, esta função é identificada como "as Equipes de administrador de serviços". Se "Administrador de Equipes" a [portal do Azure](https://portal.azure.com).

* **[As equipes de administrador de comunicações](#teams-communications-administrator)** : Os utilizadores nesta função podem gerir aspectos da carga de trabalho Microsoft Teams relacionadas com a voz e telefonia. Isto inclui as ferramentas de gestão para atribuição de número de telefone, diretivas de voz e de reunião e acesso total para o conjunto de ferramentas de análise de chamada.

* **[As equipes de engenheiro de suporte de comunicações](#teams-communications-support-engineer)** : Os utilizadores nesta função podem resolver problemas de comunicação dentro do Microsoft Teams e o Skype para empresas através da chamada de utilizador ferramentas no Microsoft Teams & Skype para o Centro de administração de empresas de resolução de problemas. Os utilizadores nesta função podem ver informações de registo de chamadas completa para todos os participantes envolvidos. Esta função não tem acesso para ver, criar ou gerir pedidos de suporte.

* **[As equipes especialistas de suporte de comunicações](#teams-communications-support-specialist)** : Os utilizadores nesta função podem resolver problemas de comunicação dentro do Microsoft Teams e o Skype para empresas através da chamada de utilizador ferramentas no Microsoft Teams & Skype para o Centro de administração de empresas de resolução de problemas. Os utilizadores nesta função apenas podem ver os detalhes de utilizador na chamada para o usuário específico que tem procurado. Esta função não tem acesso para ver, criar ou gerir pedidos de suporte.

* **[Administrador de utilizadores](#user-administrator)** : Os utilizadores com esta função situação pode criem utilizadores, gerir todos os aspetos de utilizadores com algumas restrições (ver abaixo) e podem atualizar as políticas de expiração de palavra-passe. Além disso, os utilizadores com esta função podem criar e gerir todos os grupos. Esta função também inclui a capacidade de criar e gerir vistas de utilizador, gerir pedidos de suporte e monitorizar o estado de funcionamento do serviço.

  | | |
  | --- | --- |
  |Permissões gerais|<p>Criar utilizadores e grupos</p><p>Criar e gerir vistas de utilizador</p><p>Gerir pedidos de suporte do Office<p>Atualizar as políticas de expiração de palavra-passe|
  |<p>Em todos os utilizadores, incluindo todos os administradores</p>|<p>Gerir licenças</p><p>Gerir todas as propriedades de utilizador, exceto o nome Principal de utilizador</p>
  |Apenas em utilizadores que são não-administradores ou em qualquer um dos seguintes limitada funções de administrador:<ul><li>Leitores de diretório<li>Autor de convites<li>Administrador de suporte técnico<li>Leitor do Centro de mensagens<li>Leitor de relatórios<li>Administrador de utilizadores|<p>Eliminar e restauro</p><p>Desativar e ativar</p><p>Invalidar Tokens de atualização</p><p>Gerir todas as propriedades de utilizador, incluindo o nome Principal de utilizador</p><p>Repor palavra-passe</p><p>Atualizar as chaves de dispositivo (FIDO)</p>
  
  <b>Importante</b>: Os utilizadores com esta função podem alterar as palavras-passe para as pessoas que podem ter acesso a informações confidenciais ou privadas ou críticos de configuração dentro e fora do Azure Active Directory. Alterar a palavra-passe de um utilizador pode significar que a capacidade de assumir a identidade e permissões do usuário. Por exemplo:
  * Registo de aplicação e aplicação empresarial proprietários, que podem gerir as credenciais de aplicações que possuem. Esses aplicativos podem ter privilegiado permissões no Azure AD e noutro local não é concedido a administradores de utilizador. Através deste caminho um administrador do utilizador poderá assumir a identidade de um proprietário da aplicação e, em seguida, mais assumir a identidade de um aplicativo com privilégios ao atualizar as credenciais para a aplicação.
  * Proprietários de subscrições do Azure, que poderão ter acesso a informações confidenciais ou privadas ou críticos de configuração no Azure.
  * Grupo de segurança e de grupo do Office 365 proprietários, que podem gerir a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou críticos de configuração no Azure AD e em outros locais.
  * Os administradores em outros serviços fora do Azure AD, como sistemas de Exchange Online, segurança do Office e Centro de conformidade e de recursos humanos.
  * Os não administradores como executivos, assessores jurídicos e funcionários de recursos humanos que podem ter acesso a informações confidenciais ou privadas.

## <a name="role-permissions"></a>Permissões de função
As tabelas seguintes descrevem as permissões específicas no Azure Active Directory tendo em conta a cada função. Algumas funções podem ter permissões adicionais nos serviços da Microsoft fora do Azure Active Directory.

### <a name="application-administrator"></a>Administrador da aplicação
Pode criar e gerir todos os aspetos de registos de aplicações e aplicações empresariais.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Atualize a propriedade applications.audience no Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Atualize a propriedade applications.authentication no Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Atualize as propriedades básicas em aplicações no Azure Active Directory. |
| microsoft.aad.directory/applications/create | Crie aplicações no Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Atualize a propriedade applications.credentials no Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Elimine aplicações no Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Leia appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Atualize appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Elimine appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no auditLogs no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Leia a propriedade policies.applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Atualize a propriedade policies.applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Crie políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Elimine políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Leia a propriedade policies.applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Atualize a propriedade policies.applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Leia a propriedade policies.applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Atualize a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Atualize a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Atualize a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Atualize as propriedades básicas no servicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Crie servicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Atualize a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Elimine servicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Atualize a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Atualize a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Atualize a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="application-developer"></a>Desenvolvedor de aplicativos
Pode criar registos de aplicações independentemente do "os utilizadores podem registar aplicações" definição.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Crie aplicações no Azure Active Directory. Criador é adicionado como o primeiro proprietário e o objeto criado conta contra a quota de 250 objetos criados do criador. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Crie appRoleAssignments no Azure Active Directory. Criador é adicionado como o primeiro proprietário e o objeto criado conta contra a quota de 250 objetos criados do criador. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Crie oAuth2PermissionGrants no Azure Active Directory. Criador é adicionado como o primeiro proprietário e o objeto criado conta contra a quota de 250 objetos criados do criador. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Crie servicePrincipals no Azure Active Directory. Criador é adicionado como o primeiro proprietário e o objeto criado conta contra a quota de 250 objetos criados do criador. |

### <a name="authentication-administrator"></a>Administrador de autenticação
Pode ver, definir e repor as informações de método de autenticação para qualquer utilizador não administrador.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalida todos os tokens de atualização de utilizador no Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Propriedades de atualização de autenticação forte, como informações de credenciais MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="azure-information-protection-administrator"></a>Administrador do Azure Information Protection
Pode gerir todos os aspetos do serviço do Azure Information Protection.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerir todos os aspetos do Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="b2c-user-flow-administrator"></a>Administrador de Fluxos de Utilizadores B2C
Criar e gerir todos os aspetos de fluxos de utilizador.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Ler e configurar fluxos de utilizador no Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator"></a>Administrador de Atributos de Fluxo de Utilizadores B2C
Criar e gerir o esquema de atributos disponível para todos os fluxos de utilizador.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Ler e configurar atributos de utilizador no Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator"></a>Administrador de Conjuntos de Chaves B2C IEF
Gerir segredos para Federação e de encriptação do Framework de experiência de identidade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Ler e configurar conjuntos de chaves no Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator"></a>Administrador de Políticas B2C IEF
Criar e gerir políticas de estrutura de confiança do Framework de experiência de identidade.

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft.aad.B2C/trustFramework/Policies/allTasks | Ler e configurar as políticas personalizadas no Azure Active Directory B2C. |

### <a name="billing-administrator"></a>Administrador de faturação
Pode efetuar tarefas de faturação comuns, como atualizar as informações de pagamento.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Atualize as propriedades básicas na organização no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gerir todos os aspetos da faturação do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="desktop-analytics-administrator"></a>Administrador de análise de ambiente de trabalho
Pode aceder e gerir os serviços incluindo o Intune e de ferramentas de gerenciamento de Desktops.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerir todos os aspetos da análise de ambiente de trabalho. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="cloud-application-administrator"></a>Administrador da aplicação cloud
Pode criar e gerir todos os aspetos de registos de aplicações e aplicações empresariais, exceto o Proxy de aplicações.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Atualize a propriedade applications.audience no Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Atualize a propriedade applications.authentication no Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Atualize as propriedades básicas em aplicações no Azure Active Directory. |
| microsoft.aad.directory/applications/create | Crie aplicações no Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Atualize a propriedade applications.credentials no Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Elimine aplicações no Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Atualize appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Elimine appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no auditLogs no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Crie políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Leia a propriedade policies.applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Atualize a propriedade policies.applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Elimine políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Leia a propriedade policies.applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Atualize a propriedade policies.applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Leia a propriedade policies.applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Atualize a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Atualize a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Atualize a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Atualize as propriedades básicas no servicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Crie servicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Atualize a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Elimine servicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Atualize a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Atualize a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Atualize a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="cloud-device-administrator"></a>Administrador de dispositivos de cloud
Acesso total para gerir dispositivos no Azure AD.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no auditLogs no Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leia a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Elimine dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/disable | Desative dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Permitir que os dispositivos no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |

### <a name="company-administrator"></a>Administrador de Empresa
Pode gerir todos os aspetos do Azure AD e Microsoft dos serviços de identidades que utilize o Azure AD.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize propriedades padrão no microsoft.aad.cloudAppSecurity. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Crie e elimine administrativeUnits e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Criar e eliminar aplicações e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Criar e eliminar appRoleAssignments e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no auditLogs no Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Crie e elimine contactos e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Crie e elimine contratos e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Criar e eliminar dispositivos e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Criar e eliminar directoryRoles e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Crie e elimine directoryRoleTemplates e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Crie e elimine domínios e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Crie e elimine grupos e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Crie e elimine groupSettings e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Crie e elimine groupSettingTemplates e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Crie e elimine loginTenantBranding e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Criar e eliminar oAuth2PermissionGrants e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Crie e elimine organização e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Criar e eliminar políticas e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Crie e elimine RoleAssignments fosse e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Crie e elimine roleDefinitions e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Crie e elimine scopedRoleMemberships e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Pode efetuar a ação de serviço Activateservice no Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Pode efetuar a ação de serviço Disabledirectoryfeature no Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Pode efetuar a ação de serviço Enabledirectoryfeature no Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Pode efetuar a ação de serviço Getavailableextentionproperties no Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Crie e elimine servicePrincipals e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no signInReports no Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Crie e elimine subscribedSkus e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Crie e elimine utilizadores e leia e atualize todas as propriedades no Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Execute todas as ações no Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize propriedades padrão no microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Ler todos os recursos no microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Ler todos os recursos no microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerir todos os aspetos do Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gerir todos os aspetos da faturação do Office 365. |
| microsoft.intune/allEntities/allTasks | Gerir todos os aspetos do Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gerir todos os aspectos do Gestor de conformidade do Office 365 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerir todos os aspetos da análise de ambiente de trabalho. |
| microsoft.office365.exchange/allEntities/allTasks | Gerir todos os aspetos do Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Gerir todos os aspectos do Cofre de cliente do Office 365 |
| microsoft.office365.messageCenter/messages/read | Ler mensagens na microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leia securityMessages no microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Gerir todos os aspetos do Centro de proteção do Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize propriedades padrão no microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize propriedades padrão no microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerir todos os aspetos do Skype para empresas Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Relatórios de utilização de leitura Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerir todos os aspetos do Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerir todos os aspetos do Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Ler todos os recursos no microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator"></a>Administrador de conformidade
Pode ler e gerir configurações de conformidade e relatórios no Azure AD e o Office 365.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gerir todos os aspectos do Gestor de conformidade do Office 365 |
| microsoft.office365.exchange/allEntities/allTasks | Gerir todos os aspetos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize propriedades padrão no microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerir todos os aspetos do Skype para empresas Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="conditional-access-administrator"></a>Administrador de Acesso Condicional
Pode gerir capacidades de acesso condicional.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Leia a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Atualize a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Crie políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Elimine políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Leia a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Atualize a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Leia a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Atualize a propriedade policies.conditionalAccess no Azure Active Directory. |

### <a name="crm-service-administrator"></a>Administrador de serviço CRM
Pode gerir todos os aspetos do produto do Dynamics 365.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerir todos os aspetos do Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="customer-lockbox-access-approver"></a>Aprovador de acesso ao Cofre de cliente
Pode aprovar pedidos de suporte da Microsoft para aceder aos dados organizacionais de clientes.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Gerir todos os aspectos do Cofre de cliente do Office 365 |

### <a name="device-administrators"></a>Administradores de dispositivos
Os utilizadores atribuídos a esta função são adicionados ao grupo Administradores local em dispositivos associados ao AD Azure.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Ler as propriedades básicas no groupSettings no Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Ler as propriedades básicas no groupSettingTemplates no Azure Active Directory. |

### <a name="directory-readers"></a>Leitores de diretório
Pode ler as informações de diretório básicas. Para conceder acesso a aplicações, não se destina aos utilizadores.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Ler as propriedades básicas no administrativeUnits no Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Leia a propriedade Administrativeunits no Azure Active Directory. |
| microsoft.aad.directory/applications/basic/read | Ler as propriedades básicas em aplicações no Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Leia a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/applications/policies/read | Leia a propriedade applications.policies no Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/read | Ler as propriedades básicas para contactos no Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Leia a propriedade contacts.memberOf no Azure Active Directory. |
| microsoft.aad.directory/contracts/basic/read | Ler as propriedades básicas sobre contratos no Azure Active Directory. |
| microsoft.aad.directory/devices/basic/read | Ler as propriedades básicas em dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Leia a propriedade memberof no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Leia a propriedade devices.registeredOwners no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Leia a propriedade devices.registeredUsers no Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Ler as propriedades básicas no directoryRoles no Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Leia a propriedade directoryRoles.eligibleMembers no Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Leia a propriedade directoryRoles.members no Azure Active Directory. |
| microsoft.aad.directory/domains/basic/read | Ler as propriedades básicas sobre domínios no Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Leia a propriedade groups.appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/groups/basic/read | Ler as propriedades básicas sobre os grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Leia a propriedade memberof no Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Leia a propriedade members no Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Leia a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Leia a propriedade groups.settings no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/read | Ler as propriedades básicas no groupSettings no Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Ler as propriedades básicas no groupSettingTemplates no Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Ler as propriedades básicas em oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/organization/basic/read | Ler as propriedades básicas na organização no Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Leia a propriedade organization.trustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/basic/read | Ler as propriedades básicas no RoleAssignments fosse no Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Ler as propriedades básicas no roleDefinitions no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Leia a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Leia a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Ler as propriedades básicas no servicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Leia a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Leia a propriedade Ownedobjects no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Leia a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Leia a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Ler as propriedades básicas no subscribedSkus no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Leia a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Ler as propriedades básicas sobre os utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Leia a propriedade directreports no Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Leia a propriedade Users no Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Leia a propriedade memberof no Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Leia a propriedade users.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Leia a propriedade owneddevices no Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Leia a propriedade ownedobjects no Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Leia a propriedade de registereddevices no Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Contas de sincronização de diretório
Só é utilizada pelo serviço do Azure AD Connect.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Atualize a propriedade organization.dirSync no Azure Active Directory. |
| microsoft.aad.directory/policies/create | Crie políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Elimine políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/basic/read | Ler as propriedades básicas nas políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/owners/read | Leia a propriedade policies.owners no Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Atualize a propriedade policies.owners no Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Leia a propriedade policies.policiesAppliedTo no Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Atualize a propriedade policies.tenantDefault no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Leia a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Leia a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Atualize a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Atualize a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Atualize a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Ler as propriedades básicas no servicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Atualize as propriedades básicas no servicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Crie servicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Atualize a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Leia a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Leia a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Atualize a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Leia a propriedade Ownedobjects no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Atualize a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Leia a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Atualize a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Execute todas as ações no Azure AD Connect. |

### <a name="directory-writers"></a>Gravadores de diretórios
Pode ler e gravar informações de diretório básicas. Para conceder acesso a aplicações, não se destina aos utilizadores.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groups/create | Crie grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Crie grupos no Azure Active Directory. Criador é adicionado como o primeiro proprietário e o objeto criado conta contra a quota de 250 objetos criados do criador. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Atualize a propriedade groups.appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Atualize as propriedades básicas sobre os grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Atualize a propriedade members no Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Atualize a propriedade groups.settings no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Atualize as propriedades básicas no groupSettings no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Crie groupSettings no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Elimine groupSettings no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gira licenças em utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Atualize as propriedades básicas sobre os utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalida todos os tokens de atualização de utilizador no Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Administrador de serviços do Exchange
Pode gerir todos os aspetos do produto Exchange.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Atualize a propriedade groups.unified no Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Atualize as propriedades básicas dos grupos do Office 365. |
| microsoft.aad.directory/groups/unified/create | Crie grupos do Office 365. |
| microsoft.aad.directory/groups/unified/delete | Elimine grupos do Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Atualize associação de grupos do Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Atualize a propriedade de grupos do Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.exchange/allEntities/allTasks | Gerir todos os aspetos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="external-identity-provider-administrator"></a>Administrador do fornecedor de identidade externo
Configure fornecedores de identidade para utilização na Federação direta.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Ler e configurar fornecedores de identidade no Azure Active Directory B2C. |

### <a name="guest-inviter"></a>Autor de convites
Pode convidar utilizadores convidados independentemente do ' os membros podem convidar convidados"definição.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Leia a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Ler as propriedades básicas sobre os utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Leia a propriedade directreports no Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Convide utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Leia a propriedade Users no Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Leia a propriedade memberof no Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Leia a propriedade users.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Leia a propriedade owneddevices no Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Leia a propriedade ownedobjects no Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Leia a propriedade de registereddevices no Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Administrador de suporte técnico
Pode repor palavras-passe para não administradores e administradores de suporte técnico.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leia a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalida todos os tokens de atualização de utilizador no Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Atualize as palavras-passe para todos os utilizadores no Azure Active Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="intune-service-administrator"></a>Administrador de Serviços do Intune
Pode gerir todos os aspetos do produto Intune.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Atualize as propriedades básicas para contactos no Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Crie contactos no Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Elimine contactos no Azure Active Directory. |
| microsoft.aad.directory/devices/basic/update | Atualize as propriedades básicas em dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leia a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.aad.directory/devices/create | Crie dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Elimine dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Atualize a propriedade devices.registeredOwners no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Atualize a propriedade devices.registeredUsers no Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Atualize a propriedade groups.appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Atualize as propriedades básicas sobre os grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/create | Crie grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Crie grupos no Azure Active Directory. Criador é adicionado como o primeiro proprietário e o objeto criado conta contra a quota de 250 objetos criados do criador. |
| microsoft.aad.directory/groups/delete | Elimine grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Leia a propriedade groups.hiddenMembers no Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Atualize a propriedade members no Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaure grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Atualize a propriedade groups.settings no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Atualize as propriedades básicas sobre os utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.intune/allEntities/allTasks | Gerir todos os aspetos do Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |

### <a name="kaizala-administrator"></a>Administrador do Kaizala
Pode gerir as definições para a Microsoft Kaizala.  

  > [!NOTE]
  > Esta função tem permissões de adicional fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >  
  
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Centro de administração do Office 365 de leitura. |

### <a name="license-administrator"></a>Administrador de licenças
Pode gerir licenças de produtos em utilizadores e grupos.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Gira licenças em utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Atualize a propriedade usagelocation no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |

### <a name="lync-service-administrator"></a>Administrador do serviço Lync
Pode gerir todos os aspectos do produto Skype para empresas.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerir todos os aspetos do Skype para empresas Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="message-center-privacy-reader"></a>Leitor de privacidade do Centro de mensagens
Pode ler o Centro de mensagens publicações, mensagens de privacidade de dados, grupos, domínios e subscrições.

  > [!NOTE]
  > Esta função tem permissões de adicional fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Ler mensagens na microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leia securityMessages no microsoft.office365.messageCenter. |

### <a name="message-center-reader"></a>Leitor do Centro de mensagens
Pode ler as mensagens e atualizações para a sua organização no Centro de mensagens do Office 365 apenas. 

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Ler mensagens na microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>Parceiro de Suporte de Escalão 1
Não utilize – não se destina a utilização geral.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Atualize as propriedades básicas para contactos no Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Crie contactos no Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Elimine contactos no Azure Active Directory. |
| microsoft.aad.directory/groups/create | Crie grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Crie grupos no Azure Active Directory. Criador é adicionado como o primeiro proprietário e o objeto criado conta contra a quota de 250 objetos criados do criador. |
| microsoft.aad.directory/groups/members/update | Atualize a propriedade members no Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gira licenças em utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Atualize as propriedades básicas sobre os utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/delete | Elimine utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalida todos os tokens de atualização de utilizador no Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Atualize as palavras-passe para todos os utilizadores no Azure Active Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.aad.directory/users/restore | Restaure utilizadores eliminados no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="partner-tier2-support"></a>Parceiro de Suporte de Escalão 2
Não utilize – não se destina a utilização geral.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Atualize as propriedades básicas para contactos no Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Crie contactos no Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Elimine contactos no Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Crie e elimine domínios e leia e atualize propriedades padrão no Azure Active Directory. |
| microsoft.aad.directory/groups/create | Crie grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Elimine grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Atualize a propriedade members no Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaure grupos no Azure Active Directory. |
| microsoft.aad.directory/organization/basic/update | Atualize as propriedades básicas na organização no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gira licenças em utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Atualize as propriedades básicas sobre os utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/delete | Elimine utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalida todos os tokens de atualização de utilizador no Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Atualize as palavras-passe para todos os utilizadores no Azure Active Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.aad.directory/users/restore | Restaure utilizadores eliminados no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="power-bi-service-administrator"></a>Administrador de serviço do Power BI
Pode gerir todos os aspetos do produto do Power BI.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerir todos os aspetos do Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="privileged-authentication-administrator"></a>Administrador com privilégios de autenticação
Pode ver, definir e repor as informações de método de autenticação para qualquer utilizador (administrador ou não-administrador).

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalida todos os tokens de atualização de utilizador no Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Propriedades de atualização de autenticação forte, como informações de credenciais MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="privileged-role-administrator"></a>Administrador com função privilegiada
Pode gerir atribuições de funções no Azure AD e todos os aspetos do Privileged Identity Management.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize propriedades padrão no microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/allTasks | Leia e configure a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Leia e configure a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Criar e gerir unidades administrativas (incluindo membros) |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Criar e gerir atribuições de funções. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Criar e gerir definições de funções. |

### <a name="reports-reader"></a>Leitor de relatórios
Pode ler início de sessão e relatórios de auditoria.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no auditLogs no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Relatórios de utilização de leitura Office 365. |

### <a name="search-administrator"></a>Administrador de pesquisa
Pode criar e gerir todos os aspetos das definições do Microsoft Search.

  > [!NOTE]
  > Esta função tem permissões de adicional fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Ler mensagens na microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Crie e elimine todos os recursos e leia e atualize todas as propriedades no microsoft.office365.search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Relatórios de utilização de leitura Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |

### <a name="search-editor"></a>Editor de pesquisa
Pode criar e gerir o conteúdo editorial como marcadores, Q e como, localizações, floorplan.

  > [!NOTE]
  > Esta função tem permissões de adicional fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Ler mensagens na microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Crie e elimine o conteúdo e leia e atualize todas as propriedades no microsoft.office365.search. |
| microsoft.office365.usageReports/allEntities/read | Relatórios de utilização de leitura Office 365. |

### <a name="security-administrator"></a>Administrador de Segurança
Pode ler as informações de segurança e de relatórios e gerir a configuração no Azure AD e o Office 365.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no auditLogs no Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leia a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/create | Crie políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Elimine políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Atualize a propriedade policies.owners no Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Atualize a propriedade policies.tenantDefault no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Atualize a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no signInReports no Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Ler todos os recursos no microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Atualize todos os recursos no microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Ler todos os recursos no microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Ler todos os aspetos do Centro de proteção do Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Atualize todos os recursos no microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |

### <a name="security-reader"></a>Leitor de segurança
Pode ler as informações de segurança e relatórios no Azure AD e o Office 365.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no auditLogs no Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leia a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo propriedades com privilégios), no signInReports no Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Ler todos os recursos no microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Ler todos os recursos no microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Ler todos os aspetos do Centro de proteção do Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |

### <a name="service-support-administrator"></a>Administrador de assistência técnica
Pode ler as informações de estado de funcionamento do serviço e gerir pedidos de suporte.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="sharepoint-service-administrator"></a>Administrador de serviços do SharePoint
Pode gerir todos os aspetos do serviço do SharePoint.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Atualize a propriedade groups.unified no Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Atualize as propriedades básicas dos grupos do Office 365. |
| microsoft.aad.directory/groups/unified/create | Crie grupos do Office 365. |
| microsoft.aad.directory/groups/unified/delete | Elimine grupos do Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Atualize associação de grupos do Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Atualize a propriedade de grupos do Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize propriedades padrão no microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

### <a name="teams-communications-administrator"></a>Administrador de comunicação de equipas
Pode gerir a chamar e os recursos de reuniões do serviço do Microsoft Teams.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Relatórios de utilização de leitura Office 365. |

### <a name="teams-communications-support-engineer"></a>Engenheiro de suporte de comunicação de equipas
Pode resolver problemas de comunicação entre as Equipes com ferramentas avançadas.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |

### <a name="teams-communications-support-specialist"></a>Especialista de suporte de comunicações de equipes
Pode resolver problemas de comunicação entre as Equipes com as ferramentas básicas.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |

### <a name="teams-service-administrator"></a>Administrador de serviços de equipes
Pode gerir o serviço do Microsoft Teams.

  > [!NOTE]
  > Esta função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Leia a propriedade groups.hiddenMembers no Azure Active Directory. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Atualize a propriedade groups.unified no Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Atualize as propriedades básicas dos grupos do Office 365. |
| microsoft.aad.directory/groups/unified/create | Crie grupos do Office 365. |
| microsoft.aad.directory/groups/unified/delete | Elimine grupos do Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Atualize associação de grupos do Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Atualize a propriedade de grupos do Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Relatórios de utilização de leitura Office 365. |

### <a name="user-administrator"></a>Administrador de utilizadores
Pode gerir todos os aspetos de utilizadores e grupos, incluindo repor palavras-passe para administradores limitados.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Elimine appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Atualize appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/update | Atualize as propriedades básicas para contactos no Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Crie contactos no Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Elimine contactos no Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Atualize a propriedade groups.appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Atualize as propriedades básicas sobre os grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/create | Crie grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Crie grupos no Azure Active Directory. Criador é adicionado como o primeiro proprietário e o objeto criado conta contra a quota de 250 objetos criados do criador. |
| microsoft.aad.directory/groups/delete | Elimine grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Leia a propriedade groups.hiddenMembers no Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Atualize a propriedade members no Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaure grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Atualize a propriedade groups.settings no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gira licenças em utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Atualize as propriedades básicas sobre os utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/create | Crie utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/delete | Elimine utilizadores no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalida todos os tokens de atualização de utilizador no Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Atualize as palavras-passe para todos os utilizadores no Azure Active Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.aad.directory/users/restore | Restaure utilizadores eliminados no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar o Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos no microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar o estado de funcionamento do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir pedidos de suporte do Office 365. |

## <a name="role-template-ids"></a>IDs de modelo de função

IDs de modelo de função são usados principalmente por utilizadores de Graph API ou o PowerShell.

DisplayName de gráfico | Nome a apresentar de portal do Azure | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Administrador da aplicação | Administrador da aplicação | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Desenvolvedor de aplicativos | Desenvolvedor de aplicativos | CF1C38E5-3621-4004-A7CB-879624DCED7C
Administrador de autenticação | Administrador de autenticação | c4e39bd9-1100-46d3-8c65-fb160da0071f
Administrador do Azure Information Protection | Administrador do Azure Information Protection | 7495fdc4-34c4-4d15-a289-98788ce399fd
Administrador de fluxo de utilizador de B2C | Administrador de fluxo de utilizador de B2C | 6e591065-9bad-43ed-90f3-e9424366d2f0
Administrador de Atributos de Fluxo de Utilizadores B2C | Administrador de Atributos de Fluxo de Utilizadores B2C | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Administrador de Conjuntos de Chaves B2C IEF | Administrador de Conjuntos de Chaves B2C IEF | aaf43236-0c0d-4d5f-883a-6955382ac081
Administrador de Políticas B2C IEF | Administrador de Políticas B2C IEF | 3edaf663-341e-4475-9f94-5c398ef6c070
Administrador de faturação | Administrador de faturação | b0f54661-2d74-4c50-afa3-1ec803f12efe
Administrador de análise de ambiente de trabalho | Administrador de análise de ambiente de trabalho | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Administrador da aplicação cloud | Administrador da aplicação cloud | 158c047a-c907-4556-b7ef-446551a6b5f7
Administrador de dispositivos de cloud | Administrador de dispositivos de cloud | 7698a772-787b-4ac8-901f-60d6b08affd2
Administrador de Empresa | Administrador global | 62e90394-69f5-4237-9190-012177145e10
Administrador de conformidade | Administrador de conformidade | 17315797-102d-40b4-93e0-432062caca18
Administrador de Acesso Condicional | Administrador de acesso condicional | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Administrador de serviço CRM | Administrador do Dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Aprovador de acesso ao Cofre de cliente | Aprovador de acesso do Cofre de cliente | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Administradores de dispositivos | Administradores de dispositivos | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Associação de dispositivo | Associação de dispositivo | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Gestores de dispositivos | Gestores de dispositivos | 2b499bcd-da44-4968-8aec-78e1674fa64d
Utilizadores de dispositivos | Utilizadores de dispositivos | d405c6df-0af8-4e3b-95e4-4d06e542189e
Leitores de diretório | Leitores de diretório | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Contas de sincronização de diretório | Contas de sincronização de diretório | d29b2b05-8046-44ba-8758-1e26182fcf32
Gravadores de diretórios | Gravadores de diretórios | 9360feb5-f418-4baa-8175-e2a00bac4301
Administrador de serviços do Exchange | Administrador do Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administrador do fornecedor de identidade externo | Administrador do fornecedor de identidade externo | be2f45a1-457d-42af-a067-6ec1fa63bc45
Autor de convites | Autor de convites | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Administrador de suporte técnico | Administrador de palavras-passe | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administrador de Serviços do Intune | Administrador do Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Administrador do Kaizala | Administrador do Kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Administrador de licenças | Administrador de licenças | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Administrador do serviço Lync | Skype para o administrador da empresa | 75941009-915a-4869-abe7-691bff18279e
Leitor de privacidade do Centro de mensagens | Leitor de privacidade do Centro de mensagens | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Leitor do Centro de mensagens | Leitor do Centro de mensagens | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Parceiro de Suporte de Escalão 1 | Parceiro de suporte de escalão 1 | 4ba39ca4-527c-499a-b93d-d9b492c50246
Parceiro de Suporte de Escalão 2 | Parceiro de suporte de escalão 2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Administrador de serviço do Power BI | Administrador do Power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Administrador com privilégios de autenticação | Administrador com privilégios de autenticação | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administrador com função privilegiada | Administrador com função privilegiada | e8611ab8-c189-46e8-94e1-60213ab1f814
Leitor de relatórios | Leitor de relatórios | 4a5d8f65-41da-4de4-8968-e035b65339cf
Administrador de pesquisa | Administrador de pesquisa | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Editor de pesquisa | Editor de pesquisa | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Administrador de Segurança | Administrador de segurança | 194ae4cb-b126-40b2-bd5b-6091b380977d
Leitor de segurança | Leitor de segurança | 5d6b6bb7-de71-4623-b4af-96380a352509
Administrador de assistência técnica | Administrador de serviço | f023fd81-a637-4b56-95fd-791ac0226033
Administrador de serviços do SharePoint | Administrador do SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Administrador de comunicação de equipas | Administrador de comunicação de equipas | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Engenheiro de suporte de comunicação de equipas | Engenheiro de suporte de comunicação de equipas | f70938a0-fc10-4177-9e90-2178f8765737
Especialista de suporte de comunicações de equipes | Especialista de suporte de comunicações de equipes | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Administrador de serviços de equipes | Administrador de serviços de equipes | 69091246-20e8-4a56-aa4d-066075b2a7a8
Utilizador | Utilizador | a0b1b346-4d3e-4e8b-98f8-753987be4970
Administrador de Conta de Utilizador | Administrador de utilizadores | fe930be7-5e62-47db-91af-98c3a49a38b1
Associação de dispositivo à área de trabalho | Associação de dispositivo à área de trabalho | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>Funções preteridas

As seguintes funções não devem ser utilizadas. Eles foram preteridos e serão removidos do Azure AD no futuro.

* Administrador de licenças AdHoc
* Associação de dispositivo
* Gestores de dispositivos
* Utilizadores de dispositivos
* Criador de utilizador verificado por e-mail
* Administrador de caixa de correio
* Associação de dispositivo à área de trabalho

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre como atribuir um utilizador como administrador de uma subscrição do Azure, veja [gerir o acesso com RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md)
* Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para obter mais informações sobre como o Azure Active Directory está relacionado com a sua subscrição do Azure, veja [Como as subscrições do Azure estão associadas ao Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
