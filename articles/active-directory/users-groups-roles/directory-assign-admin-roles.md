---
title: Descrições e permissões de função de administrador-Azure Active Directory | Microsoft Docs
description: Uma função de administrador pode adicionar usuários, atribuir funções administrativas, redefinir senhas de usuário, gerenciar licenças de usuário ou gerenciar domínios.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/17/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24d3da81fabf55bc0c3944f0c03829dee4fcce46
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304400"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Permissões de função de administrador no Azure Active Directory

Usando o Azure Active Directory (AD do Azure), você pode designar administradores limitados para gerenciar tarefas de identidade em funções com menos privilégios. Os administradores podem ser atribuídos para tais finalidades como adição ou alteração de usuários, atribuição de funções administrativas, redefinição de senhas de usuário, gerenciamento de licenças de usuário e gerenciamento de nomes de domínio. As permissões de usuário padrão só podem ser alteradas nas configurações do usuário no Azure AD.

O administrador global tem acesso a todos os recursos administrativos. Por predefinição, a pessoa que se inscreve para uma subscrição do Azure é atribuída a função de Administrador Global do diretório. Somente administradores globais e administradores de função com privilégios podem delegar funções de administrador. Para reduzir o risco para seu negócio, recomendamos que você atribua essa função a apenas algumas pessoas em sua empresa.

## <a name="assign-or-remove-administrator-roles"></a>Atribuir ou remover funções de administrador

Para saber como atribuir funções administrativas a um usuário no Azure Active Directory, consulte [Exibir e atribuir funções de administrador no Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Funções disponíveis

As seguintes funções de administrador estão disponíveis:

* **[Administrador do aplicativo](#application-administrator)** : Os usuários nessa função podem criar e gerenciar todos os aspectos de aplicativos empresariais, registros de aplicativos e configurações de proxy de aplicativo. Essa função também concede a capacidade de consentir com permissões delegadas e permissões de aplicativo, exceto Microsoft Graph e Azure AD Graph. Os usuários atribuídos a essa função não são adicionados como proprietários ao criar novos registros de aplicativo ou aplicativos empresariais.

  <b>Importante</b>: Essa função concede a capacidade de gerenciar credenciais de aplicativo. Os usuários atribuídos a essa função podem adicionar credenciais a um aplicativo e usar essas credenciais para representar a identidade do aplicativo. Se a identidade do aplicativo tiver recebido acesso a Azure Active Directory, como a capacidade de criar ou atualizar usuários ou outros objetos, um usuário atribuído a essa função poderá executar essas ações ao representar o aplicativo. Essa capacidade de representar a identidade do aplicativo pode ser uma elevação de privilégio sobre o que o usuário pode fazer por meio de suas atribuições de função no Azure AD. É importante entender que a atribuição de um usuário à função de administrador de aplicativos oferece a eles a capacidade de representar a identidade de um aplicativo.

* **[Desenvolvedor de aplicativos](#application-developer)** : Os usuários nessa função podem criar registros de aplicativo quando a configuração "os usuários podem registrar aplicativos" está definida como não. Essa função também concede permissão para consentimento em um nome próprio quando a configuração "os usuários podem consentir com aplicativos que acessam dados da empresa em seu nome" está definida como não. Os usuários atribuídos a essa função são adicionados como proprietários ao criar novos registros de aplicativo ou aplicativos empresariais.

* **[Administrador de autenticação](#authentication-administrator)** : Usuários com essa função podem definir ou redefinir credenciais de não senha e podem atualizar senhas para todos os usuários. Os administradores de autenticação podem exigir que os usuários se registrem novamente em relação à credencial não-senha existente (por exemplo, MFA ou FIDO) e revogue **lembram a MFA no dispositivo**, que solicita a MFA na próxima entrada de usuários que não são administradores ou atribuídas apenas as seguintes funções:
  * Administrador de autenticação
  * Leitores de diretório
  * Convite do convidado
  * Leitor do centro de mensagens
  * Leitor de relatórios

  A função Administrador de autenticação está atualmente em visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

  <b>Importante</b>: Usuários com essa função podem alterar credenciais para pessoas que podem ter acesso a informações confidenciais ou privadas ou configuração crítica dentro e fora do Azure Active Directory. Alterar as credenciais de um usuário pode significar a capacidade de assumir a identidade e as permissões desse usuário. Por exemplo:

  * Registro de aplicativo e proprietários de aplicativos empresariais, que podem gerenciar credenciais de aplicativos que eles possuem. Esses aplicativos podem ter permissões privilegiadas no Azure AD e em outros lugares não concedidos a administradores de autenticação. Por meio desse caminho, um administrador de autenticação pode assumir a identidade de um proprietário do aplicativo e assumir ainda mais a identidade de um aplicativo com privilégios atualizando as credenciais para o aplicativo.
  * Proprietários de assinatura do Azure, que podem ter acesso a informações confidenciais ou privadas ou configuração crítica no Azure.
  * Grupo de segurança e proprietários do grupo do Office 365, que podem gerenciar a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou configuração crítica no Azure AD e em outro lugar.
  * Administradores em outros serviços fora do Azure AD, como o Exchange Online, o centro de conformidade e segurança do Office e os sistemas de recursos humanos.
  * Não administradores, como executivos, consultor jurídico e funcionários de recursos humanos que podem ter acesso a informações confidenciais ou privadas.

* **[Administrador da proteção de informações do Azure](#azure-information-protection-administrator)** : Os usuários com essa função têm todas as permissões no serviço de proteção de informações do Azure. Essa função permite configurar rótulos para a política de proteção de informações do Azure, gerenciar modelos de proteção e ativar a proteção. Essa função não concede permissões no centro de proteção de identidade, Privileged Identity Management, monitora a integridade do serviço Office 365 ou Centro de Conformidade e Segurança do Office 365.

* **[Administrador do fluxo de usuário do B2C](#b2c-user-flow-administrator)** : Os usuários com essa função podem criar e gerenciar Fluxos dos Usuários B2C (também conhecido como políticas "internas") no portal do Azure. Ao criar ou editar fluxos de usuário, esses usuários podem alterar o conteúdo HTML/CSS/JavaScript da experiência do usuário, alterar os requisitos de MFA por fluxo de usuário, alterar as declarações no token e ajustar as configurações de sessão para todas as políticas no locatário. Por outro lado, essa função não inclui a capacidade de revisar os dados do usuário ou fazer alterações nos atributos que estão incluídos no esquema do locatário. As alterações feitas nas políticas da estrutura de experiência de identidade (também conhecidas como personalizadas) também estão fora do escopo dessa função.

* **[Administrador de atributos de fluxo do usuário B2C](#b2c-user-flow-attribute-administrator)** : Os usuários com essa função adicionam ou excluem atributos personalizados disponíveis para todos os fluxos de usuário no locatário. Dessa forma, os usuários com essa função podem alterar ou adicionar novos elementos ao esquema do usuário final e impactar o comportamento de todos os fluxos do usuário e resultar indiretamente em alterações em quais dados podem ser solicitados aos usuários finais e, por fim, enviados como declarações para os aplicativos. Essa função não pode editar fluxos de usuário.

* Administrador do conjunto de **[chaves B2C IEF](#b2c-ief-keyset-administrator)** :    O usuário pode criar e gerenciar chaves de política e segredos para criptografia de token, assinaturas de token e criptografia/descriptografia de declaração. Ao adicionar novas chaves a contêineres de chave existentes, esse administrador limitado pode sobrepor os segredos conforme necessário, sem afetar os aplicativos existentes. Esse usuário pode ver o conteúdo completo desses segredos e suas datas de expiração mesmo após a criação.
    
  <b>Importante:</b> essa é uma função confidencial. A função de administrador do conjunto de chaves deve ser cuidadosamente auditada e atribuída com cuidado durante a pré-produção e a produção.

* **[Administrador da política do IEF B2C](#b2c-ief-policy-administrator)** : Os usuários nessa função têm a capacidade de criar, ler, atualizar e excluir todas as políticas personalizadas no Azure AD B2C e, portanto, ter controle total sobre a estrutura de experiência de identidade no locatário do Azure AD B2C relevante. Editando políticas, esse usuário pode estabelecer a Federação direta com provedores de identidade externos, alterar o esquema de diretório, alterar todo o conteúdo voltado para o usuário (HTML, CSS, JavaScript), alterar os requisitos para concluir uma autenticação, criar novos usuários, enviar dados do usuário para sistemas externos, incluindo migrações completas, e editar todas as informações do usuário, incluindo campos confidenciais, como senhas e números de telefone. Por outro lado, essa função não pode alterar as chaves de criptografia ou editar os segredos usados para federação no locatário.

  <b>Importante:</b> O administrador da política IEF B2 é uma função altamente confidencial que deve ser atribuída de forma muito limitada para locatários em produção. As atividades por esses usuários devem ser rigorosamente auditadas, especialmente para locatários em produção.

* **[Administrador de cobrança](#billing-administrator)** : Faz compras, gerencia assinaturas, gerencia tíquetes de suporte e monitora a integridade do serviço.

* **[Administrador de aplicativos de nuvem](#cloud-application-administrator)** : Os usuários nessa função têm as mesmas permissões que a função Administrador de aplicativos, excluindo a capacidade de gerenciar o proxy de aplicativo. Essa função concede a capacidade de criar e gerenciar todos os aspectos de aplicativos empresariais e registros de aplicativos. Essa função também concede a capacidade de consentir com permissões delegadas e permissões de aplicativo, exceto Microsoft Graph e Azure AD Graph. Os usuários atribuídos a essa função não são adicionados como proprietários ao criar novos registros de aplicativo ou aplicativos empresariais.

  <b>Importante</b>: Essa função concede a capacidade de gerenciar credenciais de aplicativo. Os usuários atribuídos a essa função podem adicionar credenciais a um aplicativo e usar essas credenciais para representar a identidade do aplicativo. Se a identidade do aplicativo tiver recebido acesso a Azure Active Directory, como a capacidade de criar ou atualizar usuários ou outros objetos, um usuário atribuído a essa função poderá executar essas ações ao representar o aplicativo. Essa capacidade de representar a identidade do aplicativo pode ser uma elevação de privilégio sobre o que o usuário pode fazer por meio de suas atribuições de função no Azure AD. É importante entender que a atribuição de um usuário à função de administrador de aplicativos de nuvem oferece a eles a capacidade de representar a identidade de um aplicativo.

* **[Administrador de dispositivo de nuvem](#cloud-device-administrator)** : Os usuários nessa função podem habilitar, desabilitar e excluir dispositivos no Azure AD e ler as chaves do BitLocker do Windows 10 (se houver) no portal do Azure. A função não concede permissões para gerenciar outras propriedades no dispositivo.

* **[Administrador de conformidade](#compliance-administrator)** : Usuários com essa função têm permissões para gerenciar recursos relacionados à conformidade no centro de conformidade Microsoft 365, Microsoft 365 centro de administração, Azure e Office 365 Centro de Conformidade e Segurança. Os usuários também podem gerenciar todos os recursos no centro de administração do Exchange e as equipes & centro de administração do Skype for Business e criar tíquetes de suporte para o Azure e Microsoft 365. Mais informações estão disponíveis em [sobre as funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  Em | Pode fazer
  ----- | ----------
  [Centro de conformidade Microsoft 365](https://protection.office.com) | Proteja e gerencie os dados de sua organização em serviços Microsoft 365s<br>Gerenciar alertas de conformidade
  [Gerente de conformidade](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhe, atribua e verifique as atividades de conformidade regulatória de sua organização
  [Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerenciar governança de dados<br>Executar investigação legal e de dados<br>Gerenciar solicitação de entidade de dados
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibir todos os dados de auditoria do Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões somente leitura e pode gerenciar alertas<br>Pode criar e modificar políticas de arquivo e permitir ações de governança de arquivo<br> Pode exibir todos os relatórios internos em Gerenciamento de Dados

* **[Administrador de dados de conformidade](#compliance-data-administrator)** : Os usuários com essa função têm permissões para proteger e rastrear dados no centro de conformidade Microsoft 365, no centro de administração Microsoft 365 e no Azure. Os usuários também podem gerenciar todos os recursos no centro de administração do Exchange, no Compliance Manager e nas equipes & centro de administração do Skype for Business e criar tíquetes de suporte para o Azure e Microsoft 365.

  Em | Pode fazer
  ----- | ----------
  [Centro de conformidade Microsoft 365](https://protection.office.com) | Monitorar políticas relacionadas à conformidade em serviços de Microsoft 365<br>Gerenciar alertas de conformidade
  [Gerente de conformidade](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhe, atribua e verifique as atividades de conformidade regulatória de sua organização
  [Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerenciar governança de dados<br>Executar investigação legal e de dados<br>Gerenciar solicitação de entidade de dados
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibir todos os dados de auditoria do Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões somente leitura e pode gerenciar alertas<br>Pode criar e modificar políticas de arquivo e permitir ações de governança de arquivo<br> Pode exibir todos os relatórios internos em Gerenciamento de Dados

* **[Administrador de acesso condicional](#conditional-access-administrator)** : Os usuários com essa função têm a capacidade de gerenciar Azure Active Directory configurações de acesso condicional.
  > [!NOTE]
  > Para implantar a política de acesso condicional do Exchange ActiveSync no Azure, o usuário também deve ser um administrador global.
  
* **[Aprovador de acesso sistema de proteção de dados do cliente](#customer-lockbox-access-approver)** : Gerencia [sistema de proteção de dados do cliente solicitações](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) em sua organização. Eles recebem notificações por email para solicitações de Sistema de Proteção de Dados do Cliente e podem aprovar e negar solicitações do centro de administração de Microsoft 365. Eles também podem ativar ou desativar o recurso de Sistema de Proteção de Dados do Cliente. Somente administradores globais podem redefinir as senhas de pessoas atribuídas a essa função.
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[Administrador do desktop Analytics](#desktop-analytics-administrator)** : Os usuários nessa função podem gerenciar a análise de desktops e a personalização do Office & serviços de política. Para análise de desktops, isso inclui a capacidade de exibir o inventário de ativos, criar planos de implantação, exibir o status de integridade e implantação. Para a personalização do Office & serviço de política, essa função permite que os usuários gerenciem as políticas do Office.

* **[Administrador do dispositivo](#device-administrators)** : Essa função está disponível para atribuição somente como um administrador local adicional nas [configurações do dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Os usuários com essa função se tornam administradores do computador local em todos os dispositivos com Windows 10 que ingressaram no Azure Active Directory. Eles não têm a capacidade de gerenciar objetos de dispositivos no Azure Active Directory. 

* **[Leitores de diretório](#directory-readers)** : Essa é uma função que deve ser atribuída somente a aplicativos herdados que não dão suporte à [estrutura de consentimento](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Não o atribua aos usuários.

* **[Contas de sincronização de diretório](#directory-synchronization-accounts)** : Não use. Essa função é atribuída automaticamente ao serviço de Azure AD Connect e não é destinada nem tem suporte para nenhum outro uso.

* **[Gravadores de diretório](#directory-writers)** : Esta é uma função herdada que deve ser atribuída a aplicativos que não dão suporte à [estrutura de consentimento](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ele não deve ser atribuído a nenhum usuário.

* **[Administrador do Dynamics 365 administrador/CRM](#crm-service-administrator)** : Os usuários com essa função têm permissões globais no Microsoft Dynamics 365 online, quando o serviço está presente, bem como a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [usam a função de administrador de serviço para gerenciar seu locatário](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > Na API Microsoft Graph, no Azure AD API do Graph e no Azure AD PowerShell, essa função é identificada como "administrador de serviços do Dynamics 365". É "administrador do Dynamics 365" no [portal do Azure](https://portal.azure.com).

* **[Administrador do Exchange](#exchange-service-administrator)** : Os usuários com essa função têm permissões globais no Microsoft Exchange Online, quando o serviço está presente. Também tem a capacidade de criar e gerenciar todos os grupos do Office 365, gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [sobre as funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Na API Microsoft Graph, no Azure AD API do Graph e no Azure AD PowerShell, essa função é identificada como "administrador de serviços do Exchange". É o "administrador do Exchange" no [portal do Azure](https://portal.azure.com). É "Exchange Online administrador" no centro de [Administração do Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144). 

* **[Administrador do provedor de identidade externo](#external-identity-provider-administrator)** : Esse administrador gerencia a Federação entre Azure Active Directory locatários e provedores de identidade externos. Com essa função, os usuários podem adicionar novos provedores de identidade e definir todas as configurações disponíveis (por exemplo, caminho de autenticação, ID de serviço, contêineres de chave atribuídos). Esse usuário pode habilitar o locatário para confiar em autenticações de provedores de identidade externos. O impacto resultante sobre as experiências do usuário final depende do tipo de locatário:
  * Azure Active Directory locatários para funcionários e parceiros: A adição de uma federação (por exemplo, com o Gmail) afetará imediatamente todos os convites de convidados que ainda não foram resgatados. Consulte [adicionando o Google como um provedor de identidade para usuários convidados B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
  * Azure Active Directory B2C locatários: A adição de uma federação (por exemplo, com o Facebook ou com outro Azure Active Directory) não afeta imediatamente os fluxos do usuário final até que o provedor de identidade seja adicionado como uma opção em um fluxo de usuário (também conhecido como diretiva interna). Consulte [Configurando um conta Microsoft como um provedor de identidade](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) para obter um exemplo. Para alterar os fluxos de usuário, é necessário ter a função limitada de "administrador de fluxo de usuário do B2C".

* Administração **[global/administrador da empresa](#company-administrator)** : Os usuários com essa função têm acesso a todos os recursos administrativos no Azure Active Directory, bem como serviços que usam identidades Azure Active Directory como Microsoft 365 central de segurança, Microsoft 365 centro de conformidade, Exchange Online, SharePoint Online e Skype for Business online. A pessoa que se inscreve para o locatário de Azure Active Directory se torna um administrador global. Somente os administradores globais podem atribuir outras funções de administrador. Pode haver mais de um administrador global na sua empresa. Os administradores globais podem redefinir a senha para qualquer usuário e todos os outros administradores.

  > [!NOTE]
  > Na API Microsoft Graph, no Azure AD API do Graph e no Azure AD PowerShell, essa função é identificada como "administrador da empresa". É "administrador global" na [portal do Azure](https://portal.azure.com).
  >
  >

* **[Convite do convidado](#guest-inviter)** : Os usuários nessa função podem gerenciar Azure Active Directory convites de usuário convidado B2B quando os **Membros podem convidar** a configuração de usuário estiver definido como não. Mais informações sobre a colaboração B2B em [sobre a colaboração B2B do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Ele não inclui nenhuma outra permissão.

* **[Administrador do Intune](#intune-service-administrator)** : Os usuários com essa função têm permissões globais no Microsoft Intune online, quando o serviço está presente. Além disso, essa função contém a capacidade de gerenciar usuários e dispositivos para associar a política, bem como criar e gerenciar grupos. Mais informações no [RBAC (controle de administração baseada em função) com Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > Na API Microsoft Graph, no Azure AD API do Graph e no Azure AD PowerShell, essa função é identificada como "administrador de serviços do Intune". É o "administrador do Intune" no [portal do Azure](https://portal.azure.com).
  
 * **[Administrador do Kaizala](#kaizala-administrator)** : Os usuários com essa função têm permissões globais para gerenciar configurações no Microsoft Kaizala, quando o serviço está presente, bem como a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço.
Além disso, o usuário pode acessar relatórios relacionados à adoção & uso de Kaizala por membros da organização e relatórios comerciais gerados usando as ações do Kaizala. 

* **[Administrador de licenças](#license-administrator)** : Os usuários nessa função podem adicionar, remover e atualizar atribuições de licença em usuários, grupos (usando o licenciamento baseado em grupo) e gerenciar o local de uso nos usuários. A função não concede a capacidade de comprar ou gerenciar assinaturas, criar ou gerenciar grupos ou criar ou gerenciar usuários além do local de uso. Essa função não tem acesso para exibir, criar ou gerenciar tíquetes de suporte.

* **[Leitor de privacidade do centro de mensagens](#message-center-privacy-reader)** : Os usuários nessa função podem monitorar todas as notificações no centro de mensagens, incluindo mensagens de privacidade de dados. Os leitores de privacidade do centro de mensagens recebem notificações por email, incluindo aquelas relacionadas à privacidade dos dados, e podem cancelar a assinatura usando as preferências do centro de mensagens. Somente o administrador global e o leitor de privacidade do centro de mensagens podem ler mensagens de privacidade de dados. Além disso, essa função contém a capacidade de exibir grupos, domínios e assinaturas. Essa função não tem permissão para exibir, criar ou gerenciar solicitações de serviço.

* **[Leitor do centro de mensagens](#message-center-reader)** : Os usuários nessa função podem monitorar notificações e atualizações de integridade de consultoria no [centro de mensagens do Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) para sua organização em serviços configurados, como o Exchange, o Intune e o Microsoft Teams. Os leitores do centro de mensagens recebem resumos semanais de emails de postagens, atualizações e podem compartilhar postagens do centro de mensagens no Office 365. No Azure AD, os usuários atribuídos a essa função terão acesso somente leitura nos serviços do Azure AD, como usuários e grupos. Essa função não tem acesso para exibir, criar ou gerenciar tíquetes de suporte.

* **[Suporte nível 1 do parceiro](#partner-tier1-support)** : Não use. Essa função foi preterida e será removida do Azure AD no futuro. Essa função destina-se ao uso por um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

* **[Suporte tier2 do parceiro](#partner-tier2-support)** : Não use. Essa função foi preterida e será removida do Azure AD no futuro. Essa função destina-se ao uso por um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

* **[Administrador da assistência técnica (senha)](#helpdesk-administrator)** : Usuários com essa função podem alterar senhas, invalidar tokens de atualização, gerenciar solicitações de serviço e monitorar a integridade do serviço. A invalidação de um token de atualização força o usuário a entrar novamente. Os administradores de assistência técnica podem redefinir senhas e invalidar tokens de atualização de outros usuários que não são administradores ou que atribuíram as seguintes funções somente:
  * Leitores de diretório
  * Convite do convidado
  * Administrador de assistência técnica
  * Leitor do centro de mensagens
  * Leitor de relatórios
  
  <b>Importante</b>: Usuários com essa função podem alterar senhas para pessoas que podem ter acesso a informações confidenciais ou privadas ou configuração crítica dentro e fora do Azure Active Directory. Alterar a senha de um usuário pode significar a capacidade de assumir a identidade e as permissões desse usuário. Por exemplo:
  * Registro de aplicativo e proprietários de aplicativos empresariais, que podem gerenciar credenciais de aplicativos que eles possuem. Esses aplicativos podem ter permissões privilegiadas no Azure AD e em outros lugares não concedidos aos administradores de assistência técnica. Por meio desse caminho, um administrador de assistência técnica pode assumir a identidade de um proprietário de aplicativo e, em seguida, assumir ainda mais a identidade de um aplicativo com privilégios atualizando as credenciais para o aplicativo.
  * Proprietários de assinatura do Azure, que podem ter acesso a informações confidenciais ou privadas ou configuração crítica no Azure.
  * Grupo de segurança e proprietários do grupo do Office 365, que podem gerenciar a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou configuração crítica no Azure AD e em outro lugar.
  * Administradores em outros serviços fora do Azure AD, como o Exchange Online, o centro de conformidade e segurança do Office e os sistemas de recursos humanos.
  * Não administradores, como executivos, consultor jurídico e funcionários de recursos humanos que podem ter acesso a informações confidenciais ou privadas.


  > [!NOTE]
  > A delegação de permissões administrativas em subconjuntos de usuários e aplicação de políticas a um subconjunto de usuários é possível com [unidades administrativas (versão prévia)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).


  > [!NOTE]
  > Essa função anteriormente era chamada de "administrador de senha" no [portal do Azure](https://portal.azure.com/). Estamos alterando seu nome para "administrador de assistência técnica" para corresponder ao nome no PowerShell do Azure AD, no Azure AD API do Graph e na API de Microsoft Graph. Por um curto período, alteraremos o nome para "administrador da assistência técnica (senha)" em portal do Azure antes da alteração para "administrador de assistência técnica".


* **[Power bi administrador](#power-bi-service-administrator)** : Os usuários com essa função têm permissões globais no Microsoft Power BI, quando o serviço está presente, bem como a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [noções básicas sobre a função de administrador de Power bi](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > Na API Microsoft Graph, no Azure AD API do Graph e no Azure AD PowerShell, essa função é identificada como "administrador de serviços do Power BI". É "administrador de Power BI" na [portal do Azure](https://portal.azure.com).

* **[Administrador de autenticação privilegiada](#privileged-authentication-administrator)** : Os usuários com essa função podem definir ou redefinir credenciais de não senha para todos os usuários, incluindo administradores globais, e podem atualizar senhas para todos os usuários. Os administradores de autenticação privilegiada podem forçar os usuários a se registrarem novamente em relação à credencial não-senha existente (por exemplo, MFA, FIDO) e revogar "lembrar MFA no dispositivo", solicitando a MFA no próximo logon de todos os usuários. Os administradores de autenticação privilegiada podem:
  * Forçar os usuários a se registrar novamente em relação a credenciais de não senha existentes (por exemplo, MFA, FIDO)
  * REVOKE ' lembrar MFA no dispositivo ', solicitando a MFA no próximo logon

* **[Administrador de função com privilégios](#privileged-role-administrator)** : Os usuários com essa função podem gerenciar atribuições de função no Azure Active Directory, bem como em Azure AD Privileged Identity Management. Além disso, essa função permite o gerenciamento de todos os aspectos de Privileged Identity Management e de unidades administrativas.

  <b>Importante</b>: Essa função concede a capacidade de gerenciar atribuições para todas as funções do Azure AD, incluindo a função de administrador global. Essa função não inclui nenhuma outra capacidade privilegiada no Azure AD, como criar ou atualizar usuários. No entanto, os usuários atribuídos a essa função podem conceder a si mesmos ou a outros privilégios adicionais atribuindo funções adicionais.

* **[Leitor de relatórios](#reports-reader)** : Os usuários com essa função podem exibir dados de relatório de uso e o painel relatórios no centro de administração Microsoft 365 e o pacote de contexto de adoção no Power BI. Além disso, a função fornece acesso a relatórios de entrada e atividade no Azure AD e dados retornados pela API de relatório Microsoft Graph. Um usuário atribuído à função leitor de relatórios pode acessar apenas as métricas de adoção e uso relevantes. Eles não têm permissões de administrador para definir configurações ou acessar os centros de administração específicos do produto, como o Exchange. Essa função não tem acesso para exibir, criar ou gerenciar tíquetes de suporte.

* **[Administrador de pesquisa](#search-administrator)** : Os usuários nesta função têm acesso completo a todos os recursos de gerenciamento do Microsoft Search no centro de administração Microsoft 365. Os administradores de pesquisa podem delegar os administradores de pesquisa e as funções do editor de pesquisa aos usuários, bem como criar e gerenciar conteúdo, como indicadores, p & como e locais. Além disso, esses usuários podem exibir o centro de mensagens, monitorar a integridade do serviço e criar solicitações de serviço.

* **[Editor de pesquisa](#search-editor)** : Os usuários nessa função podem criar, gerenciar e excluir conteúdo do Microsoft Search no centro de administração Microsoft 365, incluindo indicadores, p & como e locais.

* **[Administrador de segurança](#security-administrator)** : Usuários com essa função têm permissões para gerenciar recursos relacionados à segurança na central de segurança Microsoft 365, Azure Active Directory Identity Protection, proteção de informações do Azure e Centro de Conformidade e Segurança do Office 365. Mais informações sobre as permissões do Office 365 estão disponíveis em [permissões no centro de conformidade e segurança do office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  Em | Pode fazer
  --- | ---
  [Central de segurança do Microsoft 365](https://protection.office.com) | Monitorar políticas relacionadas à segurança em serviços de Microsoft 365<br>Gerenciar ameaças de segurança e alertas<br>Exibir relatórios
  Centro de proteção de identidade | Todas as permissões da função leitor de segurança<br>Além disso, a capacidade de executar todas as operações da central de proteção de identidade, exceto para redefinição de senhas
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Todas as permissões da função leitor de segurança<br>**Não é possível** gerenciar as atribuições ou configurações de função do Azure AD
  [Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerenciar políticas de segurança<br>Exibir, investigar e responder a ameaças de segurança<br>Exibir relatórios
  Proteção Avançada Contra Ameaças do Azure | Monitorar e responder a atividades de segurança suspeitas
  Windows Defender ATP e EDR | Atribuir funções<br>Gerenciar grupos de computadores<br>Configurar a detecção de ameaças do ponto de extremidade e a correção automatizada<br>Exibir, investigar e responder a alertas
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibe informações de usuário, dispositivo, registro, configuração e aplicativo<br>Não é possível fazer alterações no Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Adicionar administradores, adicionar políticas e configurações, carregar logs e executar ações de governança
  [Centro de Segurança do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Pode exibir políticas de segurança, exibir Estados de segurança, editar políticas de segurança, exibir alertas e recomendações, ignorar alertas e recomendações
  [Integridade do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Exibir a integridade dos serviços do Office 365

* **[Operador de segurança](#security-operator)** : Os usuários com essa função podem gerenciar alertas e ter acesso somente leitura global no recurso relacionado à segurança, incluindo todas as informações na central de segurança Microsoft 365, Azure Active Directory, proteção de identidade, Privileged Identity Management e Office 365 Centro de Conformidade e Segurança. Mais informações sobre as permissões do Office 365 estão disponíveis em [permissões no centro de conformidade e segurança do office 365](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

  Em | Pode fazer
  --- | ---
  [Central de segurança do Microsoft 365](https://protection.office.com) | Todas as permissões da função leitor de segurança<br>Exibir, investigar e responder a alertas de ameaças de segurança
  Centro de proteção de identidade | Todas as permissões da função leitor de segurança<br>Além disso, a capacidade de executar todas as operações da central de proteção de identidade, exceto para redefinição de senhas
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Todas as permissões da função leitor de segurança
  [Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Todas as permissões da função leitor de segurança<br>Exibir, investigar e responder a alertas de segurança
  Windows Defender ATP e EDR | Todas as permissões da função leitor de segurança<br>Exibir, investigar e responder a alertas de segurança
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Todas as permissões da função leitor de segurança
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Todas as permissões da função leitor de segurança
  [Integridade do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Exibir a integridade dos serviços do Office 365
<!--* **[Security Operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management.-->

* **[Leitor de segurança](#security-reader)** : Os usuários com essa função têm acesso somente leitura global no recurso relacionado à segurança, incluindo todas as informações na central de segurança Microsoft 365, Azure Active Directory, proteção de identidade, Privileged Identity Management, bem como a capacidade de ler o Azure active Relatórios de entrada de diretório e logs de auditoria e no Office 365 Centro de Conformidade e Segurança. Mais informações sobre as permissões do Office 365 estão disponíveis em [permissões no centro de conformidade e segurança do office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  Em | Pode fazer
  --- | ---
  [Central de segurança do Microsoft 365](https://protection.office.com) | Exibir políticas relacionadas à segurança em serviços de Microsoft 365<br>Exibir ameaças de segurança e alertas<br>Exibir relatórios
  Centro de proteção de identidade | Ler todas as informações de configurações e relatórios de segurança para recursos de segurança<br><ul><li>Anti-spam<li>Encriptação<li>Prevenção de perda de dados<li>Anti-malware<li>Proteção avançada contra ameaças<li>Anti-phishing<li>Regras de fluxo
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Tem acesso somente leitura a todas as informações na superfície do Azure AD PIM: Políticas e relatórios para atribuições de função do Azure AD, revisões de segurança e no futuro acesso de leitura a dados de política e relatórios para cenários além da atribuição de função do Azure AD.<br>**Não é possível** se inscrever no Azure ad PIM ou fazer qualquer alteração nele. No portal do PIM ou por meio do PowerShell, alguém nessa função pode ativar funções adicionais (por exemplo, administrador global ou administradores de função com privilégios), se o usuário estiver qualificado para eles.
  [Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Ver as políticas de segurança<br>Exibir e investigar ameaças de segurança<br>Exibir relatórios
  Windows Defender ATP e EDR | Exibir e investigar alertas
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibe informações de usuário, dispositivo, registro, configuração e aplicativo. Não é possível fazer alterações no Intune.
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões somente leitura e pode gerenciar alertas
  [Centro de Segurança do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Pode exibir recomendações e alertas, exibir políticas de segurança, exibir Estados de segurança, mas não pode fazer alterações
  [Integridade do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Exibir a integridade dos serviços do Office 365

* **[Administrador de suporte de serviço](#service-support-administrator)** : Os usuários com essa função podem abrir solicitações de suporte com a Microsoft para serviços do Azure e do Office 365 e exibir o painel de serviço e o centro de mensagens no [portal do Azure](https://portal.azure.com) e [Microsoft 365 centro de administração](https://admin.microsoft.com). Mais informações em [sobre as funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Na API Microsoft Graph, no Azure AD API do Graph e no Azure AD PowerShell, essa função é identificada como "administrador de suporte de serviço". É "administrador de serviços" na [portal do Azure](https://portal.azure.com), no [centro de administração de Microsoft 365](https://admin.microsoft.com)e no portal do Intune.

* **[Administrador do SharePoint](#sharepoint-service-administrator)** : Os usuários com essa função têm permissões globais no Microsoft SharePoint Online, quando o serviço está presente, bem como a capacidade de criar e gerenciar todos os grupos do Office 365, gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [sobre as funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Na API Microsoft Graph, no Azure AD API do Graph e no Azure AD PowerShell, essa função é identificada como "administrador de serviços do SharePoint". É o "administrador do SharePoint" no [portal do Azure](https://portal.azure.com).

* **[Skype for Business/administrador do Lync](#lync-service-administrator)** : Os usuários com essa função têm permissões globais no Microsoft Skype for Business, quando o serviço está presente, bem como gerenciar atributos de usuário específicos do Skype no Azure Active Directory. Além disso, essa função concede a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço e acessar as equipes e o centro de administração do Skype for Business. A conta também deve ser licenciada para equipes ou não pode executar cmdlets do PowerShell de equipes. Mais informações [sobre a função de administrador do Skype for Business](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) e as informações de licenciamento de equipes no [Skype for Business e no licenciamento do Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > Na API Microsoft Graph, no Azure AD API do Graph e no Azure AD PowerShell, essa função é identificada como "administrador do serviço do Lync". É "administrador do Skype for Business" na [portal do Azure](https://portal.azure.com/).

* **[Administrador de equipes](#teams-service-administrator)** : Os usuários nessa função podem gerenciar todos os aspectos da carga de trabalho do Microsoft Teams via Microsoft Teams & o centro de administração do Skype for Business e os respectivos módulos do PowerShell. Isso inclui, entre outras áreas, todas as ferramentas de gerenciamento relacionadas a telefonia, mensagens, reuniões e as próprias equipes. Essa função adicionalmente concede a capacidade de criar e gerenciar todos os grupos do Office 365, gerenciar tíquetes de suporte e monitorar a integridade do serviço.
  > [!NOTE]
  > Na API Microsoft Graph, no Azure AD API do Graph e no Azure AD PowerShell, essa função é identificada como "administrador de serviços de equipes". É "administrador de equipes" na [portal do Azure](https://portal.azure.com).

* **[Administrador de comunicações de equipes](#teams-communications-administrator)** : Os usuários nessa função podem gerenciar aspectos da carga de trabalho das equipes da Microsoft relacionadas à telefonia de & de voz. Isso inclui as ferramentas de gerenciamento para atribuição de número de telefone, políticas de voz e de reunião e acesso completo ao conjunto de ferramentas de análise de chamada.

* **[Engenheiro de suporte de comunicações de equipes](#teams-communications-support-engineer)** : Os usuários nessa função podem solucionar problemas de comunicação no Microsoft Teams & Skype for Business usando as ferramentas de solução de problemas do usuário no centro de administração do Microsoft Teams & Skype for Business. Os usuários nessa função podem exibir informações completas de registro de chamada para todos os participantes envolvidos. Essa função não tem acesso para exibir, criar ou gerenciar tíquetes de suporte.

* **[Especialista em suporte a comunicações de equipes](#teams-communications-support-specialist)** : Os usuários nessa função podem solucionar problemas de comunicação no Microsoft Teams & Skype for Business usando as ferramentas de solução de problemas do usuário no centro de administração do Microsoft Teams & Skype for Business. Os usuários nessa função só podem exibir detalhes do usuário na chamada para o usuário específico que eles pesquisaram. Essa função não tem acesso para exibir, criar ou gerenciar tíquetes de suporte.

* **[Administrador do usuário](#user-administrator)** : Os usuários com essa função podem criar usuários e gerenciar todos os aspectos de usuários com algumas restrições (veja abaixo) e podem atualizar as políticas de expiração de senha. Além disso, os usuários com essa função podem criar e gerenciar todos os grupos. Essa função também inclui a capacidade de criar e gerenciar exibições de usuário, gerenciar tíquetes de suporte e monitorar a integridade do serviço.

  | | |
  | --- | --- |
  |Permissões gerais|<p>Criar usuários e grupos</p><p>Criar e gerenciar exibições de usuário</p><p>Gerenciar tíquetes de suporte do Office<p>Atualizar políticas de expiração de senha|
  |<p>Em todos os usuários, incluindo todos os administradores</p>|<p>Gerenciar licenças</p><p>Gerenciar todas as propriedades de usuário, exceto o nome principal do usuário</p>
  |Somente em usuários que não são administradores ou em qualquer uma das seguintes funções de administrador limitadas:<ul><li>Leitores de diretório<li>Convite do convidado<li>Administrador de assistência técnica<li>Leitor do centro de mensagens<li>Leitor de relatórios<li>Administrador do usuário|<p>Excluir e restaurar</p><p>Desabilitar e habilitar</p><p>Invalidar tokens de atualização</p><p>Gerenciar todas as propriedades de usuário, incluindo o nome principal do usuário</p><p>Repor palavra-passe</p><p>Atualizar chaves de dispositivo (FIDO)</p>
  
  <b>Importante</b>: Usuários com essa função podem alterar senhas para pessoas que podem ter acesso a informações confidenciais ou privadas ou configuração crítica dentro e fora do Azure Active Directory. Alterar a senha de um usuário pode significar a capacidade de assumir a identidade e as permissões desse usuário. Por exemplo:
  * Registro de aplicativo e proprietários de aplicativos empresariais, que podem gerenciar credenciais de aplicativos que eles possuem. Esses aplicativos podem ter permissões privilegiadas no Azure AD e em outros lugares não concedidos a administradores de usuários. Por meio desse caminho, um administrador de usuário pode assumir a identidade de um proprietário de aplicativo e, em seguida, assumir ainda mais a identidade de um aplicativo com privilégios atualizando as credenciais para o aplicativo.
  * Proprietários de assinatura do Azure, que podem ter acesso a informações confidenciais ou privadas ou configuração crítica no Azure.
  * Grupo de segurança e proprietários do grupo do Office 365, que podem gerenciar a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou configuração crítica no Azure AD e em outro lugar.
  * Administradores em outros serviços fora do Azure AD, como o Exchange Online, o centro de conformidade e segurança do Office e os sistemas de recursos humanos.
  * Não administradores, como executivos, consultor jurídico e funcionários de recursos humanos que podem ter acesso a informações confidenciais ou privadas.

## <a name="role-permissions"></a>Permissões de função
As tabelas a seguir descrevem as permissões específicas em Azure Active Directory dadas a cada função. Algumas funções podem ter permissões adicionais nos serviços da Microsoft fora do Azure Active Directory.

### <a name="application-administrator"></a>Administrador de aplicativos
Pode criar e gerenciar todos os aspectos de registros de aplicativo e aplicativos empresariais.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Atualizar a propriedade Applications. Audience no Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Atualize a propriedade Applications. Authentication no Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Atualize as propriedades básicas em aplicativos no Azure Active Directory. |
| microsoft.aad.directory/applications/create | Crie aplicativos no Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Atualize a propriedade Applications. Credentials no Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Excluir aplicativos no Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Atualize a propriedade Applications. Owners no Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Atualize a propriedade Applications. Permissions no Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Atualizar a propriedade Applications. Policies no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Criar appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Leia appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Atualizar appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Excluir appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Ler a propriedade Policies. applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Atualizar a propriedade Policies. applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Crie políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Excluir políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Ler a propriedade Policies. applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Atualizar a propriedade Policies. applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Ler a propriedade Policies. applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Atualizar a propriedade servicePrincipalName. appRoleAssignedTo no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Atualizar a propriedade servicePrincipalName. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Atualizar a propriedade servicePrincipalName. Audience no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Atualize a propriedade servicePrincipalName. de autenticação no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Atualize as propriedades básicas em servicePrincipalName no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Criar entidades de segurança no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Atualize a propriedade servicePrincipalName. Credentials no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Exclua as entidades de segurança no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Atualize a propriedade servicePrincipalName. Owners no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Atualize a propriedade servicePrincipalName. Permissions no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Atualizar a propriedade servicePrincipalName. Policies no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em signInReports em Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="application-developer"></a>Desenvolvedor de aplicativos
Pode criar registros de aplicativo independentemente da configuração ' os usuários podem registrar aplicativos '.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Crie aplicativos no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de objetos criados do 250 do criador. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Criar appRoleAssignments em Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de objetos criados do 250 do criador. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Criar oAuth2PermissionGrants em Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de objetos criados do 250 do criador. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Criar entidades de segurança no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de objetos criados do 250 do criador. |

### <a name="authentication-administrator"></a>Administrador de autenticação
Permissão para exibir, definir e redefinir informações do método de autenticação para qualquer usuário não administrador.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização do usuário no Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Atualize as propriedades de autenticação forte, como informações de credencial MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |
| Microsoft. AAD. Directory/Users/password/Update | Atualize as senhas de todos os usuários na organização do Office 365. Consulte a documentação online para obter mais detalhes. |

### <a name="azure-information-protection-administrator"></a>Administrador da proteção de informações do Azure
Pode gerenciar todos os aspectos do serviço de proteção de informações do Azure.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="b2c-user-flow-administrator"></a>Administrador de Fluxos de Utilizadores B2C
Crie e gerencie todos os aspectos de fluxos de usuário.

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft. AAD. B2C/userflows/tarefas | Ler e configurar fluxos de usuário no Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator"></a>Administrador de Atributos de Fluxo de Utilizadores B2C
Crie e gerencie o esquema de atributo disponível para todos os fluxos de usuário.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Ler e configurar atributos de usuário no Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator"></a>Administrador de Conjuntos de Chaves B2C IEF
Gerencie segredos para Federação e criptografia na estrutura de experiência de identidade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Ler e configurar conjuntos de chaves em Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator"></a>Administrador de Políticas B2C IEF
Crie e gerencie políticas de estrutura confiável na estrutura de experiência de identidade.

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft. AAD. B2C/trustFramework/Policies/TaskId | Ler e configurar políticas personalizadas no Azure Active Directory B2C. |

### <a name="billing-administrator"></a>Administrador de cobrança
Pode executar tarefas comuns relacionadas à cobrança, como a atualização de informações de pagamento.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft. AAD. Directory/Organization/Basic/Update | Atualize as propriedades básicas na organização no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| Microsoft. Commerce. cobrança/entidades/tarefas | Gerencie todos os aspectos da cobrança do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |


### <a name="cloud-application-administrator"></a>Administrador de aplicativos de nuvem
Pode criar e gerenciar todos os aspectos de registros de aplicativo e aplicativos empresariais, exceto o proxy de aplicativo.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Atualizar a propriedade Applications. Audience no Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Atualize a propriedade Applications. Authentication no Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Atualize as propriedades básicas em aplicativos no Azure Active Directory. |
| microsoft.aad.directory/applications/create | Crie aplicativos no Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Atualize a propriedade Applications. Credentials no Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Excluir aplicativos no Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Atualize a propriedade Applications. Owners no Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Atualize a propriedade Applications. Permissions no Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Atualizar a propriedade Applications. Policies no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Criar appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Atualizar appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Excluir appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Crie políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Ler a propriedade Policies. applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Atualizar a propriedade Policies. applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Excluir políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Ler a propriedade Policies. applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Atualizar a propriedade Policies. applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Ler a propriedade Policies. applicationConfiguration no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Atualizar a propriedade servicePrincipalName. appRoleAssignedTo no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Atualizar a propriedade servicePrincipalName. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Atualizar a propriedade servicePrincipalName. Audience no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Atualize a propriedade servicePrincipalName. de autenticação no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Atualize as propriedades básicas em servicePrincipalName no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Criar entidades de segurança no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Atualize a propriedade servicePrincipalName. Credentials no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Exclua as entidades de segurança no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Atualize a propriedade servicePrincipalName. Owners no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Atualize a propriedade servicePrincipalName. Permissions no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Atualizar a propriedade servicePrincipalName. Policies no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em signInReports em Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="cloud-device-administrator"></a>Administrador de dispositivo de nuvem
Acesso completo para gerenciar dispositivos no Azure AD.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs em Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade Devices. bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Excluir dispositivos no Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/Disable | Desabilitar dispositivos no Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/Enable | Habilitar dispositivos no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em signInReports em Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |

### <a name="company-administrator"></a>Administrador de Empresa
Pode gerenciar todos os aspectos do Azure AD e dos serviços da Microsoft que usam identidades do Azure AD.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Crie e exclua todos os recursos e leia e atualize as propriedades padrão em Microsoft. AAD. cloudAppSecurity. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Criar e excluir administrativeUnits, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/Applications/myproperties/mytasks | Criar e excluir aplicativos, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/appRoleAssignments/myproperties/mytasks | Criar e excluir appRoleAssignments, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs em Azure Active Directory. |
| Microsoft. AAD. Directory/Contacts/myproperties/mytasks | Criar e excluir contatos, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/Contracts/myproperties/mytasks | Crie e exclua contratos, e leia e atualize todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/Devices/myproperties/mytasks | Criar e excluir dispositivos, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/directoryRoles/myproperties/mytasks | Criar e excluir directoryRoles, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/directoryRoleTemplates/myproperties/mytasks | Criar e excluir directoryRoleTemplates, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/Domains/myproperties/mytasks | Criar e excluir domínios, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/groups/myproperties/mytasks | Criar e excluir grupos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Criar e excluir groupSettings, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Criar e excluir groupSettingTemplates, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/loginTenantBranding/myproperties/mytasks | Criar e excluir loginTenantBranding, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/oAuth2PermissionGrants/myproperties/mytasks | Criar e excluir oAuth2PermissionGrants, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/Organization/myproperties/mytasks | Crie e exclua a organização, e leia e atualize todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/Policies/myproperties/mytasks | Criar e excluir políticas, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/roleAssignments/myproperties/mytasks | Criar e excluir roleAssignments, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Criar e excluir roleDefinitions, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/scopedRoleMemberships/myproperties/mytasks | Criar e excluir scopedRoleMemberships, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Pode executar a ação do serviço ActivateService no Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Pode executar a ação do serviço Disabledirectoryfeature no Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Pode executar a ação do serviço Enabledirectoryfeature no Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Pode executar a ação do serviço Getavailableextentionproperties no Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Criar e excluir entidades de segurança e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em signInReports em Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Criar e excluir subscribedSkus, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. Directory/Users/myproperties/mytasks | Criar e excluir usuários, e ler e atualizar todas as propriedades no Azure Active Directory. |
| Microsoft. AAD. directorySync/myentities/tarefas | Executar todas as ações no Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Crie e exclua todos os recursos e leia e atualize as propriedades padrão em Microsoft. AAD. identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Leia todos os recursos em Microsoft. AAD. privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Leia todos os recursos em Microsoft. Azure. advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| Microsoft. Commerce. cobrança/entidades/tarefas | Gerencie todos os aspectos da cobrança do Office 365. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspectos do Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gerenciar todos os aspectos do Office 365 Compliance Manager |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerencie todos os aspectos da análise de desktops. |
| microsoft.office365.exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Gerenciar todos os aspectos do Office 365 Sistema de Proteção de Dados do Cliente |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em Microsoft. office365. messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leia securityMessages em Microsoft. office365. messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Gerencie todos os aspectos do centro de proteção do Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Crie e exclua todos os recursos e leia e atualize as propriedades padrão no Microsoft. office365. securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Crie e exclua todos os recursos, leia e atualize as propriedades padrão no Microsoft. office365. SharePoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de uso do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerencie todos os aspectos do Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerencie todos os aspectos de Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Leia todos os recursos em Microsoft. Windows. defenderAdvancedThreatProtection. |

### <a name="compliance-administrator"></a>Administrador de conformidade
Pode ler e gerenciar a configuração e os relatórios de conformidade no Azure AD e no Office 365.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gerenciar todos os aspectos do Office 365 Compliance Manager |
| microsoft.office365.exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Crie e exclua todos os recursos, leia e atualize as propriedades padrão no Microsoft. office365. SharePoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="compliance-data-administrator"></a>Administrador de dados de conformidade
Cria e gerencia o conteúdo de conformidade.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Ler e configurar Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gerenciar todos os aspectos do Office 365 Compliance Manager |
| microsoft.office365.exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Crie e exclua todos os recursos, leia e atualize as propriedades padrão no Microsoft. office365. SharePoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="conditional-access-administrator"></a>Administrador de Acesso Condicional
Pode gerenciar recursos de acesso condicional.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Ler a propriedade Policies. conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Atualizar a propriedade Policies. conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Crie políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Excluir políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Ler a propriedade Policies. conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Atualizar a propriedade Policies. conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Ler a propriedade Policies. conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Atualizar a propriedade Policies. conditionalAccess no Azure Active Directory. |

### <a name="crm-service-administrator"></a>Administrador do serviço CRM
Pode gerenciar todos os aspectos do produto Dynamics 365.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerencie todos os aspectos do Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="customer-lockbox-access-approver"></a>Aprovador de acesso de LockBox do cliente
Pode aprovar solicitações de suporte da Microsoft para acessar dados organizacionais do cliente.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.lockbox/allEntities/allTasks | Gerenciar todos os aspectos do Office 365 Sistema de Proteção de Dados do Cliente |

### <a name="desktop-analytics-administrator"></a>Administrador do desktop Analytics
O pode gerenciar a análise de desktops e a personalização do Office & serviços de política. Para análise de desktops, isso inclui a capacidade de exibir o inventário de ativos, criar planos de implantação, exibir o status de integridade e implantação. Para a personalização do Office & serviço de política, essa função permite que os usuários gerenciem as políticas do Office.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerencie todos os aspectos da análise de desktops. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="device-administrators"></a>Administradores de dispositivo
Os usuários atribuídos a essa função são adicionados ao grupo local de administradores em dispositivos ingressados no Azure AD.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Leia as propriedades básicas em groupSettings em Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Leia as propriedades básicas em groupSettingTemplates em Azure Active Directory. |

### <a name="directory-readers"></a>Leitores de diretório
Pode ler informações básicas do diretório. Para conceder acesso a aplicativos, não destinado a usuários.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Leia as propriedades básicas em administrativeUnits em Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Leia a propriedade administrativeUnits. Members em Azure Active Directory. |
| microsoft.aad.directory/applications/basic/read | Ler propriedades básicas em aplicativos no Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Ler a propriedade Applications. Owners no Azure Active Directory. |
| microsoft.aad.directory/applications/policies/read | Ler a propriedade Applications. Policies no Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/read | Ler as propriedades básicas em contatos em Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Ler a propriedade Contacts. memberOf no Azure Active Directory. |
| microsoft.aad.directory/contracts/basic/read | Ler propriedades básicas sobre contratos no Azure Active Directory. |
| microsoft.aad.directory/devices/basic/read | Ler as propriedades básicas em dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Ler a propriedade Devices. memberOf no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Ler a propriedade Devices. registeredOwners no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Ler a propriedade Devices. registeredUsers no Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Leia as propriedades básicas em directoryRoles em Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Leia a propriedade directoryRoles. eligibleMembers em Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Leia a propriedade directoryRoles. Members em Azure Active Directory. |
| microsoft.aad.directory/domains/basic/read | Ler as propriedades básicas em domínios em Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Ler a propriedade Groups. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/groups/basic/read | Ler as propriedades básicas em grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Ler a propriedade Groups. memberOf no Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Ler a propriedade Groups. Members no Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Ler a propriedade Groups. Owners no Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Ler a propriedade Groups. Settings no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/read | Leia as propriedades básicas em groupSettings em Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Leia as propriedades básicas em groupSettingTemplates em Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Leia as propriedades básicas em oAuth2PermissionGrants em Azure Active Directory. |
| microsoft.aad.directory/organization/basic/read | Ler as propriedades básicas na organização no Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Leia a propriedade Organization. trustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/basic/read | Leia as propriedades básicas em roleAssignments em Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Leia as propriedades básicas em roleDefinitions em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Leia a propriedade servicePrincipalName. appRoleAssignedTo em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Leia a propriedade servicePrincipalName. appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Ler as propriedades básicas em servicePrincipalName no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Ler a propriedade servicePrincipalName. memberOf no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Leia a propriedade servicePrincipalName. oAuth2PermissionGrants em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Leia a propriedade servicePrincipalName. ownedObjects em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Leia a propriedade servicePrincipalName. Owners em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Ler a propriedade servicePrincipalName. Policies no Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Leia as propriedades básicas em subscribedSkus em Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Ler a propriedade users. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Ler propriedades básicas em usuários em Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Ler a propriedade users. directReports no Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Ler a propriedade users. Manager no Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Ler a propriedade users. memberOf no Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Ler a propriedade users. oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Ler a propriedade users. ownedDevices no Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Ler a propriedade users. ownedObjects no Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Ler a propriedade users. registeredDevices no Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Contas de sincronização de diretório
Usado somente pelo serviço Azure AD Connect.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Atualize a propriedade Organization. DirSync no Azure Active Directory. |
| Microsoft. AAD. Directory/Policies/Create | Crie políticas no Azure Active Directory. |
| Microsoft. AAD. Directory/Policies/Delete | Excluir políticas no Azure Active Directory. |
| Microsoft. AAD. Directory/Policies/Basic/Read | Ler propriedades básicas em políticas no Azure Active Directory. |
| Microsoft. AAD. Directory/Policies/Basic/Update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/owners/read | Ler a propriedade Policies. Owners no Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Atualize a propriedade Policies. Owners no Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Ler a propriedade Policies. policiesAppliedTo no Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Atualizar a propriedade Policies. tenantDefault no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Leia a propriedade servicePrincipalName. appRoleAssignedTo em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Atualizar a propriedade servicePrincipalName. appRoleAssignedTo no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Leia a propriedade servicePrincipalName. appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Atualizar a propriedade servicePrincipalName. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Atualizar a propriedade servicePrincipalName. Audience no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Atualize a propriedade servicePrincipalName. de autenticação no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Ler as propriedades básicas em servicePrincipalName no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Atualize as propriedades básicas em servicePrincipalName no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Criar entidades de segurança no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Atualize a propriedade servicePrincipalName. Credentials no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Ler a propriedade servicePrincipalName. memberOf no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Leia a propriedade servicePrincipalName. oAuth2PermissionGrants em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Leia a propriedade servicePrincipalName. Owners em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Atualize a propriedade servicePrincipalName. Owners no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Leia a propriedade servicePrincipalName. ownedObjects em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Atualize a propriedade servicePrincipalName. Permissions no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Ler a propriedade servicePrincipalName. Policies no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Atualizar a propriedade servicePrincipalName. Policies no Azure Active Directory. |
| Microsoft. AAD. directorySync/myentities/tarefas | Executar todas as ações no Azure AD Connect. |

### <a name="directory-writers"></a>Gravadores de diretório
Pode ler & gravar informações básicas do diretório. Para conceder acesso a aplicativos, não destinado a usuários.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groups/create | Criar grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Criar grupos no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de objetos criados do 250 do criador. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Atualizar a propriedade Groups. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Atualize as propriedades básicas em grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Atualize a propriedade Groups. Members em Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Atualize a propriedade Groups. Owners no Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Atualizar a propriedade Groups. Settings no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Atualize as propriedades básicas em groupSettings em Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Criar groupSettings em Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Excluir groupSettings em Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade users. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Atualizar propriedades básicas em usuários no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização do usuário no Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Atualize a propriedade users. Manager no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualizar a propriedade users. userPrincipalName no Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Administrador de serviços do Exchange
Pode gerenciar todos os aspectos do produto Exchange.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Atualizar grupos. Propriedade unificada no Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Atualize as propriedades básicas dos grupos do Office 365. |
| microsoft.aad.directory/groups/unified/create | Crie grupos do Office 365. |
| microsoft.aad.directory/groups/unified/delete | Exclua grupos do Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Atualize a associação de grupos do Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Atualize a propriedade dos grupos do Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="external-identity-provider-administrator"></a>Administrador do provedor de identidade externo
Configure os provedores de identidade para uso na Federação direta.

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft. AAD. B2C/identityProviders/minhas tarefas | Ler e configurar provedores de identidade no Azure Active Directory B2C. |

### <a name="guest-inviter"></a>Convite do convidado
Pode convidar usuários convidados independentemente da configuração ' Membros podem convidar convidados '.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Ler a propriedade users. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Ler propriedades básicas em usuários em Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Ler a propriedade users. directReports no Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Convide usuários convidados no Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Ler a propriedade users. Manager no Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Ler a propriedade users. memberOf no Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Ler a propriedade users. oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Ler a propriedade users. ownedDevices no Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Ler a propriedade users. ownedObjects no Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Ler a propriedade users. registeredDevices no Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Administrador de assistência técnica
Pode redefinir senhas para não administradores e administradores de assistência técnica.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade Devices. bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização do usuário no Azure Active Directory. |
| Microsoft. AAD. Directory/Users/password/Update | Atualizar senhas para todos os usuários no Azure Active Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="intune-service-administrator"></a>Administrador de Serviços do Intune
Pode gerenciar todos os aspectos do produto Intune.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Crie contatos no Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Excluir contatos no Azure Active Directory. |
| microsoft.aad.directory/devices/basic/update | Atualize as propriedades básicas em dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade Devices. bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.aad.directory/devices/create | Crie dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Excluir dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Atualizar a propriedade Devices. registeredOwners no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Atualizar a propriedade Devices. registeredUsers no Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Atualizar a propriedade Groups. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Atualize as propriedades básicas em grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/create | Criar grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Criar grupos no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de objetos criados do 250 do criador. |
| microsoft.aad.directory/groups/delete | Excluir grupos em Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Ler a propriedade Groups. hiddenMembers no Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Atualize a propriedade Groups. Members em Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Atualize a propriedade Groups. Owners no Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaurar grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Atualizar a propriedade Groups. Settings no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade users. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Atualizar propriedades básicas em usuários no Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Atualize a propriedade users. Manager no Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspectos do Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |

### <a name="kaizala-administrator"></a>Administrador do Kaizala
Pode gerenciar as configurações do Microsoft Kaizala.  

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >  
  
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia o centro de administração do Office 365. |

### <a name="license-administrator"></a>Administrador de licenças
Pode gerenciar licenças de produtos em usuários e grupos.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Atualize a propriedade users. usageLocation no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |

### <a name="lync-service-administrator"></a>Administrador de serviços do Lync
Pode gerenciar todos os aspectos do produto Skype for Business.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="message-center-privacy-reader"></a>Leitor de privacidade do centro de mensagens
Pode ler postagens do centro de mensagens, mensagens de privacidade de dados, grupos, domínios e assinaturas.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em Microsoft. office365. messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leia securityMessages em Microsoft. office365. messageCenter. |

### <a name="message-center-reader"></a>Leitor do centro de mensagens
O pode ler mensagens e atualizações para sua organização somente no centro de mensagens do Office 365. 

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em Microsoft. office365. messageCenter. |

### <a name="partner-tier1-support"></a>Parceiro de Suporte de Escalão 1
Não use-não se destina ao uso geral.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Crie contatos no Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Excluir contatos no Azure Active Directory. |
| microsoft.aad.directory/groups/create | Criar grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Criar grupos no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de objetos criados do 250 do criador. |
| microsoft.aad.directory/groups/members/update | Atualize a propriedade Groups. Members em Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Atualize a propriedade Groups. Owners no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade users. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Atualizar propriedades básicas em usuários no Azure Active Directory. |
| microsoft.aad.directory/users/delete | Excluir usuários no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização do usuário no Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Atualize a propriedade users. Manager no Azure Active Directory. |
| Microsoft. AAD. Directory/Users/password/Update | Atualizar senhas para todos os usuários no Azure Active Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.aad.directory/users/restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualizar a propriedade users. userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="partner-tier2-support"></a>Parceiro de Suporte de Escalão 2
Não use-não se destina ao uso geral.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Crie contatos no Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Excluir contatos no Azure Active Directory. |
| Microsoft. AAD. Directory/Domains/TaskId | Crie e exclua domínios, e leia e atualize as propriedades padrão no Azure Active Directory. |
| microsoft.aad.directory/groups/create | Criar grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Excluir grupos em Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Atualize a propriedade Groups. Members em Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaurar grupos no Azure Active Directory. |
| Microsoft. AAD. Directory/Organization/Basic/Update | Atualize as propriedades básicas na organização no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade users. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Atualizar propriedades básicas em usuários no Azure Active Directory. |
| microsoft.aad.directory/users/delete | Excluir usuários no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização do usuário no Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Atualize a propriedade users. Manager no Azure Active Directory. |
| Microsoft. AAD. Directory/Users/password/Update | Atualizar senhas para todos os usuários no Azure Active Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.aad.directory/users/restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualizar a propriedade users. userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="power-bi-service-administrator"></a>Administrador do serviço Power BI
Pode gerenciar todos os aspectos do produto Power BI.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerencie todos os aspectos de Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="privileged-authentication-administrator"></a>Administrador de autenticação privilegiada
Permissão para exibir, definir e redefinir informações do método de autenticação para qualquer usuário (administrador ou não administrador).

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização do usuário no Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Atualize as propriedades de autenticação forte, como informações de credencial MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |
| Microsoft. AAD. Directory/Users/password/Update | Atualize as senhas de todos os usuários na organização do Office 365. Consulte a documentação online para obter mais detalhes. |
### <a name="privileged-role-administrator"></a>Administrador de função com privilégios
Pode gerenciar atribuições de função no Azure AD e todos os aspectos de Privileged Identity Management.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Crie e exclua todos os recursos e leia e atualize as propriedades padrão em Microsoft. AAD. privilegedIdentityManagement. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/allTasks | Ler e configurar a propriedade servicePrincipalName. appRoleAssignedTo em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Ler e configurar a propriedade servicePrincipalName. oAuth2PermissionGrants em Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Criar e gerenciar unidades administrativas (incluindo membros) |
| Microsoft. AAD. Directory/roleAssignments/myproperties/mytasks | Criar e gerenciar atribuições de função. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Criar e gerenciar definições de função. |

### <a name="reports-reader"></a>Leitor de relatórios
Pode ler relatórios de entrada e auditoria.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs em Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em signInReports em Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de uso do Office 365. |

### <a name="search-administrator"></a>Administrador de pesquisa
Pode criar e gerenciar todos os aspectos das configurações do Microsoft Search.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em Microsoft. office365. messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Criar e excluir todos os recursos, e ler e atualizar todas as propriedades no Microsoft. office365. Search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de uso do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |

### <a name="search-editor"></a>Editor de pesquisa
Pode criar e gerenciar o conteúdo editorial, como indicadores, p e as, Locations, floorplan.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em Microsoft. office365. messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Criar e excluir conteúdo, e ler e atualizar todas as propriedades no Microsoft. office365. Search. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de uso do Office 365. |

### <a name="security-administrator"></a>Administrador de Segurança
Pode ler informações e relatórios de segurança e gerenciar a configuração no Azure AD e no Office 365.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Atualizar a propriedade Applications. Policies no Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs em Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade Devices. bitLockerRecoveryKeys no Azure Active Directory. |
| Microsoft. AAD. Directory/Policies/Basic/Update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| Microsoft. AAD. Directory/Policies/Create | Crie políticas no Azure Active Directory. |
| Microsoft. AAD. Directory/Policies/Delete | Excluir políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Atualize a propriedade Policies. Owners no Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Atualizar a propriedade Policies. tenantDefault no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Atualizar a propriedade servicePrincipalName. Policies no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em signInReports em Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Leia todos os recursos em Microsoft. AAD. identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Atualize todos os recursos em Microsoft. AAD. identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Leia todos os recursos em Microsoft. AAD. privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.protectionCenter/allEntities/read | Leia todos os aspectos do centro de proteção do Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Atualize todos os recursos no Microsoft. office365. protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |

### <a name="security-operator"></a>Operador de segurança
Cria e gerencia eventos de segurança.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Ler e configurar Microsoft Cloud App Security. |
| microsoft.aad.identityProtection/allEntities/read | Leia todos os recursos em Microsoft. AAD. identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Leia todos os recursos em Microsoft. AAD. privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Leia e configure a proteção avançada contra ameaças do Azure AD. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspectos do Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Ler e configurar Centro de Conformidade e Segurança. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de uso do Office 365. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Leia e configure a proteção avançada contra ameaças do Windows Defender. |

### <a name="security-reader"></a>Leitor de segurança
Pode ler informações de segurança e relatórios no Azure AD e no Office 365.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs em Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade Devices. bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em signInReports em Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Leia todos os recursos em Microsoft. AAD. identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Leia todos os recursos em Microsoft. AAD. privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.protectionCenter/allEntities/read | Leia todos os aspectos do centro de proteção do Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |

### <a name="service-support-administrator"></a>Administrador de suporte de serviço
Pode ler informações de integridade do serviço e gerenciar tíquetes de suporte.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="sharepoint-service-administrator"></a>Administrador de serviços do SharePoint
Pode gerenciar todos os aspectos do serviço do SharePoint.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Atualizar grupos. Propriedade unificada no Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Atualize as propriedades básicas dos grupos do Office 365. |
| microsoft.aad.directory/groups/unified/create | Crie grupos do Office 365. |
| microsoft.aad.directory/groups/unified/delete | Exclua grupos do Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Atualize a associação de grupos do Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Atualize a propriedade dos grupos do Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Crie e exclua todos os recursos, leia e atualize as propriedades padrão no Microsoft. office365. SharePoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

### <a name="teams-communications-administrator"></a>Administrador de comunicações de equipes
Pode gerenciar recursos de chamada e de reuniões no serviço Microsoft Teams.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de uso do Office 365. |

### <a name="teams-communications-support-engineer"></a>Engenheiro de suporte de comunicações de equipes
Pode solucionar problemas de comunicação dentro das equipes usando ferramentas avançadas.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |

### <a name="teams-communications-support-specialist"></a>Especialista de suporte de comunicações de equipes
Pode solucionar problemas de comunicação dentro das equipes usando ferramentas básicas.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |

### <a name="teams-service-administrator"></a>Administrador de serviços de equipes
Pode gerenciar o serviço Microsoft Teams.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Ler a propriedade Groups. hiddenMembers no Azure Active Directory. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Atualizar grupos. Propriedade unificada no Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Atualize as propriedades básicas dos grupos do Office 365. |
| microsoft.aad.directory/groups/unified/create | Crie grupos do Office 365. |
| microsoft.aad.directory/groups/unified/delete | Exclua grupos do Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Atualize a associação de grupos do Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Atualize a propriedade dos grupos do Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de uso do Office 365. |

### <a name="user-administrator"></a>Administrador do usuário
Pode gerenciar todos os aspectos de usuários e grupos, incluindo a redefinição de senhas para administradores limitados.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Criar appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Excluir appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Atualizar appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Crie contatos no Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Excluir contatos no Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Atualizar a propriedade Groups. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Atualize as propriedades básicas em grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/create | Criar grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Criar grupos no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de objetos criados do 250 do criador. |
| microsoft.aad.directory/groups/delete | Excluir grupos em Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Ler a propriedade Groups. hiddenMembers no Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Atualize a propriedade Groups. Members em Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Atualize a propriedade Groups. Owners no Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaurar grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Atualizar a propriedade Groups. Settings no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade users. appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Atualizar propriedades básicas em usuários no Azure Active Directory. |
| Microsoft. AAD. Directory/Users/Create | Criar usuários no Azure Active Directory. |
| microsoft.aad.directory/users/delete | Excluir usuários no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização do usuário no Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Atualize a propriedade users. Manager no Azure Active Directory. |
| Microsoft. AAD. Directory/Users/password/Update | Atualizar senhas para todos os usuários no Azure Active Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.aad.directory/users/restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualizar a propriedade users. userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia as propriedades básicas em todos os recursos no Microsoft. office365. webportal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a integridade do serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie tíquetes de suporte do Office 365. |

## <a name="role-template-ids"></a>IDs de modelo de função

As IDs de modelo de função são usadas principalmente por usuários API do Graph ou PowerShell.

DisplayName de grafo | portal do Azure nome de exibição | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Administrador de aplicativos | Administrador de aplicativos | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Desenvolvedor de aplicativos | Desenvolvedor de aplicativos | CF1C38E5-3621-4004-A7CB-879624DCED7C
Administrador de autenticação | Administrador de autenticação | c4e39bd9-1100-46d3-8c65-fb160da0071f
Administrador da proteção de informações do Azure | Administrador da proteção de informações do Azure | 7495fdc4-34c4-4d15-a289-98788ce399fd
Administrador de fluxo de usuário B2C | Administrador de fluxo de usuário B2C | 6e591065-9bad-43ed-90f3-e9424366d2f0
Administrador de Atributos de Fluxo de Utilizadores B2C | Administrador de Atributos de Fluxo de Utilizadores B2C | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Administrador de Conjuntos de Chaves B2C IEF | Administrador de Conjuntos de Chaves B2C IEF | aaf43236-0c0d-4d5f-883a-6955382ac081
Administrador de Políticas B2C IEF | Administrador de Políticas B2C IEF | 3edaf663-341e-4475-9f94-5c398ef6c070
Administrador de cobrança | Administrador de faturação | b0f54661-2d74-4c50-afa3-1ec803f12efe
Administrador de aplicativos de nuvem | Administrador de aplicativos de nuvem | 158c047a-c907-4556-b7ef-446551a6b5f7
Administrador de dispositivo de nuvem | Administrador de dispositivo de nuvem | 7698a772-787b-4ac8-901f-60d6b08affd2
Administrador de Empresa | Administrador global | 62e90394-69f5-4237-9190-012177145e10
Administrador de conformidade | Administrador de conformidade | 17315797-102d-40b4-93e0-432062caca18
Administrador de dados de conformidade | Administrador de dados de conformidade | e6d1a23a-da11-4be4-9570-befc86d067a7
Administrador de Acesso Condicional | Administrador de acesso condicional | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Administrador do serviço CRM | Administrador do Dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Aprovador de acesso de LockBox do cliente | Aprovador de acesso Sistema de Proteção de Dados do Cliente | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Administrador do desktop Analytics | Administrador do desktop Analytics | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Administradores de dispositivo | Administradores de dispositivo | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Ingresso no dispositivo | Ingresso no dispositivo | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Gerenciadores de dispositivos | Gerenciadores de dispositivos | 2b499bcd-da44-4968-8aec-78e1674fa64d
Usuários do dispositivo | Usuários do dispositivo | d405c6df-0af8-4e3b-95e4-4d06e542189e
Leitores de diretório | Leitores de diretório | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Contas de sincronização de diretório | Contas de sincronização de diretório | d29b2b05-8046-44ba-8758-1e26182fcf32
Gravadores de diretório | Gravadores de diretório | 9360feb5-f418-4baa-8175-e2a00bac4301
Administrador de serviços do Exchange | Administrador do Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administrador do provedor de identidade externo | Administrador do provedor de identidade externo | be2f45a1-457d-42af-a067-6ec1fa63bc45
Convite do convidado | Convite do convidado | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Administrador de assistência técnica | Administrador de senha | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administrador de Serviços do Intune | Administrador do Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Administrador do Kaizala | Administrador do Kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Administrador de licenças | Administrador de licenças | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Administrador de serviços do Lync | Administrador do Skype for Business | 75941009-915a-4869-abe7-691bff18279e
Leitor de privacidade do centro de mensagens | Leitor de privacidade do centro de mensagens | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Leitor do centro de mensagens | Leitor do centro de mensagens | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Parceiro de Suporte de Escalão 1 | Suporte do nível 1 para parceiros | 4ba39ca4-527c-499a-b93d-d9b492c50246
Parceiro de Suporte de Escalão 2 | Suporte do tier2 para parceiros | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Administrador do serviço Power BI | Administrador de Power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Administrador de autenticação privilegiada | Administrador de autenticação privilegiada | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administrador de função com privilégios | Administrador de função com privilégios | e8611ab8-c189-46e8-94e1-60213ab1f814
Leitor de relatórios | Leitor de relatórios | 4a5d8f65-41da-4de4-8968-e035b65339cf
Administrador de pesquisa | Administrador de pesquisa | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Editor de pesquisa | Editor de pesquisa | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Administrador de Segurança | Administrador de segurança | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operador de segurança | Operador de segurança | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Leitor de segurança | Leitor de segurança | 5d6b6bb7-de71-4623-b4af-96380a352509
Administrador de suporte de serviço | Administrador de serviço | f023fd81-a637-4b56-95fd-791ac0226033
Administrador de serviços do SharePoint | Administrador do SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Administrador de comunicações de equipes | Administrador de comunicações de equipes | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Engenheiro de suporte de comunicações de equipes | Engenheiro de suporte de comunicações de equipes | f70938a0-fc10-4177-9e90-2178f8765737
Especialista de suporte de comunicações de equipes | Especialista de suporte de comunicações de equipes | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Administrador de serviços de equipes | Administrador de serviços de equipes | 69091246-20e8-4a56-aa4d-066075b2a7a8
Utilizador | Utilizador | a0b1b346-4d3e-4e8b-98f8-753987be4970
Administrador de Conta de Utilizador | Administrador do usuário | fe930be7-5e62-47db-91af-98c3a49a38b1
Ingresso no dispositivo do local de trabalho | Ingresso no dispositivo do local de trabalho | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>Funções preteridas

As funções a seguir não devem ser usadas. Eles foram preteridos e serão removidos do Azure AD no futuro.

* Administrador de licenças ad hoc
* Ingresso no dispositivo
* Gerenciadores de dispositivos
* Usuários do dispositivo
* Criador de usuário verificado por email
* Administrador da caixa de correio
* Ingresso no dispositivo do local de trabalho

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre como atribuir um usuário como administrador de uma assinatura do Azure, consulte [gerenciar o acesso usando o RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md)
* Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para obter mais informações sobre como o Azure Active Directory está relacionado com a sua subscrição do Azure, veja [Como as subscrições do Azure estão associadas ao Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
