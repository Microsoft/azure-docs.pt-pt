---
title: Descrições e permissões de funções da AZure AD - Azure Ative Directory Microsoft Docs
description: Uma função administrativa pode adicionar utilizadores, atribuir funções administrativas, redefinir palavras-passe do utilizador, gerir licenças de utilizador ou gerir domínios.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9055bb9689895a9b74f3d6b5affa325a3b594d65
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874683"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Administrator role permissions in Azure Active Directory (Permissões de cargos de administrador no Azure Active Directory)

Utilizando o Azure Ative Directory (Azure AD), pode designar administradores limitados para gerir tarefas de identidade em funções menos privilegiadas. Os administradores podem ser designados para fins como adicionar ou alterar utilizadores, atribuir funções administrativas, redefinir palavras-passe do utilizador, gerir licenças de utilizador e gerir nomes de domínio. As [permissões do utilizador predefinidas](../fundamentals/users-default-permissions.md) só podem ser alteradas nas definições do utilizador em Azure AD.

## <a name="limit-use-of-global-administrator"></a>Limitar a utilização do administrador global

Os utilizadores que estão atribuídos à função de administrador global podem ler e modificar todas as configurações administrativas da sua organização AZure AD. Por padrão, quando um utilizador se inscreve para um serviço de cloud da Microsoft, é criado um inquilino AZure AD e o utilizador é nomeado membro do papel de Administrador Global. Quando adiciona uma subscrição a um inquilino existente, não está atribuído ao papel de Administrador Global. Apenas administradores globais e administradores de funções privilegiadas podem delegar funções de administrador. Para reduzir o risco para o seu negócio, recomendamos que atribua este papel ao menor número possível de pessoas na sua organização.

Como uma boa prática, recomendamos que atribua este papel a menos de cinco pessoas na sua organização. Se tiver mais de cinco administradores atribuídos ao papel de Administrador Global na sua organização, eis algumas formas de reduzir o seu uso.

### <a name="find-the-role-you-need"></a>Encontre o papel de que precisa

Se é frustrante para si encontrar o papel que precisa de uma lista de muitos papéis, a Azure AD pode mostrar-lhe subconjuntos dos papéis baseados em categorias de papéis. Consulte o nosso novo filtro **Tipo** para [Funções AD Azure e administradores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) para mostrar apenas as funções no tipo selecionado.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Existe agora um papel que não existia quando atribuíste o papel de administrador global

É possível que um papel ou funções foram adicionados ao Azure AD que fornecem mais permissões granulares que não eram uma opção quando elevou alguns utilizadores para administrador global. Com o tempo, estamos a desenvolver papéis adicionais que realizam tarefas que só o papel de administrador global poderia fazer antes. Pode ver estes refletidos nas seguintes [funções Disponíveis.](#available-roles)

## <a name="assign-or-remove-administrator-roles"></a>Atribuir ou remover funções de administrador

Para aprender a atribuir funções administrativas a um utilizador no Azure Ative Directory, consulte [Ver e atribuir funções de administrador no Azure Ative Directory](manage-roles-portal.md).

> [!Note]
> Se tem uma licença P2 premium Azure E já é um utilizador privilegiado de Gestão de Identidade (PIM), todas as tarefas de gestão de funções são executadas na Gestão de Identidade de Privilégio e não na Ad AZure.
>
> ![Funções AD AD geridas em PIM para utilizadores que já usam PIM e têm uma licença Premium P2](./media/permissions-reference/pim-manages-roles-for-p2.png)

## <a name="available-roles"></a>Funções disponíveis

Estão disponíveis as seguintes funções de administrador:

### <a name="application-administrator"></a>[Administrador de Aplicações](#application-administrator-permissions)

Os utilizadores desta função podem criar e gerir todos os aspetos das aplicações empresariais, registos de aplicações e configurações de procuração de aplicações. Note que os utilizadores destacados para esta função não são adicionados como proprietários na criação de novos registos de aplicações ou aplicações empresariais.

Esta função também concede a capacidade de _consentir_ com permissões delegadas e permissões de aplicação, com exceção das permissões na API do Gráfico da Microsoft.

> [!IMPORTANT]
> Esta exceção significa que ainda pode consentir permissões para _outras_ aplicações (por exemplo, aplicações ou aplicações não Microsoft que tenha registado), mas não para permissões em Azure AD em si. Ainda pode _solicitar_ estas permissões como parte do registo da app, mas _conceder_ (isto é, consentir) estas permissões requer um administrador AD AZure. Isto significa que um utilizador malicioso não pode facilmente elevar as suas permissões, por exemplo, criando e consentindo numa aplicação que possa escrever para todo o diretório e através das permissões dessa aplicação elevam-se para se tornarem administradores globais.
>
>Esta função confere a capacidade de gerir as credenciais de aplicação. Os utilizadores designados para esta função podem adicionar credenciais a uma aplicação, e usar essas credenciais para personificar a identidade da aplicação. Se a identidade da aplicação tiver tido acesso a um recurso, como a capacidade de criar ou atualizar o Utilizador ou outros objetos, então um utilizador designado para esta função poderá executar essas ações enquanto se faz passar pela aplicação. Esta capacidade de personificar a identidade da aplicação pode ser uma elevação de privilégio sobre o que o utilizador pode fazer através das suas atribuições de funções. É importante entender que atribuir um utilizador à função de Administrador de Aplicação dá-lhes a capacidade de personificar a identidade de uma aplicação.

### <a name="application-developer"></a>[Programador de Aplicações](#application-developer-permissions)

Os utilizadores desta função podem criar registos de aplicações quando a definição de "Utilizadores podem registar aplicações" é definida como Nº. Esta função também permite consentir em nome próprio quando a definição de "Utilizadores podem consentir em aplicações que acedam aos dados da empresa em seu nome". Os utilizadores destacados para esta função são adicionados como proprietários ao criar novos registos de aplicações ou aplicações empresariais.

### <a name="authentication-administrator"></a>[Administrador de Autenticação](#authentication-administrator-permissions)

Os utilizadores com esta função podem definir ou redefinir credenciais de não senha para alguns utilizadores e podem atualizar palavras-passe para todos os utilizadores. Os administradores de autenticação podem exigir que os utilizadores que não sejam administradores ou que sejam designados para algumas funções se re-registem contra as credenciais não senhas existentes (por exemplo, MFA ou FIDO), podendo também revogar **o remember MFA no dispositivo**, que solicita mFA no próximo registo. Estas ações aplicam-se apenas aos utilizadores que não sejam administradores ou que lhes sejam atribuídas uma ou mais das seguintes funções:

* Administrador de Autenticação
* Leitores de Diretório
* Convidado Convidado
* Leitor do Centro de Mensagens
* Leitor de Relatórios

A [função de administrador de autenticação privilegiada](#privileged-authentication-administrator) tem permissão pode forçar o re-registo e a autenticação de vários fatores para todos os utilizadores.

> [!IMPORTANT]
> Os utilizadores com esta função podem alterar credenciais para pessoas que possam ter acesso a informações sensíveis ou privadas ou configuração crítica dentro e fora do Azure Ative Directory. Alterar as credenciais de um utilizador pode significar a capacidade de assumir a identidade e permissões desse utilizador. Por exemplo:
>
>* Os proprietários de Registo de Aplicações e Aplicações Empresariais, que podem gerir credenciais de apps que possuam. Essas aplicações podem ter permissões privilegiadas em Azure AD e em outros lugares não concedidos a Administradores de Autenticação. Por este caminho um Administrador de Autenticação poderá assumir a identidade de titular da candidatura e, em seguida, assumir a identidade de uma aplicação privilegiada atualizando as credenciais para a aplicação.
>* Proprietários de subscrições Azure, que podem ter acesso a informações sensíveis ou privadas ou configuração crítica em Azure.
>* Security Group e Microsoft 365 proprietários do grupo, que podem gerir a adesão ao grupo. Esses grupos podem conceder acesso a informações sensíveis ou privadas ou configuração crítica em Azure AD e em outros lugares.
>* Administradores em outros serviços fora da Azure AD como Exchange Online, Office Security and Compliance Center, e sistemas de recursos humanos.
>* Não administradores como executivos, advogados e funcionários de recursos humanos que possam ter acesso a informações confidenciais ou privadas.

### <a name="azure-devops-administrator"></a>[Administrador da Azure DevOps](#azure-devops-administrator-permissions)

Os utilizadores com esta função podem gerir a política Azure DevOps para restringir a criação de uma nova organização Azure DevOps a um conjunto de utilizadores ou grupos configuráveis. Os utilizadores desta função podem gerir esta política através de qualquer organização da Azure DevOps que seja apoiada pela organização Azure AD da empresa. Esta função não concede outras permissões específicas da Azure DevOps (por exemplo, Administradores de Cobrança de Projetos) dentro de qualquer uma das organizações Azure DevOps apoiadas pela organização Azure AD da empresa.

Todas as políticas da empresa Azure DevOps podem ser geridas pelos utilizadores neste papel.

### <a name="azure-information-protection-administrator"></a>[Administrador de Proteção de Informação da Azure](#azure-information-protection-administrator-permissions)

Os utilizadores com esta função têm todas as permissões no serviço de Proteção de Informação Azure. Esta função permite configurar rótulos para a política de proteção de informação Azure, gerir modelos de proteção e ativar a proteção. Esta função não concede quaisquer permissões no Centro de Proteção de Identidade, Gestão de Identidade Privilegiada, Monitor Microsoft 365 Service Health ou Office 365 Security & Compliance Center.

### <a name="b2c-ief-keyset-administrator"></a>[Administrador do keyset B2C IEF](#b2c-ief-keyset-administrator-permissions)

O utilizador pode criar e gerir chaves e segredos de política para encriptação simbólica, assinaturas simbólicas e alegar encriptação/desencriptação. Ao adicionar novas chaves aos recipientes-chave existentes, este administrador limitado pode reverter os segredos conforme necessário sem afetar as aplicações existentes. Este utilizador pode ver todo o conteúdo destes segredos e as datas de validade mesmo após a sua criação.

> [!IMPORTANT]
> Este é um papel sensível. A função de administrador do tecla deve ser cuidadosamente auditada e atribuída com cuidado durante a pré-produção e a produção.

### <a name="b2c-ief-policy-administrator"></a>[Administrador de Política B2C IEF](#b2c-ief-policy-administrator-permissions)

Os utilizadores desta função têm a capacidade de criar, ler, atualizar e eliminar todas as políticas personalizadas em Azure AD B2C e, portanto, ter total controlo sobre o Quadro de Experiência de Identidade na organização Azure AD B2C relevante. Ao editar políticas, este utilizador pode estabelecer uma federação direta com fornecedores de identidade externos, alterar o esquema de diretório, alterar todos os conteúdos voltados para o utilizador (HTML, CSS, JavaScript), alterar os requisitos para completar uma autenticação, criar novos utilizadores, enviar dados de utilizador para sistemas externos, incluindo migrações completas, e editar todas as informações do utilizador, incluindo campos sensíveis, como palavras-passe e números de telefone. Inversamente, este papel não pode alterar as chaves de encriptação ou editar os segredos usados para a federação na organização.

> [!IMPORTANT]
> O Administrador de Política B2 IEF é um papel altamente sensível que deve ser atribuído numa base muito limitada às organizações em produção. As atividades destes utilizadores devem ser auditadas de perto, especialmente para as organizações em produção.

### <a name="billing-administrator"></a>[Administrador de Faturação](#billing-administrator-permissions)

Efetua compras, gere subscrições, gere pedidos de suporte e monitoriza o estado de funcionamento do serviço.

### <a name="cloud-application-administrator"></a>[Administrador de Aplicações na Cloud](#cloud-application-administrator-permissions)

Os utilizadores nesta função têm as mesmas permissões que a função de Administrador de Aplicação, excluindo a capacidade de gerir o proxy da aplicação. Esta função confere a capacidade de criar e gerir todos os aspetos dos pedidos de empresa e dos registos de candidaturas. Esta função também concede a capacidade de consentir com permissões delegadas, e permissões de aplicação excluindo Microsoft Graph e Azure AD Graph. Os utilizadores destacados para esta função não são adicionados como proprietários ao criar novos registos de aplicações ou aplicações empresariais.

> [!IMPORTANT]
> Esta função confere a capacidade de gerir as credenciais de aplicação. Os utilizadores designados para esta função podem adicionar credenciais a uma aplicação, e usar essas credenciais para personificar a identidade da aplicação. Se a identidade da aplicação tiver tido acesso a um recurso, como a capacidade de criar ou atualizar o Utilizador ou outros objetos, então um utilizador designado para esta função poderá executar essas ações enquanto se faz passar pela aplicação. Esta capacidade de personificar a identidade da aplicação pode ser uma elevação de privilégio sobre o que o utilizador pode fazer através das suas atribuições de funções. É importante entender que atribuir um utilizador à função de Administrador de Aplicação cloud dá-lhes a capacidade de personificar a identidade de uma aplicação.


### <a name="cloud-device-administrator"></a>[Administrador de dispositivos de nuvem](#cloud-device-administrator-permissions)

Os utilizadores desta função podem ativar, desativar e eliminar dispositivos em AZure AD e ler teclas BitLocker do Windows 10 (se presentes) no portal Azure. A função não concede permissões para gerir quaisquer outras propriedades no dispositivo.

### <a name="compliance-administrator"></a>[Administrador de Conformidade](#compliance-administrator-permissions)

Os utilizadores com esta função têm permissões para gerir funcionalidades relacionadas com a conformidade no centro de conformidade microsoft 365, Microsoft 365 admin center, Azure e Office 365 Security & Compliance Center. Os assignees também podem gerir todas as funcionalidades dentro do Centro de Administração Exchange e equipas & Skype para centros de administração de negócios e criar bilhetes de apoio para Azure e Microsoft 365. Mais informações estão disponíveis em [funções de administração da Microsoft 365.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

Em | Pode fazer
----- | ----------
[Microsoft 365 centro de conformidade](https://protection.office.com) | Proteja e gere os dados da sua organização através dos serviços microsoft 365<br>Gerir alertas de conformidade
[Gestor de Conformidade](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhe, atribua e verifique as atividades de conformidade regulamentar da sua organização
[Escritório 365 Centro de Conformidade & de Segurança](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerir a governação de dados<br>Realizar investigação legal e de dados<br>Gerir pedido de assunto de dados<br><br>Esta função tem as mesmas permissões que o [RoleGroup do Administrador de Conformidade](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) no Office 365 Security & Compliance Center controlo de acesso baseado em funções.
[Intune](/intune/role-based-access-control) | Ver todos os dados de auditoria do Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Tem permissões só de leitura e pode gerir alertas<br>Pode criar e modificar políticas de ficheiros e permitir ações de governação de ficheiros<br>Pode ver todos os relatórios incorporados sob a Gestão de Dados

### <a name="compliance-data-administrator"></a>[Administrador de Dados de Conformidade](#compliance-data-administrator-permissions)

Os utilizadores com esta função têm permissões para rastrear dados no centro de conformidade Microsoft 365, Microsoft 365 admin center e Azure. Os utilizadores também podem rastrear dados de conformidade dentro do centro de administração Exchange, Compliance Manager e Teams & Skype for Business e criar bilhetes de suporte para Azure e Microsoft 365. [Esta documentação](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center?view=o365-worldwide#permissions-needed-to-use-features-in-the-security--compliance-center) tem detalhes sobre diferenças entre o Administrador de Conformidade e o Administrador de Dados de Conformidade.

Em | Pode fazer
----- | ----------
[Microsoft 365 centro de conformidade](https://protection.office.com) | Monitorize as políticas relacionadas com a conformidade nos serviços da Microsoft 365<br>Gerir alertas de conformidade
[Gestor de Conformidade](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhe, atribua e verifique as atividades de conformidade regulamentar da sua organização
[Escritório 365 Centro de Conformidade & de Segurança](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerir a governação de dados<br>Realizar investigação legal e de dados<br>Gerir pedido de assunto de dados<br><br>Esta função tem as mesmas permissões que o [RoleGroup do Administrador de Dados de Conformidade](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) no Office 365 Security & Control de acesso baseado em funções do Compliance Center.
[Intune](/intune/role-based-access-control) | Ver todos os dados de auditoria do Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Tem permissões só de leitura e pode gerir alertas<br>Pode criar e modificar políticas de ficheiros e permitir ações de governação de ficheiros<br>Pode ver todos os relatórios incorporados sob a Gestão de Dados

### <a name="conditional-access-administrator"></a>[Administrador de acesso condicional](#conditional-access-administrator-permissions)

Os utilizadores com esta função têm a capacidade de gerir as definições de Acesso Condicional do Diretório Ativo Azure.

### <a name="customer-lockbox-access-approver"></a>[Aprovador de acesso ao bloqueio do cliente](#customer-lockbox-access-approver-permissions)

Gere os [pedidos de Lockbox do Cliente](/office365/admin/manage/customer-lockbox-requests) na sua organização. Recebem notificações por e-mail para pedidos de Bloqueio de Clientes e podem aprovar e negar pedidos do centro de administração microsoft 365. Também podem ligar ou desligar a função 'Lockbox' do cliente. Só os administradores globais podem redefinir as palavras-passe das pessoas afetadas a este papel.

### <a name="desktop-analytics-administrator"></a>[Administrador de Análise de Desktop](#desktop-analytics-administrator-permissions)


Os utilizadores desta função podem gerir os serviços de Política de & de Personalização do Desktop Analytics e Office. Para desktop Analytics, isto inclui a capacidade de visualizar o inventário de ativos, criar planos de implementação, ver implementação e estado de saúde. Para o serviço de personalização do Office & Policy, esta função permite aos utilizadores gerir as políticas do Office.

### <a name="device-administrators"></a>[Administradores de Dispositivos](#device-administrators-permissions)

Esta função está disponível para atribuição apenas como administrador local adicional nas [definições do Dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Os utilizadores com esta função tornam-se administradores de máquinas locais em todos os dispositivos windows 10 que se juntam ao Azure Ative Directory. Não têm a capacidade de gerir objetos de dispositivos no Azure Ative Directory.

### <a name="directory-readers"></a>[Leitores de Diretório](#directory-readers-permissions)

Os utilizadores desta função podem ler informações básicas de diretório. Esta função deve ser utilizada para:

* Concedendo um conjunto específico de utilizadores convidados leia o acesso em vez de conceder a todos os utilizadores convidados.
* A concessão de um conjunto específico de utilizadores não administrativos ao portal Azure quando "Restringir o acesso ao portal AD Ad Azure apenas para administradores" está definido para "Sim".
* Conceder aos diretores de serviço acesso ao diretório onde o Diretório.Read.Tudo não é uma opção.

### <a name="directory-synchronization-accounts"></a>[Contas de Sincronização do Diretório](#directory-synchronization-accounts-permissions)

Não utilizar. Esta função é automaticamente atribuída ao serviço Azure AD Connect e não se destina ou suporta qualquer outra utilização.

### <a name="directory-writers"></a>[Escritores do Diretório](#directory-writers-permissions)
Os utilizadores desta função podem ler e atualizar informações básicas de utilizadores, grupos e diretores de serviço. Atribua esta função apenas a aplicações que não suportem o [Quadro de Consentimento](../develop/quickstart-register-app.md). Não deve ser atribuído a nenhum utilizadores.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Administrador dinâmico 365 / Administrador de CRM](#crm-service-administrator-permissions)

Os utilizadores com esta função têm permissões globais dentro do Microsoft Dynamics 365 Online, quando o serviço está presente, bem como a capacidade de gerir bilhetes de suporte e monitorizar a saúde do serviço. Mais informações na [Utilização da função de administrador de serviço para gerir a sua organização AZure AD](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço Dinâmico 365". É "Administrador Dinâmico 365" no [portal Azure](https://portal.azure.com).

### <a name="exchange-administrator"></a>[Administrador de Intercâmbio](#exchange-service-administrator-permissions)

Os utilizadores com esta função têm permissões globais dentro do Microsoft Exchange Online, quando o serviço está presente. Também tem a capacidade de criar e gerir todos os grupos Microsoft 365, gerir bilhetes de suporte e monitorizar a saúde do serviço. Mais informações [sobre as funções de administração da Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Na Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço de Câmbio". É "Exchange Administrator" no [portal Azure](https://portal.azure.com). É "Exchange Online administrator" no [Centro de Administração Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).


### <a name="external-id-user-flow-administrator"></a>[Administrador de fluxo de id externo](#external-id-user-flow-administrator-permissions)

Os utilizadores com esta função podem criar e gerir fluxos de utilizadores (também chamados políticas "incorporadas") no portal Azure. Estes utilizadores podem personalizar o conteúdo HTML/CSS/JavaScript, alterar os requisitos de MFA, selecionar reclamações no token, gerir conectores API e configurar as definições de sessão para todos os fluxos de utilizador na organização Azure AD. Por outro lado, esta função não inclui a capacidade de rever os dados dos utilizadores ou de fazer alterações nos atributos que estão incluídos no esquema da organização. As alterações às políticas do Quadro de Experiência de Identidade (também conhecidas como políticas personalizadas) também estão fora do âmbito desta função.

### <a name="external-id-user-flow-attribute-administrator"></a>[Administrador de atributo de fluxo de id do utilizador externo](#external-id-user-flow-attribute-administrator-permissions)

Os utilizadores com esta função adicionam ou eliminam os atributos personalizados disponíveis para todos os fluxos de utilizador na organização AZure AD. Como tal, os utilizadores com esta função podem alterar ou adicionar novos elementos ao esquema do utilizador final e impactar o comportamento de todos os fluxos de utilizador e resultar indiretamente em alterações aos dados que podem ser solicitados aos utilizadores finais e, em última análise, enviados como reivindicações para as aplicações. Esta função não pode editar fluxos de utilizador.

### <a name="external-identity-provider-administrator"></a>[Administrador de fornecedor de id entidade externa](#external-identity-provider-administrator-permissions)

Este administrador gere a federação entre organizações AD da Azure e fornecedores de identidade externa. Com esta função, os utilizadores podem adicionar novos fornecedores de identidade e configurar todas as definições disponíveis (por exemplo, percurso de autenticação, ID de serviço, recipientes-chave atribuídos). Este utilizador pode permitir que a organização Azure AD confie em autenticações de fornecedores de identidade externos. O impacto resultante nas experiências do utilizador final depende do tipo de organização:

* Azure AD organizações para colaboradores e parceiros: A adição de uma federação (por exemplo, com o Gmail) terá imediatamente impacto em todos os convites de hóspedes ainda não resgatados. Consulte [a adição do Google como um fornecedor de identidade para utilizadores convidados B2B](../external-identities/google-federation.md).
* Organizações Azure Ative Directory B2C: A adição de uma federação (por exemplo, com o Facebook, ou com outra organização AZure AD) não afeta imediatamente os fluxos de utilizador final até que o fornecedor de identidade seja adicionado como uma opção num fluxo de utilizador (também chamado de política incorporada). Consulte [configurar uma conta Microsoft como um fornecedor de identidade,](../../active-directory-b2c/identity-provider-microsoft-account.md) por exemplo. Para alterar os fluxos dos utilizadores, é necessária a função limitada de "Administrador de Fluxo de Utilizador B2C".

### <a name="global-administrator--company-administrator"></a>[Administrador Global / Administrador da Empresa](#company-administrator-permissions)

Os utilizadores com esta função têm acesso a todas as funcionalidades administrativas no Azure Ative Directory, bem como serviços que utilizam identidades do Azure Ative Directory como o Microsoft 365 security center, o Microsoft 365 compliance center, o Exchange Online, o SharePoint Online e o Skype para business online. Além disso, a Global Admins pode [elevar o seu acesso](../../role-based-access-control/elevate-access-global-admin.md) para gerir todas as subscrições e grupos de gestão da Azure. Isto permite que os Administradores Globais tenham acesso total a todos os recursos Azure usando o respetivo Azure AD Tenant. A pessoa que se inscreve na organização AZure AD torna-se um administrador global. Pode haver mais de um administrador global na sua empresa. Os administradores globais podem redefinir a palavra-passe para qualquer utilizador e todos os outros administradores.

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador da Empresa". É "Administrador Global" no [portal Azure.](https://portal.azure.com)
>
>

### <a name="global-reader"></a>[Leitor Global](#global-reader-permissions)

Os utilizadores desta função podem ler definições e informações administrativas em todos os serviços da Microsoft 365, mas não podem tomar ações de gestão. O leitor global é a contrapartida apenas de leitura para administrador global. Atribua o leitor global em vez de administrador global para planeamento, auditorias ou investigações. Use o leitor Global em combinação com outras funções de administração limitadas, como o Exchange Administrator, para facilitar o trabalho sem atribuir o papel de Administrador Global. O leitor global trabalha com o Microsoft 365 admin center, Exchange admin center, SharePoint admin center, Teams admin center, Security center, Compliance center, Azure AD admin center e Device Management admin center.

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

### <a name="groups-administrator"></a>[Administrador de Grupos](#groups-administrator-permissions)

Os utilizadores desta função podem criar/gerir grupos e as suas definições como políticas de nomeação e expiração. É importante entender que atribuir um utilizador a este papel dá-lhes a capacidade de gerir todos os grupos da organização em várias cargas de trabalho como Teams, SharePoint, Yammer, além do Outlook. Também o utilizador será capaz de gerir as configurações de vários grupos em vários portais de administração como o Microsoft Admin Center, portal Azure, bem como os específicos da carga de trabalho, como Teams e SharePoint Admin Centers.

### <a name="guest-inviter"></a>[Convidado Convidado](#guest-inviter-permissions)

Os utilizadores desta função podem gerir convites de utilizadores convidados Azure Ative Directory B2B quando os **Membros podem convidar** a definição do utilizador está definido para Nº. Mais informações sobre a colaboração B2B na [colaboração sobre a azure AD B2B](../external-identities/what-is-b2b.md). Não inclui quaisquer outras permissões.

### <a name="helpdesk-administrator"></a>[Administrador helpdesk](#helpdesk-administrator-permissions)

Os utilizadores com esta função podem alterar palavras-passe, invalidar fichas de atualização, gerir pedidos de serviço e monitorizar a saúde do serviço. Invalidar um token de atualização obriga o utilizador a iniciar novamente o sat. Os administradores da Helpdesk podem redefinir palavras-passe e invalidar fichas de atualização de outros utilizadores que não sejam administradores ou atribuam apenas as seguintes funções:

* Leitores de Diretório
* Convidado Convidado
* Administrador helpdesk
* Leitor do Centro de Mensagens
* Administrador de password
* Leitor de Relatórios

> [!IMPORTANT]
> Os utilizadores com esta função podem alterar palavras-passe para pessoas que possam ter acesso a informações sensíveis ou privadas ou configuração crítica dentro e fora do Azure Ative Directory. Alterar a palavra-passe de um utilizador pode significar a capacidade de assumir a identidade e permissões desse utilizador. Por exemplo:
>
>- Os proprietários de Registo de Aplicações e Aplicações Empresariais, que podem gerir credenciais de apps que possuam. Essas aplicações podem ter permissões privilegiadas em Azure AD e em outros lugares não concedidos a Administradores helpdesk. Por este caminho, um Administrador helpdesk poderá assumir a identidade de um titular da aplicação e, em seguida, assumir a identidade de um pedido privilegiado atualizando as credenciais para o pedido.
>- Proprietários de subscrições Azure, que podem ter acesso a informações confidenciais ou privadas ou configuração crítica em Azure.
>- Security Group e Microsoft 365 proprietários do grupo, que podem gerir a adesão ao grupo. Esses grupos podem conceder acesso a informações sensíveis ou privadas ou configuração crítica em Azure AD e em outros lugares.
>- Administradores em outros serviços fora da Azure AD como Exchange Online, Office Security and Compliance Center, e sistemas de recursos humanos.
>- Não administradores como executivos, advogados e funcionários de recursos humanos que possam ter acesso a informações confidenciais ou privadas.

Delegar permissões administrativas sobre subconjuntos de utilizadores e aplicar políticas a um subconjunto de utilizadores é possível com [Unidades Administrativas (agora em visualização pública)](administrative-units.md).

Esta função foi anteriormente denominada "Administrador de Password" no [portal Azure](https://portal.azure.com/). O nome "Helpdesk Administrator" em Azure AD agora corresponde ao seu nome no Azure AD PowerShell e na Microsoft Graph API.

### <a name="hybrid-identity-administrator"></a>[Administrador de Identidade Híbrida](#hybrid-identity-administrator-permissions)

Os utilizadores desta função podem criar, gerir e implementar a configuração de configuração de provisionamento de AD a AZure AD usando Cloud Provisioning, bem como gerir as definições da federação. Os utilizadores também podem resolver problemas e monitorizar registos utilizando esta função.  

### <a name="insights-administrator"></a>[Administrador de Insights](#insights-administrator-permissions)
Os utilizadores desta função podem aceder a todo o conjunto de capacidades administrativas na [aplicação M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Esta função tem a capacidade de ler informações de diretórios, monitorizar a saúde do serviço, bilhetes de suporte de ficheiros e aceder aos aspetos de definições de administração do Insights.

### <a name="insights-business-leader"></a>[Insights Business Leader](#insights-business-leader-permissions)
Os utilizadores desta função podem aceder a um conjunto de dashboards e insights através da [aplicação M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Isto inclui acesso total a todos os dashboards e apresenta insights e funcionalidade de exploração de dados. Os utilizadores desta função não têm acesso às definições de configuração do produto, que é da responsabilidade da função Insights Admin.

### <a name="intune-administrator"></a>[Administrador Intune](#intune-service-administrator-permissions)

Os utilizadores com esta função têm permissões globais dentro do Microsoft Intune Online, quando o serviço está presente. Além disso, esta função contém a capacidade de gerir utilizadores e dispositivos de forma a associar a política, bem como criar e gerir grupos. Mais informações no [Controlo de Administração baseado em Role (RBAC) com a Microsoft Intune](/intune/role-based-access-control).

Este papel pode criar e gerir todos os grupos de segurança. No entanto, a Intune Admin não tem direitos de administração sobre grupos de escritórios. Isto significa que o administrador não pode atualizar proprietários ou membros de todos os grupos do Office na organização. No entanto, pode gerir o grupo de Escritório que cria e que vem como parte dos seus privilégios de utilizador final. Assim, qualquer grupo de Escritório (não grupo de segurança) que cria deve ser contabilizado contra a sua quota de 250.

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço Intune". É "Administrador Intune" no [portal Azure](https://portal.azure.com).

### <a name="kaizala-administrator"></a>[Administrador kaizala](#kaizala-administrator-permissions)

Os utilizadores com esta função têm permissões globais para gerir as definições dentro da Microsoft Kaizala, quando o serviço está presente, bem como a capacidade de gerir bilhetes de suporte e monitorizar a saúde do serviço. Além disso, o utilizador pode aceder a relatórios relacionados com a adoção & uso de Kaizala por membros da Organização e relatórios de negócios gerados através das ações de Kaizala.

### <a name="license-administrator"></a>[Administrador de Licença](#license-administrator-permissions)

Os utilizadores desta função podem adicionar, remover e atualizar as atribuições de licenças em utilizadores, grupos (usando licenças baseadas em grupo) e gerir a localização de utilização nos utilizadores. A função não concede a capacidade de comprar ou gerir subscrições, criar ou gerir grupos, ou criar ou gerir utilizadores para além da localização de utilização. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

### <a name="message-center-privacy-reader"></a>[Leitor de Privacidade do Centro de Mensagens](#message-center-privacy-reader-permissions)

Os utilizadores desta função podem monitorizar todas as notificações no Centro de Mensagens, incluindo mensagens de privacidade de dados. Os Leitores de Privacidade do Centro de Mensagens recebem notificações de e-mail, incluindo as relacionadas com a privacidade dos dados e podem cancelar a subscrição através das Preferências do Centro de Mensagens. Apenas o Administrador Global e o Leitor de Privacidade do Centro de Mensagens podem ler mensagens de privacidade de dados. Além disso, esta função contém a capacidade de visualizar grupos, domínios e subscrições. Esta função não tem permissão para visualizar, criar ou gerir pedidos de serviço.

### <a name="message-center-reader"></a>[Leitor do Centro de Mensagens](#message-center-reader-permissions)

Os utilizadores desta função podem monitorizar notificações e atualizações de saúde de aconselhamento no [Centro de Mensagens](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) para a sua organização em serviços configurados como Exchange, Intune e Microsoft Teams. Os Leitores do Centro de Mensagens recebem digestão semanal de mensagens, atualizações e podem partilhar posts de centro de mensagens no Microsoft 365. No Azure AD, os utilizadores designados para esta função terão apenas acesso de leitura apenas a serviços AD Azure, como utilizadores e grupos. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

### <a name="modern-commerce-user"></a>[Utilizador de Comércio Moderno](#modern-commerce-user-permissions)

Não utilizar. Esta função é automaticamente atribuída ao Comércio, e não se destina ou é suportada para qualquer outra utilização. Veja os detalhes abaixo.

A função de Utilizador do Comércio Moderno dá a certos utilizadores permissão para aceder ao centro de administração microsoft 365 e ver as entradas de navegação esquerda para **Home**, **Billing** e **Support**. O conteúdo disponível nestas áreas é controlado por [funções específicas do comércio](../../cost-management-billing/manage/understand-mca-roles.md) atribuídas aos utilizadores para gerir produtos que compraram para si ou para a sua organização. Isto pode incluir tarefas como pagar contas ou acesso a contas de faturação e perfis de faturação. 

Os utilizadores com a função de Utilizador de Comércio Moderno normalmente têm permissões administrativas em outros sistemas de compra da Microsoft, mas não têm funções de administrador global ou administrador de faturação usadas para aceder ao centro de administração. 

**Quando é atribuída a função de Utilizador do Comércio Moderno?**

* **A compra de self-service no Microsoft 365 admin center** – A compra de self-service dá aos utilizadores a oportunidade de experimentar novos produtos comprando ou inscrevendo-se por conta própria. Estes produtos são geridos no centro de administração. Os utilizadores que fazem uma compra de self-service são atribuídos a um papel no sistema de comércio, e o papel de Utilizador do Comércio Moderno para que possam gerir as suas compras no centro de administração. Os administradores podem bloquear as compras de self-service (para Power BI, Power Apps, Power automat) através [do PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell?view=o365-worldwide). Para obter mais informações, veja [Self-service purchase FAQ](/microsoft-365/commerce/subscriptions/self-service-purchase-faq?view=o365-worldwide) (FAQ da compra personalizada).  
* **Compras no mercado comercial da Microsoft**  – Semelhante à compra de self-service, quando um utilizador compra um produto ou serviço ao Microsoft AppSource ou ao Azure Marketplace, a função de Utilizador de Comércio Moderno é atribuída se não tiver o papel de administrador global ou de administração billing. Em alguns casos, os utilizadores podem estar impedidos de fazer estas compras. Para mais informações, consulte [o mercado comercial da Microsoft.](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase)
* **Propostas da Microsoft**  – Uma proposta é uma oferta formal da Microsoft para a sua organização comprar produtos e serviços da Microsoft. Quando a pessoa que está a aceitar a proposta não tem um papel de administrador global ou de administração billing em Azure AD, é-lhes atribuído um papel específico do comércio para completar a proposta e o papel de Utilizador do Comércio Moderno para aceder ao centro de administração. Quando acedem ao centro de administração, só podem usar funcionalidades que são autorizadas pelo seu papel específico do comércio.
* **Funções específicas do comércio** – Alguns utilizadores têm funções específicas do comércio. Se um utilizador não for um administrador global ou de faturação, obtém o papel de Utilizador do Comércio Moderno para que possa aceder ao centro de administração.  

Se a função de Utilizador do Comércio Moderno não for atribuído a um utilizador, perde o acesso ao centro de administração microsoft 365. Se eles estavam a gerir quaisquer produtos, para si ou para a sua organização, eles não serão capazes de geri-los. Isto pode incluir a atribuição de licenças, alteração de métodos de pagamento, pagamento de contas ou outras tarefas para a gestão de subscrições.

### <a name="network-administrator"></a>[Administrador de rede](#network-administrator-permissions)

Os utilizadores desta função podem rever recomendações de arquitetura de perímetro de rede da Microsoft que são baseadas em telemetria de rede a partir das suas localizações de utilizadores. O desempenho da rede para o Microsoft 365 baseia-se numa arquitetura cuidadosa do perímetro da rede de clientes da empresa, que é geralmente específica da localização do utilizador. Esta função permite a edição das localizações do utilizador descobertas e a configuração de parâmetros de rede para esses locais para facilitar a melhoria das medições de telemetria e recomendações de design
### <a name="office-apps-administrator"></a>[Administrador de Aplicações de Escritório](#office-apps-administrator-permissions)

Os utilizadores desta função podem gerir as definições de cloud das aplicações da Microsoft 365. Isto inclui gerir políticas de nuvem, gestão de descarregamento de autosserviço e a capacidade de visualizar relatório relacionado com aplicações do Office. Esta função também garante a capacidade de gerir os bilhetes de apoio e monitorizar a saúde do serviço dentro do principal centro de administração. Os utilizadores destacados para esta função também podem gerir a comunicação de novas funcionalidades em aplicações do Office. 

### <a name="partner-tier1-support"></a>[Suporte parceiro Tier1](#partner-tier1-support-permissions)

Não utilizar. Este papel foi depreciado e será removido da Azure AD no futuro. Esta função destina-se a ser utilizada por um pequeno número de parceiros de revenda da Microsoft, e não se destina a ser utilizada em geral.

### <a name="partner-tier2-support"></a>[Suporte parceiro Tier2](#partner-tier2-support-permissions)

Não utilizar. Este papel foi depreciado e será removido da Azure AD no futuro. Esta função destina-se a ser utilizada por um pequeno número de parceiros de revenda da Microsoft, e não se destina a ser utilizada em geral.

### <a name="password-administrator"></a>[Administrador de password](#password-administrator-permissions)

Os utilizadores com esta função têm capacidade limitada para gerir senhas. Esta função não garante a capacidade de gerir pedidos de serviço ou monitorizar a saúde do serviço. Os administradores de passwords podem redefinir palavras-passe de outros utilizadores que não sejam administradores ou membros das seguintes funções apenas:

* Leitores de Diretório
* Convidado Convidado
* Administrador de password

### <a name="power-bi-administrator"></a>[Administrador de Bi de Energia](#power-bi-service-administrator-permissions)

Os utilizadores com esta função têm permissões globais dentro do Microsoft Power BI, quando o serviço está presente, bem como a capacidade de gerir bilhetes de suporte e monitorizar a saúde do serviço. Mais informações na [Compreensão do papel de administrador do Power BI](/power-bi/service-admin-role).

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço de Power BI". É "Power BI Administrator" no [portal Azure](https://portal.azure.com).

### <a name="power-platform-administrator"></a>[Administrador da Plataforma de Energia](#power-platform-administrator-permissions)

Os utilizadores desta função podem criar e gerir todos os aspetos de ambientes, PowerApps, Flows, Políticas de Prevenção de Perda de Dados. Além disso, os utilizadores com esta função têm a capacidade de gerir bilhetes de apoio e monitorizar a saúde do serviço.

### <a name="printer-administrator"></a>[Administrador de impressora](#printer-administrator-permissions)

Os utilizadores desta função podem registar impressoras e gerir todos os aspetos de todas as configurações da impressora na solução Microsoft Universal Print, incluindo as definições do Conector de Impressão Universal. Podem consentir com todos os pedidos de autorização de impressão delegados. Os administradores da impressora também têm acesso a relatórios de impressão.

### <a name="printer-technician"></a>[Técnico de Impressora](#printer-technician-permissions)

Os utilizadores com esta função podem registar impressoras e gerir o estado da impressora na solução De Impressão Universal da Microsoft. Também podem ler todas as informações do conector. A tarefa chave que um Técnico de Impressora não pode fazer é definir permissões do utilizador em impressoras e partilhar impressoras.

### <a name="privileged-authentication-administrator"></a>[Administrador de Autenticação Privilegiada](#privileged-authentication-administrator-permissions)

Os utilizadores com esta função podem definir ou redefinir credenciais de não-senha para todos os utilizadores, incluindo administradores globais, e podem atualizar palavras-passe para todos os utilizadores. Os Administradores de Autenticação Privilegiada podem forçar os utilizadores a re-registarem-se contra a credencial não-senha existente (como MFA ou FIDO) e revogar "lembrem-se de MFA no dispositivo", solicitando para MFA na próxima entrada de todos os utilizadores. A [função de administrador de autenticação](#authentication-administrator) pode forçar o reensimin e MFA apenas para não administradores e utilizadores atribuídos às seguintes funções AD Azure:

* Administrador de Autenticação
* Leitores de Diretório
* Convidado Convidado
* Leitor do Centro de Mensagens
* Leitor de Relatórios

### <a name="privileged-role-administrator"></a>[Administrador privilegiado](#privileged-role-administrator-permissions)

Os utilizadores com esta função podem gerir atribuições de funções no Azure Ative Directory, bem como dentro da Azure AD Privileged Identity Management. Podem criar e gerir grupos que podem ser atribuídos a funções AZure AD. Além disso, esta função permite a gestão de todos os aspetos da Gestão de Identidade Privilegiada e das unidades administrativas.

> [!IMPORTANT]
> Esta função confere a capacidade de gerir atribuições para todas as funções de AD Azure, incluindo o papel de Administrador Global. Esta função não inclui quaisquer outras habilidades privilegiadas no AD Azure, como criar ou atualizar utilizadores. No entanto, os utilizadores destacados para esta função podem conceder a si mesmos ou a outros privilégios adicionais atribuindo funções adicionais.

### <a name="reports-reader"></a>[Leitor de Relatórios](#reports-reader-permissions)

Os utilizadores com esta função podem visualizar dados de reporte de utilização e o painel de relatórios no centro de administração microsoft 365 e o pacote de contexto de adoção no Power BI. Além disso, a função fornece acesso a relatórios de inscrição e atividade em AD Azure e dados devolvidos pela API de relatório do Microsoft Graph. Um utilizador atribuído à função Reports Reader só pode aceder a métricas de utilização e adoção relevantes. Não têm permissões de administração para configurar definições ou aceder aos centros de administração específicos do produto, como o Exchange. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

### <a name="search-administrator"></a>[Administrador de Pesquisa](#search-administrator-permissions)

Os utilizadores desta função têm acesso total a todas as funcionalidades de gestão da Microsoft Search no centro de administração microsoft 365. Além disso, estes utilizadores podem ver o centro de mensagens, monitorizar a saúde do serviço e criar pedidos de serviço.

### <a name="search-editor"></a>[Editor de Pesquisa](#search-editor-permissions)

Os utilizadores desta função podem criar, gerir e eliminar conteúdo para o Microsoft Search no centro de administração microsoft 365, incluindo marcadores, Q&As e localizações.

### <a name="security-administrator"></a>[Administrador de Segurança](#security-administrator-permissions)

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

### <a name="security-operator"></a>[Operador de segurança](#security-operator-permissions)

Os utilizadores com esta função podem gerir alertas e ter acesso global apenas de leitura sobre funcionalidades relacionadas com a segurança, incluindo todas as informações no centro de segurança Microsoft 365, Diretório Ativo Azure, Proteção de Identidade, Gestão de Identidade Privilegiada e Centro de Segurança & Compliance Center do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis [em Permissões no Centro de Conformidade & de Segurança.](/office365/securitycompliance/permissions-in-the-security-and-compliance-center)

Em | Pode fazer
--- | ---
[Centro de segurança Microsoft 365](https://protection.office.com) | Todas as permissões do papel do Leitor de Segurança<br>Ver, investigar e responder a alertas de ameaças à segurança
Centro de Proteção de Identidade | Todas as permissões do papel do Leitor de Segurança<br>Além disso, a capacidade de realizar todas as operações do Centro de Proteção de Identidade, exceto para repor palavras-passe
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Todas as permissões do papel do Leitor de Segurança
[Escritório 365 Centro de Conformidade & de Segurança](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Todas as permissões do papel do Leitor de Segurança<br>Ver, investigar e responder aos alertas de segurança
Windows Defender ATP e EDR | Todas as permissões do papel do Leitor de Segurança<br>Ver, investigar e responder aos alertas de segurança
[Intune](/intune/role-based-access-control) | Todas as permissões do papel do Leitor de Segurança
[Cloud App Security](/cloud-app-security/manage-admins) | Todas as permissões do papel do Leitor de Segurança
[Saúde do serviço Microsoft 365](/office365/enterprise/view-service-health) | Ver a saúde dos serviços microsoft 365

### <a name="security-reader"></a>[Leitor de Segurança](#security-reader-permissions)

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

### <a name="service-support-administrator"></a>[Administrador de Suporte de Serviço](#service-support-administrator-permissions)

Os utilizadores com esta função podem abrir pedidos de suporte com os serviços da Microsoft para os serviços Azure e Microsoft 365, e visualizar o painel de serviço e o centro de mensagens no [portal Azure](https://portal.azure.com) e [no Microsoft 365.](https://admin.microsoft.com) Mais informações sobre [funções de administração.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> Anteriormente, esta função chamava-se "Administrador de Serviço" no [portal Azure](https://portal.azure.com) e [no Microsoft 365.](https://admin.microsoft.com) Rebatizámo-lo para "Administrador de Suporte de Serviço" para alinhar com o nome de exsiting na Microsoft Graph API, Azure AD Graph API e Azure AD PowerShell.

### <a name="sharepoint-administrator"></a>[Administrador do SharePoint](#sharepoint-service-administrator-permissions)

Os utilizadores com esta função têm permissões globais dentro do Microsoft SharePoint Online, quando o serviço está presente, bem como a capacidade de criar e gerir todos os grupos microsoft 365, gerir bilhetes de suporte e monitorizar a saúde do serviço. Mais informações sobre [funções de administração.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço SharePoint". É "SharePoint Administrator" no [portal Azure](https://portal.azure.com).

> [!NOTE]
> Esta função também concede permissões de âmbito à API do Microsoft Graph para o Microsoft Intune, permitindo a gestão e configuração de políticas relacionadas com os recursos sharePoint e OneDrive.

### <a name="skype-for-business--lync-administrator"></a>[Administrador do Skype para negócios / Lync](#lync-service-administrator-permissions)

Os utilizadores com esta função têm permissões globais dentro do Microsoft Skype para negócios, quando o serviço está presente, bem como gerir atributos de utilizador específicos do Skype no Azure Ative Directory. Além disso, esta função garante a capacidade de gerir bilhetes de apoio e monitorizar a saúde do serviço, e aceder às Equipas e Skype para o Business Admin Center. A conta também deve ser licenciada para equipas ou não pode executar as equipas PowerShell cmdlets. Mais informações sobre [o papel de administrador de negócios](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) do Skype para negócios e equipas que licenciam informações no Skype para licenciamento de [empresas e equipas da Microsoft Teams](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> Na Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço Lync". É "Skype for Business Administrator" no [portal Azure](https://portal.azure.com/).

### <a name="teams-communications-administrator"></a>[Administrador de Comunicações de Equipas](#teams-communications-administrator-permissions)

Os utilizadores desta função podem gerir aspetos da carga de trabalho das Equipas Microsoft relacionadas com a & telefonia. Isto inclui as ferramentas de gestão para a atribuição de números de telefone, políticas de voz e reunião, e acesso total ao conjunto de ferramentas de análise de chamadas.

### <a name="teams-communications-support-engineer"></a>[Engenheiro de Suporte de Comunicações de Equipas](#teams-communications-support-engineer-permissions)

Os utilizadores desta função podem resolver problemas de comunicação dentro do Microsoft Teams & Skype for Business utilizando as ferramentas de resolução de problemas de chamadas de utilizador no Centro de Administração & Skype para empresas da Microsoft Teams. Os utilizadores desta função podem ver informações completas do registo de chamadas para todos os participantes envolvidos. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

### <a name="teams-communications-support-specialist"></a>[Especialista em Apoio às Comunicações das Equipas](#teams-communications-support-specialist-permissions)

Os utilizadores desta função podem resolver problemas de comunicação dentro do Microsoft Teams & Skype for Business utilizando as ferramentas de resolução de problemas de chamadas de utilizador no Centro de Administração & Skype para empresas da Microsoft Teams. Os utilizadores desta função só podem ver os detalhes do utilizador na chamada para o utilizador específico que procuraram. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

### <a name="teams-devices-administrator"></a>[Administrador de dispositivos de equipas](#teams-devices-administrator-permissions)

Os utilizadores com esta função podem gerir [dispositivos certificados por Equipas](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) do Centro de Administração de Equipas. Esta função permite visualizar todos os dispositivos num só olhar, com capacidade de pesquisar e filtrar dispositivos. O utilizador pode verificar detalhes de cada dispositivo, incluindo conta iniciada, e modelo do dispositivo. O utilizador pode alterar as definições do dispositivo e atualizar as versões do software. Esta função não concede permissões para verificar a atividade das Equipas e chamar a qualidade do dispositivo. 

### <a name="teams-service-administrator"></a>[Administrador de Serviço de Equipas](#teams-service-administrator-permissions)

Os utilizadores desta função podem gerir todos os aspetos da carga de trabalho das Equipas Microsoft através das Equipas microsoft & Skype para centro de administração de negócios e os respetivos módulos PowerShell. Isto inclui, entre outras áreas, todas as ferramentas de gestão relacionadas com a telefonia, mensagens, reuniões e as próprias equipas. Esta função também oferece a capacidade de criar e gerir todos os grupos Microsoft 365, gerir bilhetes de suporte e monitorizar a saúde do serviço.

### <a name="user-administrator"></a>[Administrador do Utilizador](#user-administrator-permissions)

Os utilizadores com esta função podem criar utilizadores e gerir todos os aspetos dos utilizadores com algumas restrições (ver tabela) e podem atualizar as políticas de validade da palavra-passe. Além disso, os utilizadores com esta função podem criar e gerir todos os grupos. Esta função inclui também a capacidade de criar e gerir as vistas dos utilizadores, gerir bilhetes de apoio e monitorizar a saúde do serviço. Os administradores do utilizador não têm permissão para gerir algumas propriedades do utilizador para os utilizadores na maioria das funções de administrador. O utilizador com esta função não tem permissões para gerir o MFA. As funções que são exceções a esta restrição estão listadas no quadro seguinte.

| **Permissão** | **Pode fazer** |
| --- | --- |
|Permissões gerais|<p>Criar utilizadores e grupos</p><p>Criar e gerir vistas de utilizador</p><p>Gerir bilhetes de apoio ao Escritório<p>Atualizar as políticas de expiração da palavra-passe|
| <p>Em todos os utilizadores, incluindo todos os administradores</p>|<p>Gerir licenças</p><p>Gerir todas as propriedades do utilizador, exceto o nome principal do utilizador</p>
| Apenas em utilizadores que não sejam administradores ou em qualquer uma das seguintes funções de administração limitadas:<ul><li>Leitores de Diretório<li>Administrador de Grupos<li>Convidado Convidado<li>Administrador helpdesk<li>Leitor do Centro de Mensagens<li>Administrador de password<li>Leitor de Relatórios<li>Administrador de Utilizadores|<p>Eliminar e restaurar</p><p>Desativar e ativar</p><p>Tokens de atualização invalidado</p><p>Gerir todas as propriedades do utilizador, incluindo o nome principal do utilizador</p><p>Repor palavra-passe</p><p>Teclas de dispositivo de atualização (FIDO)</p>|

> [!IMPORTANT]
> Os utilizadores com esta função podem alterar palavras-passe para pessoas que possam ter acesso a informações sensíveis ou privadas ou configuração crítica dentro e fora do Azure Ative Directory. Alterar a palavra-passe de um utilizador pode significar a capacidade de assumir a identidade e permissões desse utilizador. Por exemplo:
>
>- Os proprietários de Registo de Aplicações e Aplicações Empresariais, que podem gerir credenciais de apps que possuam. Essas aplicações podem ter permissões privilegiadas em Azure AD e em outros lugares não concedidos aos Administradores de Utilizadores. Por este caminho, um Administrador de Utilizador poderá assumir a identidade de um titular da aplicação e, em seguida, assumir a identidade de uma aplicação privilegiada atualizando as credenciais para a aplicação.
>- Proprietários de subscrições Azure, que podem ter acesso a informações sensíveis ou privadas ou configuração crítica em Azure.
>- Security Group e Microsoft 365 proprietários do grupo, que podem gerir a adesão ao grupo. Esses grupos podem conceder acesso a informações sensíveis ou privadas ou configuração crítica em Azure AD e em outros lugares.
>- Administradores em outros serviços fora da Azure AD como Exchange Online, Office Security and Compliance Center, e sistemas de recursos humanos.
>- Não administradores como executivos, advogados e funcionários de recursos humanos que possam ter acesso a informações confidenciais ou privadas.

## <a name="role-permissions"></a>Permissões de função

As tabelas que se seguem descrevem as permissões específicas no Azure Ative Directory dadas a cada papel. Algumas funções podem ter permissões adicionais nos serviços da Microsoft fora do Azure Ative Directory.

### <a name="application-administrator-permissions"></a>Permissões de Administrador de Aplicação

Pode criar e gerir todos os aspetos dos registos de aplicações e aplicações empresariais.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/aplicação/appProxyAuthentication/update | Atualizar propriedades de autenticação de aplicativo proxy em diretores de serviço em Azure Ative Directy. |
| microsoft.diretório/aplicação/appProxyUrlSettings/update | Atualizar urls interno e externo de aplicação no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/aplicaçãoProxy/read | Leia todas as propriedades da App Proxy. |
| microsoft.diretório/aplicações/applicationProxy/update | Atualize todas as propriedades do App Proxy. |
| microsoft.diretório/aplicações/audiência/atualização | Atualizar aplicações.propriedade audiência em Azure Ative Directory. |
| microsoft.diretório/aplicações/autenticação/atualização | Atualizar aplicações.autenticação propriedade no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas em aplicações no Azure Ative Directory. |
| microsoft.diretório/aplicações/criar | Criar aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/credenciais/atualização | Atualizar aplicações.credenciais propriedade em Azure Ative Directory. |
| microsoft.diretório/aplicações/delete | Eliminar aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/proprietários/atualização | Atualizar aplicações.propriedade dos proprietários em Azure Ative Directory. |
| microsoft.diretório/aplicações/permissões/atualização | Atualizar aplicações.permissões propriedade em Azure Ative Directory. |
| microsoft.diretório/aplicações/políticas/atualização | Atualizar aplicações.apólices propriedade em Azure Ative Directory. |
| microsoft.diretório/appRoleAssignments/create | Crie apreciações apreciá-lo no Diretório Ativo Azure. |
| microsoft.diretório/appRoleAssignments/read | Leia as assinaturas do appRoleAss no Diretório Ativo Azure. |
| microsoft.diretório/appRoleAssignments/update | Atualizar apreciamentos AlooleAssignments no Azure Ative Directory. |
| microsoft.diretório/appRoleAssignments/delete | Elimine as assinaturas do AppRoleAss no Diretório Ativo Azure. |
| microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs no Azure Ative Directory. |
| microsoft.diretório/conectorGroups/allProperties/read | Leia as propriedades do grupo de conector proxy de aplicação no Azure Ative Directory. |
| microsoft.diretório/conectorGroups/allProperties/update | Atualize todas as propriedades do grupo de conector proxy de aplicação em Azure Ative Directory. |
| microsoft.diretório/conectorGroups/criar | Criar grupos de conector de procuração de aplicação no Diretório Ativo Azure. |
| microsoft.diretório/conectorGroups/delete | Eliminar grupos de conector de procuração de aplicações no Diretório Ativo Azure. |
| microsoft.diretório/conectores/allProperties/read | Leia todas as propriedades do conector proxy de aplicação no Azure Ative Directory. |
| microsoft.diretório/conectores/criar | Criar conectores de procuração de aplicação no Diretório Ativo Azure. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/basic/read | Ler políticas.aplicaçãoConfiguration propriedade em Azure Ative Directory. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/basic/update | Atualização políticas.aplicaçãoConfiguration propriedade em Azure Ative Directory. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/create | Criar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/delete | Eliminar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/owners/read | Ler políticas.aplicaçãoConfiguration propriedade em Azure Ative Directory. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/owners/update | Atualização políticas.aplicaçãoConfiguration propriedade em Azure Ative Directory. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/policyAppliedTo/read | Ler políticas.aplicaçãoConfiguration propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update | Serviço de actualizaçãoPrincipals.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignments/update | Serviço de actualizaçãoPrincipals.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/audience/update | Serviço de actualizaçãoPrincipals.propriedade do público em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/autenticação/atualização | Serviço de actualizaçãoPrincipals.authentication property in Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/básico/atualização | Atualizar propriedades básicas em serviçoPrincipals em Azure Ative Directy. |
| microsoft.diretório/serviçoPrincipals/criar | Criar serviçoPrincipals em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/credenciais/atualização | Serviço de actualizaçãoPrincipals.credenciais propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/delete | Eliminar serviçoPrincipals em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/proprietários/atualização | Serviço de atualizaçãoPrincipals.proprietários propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/permissões/atualização | Serviço de actualizaçãoPrincipals.permissões propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/políticas/atualização | Serviço de actualizaçãoPrincipals.políticas propriedade em Azure Ative Directory. |
| microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

### <a name="application-developer-permissions"></a>Permissões do Desenvolvedor de Aplicações

Pode criar registos de aplicações independentemente da definição de "Os Utilizadores podem registar as aplicações".

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/aplicações/createAsOwner | Criar aplicações no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta contra a quota de objetos criados pelo criador. |
| microsoft.diretório/appRoleAssignments/createAsOwner | Crie apreciações apreciá-lo no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta contra a quota de objetos criados pelo criador. |
| microsoft.diretório/oAuth2PermissionGrants/createAsOwner | Criar oAuth2PermissionGrants no Azure Ative Directory. O criador é adicionado como o primeiro proprietário, e o objeto criado conta contra a quota de objetos criados pelo criador. |
| microsoft.diretório/serviçoPrincipals/createAsOwner | Criar serviçoPrincipals em Azure Ative Directory. O criador é adicionado como o primeiro proprietário, e o objeto criado conta contra a quota de objetos criados pelo criador. |

### <a name="authentication-administrator-permissions"></a>Permissões do Administrador de Autenticação

Permitido visualizar, definir e redefinir informações do método de autenticação para qualquer utilizador não administrado.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Invalidar todos os tokens de atualização do utilizador no Azure Ative Directory. |
| microsoft.diretório/utilizadores/strongAuthentication/update | Atualizar propriedades de autenticação forte como informações credenciais MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.diretório/utilizadores/password/atualização | Atualizar palavras-passe para todos os utilizadores da organização Microsoft 365. Consulte a documentação online para obter mais detalhes. |

### <a name="azure-devops-administrator-permissions"></a>Permissões de administrador da Azure DevOps

Pode gerir a política e configurações da organização Azure DevOps.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a [descrição da função](#azure-devops-administrator) acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | Leia e configuure Azure DevOps. |

### <a name="azure-information-protection-administrator-permissions"></a>Permissões do Administrador de Proteção de Informação do Azure

Pode gerir todos os aspetos do serviço de Proteção de Informação Azure.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a [descrição da função](#) acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerir todos os aspetos da Proteção de Informação Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>Permissões de administrador de chaves B2C IEF

Gerir segredos para a federação e encriptação no Quadro de Experiência de Identidade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Leia e configuure conjuntos de chaves no Azure Ative Directory B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>Permissões de administrador de política B2C IEF

Criar e gerir políticas-quadro de confiança no Quadro de Experiência de Identidade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Leia e configuure políticas personalizadas no Azure Ative Directory B2C. |

### <a name="billing-administrator-permissions"></a>Permissões de administrador de faturação

Pode executar tarefas comuns relacionadas com a faturação, como atualizar informações de pagamento.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/organização/básico/atualização | Atualizar propriedades básicas na organização no Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.commerce.billing/allEntities/allTasks | Gerir todos os aspetos da faturação. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

### <a name="cloud-application-administrator-permissions"></a>Permissões de administrador de aplicação em nuvem

Pode criar e gerir todos os aspetos dos registos de aplicações e aplicações empresariais, exceto App Proxy.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/aplicações/audiência/atualização | Atualizar aplicações.propriedade audiência em Azure Ative Directory. |
| microsoft.diretório/aplicações/autenticação/atualização | Atualizar aplicações.autenticação propriedade no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas em aplicações no Azure Ative Directory. |
| microsoft.diretório/aplicações/criar | Criar aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/credenciais/atualização | Atualizar aplicações.credenciais propriedade em Azure Ative Directory. |
| microsoft.diretório/aplicações/delete | Eliminar aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/proprietários/atualização | Atualizar aplicações.propriedade dos proprietários em Azure Ative Directory. |
| microsoft.diretório/aplicações/permissões/atualização | Atualizar aplicações.permissões propriedade em Azure Ative Directory. |
| microsoft.diretório/aplicações/políticas/atualização | Atualizar aplicações.apólices propriedade em Azure Ative Directory. |
| microsoft.diretório/appRoleAssignments/create | Crie apreciações apreciá-lo no Diretório Ativo Azure. |
| microsoft.diretório/appRoleAssignments/update | Atualizar apreciamentos AlooleAssignments no Azure Ative Directory. |
| microsoft.diretório/appRoleAssignments/delete | Elimine as assinaturas do AppRoleAss no Diretório Ativo Azure. |
| microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs no Azure Ative Directory. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/create | Criar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/basic/read | Ler políticas.aplicaçãoConfiguration propriedade em Azure Ative Directory. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/basic/update | Atualização políticas.aplicaçãoConfiguration propriedade em Azure Ative Directory. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/delete | Eliminar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/owners/read | Ler políticas.aplicaçãoConfiguration propriedade em Azure Ative Directory. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/owners/update | Atualização políticas.aplicaçãoConfiguration propriedade em Azure Ative Directory. |
| microsoft.diretório/políticas/aplicaçãoConfiguration/policyAppliedTo/read | Ler políticas.aplicaçãoConfiguration propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update | Serviço de actualizaçãoPrincipals.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignments/update | Serviço de actualizaçãoPrincipals.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/audience/update | Serviço de actualizaçãoPrincipals.propriedade do público em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/autenticação/atualização | Serviço de actualizaçãoPrincipals.authentication property in Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/básico/atualização | Atualizar propriedades básicas em serviçoPrincipals em Azure Ative Directy. |
| microsoft.diretório/serviçoPrincipals/criar | Criar serviçoPrincipals em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/credenciais/atualização | Serviço de actualizaçãoPrincipals.credenciais propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/delete | Eliminar serviçoPrincipals em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/proprietários/atualização | Serviço de atualizaçãoPrincipals.proprietários propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/permissões/atualização | Serviço de actualizaçãoPrincipals.permissões propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/políticas/atualização | Serviço de actualizaçãoPrincipals.políticas propriedade em Azure Ative Directory. |
| microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

### <a name="cloud-device-administrator-permissions"></a>Permissões de administrador de dispositivos de nuvem

Acesso total a dispositivos de gestão em Azure AD.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs no Azure Ative Directory. |
| microsoft.diretório/dispositivos/bitLockerRecoveryKeys/read | Leia a propriedade dispositivos.bitLockerRecoveryKeys no Azure Ative Directory. |
| microsoft.diretório/dispositivos/delete | Eliminar dispositivos no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/desativar | Desativar dispositivos no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/enable | Ativar dispositivos no Diretório Ativo Azure. |
| microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |

### <a name="company-administrator-permissions"></a>Permissões de Administrador da Empresa

Pode gerir todos os aspetos dos serviços Azure AD e Microsoft que utilizam identidades AZure AD. Este papel também é conhecido como o papel de Administrador Global. 

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize propriedades padrão em microsoft.aad.cloudAppSecurity. |
| microsoft.diretório/administrativoSIns/allProperties/allTasks | Criar e eliminar unidades administrativas e ler e atualizar todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/aplicações/allProperties/allTasks | Criar e eliminar aplicações e ler e atualizar todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/appRoleAssignments/allProperties/allTasks | Criar e apagar apreciamentos apreciá-lo e ler e atualizar todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs no Azure Ative Directory. |
| microsoft.diretório/contactos/allProperties/allTasks | Crie e elimine os contactos e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.diretório/contratos/allProperties/allTasks | Crie e elimine contratos e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/allProperties/allTasks | Crie e elimine os dispositivos e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.diretório/directórioRoles/allProperties/allTasks | Crie e elimine o directórioRoles e leia e atualize todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/directórioRoleTemplates/allProperties/allTasks | Crie e elimine os DirectóriosRoleTemplates e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.diretório/domínios/allProperties/allTasks | Criar e eliminar domínios e ler e atualizar todas as propriedades no Diretório Ativo Azure. |
| microsoft.diretório/grupos/allProperties/allTasks | Criar e eliminar grupos e ler e atualizar todas as propriedades no Diretório Ativo Azure. |
| microsoft.diretório/gruposAssignableToRoles/allProperties/update | Atualizar grupos com propriedade IsAssignableToRole definida para ser verdadeira no Azure Ative Directory. |
| microsoft.diretório/gruposAssignableToRoles/create | Criar grupos com propriedade isAssignableToRole definida para verdade no Azure Ative Directory. |
| microsoft.diretório/gruposAssignableToRoles/delete | Eliminar grupos com propriedade isAssignableToRole definida para ser verdadeira no Azure Ative Directory. |
| microsoft.diretório/grupoSettings/allProperties/allTasks | Criar e eliminar gruposSettings e ler e atualizar todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/grupoSettingTemplates/allProperties/allTasks | Criar e eliminar grupoSettingTemplates e ler e atualizar todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/loginTenantBranding/allProperties/allTasks | Crie e elimine o loginTenantBranding e leia e atualize todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/oAuth2PermissionGrants/allProperties/allTasks | Criar e eliminar oAuth2PermissionGrants e ler e atualizar todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/organização/allProperties/allTasks | Criar e eliminar organização, e ler e atualizar todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/políticas/allProperties/allTasks | Criar e eliminar políticas e ler e atualizar todas as propriedades no Diretório Ativo Azure. |
| microsoft.diretório/papelAssignments/allProperties/allTasks | Criar e eliminar funçõesAsignments e ler e atualizar todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/roleDefinitions/allProperties/allTasks | Criar e eliminar funçõesDefinitions e ler e atualizar todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/scopedRoleMemberships/allProperties/allTasks | Criar e eliminar as empresas scopedRoleMemberberships e ler e atualizar todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/serviçoAction/activateService | Pode realizar a ação de serviço Activateservice no Azure Ative Directory |
| microsoft.directy/serviceAction/disableDirectDirectfature | Pode realizar a ação de serviço de desativação de condutas no Diretório Ativo Azure |
| microsoft.directy/serviceAction/enableDirectoryFeature | Pode executar a ação de serviço Enabledirectoryfeature no Azure Ative Directory |
| microsoft.diretório/serviçoAction/getAvailableExtentionProperties | Pode realizar a ação de serviço de propriedades de propriedades de retenção disponível no Azure Ative Directory |
| microsoft.diretório/serviçoPrincipals/allProperties/allTasks | Criar e eliminar serviçosPrincipals e ler e atualizar todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.diretório/subscritoSkus/allProperties/allTasks | Criar e eliminar subscriçõesSkus e ler e atualizar todas as propriedades no Azure Ative Directory. |
| microsoft.diretório/utilizadores/allProperties/allTasks | Criar e eliminar utilizadores e ler e atualizar todas as propriedades no Azure Ative Directory. |
| microsoft.directySync/allEntities/allTasks | Execute todas as ações no Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Criar e eliminar todos os recursos e ler e atualizar propriedades padrão em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Leia todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Leia todos os recursos em microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerir todos os aspetos da Proteção de Informação Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.commerce.billing/allEntities/allTasks | Gerir todos os aspetos da faturação. |
| microsoft.intune/allEntities/allTasks | Gerir todos os aspetos do Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gerir todos os aspetos do Office 365 Compliance Manager |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerir todos os aspetos do Desktop Analytics. |
| microsoft.office365.exchange/allEntities/allTasks | Gerir todos os aspetos do Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Gerir todos os aspetos do Lockbox de clientes do Office 365 |
| microsoft.office365.messageCenter/messages/read | Leia as mensagens no microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leia segurançaSessages em microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Gerir todos os aspetos do Centro de Proteção do Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Criar e eliminar todos os recursos e ler e atualizar propriedades padrão em microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize propriedades padrão em microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerir todos os aspetos do Skype para business online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de utilização do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerir todos os aspetos da Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerir todos os aspetos do Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Leia todos os recursos em microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator-permissions"></a>Permissões do Administrador de Conformidade

Pode ler e gerir a configuração e relatórios de conformidade em Azure AD e Microsoft 365.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gerir todos os aspetos do Office 365 Compliance Manager |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="compliance-data-administrator-permissions"></a>Permissões de Administrador de Dados de Conformidade

Cria e gere o conteúdo de conformidade.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy.cloudAppSecurity/allEntities/allTasks | Leia e configuure a Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerir todos os aspetos da Proteção de Informação Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gerir todos os aspetos do Office 365 Compliance Manager |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="conditional-access-administrator-permissions"></a>Permissões de administrador de acesso condicional

Pode gerir as capacidades de Acesso Condicional.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/políticas/condicionalAccess/basic/read | Ler políticas.conditionalAposs propriedade em Azure Ative Directory. |
| microsoft.diretório/políticas/condicionalAccess/basic/update | Atualizar políticas.conditionalApoiaçãoalolo em Azure Ative Directory. |
| microsoft.diretório/políticas/condicionalAccess/create | Criar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/condicionalAccess/delete | Eliminar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/condicionalAccess/owners/read | Ler políticas.conditionalAposs propriedade em Azure Ative Directory. |
| microsoft.diretório/políticas/condicionalAccess/owners/update | Atualizar políticas.conditionalApoiaçãoalolo em Azure Ative Directory. |
| microsoft.diretório/políticas/condicionalAccess/policiesAppliedTo/read | Ler políticas.conditionalAposs propriedade em Azure Ative Directory. |
| microsoft.diretório/políticas/condicionalAccess/tenantDefault/update | Atualizar políticas.conditionalApoiaçãoalolo em Azure Ative Directory. |

### <a name="crm-service-administrator-permissions"></a>Permissões de administrador de serviços crm

Pode gerir todos os aspetos do produto Dynamics 365.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerir todos os aspetos da Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

### <a name="customer-lockbox-access-approver-permissions"></a>Permissões do aprovador de acesso ao cliente LockBox

Pode aprovar pedidos de suporte da Microsoft para aceder a dados organizacionais do cliente.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Gerir todos os aspetos do Lockbox de clientes do Office 365 |

### <a name="desktop-analytics-administrator-permissions"></a>Permissões de administrador de análise de ambiente de trabalho

Pode gerir os serviços de Desktop Analytics e Personalização do Office & Policy. Para desktop Analytics, isto inclui a capacidade de visualizar o inventário de ativos, criar planos de implementação, ver implementação e estado de saúde. Para o serviço de personalização do Office & Policy, esta função permite aos utilizadores gerir as políticas do Office.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerir todos os aspetos do Desktop Analytics. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

### <a name="device-administrators-permissions"></a>Permissões de administradores de dispositivos

Os utilizadores destacados para esta função são adicionados ao grupo de administradores locais em dispositivos aderidos a Azure AD.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/grupoSettings/basic/read | Leia propriedades básicas em grupoSettings no Azure Ative Directory. |
| microsoft.diretório/grupoSettingTemplates/básico/ler | Leia propriedades básicas em grupoSettingTemplates no Azure Ative Directory. |

### <a name="directory-readers-permissions"></a>Permissões de Leitores de Diretório
Pode ler informações básicas de diretório. Para a concessão de acesso a aplicações, não destinadas aos utilizadores.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/administrativoSSunits/básico/ler | Leia propriedades básicas em unidades administrativas no Diretório Ativo Azure. |
| microsoft.diretório/administrativoSIns/membros/ler | Leia a propriedade administrativeUnnits.members no Azure Ative Directory. |
| microsoft.diretório/aplicações/básico/ler | Leia propriedades básicas em aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/proprietários/ler | Leia aplicações.propriedade dos proprietários em Azure Ative Directory. |
| microsoft.diretório/aplicações/políticas/ler | Leia aplicações.políticas propriedade em Azure Ative Directory. |
| microsoft.diretório/contactos/básico/ler | Leia propriedades básicas em contactos no Diretório Ativo Azure. |
| microsoft.diretório/contactos/memberOf/read | Ler contactos.memberOf propriedade no Azure Ative Directory. |
| microsoft.diretório/contratos/básico/ler | Leia propriedades básicas em contratos no Azure Ative Directory. |
| microsoft.diretório/dispositivos/básico/ler | Leia propriedades básicas em dispositivos no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/memberOf/read | Leia dispositivos.memberOf propriedade no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/registadosSowners/read | Leia dispositivos.registre propriedade proprietário em Azure Ative Directory. |
| microsoft.diretório/dispositivos/registros/ler | Leia dispositivos.registre a propriedade dos Ussers no Diretório Ativo Azure. |
| microsoft.diretório/directórioRoles/básico/ler | Leia propriedades básicas no directórioRoles no Diretório Azure Ative. |
| microsoft.diretório/directórioRoles/elegíveisMembers/read | Leia a propriedade do diretório.elegível Em Azure Ative Directory. |
| microsoft.diretório/directórioRoles/membros/ler | Leia a propriedade do diretório.members no Azure Ative Directory. |
| microsoft.diretório/domínios/básico/ler | Leia propriedades básicas em domínios no Diretório Ativo Azure. |
| microsoft.diretório/grupos/appRoleAssignments/read | Ler propriedade grupos.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/grupos/básico/ler | Leia propriedades básicas em grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/memberOf/read | Ler grupos.memberOf propriedade em Azure Ative Directory. |
| microsoft.diretório/grupos/membros/ler | Leia a propriedade grupos.members no Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/ler | Leia a propriedade grupos.proprietários em Azure Ative Directory. |
| microsoft.diretório/grupos/definições/ler | Leia a propriedade grupos.settings no Azure Ative Directory. |
| microsoft.diretório/grupoSettings/basic/read | Leia propriedades básicas em grupoSettings no Azure Ative Directory. |
| microsoft.diretório/grupoSettingTemplates/básico/ler | Leia propriedades básicas em grupoSettingTemplates no Azure Ative Directory. |
| microsoft.diretório/oAuth2PermissionGrants/basic/read | Leia propriedades básicas em oAuth2PermissionGrants em Azure Ative Directory. |
| microsoft.diretório/organização/básico/ler | Leia propriedades básicas sobre organização no Azure Ative Directory. |
| microsoft.diretório/organização/trustedCAsForPasslessAuth/read | Leia a propriedade do Site Azure Ative. |
| microsoft.diretório/papelAssinhas/básico/ler | Leia propriedades básicas sobre funçõesAssinsignments no Azure Ative Directory. |
| microsoft.diretório/roleDefinitions/basic/read | Leia propriedades básicas sobre funDefinitions no Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/read | Leia o serviçoPrincipals.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignments/read | Leia o serviçoPrincipals.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/básico/ler | Leia propriedades básicas em serviçoPrincipals em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/memberOf/read | Ler serviçoPrincipals.memberOf propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/oAuth2PermissionGrants/basic/read | Leia o serviçoPrincipals.oAuth2PermissionGrants propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/ownedObjects/read | Ler serviçoPrincipals.ownedObjects propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/proprietários/ler | Ler serviçoPrincipals.proprietários propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/políticas/ler | Ler serviçoPrincipals.policies propriedade em Azure Ative Directory. |
| microsoft.diretório/subscritoSkus/básico/ler | Leia propriedades básicas em SubscriçãoSkus em Azure Ative Directory. |
| microsoft.diretório/utilizadores/appRoleAssignments/read | Leia a propriedade users.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/utilizadores/básico/ler | Leia propriedades básicas nos utilizadores no Azure Ative Directory. |
| microsoft.directy/users/directReports/read | Leia a propriedade users.directReports em Azure Ative Directory. |
| microsoft.diretório/utilizadores/manager/read | Leia a propriedade users.manager no Azure Ative Directory. |
| microsoft.diretório/utilizadores/membroOf/read | Leia utilizadores.membroSDes propriedade em Azure Ative Directory. |
| microsoft.diretório/utilizadores/oAuth2PermissionGrants/basic/read | Leia a propriedade users.oAuth2PermissionGrants em Azure Ative Directory. |
| microsoft.diretório/utilizadores/propriedadeDevices/ler | Leia a propriedade de utilizadores.propriedadeDevices em Azure Ative Directory. |
| microsoft.diretório/utilizadores/ownedObjects/read | Leia a propriedade usumente utilizadores.ownjects em Azure Ative Directory. |
| microsoft.diretório/utilizadores/registradoDevices/read | Leia utilizadores.registradoDevices propriedade em Azure Ative Directory. |

### <a name="directory-synchronization-accounts-permissions"></a>Permissões de Contas de Sincronização do Diretório

Utilizado apenas pelo serviço Azure AD Connect.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/organização/dirSync/update | Atualizar propriedade da organização.dirSync em Azure Ative Directory. |
| microsoft.diretório/políticas/criar | Criar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/excluir | Eliminar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/básico/ler | Leia propriedades básicas sobre políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/básico/atualização | Atualizar propriedades básicas sobre políticas no Azure Ative Directory. |
| microsoft.diretório/políticas/proprietários/ler | Leia a propriedade política.proprietários em Azure Ative Directory. |
| microsoft.diretório/políticas/proprietários/atualização | Atualizar apólices.propriedade dos proprietários em Azure Ative Directory. |
| microsoft.diretório/políticas/políticasAppliedTo/read | Ler políticas.policiesAppliedTo propriedade em Azure Ative Directory. |
| microsoft.diretório/políticas/tenantDefault/update | Atualizar políticas.tenantdefault propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/read | Leia o serviçoPrincipals.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update | Serviço de actualizaçãoPrincipals.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignments/read | Leia o serviçoPrincipals.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignments/update | Serviço de actualizaçãoPrincipals.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/audience/update | Serviço de actualizaçãoPrincipals.propriedade do público em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/autenticação/atualização | Serviço de actualizaçãoPrincipals.authentication property in Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/básico/ler | Leia propriedades básicas em serviçoPrincipals em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/básico/atualização | Atualizar propriedades básicas em serviçoPrincipals em Azure Ative Directy. |
| microsoft.diretório/serviçoPrincipals/criar | Criar serviçoPrincipals em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/credenciais/atualização | Serviço de actualizaçãoPrincipals.credenciais propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/memberOf/read | Ler serviçoPrincipals.memberOf propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/oAuth2PermissionGrants/basic/read | Leia o serviçoPrincipals.oAuth2PermissionGrants propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/proprietários/ler | Ler serviçoPrincipals.proprietários propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/proprietários/atualização | Serviço de atualizaçãoPrincipals.proprietários propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/ownedObjects/read | Ler serviçoPrincipals.ownedObjects propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/permissões/atualização | Serviço de actualizaçãoPrincipals.permissões propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/políticas/ler | Ler serviçoPrincipals.policies propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/políticas/atualização | Serviço de actualizaçãoPrincipals.políticas propriedade em Azure Ative Directory. |
| microsoft.directySync/allEntities/allTasks | Execute todas as ações no Azure AD Connect. |

### <a name="directory-writers-permissions"></a>Permissões de Escritores de Diretórios

Pode ler & escrever informações básicas de diretório. Para a concessão de acesso a aplicações, não destinadas aos utilizadores.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/grupos/appRoleAssignments/update | Atualizar a propriedade grupos.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/grupos/assignLicense | Gerir licenças em grupos no Azure Ative Directory. |
| microsoft.diretório/grupos/básico/atualização | Atualizar propriedades básicas em grupos no Azure Ative Directory.  |
| microsoft.diretório/grupos/classificação/atualização | Atualizar a propriedade de classificação do grupo no Azure Ative Directory. |
| microsoft.diretório/grupos/criar | Criar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/grupoType/atualização | Atualizar a propriedade do grupoType de um grupo no Azure Ative Directory. |
| microsoft.diretório/grupos/membros/atualização | Atualizar propriedade grupos.membros em Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/atualização | Atualizar propriedade de grupos.proprietários em Azure Ative Directory. |
| microsoft.diretório/grupos/reprocessLicenseAssignment | Reprocesse as atribuições de licença para um grupo no Azure Ative Directory. |
| microsoft.diretório/grupos/securityEnabled/update | Atualize a propriedade secutiryEnabled de um grupo no Azure Ative Directory. |
| microsoft.diretório/grupos/definições/atualização | Atualizar a propriedade grupos.configurações em Azure Ative Directory. |
| microsoft.diretório/grupos/visibilidade/atualização | Atualizar a propriedade de visibilidade do grupo |
| microsoft.diretório/grupoSettings/basic/update | Atualizar propriedades básicas em grupoSettings no Azure Ative Directory. |
| microsoft.diretório/grupoSettings/create | Criar conjuntosS no Azure Ative Directory.. |
| microsoft.diretório/grupoSettings/delete | Eliminar gruposSettings no Azure Ative Directory. |
| microsoft.diretório/oAuth2PermissionGrants/basic/update | Atualizar propriedades básicas de oAuth2PermissionGrants em Azure Ative Directory. |
| microsoft.diretório/oAuth2PermissionGrants/create | Criar oAuth2PermissionGrants no Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/synchronizationCredentiss/manage | Gerir a aplicação que prevê segredos e credenciais. |
| microsoft.diretório/serviçoPrincipals/sincronizaçãoJobs/gerir | Iniciar, reiniciar e interromper a aplicação de provisão de trabalhos de sincronização. |
| microsoft.diretório/serviçoPrincipals/sincronizaçãoSchema/gerir | Criar e gerir os trabalhos de sincronização e esquemas de sincronização de aplicações. |
| microsoft.diretório/utilizadores/appRoleAssignments/update | Atualizar a propriedade users.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/utilizadores/assignLicense | Gerir licenças em utilizadores no Azure Ative Directory. |
| microsoft.diretório/utilizadores/básico/atualização | Atualizar propriedades básicas nos utilizadores no Azure Ative Directory. |
| microsoft.diretório/utilizadores/desativar | Desative uma conta de utilizador no Azure Ative Directory. |
| microsoft.diretório/utilizadores/enable | Ativar uma conta de utilizador no Azure Ative Directory |
| microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Invalidar todos os tokens de atualização do utilizador no Azure Ative Directory, exigindo que os utilizadores reautençam no seu próximo senting |
| microsoft.diretório/utilizadores/gestor/atualização | Atualizar propriedade users.manager em Azure Ative Directory. |
| microsoft.diretório/utilizadores/reprocessLicenseAssignment | Reprocesse as atribuições de licença para um utilizador no Azure Ative Directory. |
| microsoft.diretório/utilizadores/userPrincipalName /update | Atualize a propriedade users.userPrincipalName em Azure Ative Directory. |

### <a name="exchange-service-administrator-permissions"></a>Permissões de Administrador de Serviço de Intercâmbio

Pode gerir todos os aspetos do produto Exchange.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.diretório/grupos/uned/appRoleAssignments/update | Atualizar grupos.propriedade unificada em Azure Ative Directory. |
| microsoft.diretório/grupos/unificado/básico/atualização | Atualizar propriedades básicas dos grupos Microsoft 365. |
| microsoft.diretório/grupos/unificado/criar | Crie grupos Microsoft 365. |
| microsoft.diretório/grupos/unificado/delete | Elimine os grupos Microsoft 365. |
| microsoft.diretório/grupos/unificado/membros/atualização | Atualizar a adesão aos grupos Microsoft 365. |
| microsoft.diretório/grupos/unificado/proprietários/atualização | Atualizar a propriedade dos grupos Microsoft 365. |
| microsoft.office365.exchange/allEntities/allTasks | Gerir todos os aspetos do Exchange Online. |
| microsoft.office365.network/performance/allProperties/read | Leia as páginas de desempenho da rede no Microsoft 365 Admin Center. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de utilização do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="external-id-user-flow-administrator-permissions"></a>Permissões externas do administrador de fluxo do utilizador do id

Criar e gerir todos os aspetos dos fluxos de utilizador.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Leia e configuure fluxos de utilizador no Azure Ative Directory B2C. |

### <a name="external-id-user-flow-attribute-administrator-permissions"></a>Permissões externas do iD User Flow Attribute Administrador

Criar e gerir o esquema de atributos disponíveis para todos os fluxos do utilizador.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Leia e configuure os atributos do utilizador no Azure Ative Directory B2C. |

### <a name="external-identity-provider-administrator-permissions"></a>Permissões de administrador de fornecedores de identidade externas

Configure os fornecedores de identidade para utilização na federação direta.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Leia e configugue fornecedores de identidade no Azure Ative Directory B2C. |

### <a name="global-reader-permissions"></a>Permissões globais do leitor
Pode ler tudo o que um Administrador Global pode, mas não editar nada.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a [descrição da função](#global-reader) acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.commerce.billing/allEntities/read    | Leia todos os aspetos da faturação. |
| microsoft.diretório/administrativoSSunits/básico/ler    | Leia propriedades básicas em unidades administrativas no Diretório Ativo Azure. |
| microsoft.diretório/administrativoSIns/membros/ler    | Leia a propriedade administrativeUnnits.members no Azure Ative Directory. |
| microsoft.diretório/aplicações/básico/ler    | Leia propriedades básicas em aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/proprietários/ler    | Leia aplicações.propriedade dos proprietários em Azure Ative Directory. |
| microsoft.diretório/aplicações/políticas/ler    | Leia aplicações.políticas propriedade em Azure Ative Directory. |
| microsoft.diretório/contactos/básico/ler    | Leia propriedades básicas em contactos no Diretório Ativo Azure. |
| microsoft.diretório/contactos/memberOf/read    | Ler contactos.memberOf propriedade no Azure Ative Directory. |
| microsoft.diretório/contratos/básico/ler    | Leia propriedades básicas em contratos no Azure Ative Directory. |
| microsoft.diretório/dispositivos/básico/ler    | Leia propriedades básicas em dispositivos no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/memberOf/read    | Leia dispositivos.memberOf propriedade no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/registadosSowners/read    | Leia dispositivos.registre propriedade proprietário em Azure Ative Directory. |
| microsoft.diretório/dispositivos/registros/ler    | Leia dispositivos.registre a propriedade dos Ussers no Diretório Ativo Azure. |
| microsoft.diretório/directórioRoles/básico/ler    | Leia propriedades básicas no directórioRoles no Diretório Azure Ative. |
| microsoft.diretório/directórioRoles/elegíveisMembers/read    | Leia a propriedade do diretório.elegível Em Azure Ative Directory. |
| microsoft.diretório/directórioRoles/membros/ler    | Leia a propriedade do diretório.members no Azure Ative Directory. |
| microsoft.diretório/domínios/básico/ler    | Leia propriedades básicas em domínios no Diretório Ativo Azure. |
| microsoft.diretório/grupos/appRoleAssignments/read    | Ler propriedade grupos.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/grupos/básico/ler    | Leia propriedades básicas em grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/hiddenMembers/read    | Ler propriedade de grupos.hiddenMembers em Azure Ative Directory. |
| microsoft.diretório/grupos/memberOf/read    | Ler grupos.memberOf propriedade em Azure Ative Directory. |
| microsoft.diretório/grupos/membros/ler    | Leia a propriedade grupos.members no Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/ler    | Leia a propriedade grupos.proprietários em Azure Ative Directory. |
| microsoft.diretório/grupos/definições/ler    | Leia a propriedade grupos.settings no Azure Ative Directory. |
| microsoft.diretório/grupoSettings/basic/read    | Leia propriedades básicas em grupoSettings no Azure Ative Directory. |
| microsoft.diretório/grupoSettingTemplates/básico/ler    | Leia propriedades básicas em grupoSettingTemplates no Azure Ative Directory. |
| microsoft.diretório/oAuth2PermissionGrants/basic/read    | Leia propriedades básicas em oAuth2PermissionGrants em Azure Ative Directory. |
| microsoft.diretório/organização/básico/ler    | Leia propriedades básicas sobre organização no Azure Ative Directory. |
| microsoft.diretório/organização/trustedCAsForPasslessAuth/read    | Leia a propriedade do Site Azure Ative. |
| microsoft.diretório/políticas/standard/read    | Leia as políticas padrão no Diretório Ativo Azure. |
| microsoft.diretório/papelAssinhas/básico/ler    | Leia propriedades básicas sobre funçõesAssinsignments no Azure Ative Directory. |
| microsoft.diretório/roleDefinitions/basic/read    | Leia propriedades básicas sobre funDefinitions no Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/read    | Leia o serviçoPrincipals.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignments/read    | Leia o serviçoPrincipals.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/básico/ler    | Leia propriedades básicas em serviçoPrincipals em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/memberOf/read    | Ler serviçoPrincipals.memberOf propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/oAuth2PermissionGrants/basic/read    | Leia o serviçoPrincipals.oAuth2PermissionGrants propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/ownedObjects/read    | Ler serviçoPrincipals.ownedObjects propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/proprietários/ler    | Ler serviçoPrincipals.proprietários propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/políticas/ler    | Ler serviçoPrincipals.policies propriedade em Azure Ative Directory. |
| microsoft.diretório/signInReports/allProperties/read    | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.diretório/subscritoSkus/básico/ler    | Leia propriedades básicas em SubscriçãoSkus em Azure Ative Directory. |
| microsoft.diretório/utilizadores/appRoleAssignments/read    | Leia a propriedade users.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/utilizadores/básico/ler    | Leia propriedades básicas nos utilizadores no Azure Ative Directory. |
| microsoft.directy/users/directReports/read    | Leia a propriedade users.directReports em Azure Ative Directory. |
| microsoft.diretório/utilizadores/manager/read    | Leia a propriedade users.manager no Azure Ative Directory. |
| microsoft.diretório/utilizadores/membroOf/read    | Leia utilizadores.membroSDes propriedade em Azure Ative Directory. |
| microsoft.diretório/utilizadores/oAuth2PermissionGrants/basic/read    | Leia a propriedade users.oAuth2PermissionGrants em Azure Ative Directory. |
| microsoft.diretório/utilizadores/propriedadeDevices/ler    | Leia a propriedade de utilizadores.propriedadeDevices em Azure Ative Directory. |
| microsoft.diretório/utilizadores/ownedObjects/read    | Leia a propriedade usumente utilizadores.ownjects em Azure Ative Directory. |
| microsoft.diretório/utilizadores/registradoDevices/read    | Leia utilizadores.registradoDevices propriedade em Azure Ative Directory. |
| microsoft.diretório/utilizadores/strongAuthentication/read    | Leia propriedades de autenticação forte como informações credenciais de MFA. |
| microsoft.office365.exchange/allEntities/read    | Leia todos os aspetos do Exchange Online. |
| microsoft.office365.messageCenter/messages/read    | Leia as mensagens no microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read    | Leia segurançaSessages em microsoft.office365.messageCenter. |
| microsoft.office365.network/performance/allProperties/read | Leia as páginas de desempenho da rede no Microsoft 365 Admin Center. |
| microsoft.office365.protectionCenter/allEntities/read    | Leia todos os aspetos do Centro de Proteção do Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/read    | Leia todas as propriedades standard em microsoft.office365.securityComplianceCenter. |
| microsoft.office365.usageReports/allEntities/read    | Leia os relatórios de utilização do Office 365. |
| microsoft.office365.webPortal/allEntities/standard/read    | Leia as propriedades padrão em todos os recursos em microsoft.office365.webPortal. |

### <a name="groups-administrator-permissions"></a>Permissões de administrador de grupos
Pode gerir todos os aspetos de grupos e configurações de grupo, como as políticas de nomeação e expiração.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/grupos/básico/ler | Leia propriedades padrão em Grupos no Diretório Ativo Azure.  |
| microsoft.diretório/grupos/básico/atualização | Atualizar propriedades básicas em grupos no Azure Ative Directory. |
| microsoft.diretório/grupos/criar | Criar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/createAsOwner | Criar grupos no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta contra a quota de objetos criados pelo criador. |
| microsoft.diretório/grupos/delete | Eliminar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/hiddenMembers/read | Ler propriedade de grupos.hiddenMembers em Azure Ative Directory. |
| microsoft.diretório/grupos/membros/atualização | Atualizar propriedade grupos.membros em Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/atualização | Atualizar propriedade de grupos.proprietários em Azure Ative Directory. |
| microsoft.diretório/grupos/restaurar | Restaurar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/definições/atualização | Atualizar a propriedade grupos.configurações em Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.office365.messageCenter/messages/read | Leia as mensagens no microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

### <a name="guest-inviter-permissions"></a>Permissões do Convidado Convidado
Pode convidar utilizadores convidados independentemente da definição de "membros podem convidar os hóspedes".

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/utilizadores/appRoleAssignments/read | Leia a propriedade users.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/utilizadores/básico/ler | Leia propriedades básicas nos utilizadores no Azure Ative Directory. |
| microsoft.directy/users/directReports/read | Leia a propriedade users.directReports em Azure Ative Directory. |
| microsoft.diretório/utilizadores/inviteGuest | Convidar utilizadores convidados no Azure Ative Directory. |
| microsoft.diretório/utilizadores/manager/read | Leia a propriedade users.manager no Azure Ative Directory. |
| microsoft.diretório/utilizadores/membroOf/read | Leia utilizadores.membroSDes propriedade em Azure Ative Directory. |
| microsoft.diretório/utilizadores/oAuth2PermissionGrants/basic/read | Leia a propriedade users.oAuth2PermissionGrants em Azure Ative Directory. |
| microsoft.diretório/utilizadores/propriedadeDevices/ler | Leia a propriedade de utilizadores.propriedadeDevices em Azure Ative Directory. |
| microsoft.diretório/utilizadores/ownedObjects/read | Leia a propriedade usumente utilizadores.ownjects em Azure Ative Directory. |
| microsoft.diretório/utilizadores/registradoDevices/read | Leia utilizadores.registradoDevices propriedade em Azure Ative Directory. |

### <a name="helpdesk-administrator-permissions"></a>Permissões do administrador helpdesk

Pode redefinir palavras-passe para administradores não-administradores e administradores de helpdesk.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/dispositivos/bitLockerRecoveryKeys/read | Leia a propriedade dispositivos.bitLockerRecoveryKeys no Azure Ative Directory. |
| microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Invalidar todos os tokens de atualização do utilizador no Azure Ative Directory. |
| microsoft.diretório/utilizadores/password/atualização | Atualizar palavras-passe para todos os utilizadores no Azure Ative Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

### <a name="hybrid-identity-administrator-permissions"></a>Permissões híbridas de administrador de identidade

Ativar, configurar, configurar, gerir, monitorizar e resolver problemas serviços de fornecimento e autenticação em nuvem. 

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.diretório/aplicações/audiência/atualização  | Atualizar aplicações.propriedade audiência em Azure Ative Directory. |
| microsoft.diretório/aplicações/autenticação/atualização | Atualizar aplicações.autenticação propriedade no Diretório Ativo Azure.  |
| microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas em aplicações no Azure Ative Directory. |
| microsoft.diretório/aplicações/criar | Criar aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/credenciais/atualização | Atualizar aplicações.credenciais propriedade em Azure Ative Directory. |
| microsoft.diretório/aplicações/delete | Eliminar aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/proprietários/atualização | Atualizar aplicações.propriedade dos proprietários em Azure Ative Directory. |
| microsoft.diretório/aplicações/permissões/atualização | Atualizar aplicações.permissões propriedade em Azure Ative Directory. |
| microsoft.diretório/aplicações/políticas/atualização | Atualizar aplicações.apólices propriedade em Azure Ative Directory. |
| microsoft.diretório/aplicaçãoTemplates/instantiate | Aplicações instantâneas de galeria a partir de modelos de aplicação. |
| microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs no Azure Ative Directory. |
| microsoft.directy/cloudProvisioning/allProperties/allTasks | Leia e configuure todas as propriedades do serviço Azure AD Cloud Provisioning. |
| microsoft.diretório/federatedAuthentication/allProperties/allTasks | Gerir todos os aspetos dos Serviços Federados de Diretório Ativo (ADFS) ou do provedor da 3ª federação partidária em Azure AD. |
| microsoft.diretório/organização/dirSync/update | Atualizar propriedade da organização.dirSync em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/audience/update | Serviço de actualizaçãoPrincipals.propriedade do público em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/autenticação/atualização | Serviço de actualizaçãoPrincipals.authentication property in Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/básico/atualização | Atualizar propriedades básicas em serviçoPrincipals em Azure Ative Directy. |
| microsoft.diretório/serviçoPrincipals/criar | Criar serviçoPrincipals em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/credenciais/atualização | Serviço de actualizaçãoPrincipals.credenciais propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/delete | Eliminar serviçoPrincipals em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/proprietários/atualização | Serviço de atualizaçãoPrincipals.proprietários propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/permissões/atualização | Serviço de actualizaçãoPrincipals.permissões propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/políticas/atualização | Serviço de actualizaçãoPrincipals.políticas propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/sincronizaçãoJobs/gerir | Gerir todos os aspetos dos trabalhos de sincronização em Azure AD. |
| microsoft.diretório/serviçoPrincipals/sincronizaçãoSchema/gerir | Gerir todos os aspetos do esquema de sincronização em Azure AD. |
| microsoft.diretório/serviçoPrincipals/synchronizationCredentiss/manage | Gerir todos os aspetos das credenciais de sincronização em Azure AD. |
| microsoft.diretório/serviçoPrincipals/tag/update | Serviço de actualizaçãoPrincipals.tag propriedade em Azure Ative Directory. |
| microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.office365.messageCenter/messages/read | Leia as mensagens no microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

### <a name="insights-administrator-permissions"></a>Permissões de administrador insights

Tem acesso administrativo na aplicação Microsoft 365 Insights. 

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.insights/allEntities/allTasks | Gerir todos os aspetos do Insights. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="insights-business-leader-permissions"></a>Insights Business Leader permissões

Pode ver e partilhar dashboards e insights através da aplicação M365 Insights.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.insights/reports/read | Ver relatórios e dashboard na aplicação Insights. |
| microsoft.insights/programas/update | Implementar e gerir programas na aplicação Insights. |

### <a name="intune-service-administrator-permissions"></a>Permissões de Administrador de Serviço Intune

Pode gerir todos os aspetos do produto Intune.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/contactos/básico/atualização | Atualizar propriedades básicas em contactos no Azure Ative Directory. |
| microsoft.diretório/contactos/criar | Criar contactos no Diretório Ativo Azure. |
| microsoft.diretório/contactos/excluir | Eliminar contactos no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/básico/atualização | Atualizar propriedades básicas em dispositivos no Azure Ative Directory. |
| microsoft.diretório/dispositivos/bitLockerRecoveryKeys/read | Leia a propriedade dispositivos.bitLockerRecoveryKeys no Azure Ative Directory. |
| microsoft.diretório/dispositivos/criar | Criar dispositivos no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/delete | Eliminar dispositivos no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/registadosSowners/update | Atualizar dispositivos.registrar propriedade de Proprietários em Azure Ative Directory. |
| microsoft.diretório/dispositivos/registros/update | Atualizar dispositivos.registrou propriedade dos Ussers no Azure Ative Directory. |
| microsoft.diretório/grupos/appRoleAssignments/update | Atualizar a propriedade grupos.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/grupos/básico/atualização | Atualizar propriedades básicas em grupos no Azure Ative Directory. |
| microsoft.diretório/grupos/criar | Criar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/createAsOwner | Criar grupos no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta contra a quota de objetos criados pelo criador. |
| microsoft.diretório/grupos/delete | Eliminar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/hiddenMembers/read | Ler propriedade de grupos.hiddenMembers em Azure Ative Directory. |
| microsoft.diretório/grupos/membros/atualização | Atualizar propriedade grupos.membros em Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/atualização | Atualizar propriedade de grupos.proprietários em Azure Ative Directory. |
| microsoft.diretório/grupos/restaurar | Restaurar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/definições/atualização | Atualizar a propriedade grupos.configurações em Azure Ative Directory. |
| microsoft.diretório/utilizadores/appRoleAssignments/update | Atualizar a propriedade users.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/utilizadores/básico/atualização | Atualizar propriedades básicas nos utilizadores no Azure Ative Directory. |
| microsoft.diretório/utilizadores/gestor/atualização | Atualizar propriedade users.manager em Azure Ative Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.intune/allEntities/allTasks | Gerir todos os aspetos do Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="kaizala-administrator-permissions"></a>Permissões do Administrador Kaizala

Pode gerir as definições para o Microsoft Kaizala.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia o Centro de Administração Microsoft 365. |

### <a name="license-administrator-permissions"></a>Permissões de Administrador de Licença

Pode gerir licenças de produtos em utilizadores e grupos.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/utilizadores/assignLicense | Gerir licenças em utilizadores no Azure Ative Directory. |
| microsoft.diretório/utilizadores/usageLocation/update | Atualizar utilizadores.usageLotação propriedade em Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |

### <a name="lync-service-administrator-permissions"></a>Permissões de Administrador de Serviço Lync

Pode gerir todos os aspetos do produto Skype for Business.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerir todos os aspetos do Skype para business online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.usageReports/allEntities/read    | Leia os relatórios de utilização do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |


### <a name="message-center-privacy-reader-permissions"></a>Permissões do Leitor de Privacidade do Centro de Mensagens

Pode ler posts do Centro de Mensagens, mensagens de privacidade de dados, grupos, domínios e subscrições.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Leia as mensagens no microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leia segurançaSessages em microsoft.office365.messageCenter. |

### <a name="message-center-reader-permissions"></a>Permissões do Leitor do Centro de Mensagens
Pode ler mensagens e atualizações apenas para a sua organização no Centro de Mensagens. 

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Leia as mensagens no microsoft.office365.messageCenter. |

### <a name="modern-commerce-user-permissions"></a>Permissões do utilizador do comércio moderno
Pode gerir compras comerciais para uma empresa, departamento ou equipa. 

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.commerce.billing/partners/read | Leia propriedade parceira da Microsoft 365 Billing. |
| microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | Gerir todos os aspetos do Centro de Serviços de Licenciamento de Volume. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e veja os bilhetes de apoio do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |


### <a name="network-administrator-permissions"></a>Permissões de administrador de rede
Pode gerir as localizações da rede e rever insights de design de rede empresarial para o Microsoft 365 Software como aplicações de serviço.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.network/performance/allProperties/read | Leia as páginas de desempenho da rede no M365 Admin Center.  |
| microsoft.office365.network/locations/allProperties/allTasks | Leia e configuure as propriedades da rede para cada local. |

### <a name="office-apps-administrator-permissions"></a>Permissões de administrador de aplicações de escritório
Pode gerir os serviços de cloud de aplicações do Office, incluindo a gestão de políticas e definições, e gerir a capacidade de selecionar, desescolhê-lo e publicar conteúdo de funcionalidade "quais as novidades" para os dispositivos do utilizador final.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.office365.messageCenter/messages/read | Leia as mensagens no microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.userCommunication/allEntities/allTasks | Leia e atualize a visibilidade das mensagens What's New. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="partner-tier1-support-permissions"></a>Permissões de suporte do Parceiro Tier1

Não utilizar - não destinado a uso geral.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/contactos/básico/atualização | Atualizar propriedades básicas em contactos no Azure Ative Directory. |
| microsoft.diretório/contactos/criar | Criar contactos no Diretório Ativo Azure. |
| microsoft.diretório/contactos/excluir | Eliminar contactos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/criar | Criar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/createAsOwner | Criar grupos no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta contra a quota de objetos criados pelo criador. |
| microsoft.diretório/grupos/membros/atualização | Atualizar propriedade grupos.membros em Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/atualização | Atualizar propriedade de grupos.proprietários em Azure Ative Directory. |
| microsoft.diretório/utilizadores/appRoleAssignments/update | Atualizar a propriedade users.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/utilizadores/assignLicense | Gerir licenças em utilizadores no Azure Ative Directory. |
| microsoft.diretório/utilizadores/básico/atualização | Atualizar propriedades básicas nos utilizadores no Azure Ative Directory. |
| microsoft.diretório/utilizadores/delete | Eliminar os utilizadores no Diretório Ativo Azure. |
| microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Invalidar todos os tokens de atualização do utilizador no Azure Ative Directory. |
| microsoft.diretório/utilizadores/gestor/atualização | Atualizar propriedade users.manager em Azure Ative Directory. |
| microsoft.diretório/utilizadores/password/atualização | Atualizar palavras-passe para todos os utilizadores no Azure Ative Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.diretório/utilizadores/restaurar | Restaurar os utilizadores eliminados no Azure Ative Directory. |
| microsoft.diretório/utilizadores/userPrincipalName/update | Atualizar a propriedade users.userPrincipalName em Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

### <a name="partner-tier2-support-permissions"></a>Permissões de suporte de parceiro Tier2

Não utilizar - não destinado a uso geral.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/contactos/básico/atualização | Atualizar propriedades básicas em contactos no Azure Ative Directory. |
| microsoft.diretório/contactos/criar | Criar contactos no Diretório Ativo Azure. |
| microsoft.diretório/contactos/excluir | Eliminar contactos no Diretório Ativo Azure. |
| microsoft.diretório/domínios/allTasks | Criar e eliminar domínios e ler e atualizar propriedades padrão no Diretório Ativo Azure. |
| microsoft.diretório/grupos/criar | Criar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/delete | Eliminar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/membros/atualização | Atualizar propriedade grupos.membros em Azure Ative Directory. |
| microsoft.diretório/grupos/restaurar | Restaurar grupos no Diretório Ativo Azure. |
| microsoft.diretório/organização/básico/atualização | Atualizar propriedades básicas na organização no Azure Ative Directory. |
| microsoft.diretório/utilizadores/appRoleAssignments/update | Atualizar a propriedade users.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/utilizadores/assignLicense | Gerir licenças em utilizadores no Azure Ative Directory. |
| microsoft.diretório/utilizadores/básico/atualização | Atualizar propriedades básicas nos utilizadores no Azure Ative Directory. |
| microsoft.diretório/utilizadores/delete | Eliminar os utilizadores no Diretório Ativo Azure. |
| microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Invalidar todos os tokens de atualização do utilizador no Azure Ative Directory. |
| microsoft.diretório/utilizadores/gestor/atualização | Atualizar propriedade users.manager em Azure Ative Directory. |
| microsoft.diretório/utilizadores/password/atualização | Atualizar palavras-passe para todos os utilizadores no Azure Ative Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.diretório/utilizadores/restaurar | Restaurar os utilizadores eliminados no Azure Ative Directory. |
| microsoft.diretório/utilizadores/userPrincipalName/update | Atualizar a propriedade users.userPrincipalName em Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

### <a name="password-administrator-permissions"></a>Permissões de administrador de password

Pode redefinir palavras-passe para administradores não-administradores e password.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/utilizadores/password/atualização | Atualizar palavras-passe para todos os utilizadores no Azure Ative Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="power-bi-service-administrator-permissions"></a>Permissões de administrador de serviço de power BI

Pode gerir todos os aspetos do produto Power BI.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerir todos os aspetos do Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |


### <a name="power-platform-administrator-permissions"></a>Permissões de administrador da plataforma de energia

Pode criar e gerir todos os aspetos do Microsoft Dynamics 365, PowerApps e Power Automamate.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.dynamics365/allEntities/allTasks | Gerir todos os aspetos da Dynamics 365. |
| microsoft.flow/allEntities/allTasks | Gerir todos os aspetos da Power Automamate. |
| microsoft.powerApps/allEntities/allTasks | Gerir todos os aspetos do PowerApps. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

### <a name="printer-administrator-permissions"></a>Permissões de administrador de impressora

Pode gerir todos os aspetos das impressoras e conectores de impressoras.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.print/allEntities/allProperties/allTasks | Crie e elimine impressoras e conectores e leia e atualize todas as propriedades na Microsoft Print. |

### <a name="printer-technician-permissions"></a>Permissões do Técnico de Impressora

Pode registar-se e não registar impressoras e atualizar o estado da impressora.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.print/connectors/allProperties/read | Leia todas as propriedades dos conectores na Microsoft Print. |
| microsoft.azure.print/printers/allProperties/read | Leia todas as propriedades das impressoras na Microsoft Print. |
| microsoft.azure.print/printers/basic/update | Atualizar propriedades básicas de impressoras na Microsoft Print. |
| microsoft.azure.print/printers/register | Registar impressoras na Microsoft Print. |
| microsoft.azure.print/printers/unregister | Impressoras não registadas na Microsoft Print. |

### <a name="privileged-authentication-administrator-permissions"></a>Permissões privilegiadas do Administrador de Autenticação

Permitido visualizar, definir e redefinir informações do método de autenticação para qualquer utilizador (administrador ou não administrador).

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Invalidar todos os tokens de atualização do utilizador no Azure Ative Directory. |
| microsoft.diretório/utilizadores/strongAuthentication/update | Atualizar propriedades de autenticação forte como informações credenciais MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.diretório/utilizadores/password/atualização | Atualizar palavras-passe para todos os utilizadores da organização Microsoft 365. Consulte a documentação online para obter mais detalhes. |

### <a name="privileged-role-administrator-permissions"></a>Permissões privilegiadas de administrador de funções

Pode gerir atribuições de funções em AD Azure, e todos os aspetos da Gestão de Identidade Privilegiada.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/gruposAssignableToRoles/allProperties/update | Atualizar grupos com propriedade IsAssignableToRole definida para ser verdadeira no Azure Ative Directory. |
| microsoft.diretório/gruposAssignableToRoles/create | Criar grupos com propriedade isAssignableToRole definida para verdade no Azure Ative Directory. |
| microsoft.diretório/gruposAssignableToRoles/delete | Eliminar grupos com propriedade isAssignableToRole definida para ser verdadeira no Azure Ative Directory. |
| microsoft.diretório/privilegiadoIdinsmanagement/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize propriedades padrão em microsoft.aad.privilegedIdentityManagement. |
| microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/allTasks | Ler e configurar serviçoPrincipals.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.diretório/serviçoPrincipals/oAuth2PermissionGrants/allTasks | Ler e configurar serviçoPrincipals.oAuth2PermissionS property in Azure Ative Directory. |
| microsoft.diretório/administrativoSIns/allProperties/allTasks | Criar e gerir unidades administrativas (incluindo membros) |
| microsoft.diretório/papelAssignments/allProperties/allTasks | Criar e gerir tarefas de função. |
| microsoft.diretório/roleDefinitions/allProperties/allTasks | Criar e gerir definições de funções. |

### <a name="reports-reader-permissions"></a>Relatórios permissões do leitor

Pode ler relatórios de inscrição e auditoria.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs no Azure Ative Directory. |
| microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de utilização do Office 365. |

### <a name="search-administrator-permissions"></a>Permissões do Administrador de Pesquisa

Pode criar e gerir todos os aspetos das definições de Pesquisa do Microsoft.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Leia as mensagens no microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Crie e elimine todos os recursos e leia e atualize todas as propriedades em microsoft.office365.search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="search-editor-permissions"></a>Permissões do Editor de Pesquisa

Pode criar e gerir os conteúdos editoriais tais como marcadores, Q e As, localizações, planta.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Leia as mensagens no microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Crie e elimine o conteúdo e leia e atualize todas as propriedades em microsoft.office365.search. |

### <a name="security-administrator-permissions"></a>Permissões de administrador de segurança

Pode ler informações de segurança e relatórios e gerir a configuração em AZure AD e Microsoft 365.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.diretório/aplicações/políticas/atualização | Atualizar aplicações.apólices propriedade em Azure Ative Directory. |
| microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs no Azure Ative Directory. |
| microsoft.diretório/dispositivos/bitLockerRecoveryKeys/read | Leia a propriedade dispositivos.bitLockerRecoveryKeys no Azure Ative Directory. |
| microsoft.diretório/identidadeProteção/todas as ofertas/ler | Leia todos os recursos em microsoft.aad.identityProtection. |
| microsoft.diretório/identidadeProteção/todas as ofertas/atualização | Atualizar todos os recursos em microsoft.aad.identityProtection. |
| microsoft.diretório/políticas/básico/atualização | Atualizar propriedades básicas sobre políticas no Azure Ative Directory. |
| microsoft.diretório/políticas/criar | Criar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/excluir | Eliminar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/proprietários/atualização | Atualizar apólices.propriedade dos proprietários em Azure Ative Directory. |
| microsoft.diretório/políticas/tenantDefault/update | Atualizar políticas.tenantdefault propriedade em Azure Ative Directory. |
| microsoft.diretório/privilegiadoIdmentManagement/allProperties/read | Leia todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.diretório/serviçoPrincipals/políticas/atualização | Serviço de actualizaçãoPrincipals.políticas propriedade em Azure Ative Directory. |
| microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.office365.protectionCenter/allEntities/read | Leia todos os aspetos do Centro de Proteção do Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Atualize todos os recursos em microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="security-operator-permissions"></a>Permissões do Operador de Segurança

Cria e gere eventos de segurança.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.advancedThreatProtection/allEntities/read | Leia e configuure Azure AD Advanced Threat Protection. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.directy/cloudAppSecurity/allProperties/allTasks | Leia e configuure a Microsoft Cloud App Security. |
| microsoft.diretório/identidadeProteção/todas as ofertas/ler | Leia todos os recursos em microsoft.aad.identityProtection. |
| microsoft.diretório/privilegiadoIdmentManagement/allProperties/read | Leia todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.intune/allEntities/allTasks | Gerir todos os aspetos do Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Leia e configuure o Centro de Conformidade & de Segurança. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Leia e configuure a proteção avançada de ameaças do Windows Defender. |

### <a name="security-reader-permissions"></a>Permissões do Leitor de Segurança

Pode ler informações de segurança e relatórios em Azure AD e Microsoft 365.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/auditoriaLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditLogs no Azure Ative Directory. |
| microsoft.diretório/dispositivos/bitLockerRecoveryKeys/read | Leia a propriedade dispositivos.bitLockerRecoveryKeys no Azure Ative Directory. |
| microsoft.diretório/políticas/condicionalAccess/basic/read | Ler políticas.conditionalAposs propriedade em Azure Ative Directory. |
| microsoft.diretório/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.aad.identityProtection/allEntities/read | Leia todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Leia todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Leia todos os aspetos do Centro de Proteção do Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |

### <a name="service-support-administrator-permissions"></a>Permissões do Administrador de Suporte de Serviço

Pode ler informações de saúde do serviço e gerir bilhetes de apoio.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

### <a name="sharepoint-service-administrator-permissions"></a>Permissões de Administrador de Serviço do SharePoint

Pode gerir todos os aspetos do serviço SharePoint.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.diretório/grupos/uned/appRoleAssignments/update | Atualizar grupos.propriedade unificada em Azure Ative Directory. |
| microsoft.diretório/grupos/unificado/básico/atualização | Atualizar propriedades básicas dos grupos Microsoft 365. |
| microsoft.diretório/grupos/unificado/criar | Crie grupos Microsoft 365. |
| microsoft.diretório/grupos/unificado/delete | Elimine os grupos Microsoft 365. |
| microsoft.diretório/grupos/unificado/membros/atualização | Atualizar a adesão aos grupos Microsoft 365. |
| microsoft.diretório/grupos/unificado/proprietários/atualização | Atualizar a propriedade dos grupos Microsoft 365. |
| microsoft.office365.network/performance/allProperties/read | Leia as páginas de desempenho da rede no M365 Admin Center. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize propriedades padrão em microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.usageReports/allEntities/read    | Leia os relatórios de utilização do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="teams-communications-administrator-permissions"></a>Permissões de Administrador de Comunicações de Equipas

Pode gerir funcionalidades de chamadas e reuniões dentro do serviço Microsoft Teams.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de utilização do Office 365. |
| microsoft.teams/meetings/allProperties/allTasks | Gerir reuniões, incluindo políticas de reunião, configurações e pontes de conferências. |
| microsoft.teams/voice/allProperties/allTasks | Gerir a voz, incluindo políticas de chamadas e inventário de números de telefone e atribuição. |
| microsoft.teams/callQuality/allProperties/read | Leia todos os dados no Painel de Qualidade de Chamada (CQD). |

### <a name="teams-communications-support-engineer-permissions"></a>Permissões de Engenheiro de Suporte de Comunicações de Equipas

Pode resolver problemas de comunicação dentro das equipas usando ferramentas avançadas.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.teams/callQuality/allProperties/read | Leia todos os dados no Painel de Qualidade de Chamada (CQD). |

### <a name="teams-communications-support-specialist-permissions"></a>Permissões especializadas em apoio às comunicações das equipas

Pode resolver problemas de comunicação dentro das equipas usando ferramentas básicas.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.teams/callQuality/basic/read | Leia os dados básicos no Painel de Qualidade de Chamada (CQD). |

### <a name="teams-devices-administrator-permissions"></a>Permissões de administrador de dispositivos de equipas

Pode executar tarefas relacionadas com a gestão em dispositivos certificados por Equipas.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.teams/devices/basic/read | Gerir todos os aspetos dos dispositivos certificados por Equipas, incluindo políticas de configuração. |

### <a name="teams-service-administrator-permissions"></a>Permissões de Administrador de Serviço de Equipas

Pode gerir o serviço Microsoft Teams.

> [!NOTE]
> Esta função tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição da função acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.diretório/grupos/hiddenMembers/read | Ler propriedade de grupos.hiddenMembers em Azure Ative Directory. |
| microsoft.diretório/grupos/uned/appRoleAssignments/update | Atualizar grupos.propriedade unificada em Azure Ative Directory. |
| microsoft.diretório/grupos/unificado/básico/atualização | Atualizar propriedades básicas dos grupos Microsoft 365. |
| microsoft.diretório/grupos/unificado/criar | Crie grupos Microsoft 365. |
| microsoft.diretório/grupos/unificado/delete | Elimine os grupos Microsoft 365. |
| microsoft.diretório/grupos/unificado/membros/atualização | Atualizar a adesão aos grupos Microsoft 365. |
| microsoft.diretório/grupos/unificado/proprietários/atualização | Atualizar a propriedade dos grupos Microsoft 365. |
| microsoft.office365.network/performance/allProperties/read | Leia as páginas de desempenho da rede no M365 Admin Center. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de utilização do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.teams/allEntities/allProperties/allTasks | Gerir todos os recursos em Equipas. |

### <a name="user-administrator-permissions"></a>Permissões de Administrador de Utilizador
Pode gerir todos os aspetos dos utilizadores e grupos, incluindo a reposição de palavras-passe para administradores limitados.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/appRoleAssignments/create | Crie apreciações apreciá-lo no Diretório Ativo Azure. |
| microsoft.diretório/appRoleAssignments/delete | Elimine as assinaturas do AppRoleAss no Diretório Ativo Azure. |
| microsoft.diretório/appRoleAssignments/update | Atualizar apreciamentos AlooleAssignments no Azure Ative Directory. |
| microsoft.diretório/contactos/básico/atualização | Atualizar propriedades básicas em contactos no Azure Ative Directory. |
| microsoft.diretório/contactos/criar | Criar contactos no Diretório Ativo Azure. |
| microsoft.diretório/contactos/excluir | Eliminar contactos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/appRoleAssignments/update | Atualizar a propriedade grupos.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/grupos/básico/atualização | Atualizar propriedades básicas em grupos no Azure Ative Directory. |
| microsoft.diretório/grupos/criar | Criar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/createAsOwner | Criar grupos no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta contra a quota de objetos criados pelo criador. |
| microsoft.diretório/grupos/delete | Eliminar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/hiddenMembers/read | Ler propriedade de grupos.hiddenMembers em Azure Ative Directory. |
| microsoft.diretório/grupos/membros/atualização | Atualizar propriedade grupos.membros em Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/atualização | Atualizar propriedade de grupos.proprietários em Azure Ative Directory. |
| microsoft.diretório/grupos/restaurar | Restaurar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/definições/atualização | Atualizar a propriedade grupos.configurações em Azure Ative Directory. |
| microsoft.diretório/utilizadores/appRoleAssignments/update | Atualizar a propriedade users.appRoleAssignments em Azure Ative Directory. |
| microsoft.diretório/utilizadores/assignLicense | Gerir licenças em utilizadores no Azure Ative Directory. |
| microsoft.diretório/utilizadores/básico/atualização | Atualizar propriedades básicas nos utilizadores no Azure Ative Directory. |
| microsoft.diretório/utilizadores/criar | Crie utilizadores no Azure Active Directory. |
| microsoft.diretório/utilizadores/delete | Eliminar os utilizadores no Diretório Ativo Azure. |
| microsoft.diretório/utilizadores/invalidadoAllRefreshTokens | Invalidar todos os tokens de atualização do utilizador no Azure Ative Directory. |
| microsoft.diretório/utilizadores/gestor/atualização | Atualizar propriedade users.manager em Azure Ative Directory. |
| microsoft.diretório/utilizadores/password/atualização | Atualizar palavras-passe para todos os utilizadores no Azure Ative Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.diretório/utilizadores/restaurar | Restaurar os utilizadores eliminados no Azure Ative Directory. |
| microsoft.diretório/utilizadores/userPrincipalName/update | Atualizar a propriedade users.userPrincipalName em Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gere os bilhetes de apoio da Azure para serviços de nível de diretório. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configuure a Microsoft 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerir os bilhetes de apoio do Office 365. |

## <a name="role-template-ids"></a>IDs de modelo de papel

Os IDs do modelo de função são usados principalmente pelos utilizadores da Microsoft Graph API ou PowerShell.

Graph displayName | Nome de exibição do portal Azure | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Administrador da Aplicação | Administrador de aplicação | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Programador de Aplicações | Desenvolvedor de aplicações | CF1C38E5-3621-4004-A7CB-879624DCED7C
Administrador de Autenticação | Administrador de autenticação | c4e39bd9-1100-46d3-8c65-fb160da00071f
Administrador da Azure DevOps | Administrador da Azure DevOps | e3973bdf-4987-49ae-837a-ba8e231c7286
Administrador de Proteção de Informação da Azure | Administrador de Proteção de Informação da Azure | 7495fdc4-34c4-4d15-a289-98788ce399fd
Administrador do keyset B2C IEF | Administrador do keyset B2C IEF | aaf43236-0c0d-4d5f-883a-6955382ac081
Administrador de Política B2C IEF | Administrador de Política B2C IEF | 3edaf663-341e-4475-9f94-5c398ef6c070
Administrador de Faturação | Administrador de faturação | b0f54661-2d74-4c50-afa3-1ec803f12efe
Administrador de Aplicações na Cloud | Administrador de aplicação em nuvem | 158c047a-c907-4556-b7ef-446551a6b5f7
Administrador de dispositivos de nuvem | Administrador de dispositivos em nuvem | 7698a772-787b-4ac8-901f-60d6b08affd2
Administrador da Empresa | Administrador global | 62e90394-69f5-4237-9190-012177145e10
Administrador de Conformidade | Administrador de conformidade | 17315797-102d-40b4-93e0-432062caca18
Administrador de Dados de Conformidade | Administrador de dados de conformidade | e6d1a23a-da11-4be4-9570-befc86d067a7
Administrador de acesso condicional | Administrador de acesso condicional | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Administrador de serviço crm | Administrador dinâmico 365 | 44367163-eba1-44c3-98af-f5787879f96a
Aprovação de acesso ao cliente LockBox | Aprovador de acesso ao bloqueio do cliente | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Administrador de Análise de Desktop | Administrador de Análise de Desktop | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Administradores de Dispositivos | Administradores de dispositivos | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Aderir ao dispositivo | Preterido | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Gestores de Dispositivos | Preterido | 2b499bcd-da44-4968-8aec-78e1674fa64d
Utilizadores de Dispositivos | Preterido | d405c6df-0af8-4e3b-95e4-4d06e542189e
Leitores de Diretório | Leitores de diretório | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Contas de Sincronização do Diretório | Não mostrado porque não deve ser usado | d29b2b05-8046-44ba-8758-1e26182fcf32
Escritores do Diretório | Escritores do Diretório | 9360feb5-f418-4baa-8175-e2a00bac4301
Administrador de Serviço de Intercâmbio | Administrador do Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administrador de fluxo de id de id externo | Administrador de fluxo de id de id externo | 6e591065-9bad-43ed-90f3-e9424366d2f0
Administrador de atributo de fluxo de id de id externo | Administrador de atributo de fluxo de id de id externo | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Administrador de fornecedor de identidade externa | Administrador de fornecedor de identidade externa | be2f45a1-457d-42af-a067-6ec1fa63bc45
Leitor Global | Leitor global | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Administrador de Grupos | Administrador de grupos | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Convidado Convidado | Convidado convidado | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Administrador helpdesk | Administrador da Helpdesk | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administrador de Identidade Híbrida | Administrador de identidade híbrido | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Administrador de Insights | Administrador de insights | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c
Insights Business Leader | Insights líder de negócios | 31e939ad-9672-4796-9c2e-873181342d2d
Administrador de Serviços do Intune | Administrador do Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Administrador kaizala | Administrador kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Administrador de Licença | Administrador de licenças | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Administrador de Serviço Lync | Administrador do Skype para Empresas | 75941009-915a-4869-abe7-691bff18279e
Leitor de Privacidade do Centro de Mensagens | Leitor de privacidade do centro de mensagens | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Leitor do Centro de Mensagens | Leitor de centro de mensagens | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Utilizador de Comércio Moderno | Utilizador de Comércio Moderno | d24aef57-1500-4070-84db-2666f29cf966
Administrador de rede | Administrador de rede | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Administrador de Aplicações de Escritório | Administrador de aplicativos de escritório | 2b745bdf-0803-4d80-aa65-822c493daac
Suporte parceiro Tier1 | Não mostrado porque não deve ser usado | 4ba39ca4-527c-499a-b93d-d9b492c50246
Suporte parceiro Tier2 | Não mostrado porque não deve ser usado | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Administrador de password | Administrador de palavras-passe | 966707d0-3269-4727-9be2-8c3a10f19b9d
Administrador do Serviço Power BI | Administrador de Bi de potência | a9ea8996-122f-4c74-9520-8edcd192826c
Administrador da Plataforma de Energia | Administrador da plataforma de energia | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Administrador de impressora | Administrador de impressora | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
Técnico de Impressora | Técnico de impressora | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
Administrador de Autenticação Privilegiada | Administrador de autenticação privilegiada | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administrador privilegiado | Administrador privilegiado | e8611ab8-c189-46e8-94e1-60213ab1f814
Leitor de Relatórios | Leitor de relatórios | 4a5d8f65-41da-4de4-8968-e035b65339cf
Administrador de Pesquisa | Administrador de pesquisa | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Editor de Pesquisa | Editor de pesquisa | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Administrador de Segurança | Administrador de segurança | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operador de Segurança | Operador de segurança | 5f222b1-57c3-48ba-8ad5-d4759f1f1fde6f
Leitor de Segurança | Leitor de segurança | 5d6b6bb7-de71-4623-b4af-96380a352509
Administrador de Suporte de Serviço | Administrador de suporte de serviços | f023fd81-a637-4b56-95fd-791ac0226033
Administrador de serviço do SharePoint | Administrador do SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Administrador de Comunicações de Equipas | Administrador de Comunicações de Equipas | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Engenheiro de Suporte de Comunicações de Equipas | Engenheiro de Suporte de Comunicações de Equipas | f70938a0-fc10-4177-9e90-2178f8765737
Especialista em Apoio às Comunicações das Equipas | Especialista em Apoio às Comunicações das Equipas | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Administrador de dispositivos de equipas | Administrador de dispositivos de equipas | 3d762c5a-1b6c-493f-843e-55a3b42923d4
Administrador de Serviço de Equipas | Administrador de Serviço de Equipas | 69091246-20e8-4a56-aa4d-066075b2a7a8
Utilizador | Não mostrado porque não pode ser usado | a0b1b346-4d3e-4e8b-98f8-753987be4970
Administrador de Conta de Utilizador | Administrador de utilizadores | fe930be7-5e62-47db-91af-98c3a49a38b1
Aderir ao dispositivo de trabalho | Preterido | c34f683f-4d5a-4403-affd-6615e00e3a7f

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
Administrador da Empresa | Administrador Global | [Nome alterado para melhor clareza](permissions-reference.md#role-template-ids)
Administrador de serviço crm | Administrador dinâmico 365 | [Reflete a marca atual do produto](permissions-reference.md#role-template-ids)
Aderir ao dispositivo | Preterido | [Documentação de papéis precotados](permissions-reference.md#deprecated-roles)
Gestores de Dispositivos | Preterido | [Documentação de papéis precotados](permissions-reference.md#deprecated-roles)
Utilizadores de Dispositivos | Preterido | [Documentação de papéis precotados](permissions-reference.md#deprecated-roles)
Contas de Sincronização do Diretório | Não mostrado porque não deve ser usado | [Documentação de Contas de Sincronização do Diretório](permissions-reference.md#directory-synchronization-accounts)
Escritores do Diretório | Não mostrado porque não deve ser usado | [Documentação do Diretório escritores](permissions-reference.md#directory-writers)
Utilizador Convidado | Não mostrado porque não pode ser usado  | ND
Administrador de Serviço Lync | Administrador do Skype para Empresas | [Reflete a marca atual do produto](permissions-reference.md#role-template-ids)
Suporte de Nível 1 do Parceiro | Não mostrado porque não deve ser usado | [Documentação de suporte do Parceiro Tier1](permissions-reference.md#partner-tier1-support)
Suporte parceiro tier 2 | Não mostrado porque não deve ser usado | [Documentação de suporte de parceiro Tier2](permissions-reference.md#partner-tier2-support)
Utilizador restrito de hóspedes | Não mostrado porque não pode ser usado | ND
Utilizador | Não mostrado porque não pode ser usado | ND
Aderir ao dispositivo de trabalho | Preterido | [Documentação de papéis precotados](permissions-reference.md#deprecated-roles)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como atribuir um utilizador como administrador de uma subscrição do Azure, consulte [Adicionar ou remover atribuições de funções Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)
* Para saber mais sobre como o acesso a recursos é controlado no Microsoft Azure, consulte [Compreender as diferentes funções](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para mais detalhes sobre a relação entre subscrições e um inquilino AZure AD, ou para instruções para associar ou adicionar uma subscrição, consulte [Associado ou adicione uma assinatura Azure ao seu inquilino Azure Ative Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
