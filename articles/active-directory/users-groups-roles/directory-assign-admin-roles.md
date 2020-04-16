---
title: Descrições e permissões de papéis de administrador - Azure AD / Microsoft Docs
description: Uma função de administrador pode adicionar utilizadores, atribuir funções administrativas, redefinir as palavras-passe dos utilizadores, gerir as licenças do utilizador ou gerir domínios.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3f284efd6a9a2fd83c8e2a8f9fb7a962c1cacc1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406468"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Administrator role permissions in Azure Active Directory (Permissões de cargos de administrador no Azure Active Directory)

Utilizando o Azure Ative Directory (Azure AD), pode designar administradores limitados para gerir tarefas de identidade em funções menos privilegiadas. Os administradores podem ser atribuídos para fins como adicionar ou alterar utilizadores, atribuir funções administrativas, redefinir palavras-passe de utilizador, gerir as licenças dos utilizadores e gerir nomes de domínio. As [permissões de utilizador predefinidas](../fundamentals/users-default-permissions.md) só podem ser alteradas nas definições do utilizador em AD Azure.

## <a name="limit-use-of-global-administrator"></a>Limitar a utilização de administrador global

Os utilizadores que são atribuídos ao papel de administrador global podem ler e modificar todas as configurações administrativas da sua organização Azure AD. Por predefinição, a pessoa que se inscreve para uma subscrição do Azure é atribuída ao papel de administrador global para a organização Azure AD. Apenas administradores globais e administradores de papel privilegiado podem delegar funções de administrador. Para reduzir o risco para o seu negócio, recomendamos que atribua este papel ao menor número possível de pessoas na sua organização.

Como uma boa prática, recomendamos que atribua este papel a menos de cinco pessoas na sua organização. Se tem mais de cinco administradores atribuídos ao papel de Administrador Global na sua organização, aqui estão algumas formas de reduzir o seu uso.

### <a name="find-the-role-you-need"></a>Encontre o papel que precisa

Se é frustrante para si encontrar o papel que precisa de uma lista de muitos papéis, o Azure AD pode mostrar-lhe subconjuntos dos papéis com base nas categorias de papéis. Consulte o nosso novo filtro **Tipo** para [Funções e administradores de AD Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) para lhe mostrar apenas as funções no tipo selecionado.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Existe agora um papel que não existia quando atribuiu o papel de administrador global

É possível que um papel ou funções tenham sido adicionados à Azure AD que fornecem permissões mais granulares que não eram uma opção quando elevou alguns utilizadores a administrador global. Com o tempo, estamos a desempenhar funções adicionais que desempenham tarefas que só o papel de administrador global poderia fazer antes. Pode ver estes refletidos nas seguintes [funções disponíveis.](#available-roles)

## <a name="assign-or-remove-administrator-roles"></a>Atribuir ou remover funções de administrador

Para aprender a atribuir funções administrativas a um utilizador no Azure Ative Directory, consulte [o View e atribua funções de administrador no Diretório Ativo do Azure](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Funções disponíveis

Estão disponíveis as seguintes funções de administrador:

### <a name="application-administrator"></a>[Administrador de Aplicações](#application-administrator-permissions)

Os utilizadores desta função podem criar e gerir todos os aspetos das aplicações da empresa, registos de aplicações e configurações de procuração de aplicações. Note que os utilizadores atribuídos a esta função não são adicionados como proprietários na criação de novos registos de aplicações ou aplicações empresariais.

Os Administradores de Aplicação podem gerir credenciais de aplicação que lhes permitem personificar a aplicação. Assim, os utilizadores designados para esta função podem gerir credenciais de aplicação apenas das aplicações que não estejam atribuídas a quaisquer funções de AD Azure ou as atribuídas apenas às seguintes funções de administrador:
* Administrador da Aplicação
* Programador de Aplicações
* Administrador de Aplicações na Cloud
* Leitores de Diretório

Se um pedido for atribuído a qualquer outra função que não seja mencionada acima, então o Administrador de Aplicação não pode gerir credenciais desse pedido. 
 
Esta função também concede a possibilidade de _consentir_ com permissões delegadas e permissões de aplicação, com exceção das permissões na Microsoft Graph API.

> [!IMPORTANT]
> Esta exceção significa que ainda pode consentir com permissões para _outras_ aplicações (por exemplo, aplicações ou aplicações de terceiros que tenha registado), mas não permissões em aprópria AD Azure. Ainda pode _solicitar_ estas permissões como parte do registo da aplicação, mas _a concessão_ (ou seja, consentir) estas permissões requer um administrador da AD Azure. Isto significa que um utilizador malicioso não pode facilmente elevar as suas permissões, por exemplo, criando e consentir com uma aplicação que pode escrever para todo o diretório e através das permissões dessa app elevar-se para se tornar um administrador global.

### <a name="application-developer"></a>[Programador de Aplicações](#application-developer-permissions)

Os utilizadores desta função podem criar registos de aplicações quando a definição "Utilizadores podem registar aplicações" é definida como Nº. Esta função também concede permissão para consentir em nome próprio quando a definição "Utilizadores podem consentir em aplicações que acedam a dados da empresa em seu nome" está definida como Nº. Os utilizadores atribuídos a esta função são adicionados como proprietários na criação de novos registos de aplicações ou aplicações empresariais.

### <a name="authentication-administrator"></a>[Administrador de Autenticação](#authentication-administrator-permissions)

O papel de administrador de Autenticação está atualmente em pré-visualização pública. Os utilizadores com esta função podem definir ou redefinir credenciais de não-palavra-passe e atualizar palavras-passe para todos os utilizadores. Os Administradores de Autenticação podem exigir que os utilizadores voltem a registar-se contra a credencial não-senha existente (por exemplo, MFA ou FIDO) e revogar **a desconhecer o MFA no dispositivo,** que indica o MFA no próximo registo de utilizadores que não sejam administradores ou que sejam atribuídos apenas:

* Administrador de Autenticação
* Leitores de Diretório
* Convidado convidado
* Leitor de centro de mensagens
* Relatórios Leitor

> [!IMPORTANT]
> Os utilizadores com esta função podem alterar credenciais para pessoas que possam ter acesso a informações sensíveis ou privadas ou configuração crítica dentro e fora do Diretório Ativo do Azure. Alterar as credenciais de um utilizador pode significar a capacidade de assumir a identidade e as permissões desse utilizador. Por exemplo:
>
>- Registo de aplicações e proprietários de aplicações empresariais, que podem gerir credenciais de aplicações que possuam. Essas aplicações podem ter permissões privilegiadas em Azure AD e em outros lugares não concedidos a Administradores de Autenticação. Através deste percurso, um Administrador de Autenticação poderá assumir a identidade de um titular da candidatura e, em seguida, assumir a identidade de uma aplicação privilegiada atualizando as credenciais para a aplicação.
>- Proprietários de subscrições Azure, que podem ter acesso a informações sensíveis ou privadas ou configuração crítica no Azure.
>- Grupo de Segurança e proprietários do Grupo 365, que podem gerir a adesão ao grupo. Esses grupos podem conceder acesso a informações sensíveis ou privadas ou configuração crítica em Azure AD e em outros lugares.
>- Administradores de outros serviços fora da Azure AD como Exchange Online, Office Security and Compliance Center, e sistemas de recursos humanos.
>- Não administradores como executivos, advogados e funcionários de recursos humanos que possam ter acesso a informações sensíveis ou privadas.

### <a name="azure-devops-administrator"></a>[Administrador azure DevOps](#azure-devops-administrator-permissions)

Os utilizadores com esta função podem gerir a política azure DevOps para restringir a criação de novos Azure DevOps a um conjunto de utilizadores ou grupos configuráveis. Os utilizadores desta função podem gerir esta política através de qualquer organização Azure DevOps que tenha o apoio da organização Azure AD da empresa.

Todas as políticas da empresa Azure DevOps podem ser geridas pelos utilizadores nesta função.

### <a name="azure-information-protection-administrator"></a>[Administrador de Proteção de Informação Azure](#azure-information-protection-administrator-permissions)

Os utilizadores com esta função têm todas as permissões no serviço de Proteção de Informação Azure. Esta função permite configurar rótulos para a política de proteção de informação Azure, gerir modelos de proteção e ativar a proteção. Esta função não concede permissões no Centro de Proteção de Identidade, Gestão de Identidade Privilegiada, Gabinete de Monitorização 365 Saúde de Serviço, ou Office 365 Security & Compliance Center.

### <a name="b2c-user-flow-administrator"></a>[Administrador de fluxo de utilizador B2C](#b2c-user-flow-administrator-permissions)

Os utilizadores com esta função podem criar e gerir fluxos de utilizadores B2C (também chamados de políticas "incorporadas") no portal Azure.Ao criar ou editar fluxos de utilizadores, estes utilizadores podem alterar o conteúdo html/CSS/javascript da experiência do utilizador, alterar os requisitos de MFA por fluxo de utilizador, alterar reclamações no token e ajustar as definições de sessão para todas as políticas do inquilino. Por outro lado, esta função não inclui a capacidade de rever os dados dos utilizadores, nem fazer alterações aos atributos que estão incluídos no esquema do inquilino.As alterações ao Quadro de Experiência de Identidade (também conhecida como Personalizada) também estão fora do âmbito desta função.

### <a name="b2c-user-flow-attribute-administrator"></a>[Administrador de atributo de fluxo de utilizador B2C](#b2c-user-flow-attribute-administrator-permissions)

Os utilizadores com esta função adicionam ou apagam atributos personalizados disponíveis para todos os fluxos de utilizador no inquilino.Como tal, os utilizadores com esta função podem alterar ou adicionar novos elementos ao esquema do utilizador final e impactar o comportamento de todos os fluxos de utilizadores e resultar indiretamente em alterações aos dados que podem ser solicitados aos utilizadores finais e, em última análise, enviados como alegações às aplicações.Esta função não pode editar fluxos de utilizadores.

### <a name="b2c-ief-keyset-administrator"></a>[Administrador de teclas B2C IEF](#b2c-ief-keyset-administrator-permissions)

O utilizador pode criar e gerir chaves e segredos de política para encriptação simbólica, assinaturas simbólicas e alegar encriptação/desencriptação.Ao adicionar novas chaves aos recipientes-chave existentes, este administrador limitado pode reverter segredos conforme necessário sem afetar as aplicações existentes.Este utilizador pode ver o conteúdo completo destes segredos e as suas datas de validade mesmo após a sua criação.

> [!IMPORTANT]
> Este é um papel sensível.O papel de administrador de keyset deve ser cuidadosamente auditado e atribuído com cuidado durante a pré-produção e produção.

### <a name="b2c-ief-policy-administrator"></a>[Administrador de Política B2C IEF](#b2c-ief-policy-administrator-permissions)

Os utilizadores desta função têm a capacidade de criar, ler, atualizar e eliminar todas as políticas personalizadas em Azure AD B2C e, portanto, ter total controlo sobre o Quadro de Experiência de Identidade no inquilino Azure AD B2C relevante. Ao editar políticas, este utilizador pode estabelecer uma federação direta com fornecedores de identidade externos, alterar o esquema de diretório, alterar todos os conteúdos virados para o utilizador (HTML, CSS, JavaScript), alterar os requisitos para completar uma autenticação, criar novos utilizadores, enviar dados de utilizadores para sistemas externos, incluindo migrações completas, e editar todas as informações do utilizador, incluindo campos sensíveis como palavras-passe e números de telefone. Inversamente, este papel não pode alterar as chaves de encriptação ou editar os segredos usados para a federação no inquilino.

> [!IMPORTANT]
> O Administrador de Política B2 IEF é um papel altamente sensível que deve ser atribuído numa base muito limitada aos inquilinos em produção.As atividades destes utilizadores devem ser auditadas de perto, especialmente para os inquilinos em produção.

### <a name="billing-administrator"></a>[Administrador de Faturação](#billing-administrator-permissions)

Efetua compras, gere subscrições, gere pedidos de suporte e monitoriza o estado de funcionamento do serviço.

### <a name="cloud-application-administrator"></a>[Administrador de Aplicações na Cloud](#cloud-application-administrator-permissions)

Os utilizadores desta função têm as mesmas permissões que a função de Administrador de Aplicação, excluindo a capacidade de gerir o proxy da aplicação. Este papel confere a capacidade de criar e gerir todos os aspetos das aplicações empresariais e dos registos de candidaturas. Esta função também concede a possibilidade de consentir com permissões delegadas, e permissões de aplicação excluindo a API do Microsoft Graph. Os utilizadores atribuídos a esta função não são adicionados como proprietários na criação de novos registos de aplicações ou aplicações empresariais.

Os Administradores de Aplicação cloud podem gerir credenciais de aplicação que lhes permitem personificar a aplicação. Assim, os utilizadores designados para esta função podem gerir credenciais de aplicação apenas das aplicações que não estejam atribuídas a quaisquer funções de AD Azure ou as atribuídas apenas às seguintes funções de administrador:
* Programador de Aplicações
* Administrador de Aplicações na Cloud
* Leitores de Diretório

Se uma aplicação for atribuída a qualquer outra função que não seja mencionada acima, então o Administrador de Aplicação cloud não pode gerir credenciais dessa aplicação.

### <a name="cloud-device-administrator"></a>[Administrador de dispositivos de nuvem](#cloud-device-administrator-permissions)

Os utilizadores desta função podem ativar, desativar e eliminar dispositivos em Azure AD e ler as teclas Do Windows 10 BitLocker (se estiverem presentes) no portal Azure. A função não concede permissões para gerir quaisquer outras propriedades no dispositivo.

### <a name="compliance-administrator"></a>[Administrador de Conformidade](#compliance-administrator-permissions)

Os utilizadores com esta função têm permissões para gerir funcionalidades relacionadas com a conformidade no centro de conformidade microsoft 365, microsoft 365 centro de administração, Azure e Office 365 Security & Compliance Center. Os signees também podem gerir todas as funcionalidades dentro do centro de administração exchange e as equipas & Skype para centros de administração de negócios e criar bilhetes de suporte para Azure e Microsoft 365. Mais informações estão disponíveis nas funções de [administrador do Gabinete 365.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

Entrada | Pode fazer
----- | ----------
[Microsoft 365 compliance center](https://protection.office.com) | Proteja e gerencie os dados da sua organização através dos serviços da Microsoft 365<br>Gerir alertas de conformidade
[Gestor de Conformidade](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhe, atribua e verifique as atividades de conformidade regulamentar da sua organização
[Escritório 365 Centro de Conformidade & de Segurança](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerir a governação de dados<br>Realizar investigação legal e de dados<br>Gerir pedido de assunto de dados<br><br>Esta função tem as mesmas permissões que o [Compliance Administrator RoleGroup](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) no Office 365 Security & Compliance Center controlo de acesso baseado em funções.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Ver todos os dados de auditoria intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões de leitura e pode gerir alertas<br>Pode criar e modificar políticas de ficheiros e permitir ações de governação de ficheiros<br>Pode ver todos os relatórios incorporados no âmbito da Gestão de Dados

### <a name="compliance-data-administrator"></a>[Administrador de Dados de Conformidade](#compliance-data-administrator-permissions)

Os utilizadores com esta função têm permissões para rastrear dados no centro de conformidade microsoft 365, microsoft 365 centro de administração e Azure. Os utilizadores também podem rastrear os dados de conformidade dentro do centro de administração exchange, Compliance Manager e Teams & Skype for Business admin center e criar bilhetes de suporte para Azure e Microsoft 365.

Entrada | Pode fazer
----- | ----------
[Microsoft 365 compliance center](https://protection.office.com) | Monitorize as políticas relacionadas com a conformidade através dos serviços da Microsoft 365<br>Gerir alertas de conformidade
[Gestor de Conformidade](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhe, atribua e verifique as atividades de conformidade regulamentar da sua organização
[Escritório 365 Centro de Conformidade & de Segurança](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerir a governação de dados<br>Realizar investigação legal e de dados<br>Gerir pedido de assunto de dados<br><br>Esta função tem as mesmas permissões que o [Compliance Data Administrator RoleGroup](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) no Office 365 Security & Compliance Center controlo de acesso baseado em funções.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Ver todos os dados de auditoria intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões de leitura e pode gerir alertas<br>Pode criar e modificar políticas de ficheiros e permitir ações de governação de ficheiros<br>Pode ver todos os relatórios incorporados no âmbito da Gestão de Dados

### <a name="conditional-access-administrator"></a>[Administrador de Acesso Condicional](#conditional-access-administrator-permissions)

Os utilizadores com esta função têm a capacidade de gerir as definições de Acesso Condicional do Diretório Ativo Azure.
> [!NOTE]
> Para implementar a política de acesso condicional Exchange ActiveSync em Azure, o utilizador também deve ser um Administrador Global.

### <a name="customer-lockbox-access-approver"></a>[Aprovador de acesso ao bloqueio do cliente](#customer-lockbox-access-approver-permissions)

Gere [os pedidos](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) de Bloqueio de Clientes na sua organização. Recebem notificações por e-mail para pedidos de Bloqueio de Clientes e podem aprovar e negar pedidos do centro de administração da Microsoft 365. Também podem ligar ou desligar a funcionalidade 'Lockbox ao Cliente'. Só os administradores globais podem redefinir as palavras-passe das pessoas atribuídas a este papel.

### <a name="desktop-analytics-administrator"></a>[Administrador de Análise de Desktop](#desktop-analytics-administrator-permissions)


Os utilizadores desta função podem gerir os serviços de Política de & De Personalização de Desktop analytics e Office Customização. Para desktop Analytics, isto inclui a capacidade de visualizar o inventário de ativos, criar planos de implementação, visualizar a implementação e o estado de saúde. Para o serviço de personalização de & Política de Escritórios, esta função permite aos utilizadores gerir as políticas do Office.

### <a name="device-administrator"></a>[Administrador de Dispositivos](#device-administrators-permissions)

Esta função está disponível para atribuição apenas como administrador local adicional nas [definições do Dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Os utilizadores com esta função tornam-se administradores de máquinas locais em todos os dispositivos do Windows 10 que se juntam ao Diretório Ativo do Azure. Não têm a capacidade de gerir objetos de dispositivos no Diretório Ativo Azure.

### <a name="directory-readers"></a>[Leitores de Diretório](#directory-readers-permissions)

Os utilizadores desta função podem ler informações básicas sobre o diretório. Este papel deve ser utilizado para:
* Concedendo um conjunto específico de utilizadores convidados, leia o acesso em vez de conceder a todos os utilizadores convidados.
* A concessão de um conjunto específico de utilizadores não administrativos ao portal Azure quando "Restringir o acesso ao portal Azure AD apenas aos administradores" está definido para "Sim".
* Conceder aos diretores de serviço acesso ao diretório onde o Diretório.Read.All não é uma opção.

### <a name="directory-synchronization-accounts"></a>[Contas de Sincronização de Diretório](#directory-synchronization-accounts-permissions)

Não utilizar. Esta função é automaticamente atribuída ao serviço Azure AD Connect, e não é destinada ou suportada para qualquer outra utilização.

### <a name="directory-writers"></a>[Escritores de Diretório](#directory-writers-permissions)

Este é um papel legado que deve ser atribuído a candidaturas que não apoiem o Quadro de [Consentimento.](../develop/quickstart-register-app.md) Não deve ser atribuído a nenhum utilizador.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Administrador dinâmica 365 / Administrador crm](#crm-service-administrator-permissions)

Os utilizadores com esta função têm permissões globais dentro do Microsoft Dynamics 365 Online, quando o serviço está presente, bem como a capacidade de gerir bilhetes de suporte e monitorizar a saúde do serviço. Mais informações na Utilização do serviço de [administração para gerir o seu inquilino.](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço Dynamics 365". É "Administrador Da Dinâmica 365" no [portal Azure.](https://portal.azure.com)

### <a name="exchange-administrator"></a>[Administrador de Intercâmbio](#exchange-service-administrator-permissions)

Os utilizadores com esta função têm permissões globais dentro do Microsoft Exchange Online, quando o serviço está presente. Tem também a capacidade de criar e gerir todos os Grupos Office 365, gerir bilhetes de apoio e monitorizar a saúde do serviço. Mais informações sobre o [Office 365 funções de administrador.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço de Intercâmbio". É "Administrador de Intercâmbio" no [portal Azure.](https://portal.azure.com) É "Exchange Online administrator" no [centro de administração](https://go.microsoft.com/fwlink/p/?LinkID=529144)exchange .

### <a name="external-identity-provider-administrator"></a>[Administrador de Fornecedor de Identidade Externa](#external-identity-provider-administrator-permissions)

Este administrador gere a federação entre inquilinos do Azure Ative Directory e fornecedores de identidade externa.Com esta função, os utilizadores podem adicionar novos fornecedores de identidade e configurar todas as definições disponíveis (por exemplo, via de autenticação, ID de serviço, recipientes-chave atribuídos).Este utilizador pode permitir ao arrendatário confiar em autenticações de fornecedores de identidade externos.O impacto resultante nas experiências do utilizador final depende do tipo de inquilino:

* Inquilinos do Diretório Ativo Azure para colaboradores e parceiros: A adição de uma federação (por exemplo, com o Gmail) terá imediatamente impacto em todos os convites de hóspedes ainda não resgatados. Consulte a adição do Google como fornecedor de [identidade para utilizadores convidados B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
* Inquilinos do Azure Ative Directory B2C: A adição de uma federação (por exemplo, com o Facebook, ou com outra organização Azure AD) não impacta imediatamente os fluxos de utilizadores finais até que o fornecedor de identidade seja adicionado como uma opção num fluxo de utilizador (também chamado de política incorporada). Consulte [configurar uma conta microsoft como um fornecedor de identidade,](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) por exemplo.Para alterar os fluxos de utilizador, é necessária a função limitada de "Administrador de Fluxo de Utilizador B2C".

### <a name="global-administrator--company-administrator"></a>[Administrador Global / Administrador da Empresa](#company-administrator-permissions)

Os utilizadores com esta função têm acesso a todas as funcionalidades administrativas do Diretório Ativo do Azure, bem como a serviços que utilizam identidades do Azure Ative Directory como o Microsoft 365 security center, microsoft 365 compliance center, Exchange Online, SharePoint Online e Skype para Business Online. A pessoa que se inscreve no inquilino do Azure Ative Directory torna-se administradora global. Pode haver mais do que um administrador global na sua empresa. Os administradores globais podem redefinir a palavra-passe para qualquer utilizador e todos os outros administradores.

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador da Empresa". É "Administrador Global" no [portal Azure.](https://portal.azure.com)
>
>

### <a name="global-reader"></a>[Leitor Global](#global-reader-permissions)

Os utilizadores desta função podem ler configurações e informações administrativas através dos serviços da Microsoft 365, mas não podem tomar ações de gestão. O leitor global é a contraparte apenas para administrador global. Atribuir leitor global em vez de administrador global para planeamento, auditorias ou investigações. Utilize o leitor global em combinação com outras funções de administrador limitada, como o Exchange Administrator, para facilitar o trabalho sem atribuir o papel de Administrador Global. O leitor global trabalha com o centro de administração microsoft 365, centro de administração de exchange, centro de administração SharePoint, centro de administração de equipas, centro de segurança, centro de conformidade, centro de administração da AD Azure e centro de administração de gestão de dispositivos.

> [!NOTE]
> O papel global dos leitores tem algumas limitações neste momento -
>
>- [OneDrive centro de administração](https://admin.onedrive.com/) - O centro de administração OneDrive não suporta o papel do leitor Global.
>- [Portal Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) - O leitor global não consegue ler o modo de provisionamento de uma aplicação empresarial.
>- [Centro de administração M365](https://admin.microsoft.com/Adminportal/Home#/homepage) - O leitor global não consegue ler pedidos de bloqueio de clientes. Não encontrará o separador de pedidos de **bloqueio do Cliente** sob **suporte** no painel esquerdo do Centro de Administração M365.
>- [Centro de Segurança M365](https://security.microsoft.com/homepage) - O leitor global não consegue ler etiquetas de sensibilidade e retenção. Não encontrará **etiquetas**de sensibilidade, etiquetas de **retenção**e separadores de análise de **etiquetas** no painel esquerdo do centro de segurança M365.
>- [Office Security & Compliance Center](https://sip.protection.office.com/homepage) - O leitor global não consegue ler registos de auditoria do SCC, fazer pesquisa de conteúdo ou ver Secure Score.
>- [Teams admin center](https://admin.teams.microsoft.com) - Global reader can read **Teams lifecycle**, **Analytics & reports**, IP phone device **management** and **App catalog**.
>- [A Privileged Access Management (PAM)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) não apoia o papel de leitor global.
>- [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) - O leitor global é suportado apenas [para reporte central,](https://docs.microsoft.com/azure/information-protection/reports-aip) e quando a sua organização Azure AD não está na [plataforma de rotulagem unificada](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
>
> Estas funcionalidades estão atualmente em desenvolvimento.
>

### <a name="groups-administrator"></a>[Administrador de Grupos](#groups-administrator-permissions)

Os utilizadores desta função podem criar/gerir grupos e suas configurações como políticas de nomeação e expiração. É importante entender que atribuir um utilizador a esta função lhes dá a capacidade de gerir todos os grupos do inquilino em várias cargas de trabalho como Teams, SharePoint, Yammer para além do Outlook. Também o utilizador será capaz de gerir as configurações dos vários grupos em vários portais de administração como o Microsoft Admin Center, portal Azure, bem como os específicos da carga de trabalho, como Teams e SharePoint Admin Centers.

### <a name="guest-inviter"></a>[Convidado convidado](#guest-inviter-permissions)

Os utilizadores desta função podem gerir os convites dos utilizadores do Azure Ative Directory B2B quando os **Membros podem convidar a** definição do utilizador para Nº. Mais informações sobre a colaboração b2B na [colaboração Azure AD B2B.](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) Não inclui outras permissões.

### <a name="helpdesk-administrator"></a>[Administrador de Helpdesk](#helpdesk-administrator-permissions)

Os utilizadores com esta função podem alterar palavras-passe, invalidar tokens de atualização, gerir pedidos de serviço e monitorizar a saúde do serviço. Invalidar um token refrescante obriga o utilizador a voltar a inscrever-se. Os administradores do Helpdesk podem redefinir palavras-passe e invalidar fichas de atualização de outros utilizadores que não sejam administradores ou que apenas sejam atribuídas:

* Leitores de Diretório
* Convidado convidado
* Administrador de Helpdesk
* Leitor de centro de mensagens
* Relatórios Leitor

> [!IMPORTANT]
> Os utilizadores com esta função podem alterar palavras-passe para pessoas que possam ter acesso a informações sensíveis ou privadas ou configuração crítica dentro e fora do Diretório Ativo do Azure. Alterar a palavra-passe de um utilizador pode significar a capacidade de assumir a identidade e as permissões desse utilizador. Por exemplo:
>
>- Registo de aplicações e proprietários de aplicações empresariais, que podem gerir credenciais de aplicações que possuam. Essas aplicações podem ter permissões privilegiadas em Azure AD e em outros lugares não concedidos a Administradores de Helpdesk. Através deste caminho, um Administrador de Helpdesk poderá assumir a identidade de um titular da aplicação e, em seguida, assumir a identidade de uma aplicação privilegiada atualizando as credenciais para a aplicação.
>- Proprietários de subscrições Azure, que podem ter acesso a informações sensíveis ou privadas ou configuração crítica no Azure.
>- Grupo de Segurança e proprietários do Grupo 365, que podem gerir a adesão ao grupo. Esses grupos podem conceder acesso a informações sensíveis ou privadas ou configuração crítica em Azure AD e em outros lugares.
>- Administradores de outros serviços fora da Azure AD como Exchange Online, Office Security and Compliance Center, e sistemas de recursos humanos.
>- Não administradores como executivos, advogados e funcionários de recursos humanos que possam ter acesso a informações sensíveis ou privadas.

Delegar permissões administrativas sobre subconjuntos de utilizadores e aplicar políticas a um subconjunto de utilizadores é possível com [Unidades Administrativas (agora em pré-visualização pública)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).

Esta função foi anteriormente chamada de "Administrador de Passwords" no [portal Azure.](https://portal.azure.com/) O nome "Administrador de Helpdesk" em Azure AD corresponde agora ao seu nome no Azure AD PowerShell e no Microsoft Graph API.

### <a name="hybrid-identity-administrator"></a>[Administrador de Identidade Híbrida](#hybrid-identity-administrator-permissions)

Os utilizadores desta função podem ativar, configurar e gerir serviços e configurações relacionadas com a ativação da identidade híbrida em Azure AD. Esta função confere a capacidade de configurar a AD Azure a um dos três métodos de autenticação suportados, sincronização de hash password (PHS), autenticação pass-through (PTA) ou Federação (AD FS ou fornecedor de 3.ª federação), e implantar infraestruturas relacionadas no local para os permitir. A infraestrutura on-prem inclui agentes de provisionamento e PTA. Esta função permite ativar o Single Sign-On (S-SSO) sem emenda para permitir a autenticação perfeita em dispositivos não Windows 10 ou computadores não Windows Server 2016. Além disso, este papel confere a capacidade de ver registos de login e acesso à saúde e análise para fins de monitorização e resolução de problemas. 

### <a name="intune-administrator"></a>[Administrador inafinado](#intune-service-administrator-permissions)

Os utilizadores com esta função têm permissões globais dentro do Microsoft Intune Online, quando o serviço está presente. Além disso, esta função contém a capacidade de gerir utilizadores e dispositivos de forma a associar a política, bem como criar e gerir grupos. Mais informações sobre [o controlo da administração baseado em Role (RBAC) com](https://docs.microsoft.com/intune/role-based-access-control)a Microsoft Intune .

Este papel pode criar e gerir todos os grupos de segurança. No entanto, a Intune Admin não tem direitos de administração sobre os grupos de escritórios. Isto significa que o administrador não pode atualizar os proprietários ou membros de todos os grupos de escritórios do inquilino. No entanto, pode gerir o grupo office que cria, que vem como parte dos seus privilégios de utilizador final. Assim, qualquer grupo do Office (não grupo de segurança) que cria deve ser contado contra a sua quota de 250.

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Intune Service Administrator". É "Administrador Intune" no [portal Azure.](https://portal.azure.com)

### <a name="kaizala-administrator"></a>[Administrador kaizala](#kaizala-administrator-permissions)

Os utilizadores com esta função têm permissões globais para gerir as definições dentro do Microsoft Kaizala, quando o serviço está presente, bem como a capacidade de gerir bilhetes de suporte e monitorizar a saúde do serviço. Além disso, o utilizador pode aceder a relatórios relacionados com a adoção & utilização de Kaizala por membros da Organização e relatórios empresariais gerados usando as ações kaizala.

### <a name="license-administrator"></a>[Administrador de Licença](#license-administrator-permissions)

Os utilizadores desta função podem adicionar, remover e atualizar as atribuições de licenças em utilizadores, grupos (utilizando licenciamento baseado em grupo), e gerir a localização de utilização nos utilizadores. A função não concede a capacidade de comprar ou gerir subscrições, criar ou gerir grupos, ou criar ou gerir utilizadores para além da localização de utilização. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

### <a name="message-center-privacy-reader"></a>[Leitor de privacidade do Centro de Mensagens](#message-center-privacy-reader-permissions)

Os utilizadores desta função podem monitorizar todas as notificações no Centro de Mensagens, incluindo mensagens de privacidade de dados. O Message Center Privacy Readers recebe notificações por e-mail, incluindo as relacionadas com a privacidade dos dados, e podem cancelar a subscrição usando as Preferências do Centro de Mensagens. Apenas o Administrador Global e o Leitor de Privacidade do Centro de Mensagens podem ler mensagens de privacidade de dados. Além disso, esta função contém a capacidade de ver grupos, domínios e subscrições. Esta função não tem permissão para visualizar, criar ou gerir pedidos de serviço.

### <a name="message-center-reader"></a>[Leitor de centro de mensagens](#message-center-reader-permissions)

Os utilizadores desta função podem monitorizar notificações e atualizações de saúde consultivas no [Office 365 Message center](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) para a sua organização em serviços configurados como Exchange, Intune e Microsoft Teams. Os leitores do Centro de Mensagens recebem semanalmente e-mails de publicações, atualizações e podem partilhar posts de centros de mensagens no Office 365. No Azure AD, os utilizadores atribuídos a esta função só terão acesso apenas a leitura sacana em serviços da Azure AD, como utilizadores e grupos. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

### <a name="network-administrator"></a>[Administrador de Rede](#network-administrator-permissions)

Os utilizadores desta função podem rever as recomendações de arquitetura do perímetro da rede da Microsoft que se baseiam na telemetria da rede a partir das suas localizações de utilizador. O desempenho da rede para o Office 365 baseia-se na arquitetura cuidadosa do perímetro da rede de clientes empresariais, que é geralmente específica para a localização do utilizador. Esta função permite a edição de locais de utilizador descobertos e a configuração de parâmetros de rede para esses locais para facilitar a melhoria das medições de telemetria e recomendações de design. 

### <a name="office-apps-administrator"></a>[Administrador de aplicações de escritório](#office-apps-administrator-permissions)

Os utilizadores desta função podem gerir as definições de nuvem das aplicações do Office 365. Isto inclui a gestão de políticas de nuvem, gestão de autosserviços e a capacidade de ver relatóriorelacionado com aplicações do Office. Este papel também confere a capacidade de gerir os bilhetes de apoio e monitorizar a saúde do serviço dentro do principal centro de administração. Os utilizadores atribuídos a esta função também podem gerir a comunicação de novas funcionalidades nas aplicações do Office. 

### <a name="partner-tier1-support"></a>[Suporte de parceiro tier1](#partner-tier1-support-permissions)

Não utilizar. Este papel foi depreciado e será removido da AD Azure no futuro. Esta função destina-se a ser utilizada por um pequeno número de parceiros de revenda da Microsoft, e não se destina a uma utilização geral.

### <a name="partner-tier2-support"></a>[Suporte de parceiro tier2](#partner-tier2-support-permissions)

Não utilizar. Este papel foi depreciado e será removido da AD Azure no futuro. Esta função destina-se a ser utilizada por um pequeno número de parceiros de revenda da Microsoft, e não se destina a uma utilização geral.

### <a name="password-administrator"></a>[Administrador de palavra-passe](#password-administrator-permissions)

Os utilizadores com esta função têm capacidade limitada para gerir senhas. Esta função não confere a capacidade de gerir pedidos de serviçoou monitorizar a saúde do serviço. Os administradores de passwords podem redefinir palavras-passe de outros utilizadores que não sejam administradores ou membros das seguintes funções:

* Leitores de Diretório
* Convidado convidado
* Administrador de palavra-passe

### <a name="power-bi-administrator"></a>[Administrador power BI](#power-bi-service-administrator-permissions)

Os utilizadores com esta função têm permissões globais dentro do Microsoft Power BI, quando o serviço está presente, bem como a capacidade de gerir bilhetes de suporte e monitorizar a saúde do serviço. Mais informações sobre [a compreensão do papel de administrador do Power BI.](https://docs.microsoft.com/power-bi/service-admin-role)

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Power BI Service Administrator". É "Power BI Administrator" no [portal Azure.](https://portal.azure.com)

### <a name="power-platform-administrator"></a>[Administrador da Plataforma de Energia](#power-platform-administrator-permissions)

Os utilizadores desta função podem criar e gerir todos os aspetos dos ambientes, PowerApps, Flows, Data Loss Prevention. Além disso, os utilizadores com esta função têm a capacidade de gerir os bilhetes de apoio e monitorizar a saúde do serviço.

### <a name="printer-administrator"></a>[Administrador de impressora](#printer-administrator-permissions)

Os utilizadores desta função podem registar impressoras e gerir todos os aspetos de todas as configurações da impressora na solução Microsoft Universal Print, incluindo as definições do Conector Universal Print. Podem consentir em todos os pedidos de autorização de impressão delegados. Os Administradores da Impressora também têm acesso a relatórios de impressão.

### <a name="printer-technician"></a>[Técnico de impressora](#printer-technician-permissions)

Os utilizadores com esta função podem registar impressoras e gerir o estado da impressora na solução Microsoft Universal Print. Também podem ler todas as informações sobre conector. Tarefa chave que um Técnico de Impressora não pode fazer é definir permissões do utilizador em impressoras e partilhar impressoras.

### <a name="privileged-authentication-administrator"></a>[Administrador de Autenticação Privilegiada](#privileged-authentication-administrator-permissions)

Os utilizadores com esta função podem definir ou redefinir credenciais de não-senha para todos os utilizadores, incluindo administradores globais, e podem atualizar palavras-passe para todos os utilizadores. Os Administradores de Autenticação Privilegiada podem forçar os utilizadores a reregistarem-se contra a credencial não-senha existente (por exemplo, MFA, FIDO) e a revogar "lembre-se do MFA no dispositivo", solicitando ao MFA o próximo login de todos os utilizadores.

### <a name="privileged-role-administrator"></a>[Administrador privilegiado](#privileged-role-administrator-permissions)

Os utilizadores com esta função podem gerir atribuições de funções no Diretório Ativo Azure, bem como na Azure AD Privileged Identity Management. Além disso, esta função permite a gestão de todos os aspetos da Gestão de Identidade Privilegiada e unidades administrativas.

> [!IMPORTANT]
> Este papel confere a capacidade de gerir atribuições para todas as funções da AD Azure, incluindo o papel de Administrador Global. Esta função não inclui quaisquer outras habilidades privilegiadas em Anúncios Azure, como criar ou atualizar utilizadores. No entanto, os utilizadores atribuídos a esta função podem conceder a si mesmos ou a outros privilégios adicionais, atribuindo funções adicionais.

### <a name="reports-reader"></a>[Relatórios Leitor](#reports-reader-permissions)

Os utilizadores com esta função podem ver os dados de relatórios de utilização e o dashboard de relatórios no centro de administração da Microsoft 365 e o pacote de contexto de adoção no Power BI. Além disso, a função fornece acesso a relatórios de inscrição e atividade em AD Azure e dados devolvidos pela API do Microsoft Graph. Um utilizador atribuído à função De Leitor de Relatórios só pode aceder a métricas de utilização e adoção relevantes. Não têm permissões de administração para configurar configurações ou aceder aos centros de administração específicos do produto, como o Exchange. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

### <a name="search-administrator"></a>[Administrador de Pesquisa](#search-administrator-permissions)

Os utilizadores desta função têm acesso total a todas as funcionalidades de gestão do Microsoft Search no centro de administração da Microsoft 365. Os Administradores de Pesquisa podem delegar as funções de Administradores de Pesquisa e Editor de Pesquisa aos utilizadores, e criar e gerir conteúdos, como marcadores, Q&As e locais. Além disso, estes utilizadores podem ver o centro de mensagens, monitorizar a saúde do serviço e criar pedidos de serviço.

### <a name="search-editor"></a>[Editor de Pesquisa](#search-editor-permissions)

Os utilizadores desta função podem criar, gerir e eliminar conteúdos para o Microsoft Search no centro de administração da Microsoft 365, incluindo marcadores, Q&As e localizações.

### <a name="security-administrator"></a>[Administrador de Segurança](#security-administrator-permissions)

Os utilizadores com esta função têm permissões para gerir funcionalidades relacionadas com a segurança no centro de segurança Microsoft 365, Azure Ative Directory Identity Protection, Azure Information Protection e Office 365 Security & Compliance Center. Mais informações sobre as permissões do Office 365 estão disponíveis [nas Permissões do Office 365 Security & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

Entrada | Pode fazer
--- | ---
[Centro de segurança Microsoft 365](https://protection.office.com) | Monitorize as políticas relacionadas com a segurança em todos os serviços da Microsoft 365<br>Gerir ameaças e alertas de segurança<br>Ver relatórios
Centro de Proteção de Identidade | Todas as permissões do papel do Leitor de Segurança<br>Adicionalmente, a capacidade de executar todas as operações do Centro de Proteção de Identidade, com exceção da reposição de senhas
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Todas as permissões do papel do Leitor de Segurança<br>**Não é possível** gerir atribuições ou configurações de funções da Azure AD
[Escritório 365 Centro de Conformidade & de Segurança](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerir políticas de segurança<br>Ver, investigar e responder a ameaças à segurança<br>Ver relatórios
Proteção Avançada Contra Ameaças do Azure | Monitorizar e responder a atividades de segurança suspeitas
Windows Defender ATP e EDR | Atribuir funções<br>Gerir grupos de máquinas<br>Configurar deteção de ameaças de ponto final e remediação automatizada<br>Ver, investigar e responder a alertas
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visualizações de utilizador, dispositivo, inscrição, configuração e informações de aplicação<br>Não pode fazer alterações ao Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Adicione administradores, adicione políticas e configurações, carregue registos e execute ações de governação
[Centro de Segurança do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Pode ver políticas de segurança, ver estados de segurança, editar políticas de segurança, ver alertas e recomendações, rejeitar alertas e recomendações
[Escritório 365 saúde de serviço](https://docs.microsoft.com/office365/enterprise/view-service-health) | Ver a saúde dos serviços do Office 365

### <a name="security-operator"></a>[Operador de segurança](#security-operator-permissions)

Os utilizadores com esta função podem gerir alertas e ter acesso global apenas a leitura sondar funcionalidades relacionadas com a segurança, incluindo todas as informações no centro de segurança Microsoft 365, Diretório Ativo Azure, Proteção de Identidade, Gestão de Identidade Privilegiada e Office 365 Security & Compliance Center. Mais informações sobre as permissões do Office 365 estão disponíveis [nas Permissões do Office 365 Security & Compliance Center](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

Entrada | Pode fazer
--- | ---
[Centro de segurança Microsoft 365](https://protection.office.com) | Todas as permissões do papel do Leitor de Segurança<br>Ver, investigar e responder a alertas de ameaças à segurança
Centro de Proteção de Identidade | Todas as permissões do papel do Leitor de Segurança<br>Adicionalmente, a capacidade de executar todas as operações do Centro de Proteção de Identidade, com exceção da reposição de senhas
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Todas as permissões do papel do Leitor de Segurança
[Escritório 365 Centro de Conformidade & de Segurança](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Todas as permissões do papel do Leitor de Segurança<br>Ver, investigar e responder a alertas de segurança
Windows Defender ATP e EDR | Todas as permissões do papel do Leitor de Segurança<br>Ver, investigar e responder a alertas de segurança
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Todas as permissões do papel do Leitor de Segurança
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Todas as permissões do papel do Leitor de Segurança
[Escritório 365 saúde de serviço](https://docs.microsoft.com/office365/enterprise/view-service-health) | Ver a saúde dos serviços do Office 365

### <a name="security-reader"></a>[Leitor de Segurança](#security-reader-permissions)

Os utilizadores com esta função têm acesso global apenas a leitura sonoridade seleções em funcionalidades relacionadas com a segurança, incluindo todas as informações no centro de segurança Microsoft 365, Diretório Ativo Azure, Proteção de Identidade, Gestão de Identidade Privilegiada, bem como a capacidade de ler relatórios de inscrição e registos de auditoria do Azure Ative Directory, e no Office 365 Security & Compliance Center. Mais informações sobre as permissões do Office 365 estão disponíveis [nas Permissões do Office 365 Security & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

Entrada | Pode fazer
--- | ---
[Centro de segurança Microsoft 365](https://protection.office.com) | Ver políticas relacionadas com a segurança em todos os serviços da Microsoft 365<br>Ver ameaças e alertas de segurança<br>Ver relatórios
Centro de Proteção de Identidade | Leia todos os relatórios de segurança e informações sobre as funcionalidades de segurança<br><ul><li>Anti-correio publicitário não solicitado<li>Encriptação<li>Prevenção de perdas de dados<li>Anti-malware<li>Proteção avançada contra ameaças<li>Anti-phishing<li>Regras de fluxo de correio
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Tem acesso apenas a todas as informações que surgiram na Azure AD Privileged Identity Management: Policies and reports for Azure AD role assignments and security reviews.<br>**Não é possível** inscrever-se na Azure AD Privileged Identity Management ou fazer quaisquer alterações à sua gestão. No portal de Gestão de Identidade Privilegiada ou via PowerShell, alguém nesta função pode ativar funções adicionais (por exemplo, Administrador Global ou Administrador de Papel Privilegiado), se o utilizador for elegível para eles.
[Escritório 365 Centro de Conformidade & de Segurança](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Ver as políticas de segurança<br>Ver e investigar ameaças à segurança<br>Ver relatórios
Windows Defender ATP e EDR | Ver e investigar alertas. Quando liga o controlo de acesso baseado em funções no Windows Defender ATP, os utilizadores com permissões de leitura como a função de leitor de Segurança AD Azure perdem acesso até serem atribuídos a uma função ATP do Windows Defender.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Vê informações sobre utilizadores, dispositivos, inscrições, configuração e aplicação. Não pode fazer alterações ao Intune.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões de leitura e pode gerir alertas
[Centro de Segurança do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Pode ver recomendações e alertas, ver políticas de segurança, ver estados de segurança, mas não pode fazer alterações
[Escritório 365 saúde de serviço](https://docs.microsoft.com/office365/enterprise/view-service-health) | Ver a saúde dos serviços do Office 365

### <a name="service-support-administrator"></a>[Administrador de Suporte ao Serviço](#service-support-administrator-permissions)

Os utilizadores com esta função podem abrir pedidos de suporte com a Microsoft para serviços Azure e Office 365, e visualizar o dashboard de serviço e o centro de mensagens no [portal Azure](https://portal.azure.com) e no centro de administração da [Microsoft 365.](https://admin.microsoft.com) Mais informações sobre [as funções de administrador.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> Anteriormente, esta função chamava-se "Administrador de Serviços" no [portal Azure](https://portal.azure.com) e no centro de administração da [Microsoft 365.](https://admin.microsoft.com) Rebatizámo-lo como "Administrador de Suporte de Serviço" para alinhar com o nome exsitante no Microsoft Graph API, Azure AD Graph API e Azure AD PowerShell.

### <a name="sharepoint-administrator"></a>[Administrador do SharePoint](#sharepoint-service-administrator-permissions)

Os utilizadores com esta função têm permissões globais dentro do Microsoft SharePoint Online, quando o serviço está presente, bem como a capacidade de criar e gerir todos os Grupos Office 365, gerir bilhetes de suporte e monitorizar a saúde do serviço. Mais informações sobre [as funções de administrador.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço SharePoint". É "Administrador SharePoint" no [portal Azure](https://portal.azure.com).

### <a name="skype-for-business--lync-administrator"></a>[Skype para Administrador de Negócios / Lync](#lync-service-administrator-permissions)

Os utilizadores com esta função têm permissões globais dentro do Microsoft Skype para o Negócio, quando o serviço está presente, bem como gerem atributos de utilizador específicos do Skype no Diretório Ativo do Azure. Além disso, este papel confere a capacidade de gerir bilhetes de apoio e monitorizar a saúde do serviço, e de aceder às Equipas e Skype para o Business Admin Center. A conta também deve ser licenciada para Equipas ou não pode executar as equipas PowerShell cmdlets. Mais informações sobre o papel de administrador do [Skype para empresas](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) e informações de licenciamento de equipas no Skype para licenciamento de [empresas e equipas microsoft](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> No Microsoft Graph API e Azure AD PowerShell, esta função é identificada como "Administrador de Serviço Lync". É "Skype for Business Administrator" no [portal Azure.](https://portal.azure.com/)

### <a name="teams-communications-administrator"></a>[Administrador de Comunicações de Equipas](#teams-communications-administrator-permissions)

Os utilizadores desta função podem gerir aspetos da carga de trabalho das Equipas Microsoft relacionadas com a telefonia & voz. Isto inclui as ferramentas de gestão para atribuição de números de telefone, políticas de voz e reunião, e acesso total ao conjunto de ferramentas de análise de chamadas.

### <a name="teams-communications-support-engineer"></a>[Engenheiro de Apoio às Comunicações de Equipas](#teams-communications-support-engineer-permissions)

Os utilizadores desta função podem resolver problemas de comunicação dentro das Microsoft Teams & Skype for Business utilizando as ferramentas de resolução de problemas de chamadas de utilizadores nas Equipas microsoft & Skype para o centro de administração de Negócios. Os utilizadores desta função podem visualizar informações completas sobre registos de chamadas para todos os participantes envolvidos. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

### <a name="teams-communications-support-specialist"></a>[Especialista em Apoio às Comunicações de Equipas](#teams-communications-support-specialist-permissions)

Os utilizadores desta função podem resolver problemas de comunicação dentro das Microsoft Teams & Skype for Business utilizando as ferramentas de resolução de problemas de chamadas de utilizadores nas Equipas microsoft & Skype para o centro de administração de Negócios. Os utilizadores desta função só podem visualizar os detalhes do utilizador na chamada para o utilizador específico que procuraram. Esta função não tem acesso a visualização, criação ou gestão de bilhetes de apoio.

### <a name="teams-service-administrator"></a>[Administrador de Serviço de Equipas](#teams-service-administrator-permissions)

Os utilizadores desta função podem gerir todos os aspetos da carga de trabalho das Equipas Microsoft através das Equipas Microsoft & Skype para o centro de administração de negócios e os respetivos módulos PowerShell. Isto inclui, entre outras áreas, todas as ferramentas de gestão relacionadas com a telefonia, mensagens, reuniões e as próprias equipas. Este papel também confere a capacidade de criar e gerir todos os Grupos do Office 365, gerir os bilhetes de apoio e monitorizar a saúde do serviço.

### <a name="user-administrator"></a>[Administrador de Utilizadores](#user-administrator-permissions)

Os utilizadores com esta função podem criar utilizadores e gerir todos os aspetos dos utilizadores com algumas restrições (ver abaixo), e podem atualizar as políticas de expiração da palavra-passe. Além disso, os utilizadores com esta função podem criar e gerir todos os grupos. Esta função inclui também a capacidade de criar e gerir as opiniões dos utilizadores, gerir bilhetes de apoio e monitorizar a saúde do serviço. Os administradores de utilizadores não têm permissão para gerir algumas propriedades de utilizadores para utilizadores na maioria das funções de administrador. O utilizador com esta função não tem permissões para gerir o MFA. As funções que são exceções a esta restrição constam da tabela seguinte.

| | |
| --- | --- |
|Permissões gerais|<p>Criar utilizadores e grupos</p><p>Criar e gerir vistas de utilizador</p><p>Gerir bilhetes de apoio ao Office<p>Atualizar as políticas de expiração da palavra-passe|
|<p>Em todos os utilizadores, incluindo todos os administradores</p>|<p>Gerir licenças</p><p>Gerir todas as propriedades do utilizador, exceto o nome principal do utilizador</p>
|Apenas nos utilizadores que não sejam administradores ou em qualquer uma das seguintes funções de administração limitada:<ul><li>Leitores de Diretório<li>Convidado convidado<li>Administrador de Helpdesk<li>Leitor de centro de mensagens<li>Relatórios Leitor<li>Administrador de Utilizadores|<p>Eliminar e restaurar</p><p>Desativar e ativar</p><p>Invalidar tokens de refresco</p><p>Gerir todas as propriedades do utilizador, incluindo o nome principal do utilizador</p><p>Repor palavra-passe</p><p>Teclas do dispositivo de atualização (FIDO)</p>|

> [!IMPORTANT]
> Os utilizadores com esta função podem alterar palavras-passe para pessoas que possam ter acesso a informações sensíveis ou privadas ou configuração crítica dentro e fora do Diretório Ativo do Azure. Alterar a palavra-passe de um utilizador pode significar a capacidade de assumir a identidade e as permissões desse utilizador. Por exemplo:
>
>- Registo de aplicações e proprietários de aplicações empresariais, que podem gerir credenciais de aplicações que possuam. Essas aplicações podem ter permissões privilegiadas em Azure AD e em outros lugares não concedidos a Administradores de Utilizadores. Através deste caminho, um Administrador de Utilizador poderá assumir a identidade de um titular da aplicação e, em seguida, assumir a identidade de uma aplicação privilegiada atualizando as credenciais para a aplicação.
>- Proprietários de subscrições Azure, que podem ter acesso a informações sensíveis ou privadas ou configuração crítica no Azure.
>- Grupo de Segurança e proprietários do Grupo 365, que podem gerir a adesão ao grupo. Esses grupos podem conceder acesso a informações sensíveis ou privadas ou configuração crítica em Azure AD e em outros lugares.
>- Administradores de outros serviços fora da Azure AD como Exchange Online, Office Security and Compliance Center, e sistemas de recursos humanos.
>- Não administradores como executivos, advogados e funcionários de recursos humanos que possam ter acesso a informações sensíveis ou privadas.

## <a name="role-permissions"></a>Permissões de funções

As tabelas seguintes descrevem as permissões específicas no Diretório Ativo Azure dadas a cada função. Algumas funções podem ter permissões adicionais nos serviços da Microsoft fora do Azure Ative Directory.

### <a name="application-administrator-permissions"></a>Permissões do Administrador de Aplicação

Pode criar e gerir todos os aspetos dos registos de aplicações e aplicações empresariais.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/Application/appProxyAuthentication/update | Atualizar as propriedades de autenticação da App Proxy nos principais de serviço sintetizar em Diretório Ativo Azure. |
| microsoft.directory/Application/appProxyUrlSettings/update | Atualizar urls internos e externos proxy proxy em Diretório Ativo Azure. |
| microsoft.directy/applications/applicationProxy/read | Leia todas as propriedades da App Proxy. |
| microsoft.directy/applications/applicationProxy/update | Atualize todas as propriedades da App Proxy. |
| microsoft.directy/applications/audience/update | Atualizar aplicações.propriedade do público em Azure Ative Directory. |
| microsoft.directy/applications/autenticação/atualização | Atualizar aplicações.propriedade de autenticação no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas sobre aplicações no Diretório Ativo Azure. |
| microsoft.directy/applications/create | Criar aplicações no Diretório Ativo Azure. |
| microsoft.directy/applications/credentials/update | Atualizar aplicações.credenciais propriedade em Azure Ative Directory. |
| microsoft.directy/applications/delete | Eliminar aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/proprietários/atualização | Atualizar aplicações.proprietários propriedade em Azure Ative Diretório. |
| microsoft.directy/applications/permissions/update | Atualizar aplicações.permissões propriedade em Azure Ative Directory. |
| microsoft.directy/applications/policies/update | Atualizar aplicações.policies property in Azure Ative Directory. |
| microsoft.directy/appRoleAssignments/create | Crie appRoleAssignments no Diretório Ativo Azure. |
| microsoft.directy/appRoleAssignments/read | Leia as atribuições de aplicações no Diretório Ativo Azure. |
| microsoft.directy/appRoleAssignments/update | Atualizar appRoleAssignments no Diretório Ativo Azure. |
| microsoft.directy/appRoleAssignments/delete | Eliminar as atribuições de aplicações no Diretório Ativo Azure. |
| microsoft.directy/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditlogs no Diretório Ativo Azure. |
| microsoft.directy/connectorGroups/everything/read | Leia as propriedades do grupo de conector proxy de aplicação em Azure Ative Directory. |
| microsoft.directy/connectorGroups/everything/update | Atualize todas as propriedades do grupo de conector proxy de aplicação em Azure Ative Directory. |
| microsoft.directy/connectorGroups/create | Crie grupos de conector proxy de aplicação no Diretório Ativo Azure. |
| microsoft.directy/connectorGroups/delete | Eliminar os grupos de conector proxy de aplicação no Diretório Ativo Azure. |
| microsoft.diretório/conectores/tudo/ler | Leia todas as propriedades do conector proxy de aplicação no Diretório Ativo Azure. |
| microsoft.diretório/conectores/criar | Crie conectores proxy de aplicação no Diretório Ativo Azure. |
| microsoft.diretório/policies/applicationConfiguração/básico/leitura | Leia políticas.applicationConfiguração propriedade em Diretório Ativo Azure. |
| microsoft.directy/policies/applicationConfiguração/básico/atualização | Atualizar políticas.applicationConfiguração propriedade em Diretório Ativo Azure. |
| microsoft.diretório/policies/applicationConfiguração/criação | Criar políticas no Diretório Ativo Azure. |
| microsoft.diretório/policies/applicationConfiguração/exclusão | Eliminar políticas no Diretório Ativo Azure. |
| microsoft.diretório/policies/applicationConfiguração/proprietários/leitura | Leia políticas.applicationConfiguração propriedade em Diretório Ativo Azure. |
| microsoft.directy/policies/applicationConfigura/owners/update | Atualizar políticas.applicationConfiguração propriedade em Diretório Ativo Azure. |
| microsoft.directy/policies/applicationConfiguração/policyAppliedTo/read | Leia políticas.applicationConfiguração propriedade em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/appRoleAssignedTo/update | Atualizar serviçoSPrincipais.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/appRoleAssignments/update | Atualizar serviçoSPrincipais.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/audience/update | Atualizar serviçoSPrincipais.propriedade do público em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/autenticação/atualização | Atualizar serviçoSPrincipais.propriedade de autenticação em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/basic/update | Atualizar propriedades básicas em serviçoPrincipais em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/criar | Crie serviços Diretores em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/credenciais/atualização | Atualizar serviçoSPrincipais.credenciais propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/delete | Elimine os principais de serviço no Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/proprietários/atualização | Atualizar serviçoSPrincipais.proprietários propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/permissões/atualização | Atualizar serviçoSPrincipais.permissions propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/policies/update | Atualizar serviçoSPrincipais.policies propriedade em Azure Ative Directory. |
| microsoft.directy/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="application-developer-permissions"></a>Permissões de Desenvolvedor de Aplicações

Pode criar registos de aplicações independentes da definição de 'Utilizadores podem registar aplicações'.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/applications/createAsOwner | Criar aplicações no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta com a quota de 250 objetos criados pelo criador. |
| microsoft.directy/appRoleAssignments/createAsOwner | Crie appRoleAssignments no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta com a quota de 250 objetos criados pelo criador. |
| microsoft.directy/oAuth2PermissionGrants/createAsOwner | Crie oAuth2PermissionGrants no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta com a quota de 250 objetos criados pelo criador. |
| microsoft.directy/servicePrincipas/createAsOwner | Crie serviços Diretores em Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta com a quota de 250 objetos criados pelo criador. |

### <a name="authentication-administrator-permissions"></a>Autorizações do Administrador de Autenticação

Permitido visualizar, definir e repor informações do método de autenticação para qualquer utilizador não administrador.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/users/invalidateAllRefreshTokens | Invalide todas as fichas de atualização do utilizador no Diretório Ativo Azure. |
| microsoft.directy/users/strongAuthentication/update | Atualizar propriedades de autenticação forte como informações de credenciais MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |
| microsoft.directy/users/password/update | Atualizar palavras-passe para todos os utilizadores da organização Office 365. Consulte a documentação online para mais detalhes. |

### <a name="azure-devops-administrator-permissions"></a>Permissões do administrador Azure DevOps

Pode gerir a política e configurações da organização Azure DevOps.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a [descrição](#azure-devops-administrator) do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | Leia e configure Os DevOps Azure. |

### <a name="azure-information-protection-administrator-permissions"></a>Permissão do Administrador de Proteção de Informação Azure

Pode gerir todos os aspetos do serviço de Proteção de Informação Azure.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a [descrição](#) do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerir todos os aspetos da Proteção de Informação Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="b2c-user-flow-administrator-permissions"></a>Permissões do Administrador de Fluxo de Utilizador B2C

Criar e gerir todos os aspetos dos fluxos de utilizadores.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Leia e configure os fluxos de utilizadores no Diretório Ativo Azure B2C. |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>Permissões do administrador do utilizador B2C

Crie e gerencie o esquema de atributo disponível para todos os fluxos de utilizador.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/userAtributos/allTasks | Leia e configure os atributos dos utilizadores no Diretório Ativo Azure B2C. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>Permissões do administrador de keyset B2C IEF

Gerir segredos para a federação e encriptação no Quadro de Experiência de Identidade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Leia e configure os conjuntos de chaves no Diretório Ativo Azure B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>Permissões do Administrador de Política B2C IEF

Criar e gerir políticas-quadro de confiança no Quadro de Experiência seleção de Identidade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Leia e configure políticas personalizadas no Diretório Ativo Azure B2C. |

### <a name="billing-administrator-permissions"></a>Permissões do Administrador de Faturação

Pode executar tarefas relacionadas com a faturação comum, como atualizar informações de pagamento.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/organization/basic/update | Atualizar propriedades básicas sobre organização em Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gerencie todos os aspetos da faturação do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="cloud-application-administrator-permissions"></a>Permissões do Administrador de Aplicação cloud

Pode criar e gerir todos os aspetos dos registos de aplicações e aplicações empresariais, exceto app Proxy.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/applications/audience/update | Atualizar aplicações.propriedade do público em Azure Ative Directory. |
| microsoft.directy/applications/autenticação/atualização | Atualizar aplicações.propriedade de autenticação no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas sobre aplicações no Diretório Ativo Azure. |
| microsoft.directy/applications/create | Criar aplicações no Diretório Ativo Azure. |
| microsoft.directy/applications/credentials/update | Atualizar aplicações.credenciais propriedade em Azure Ative Directory. |
| microsoft.directy/applications/delete | Eliminar aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/proprietários/atualização | Atualizar aplicações.proprietários propriedade em Azure Ative Diretório. |
| microsoft.directy/applications/permissions/update | Atualizar aplicações.permissões propriedade em Azure Ative Directory. |
| microsoft.directy/applications/policies/update | Atualizar aplicações.policies property in Azure Ative Directory. |
| microsoft.directy/appRoleAssignments/create | Crie appRoleAssignments no Diretório Ativo Azure. |
| microsoft.directy/appRoleAssignments/update | Atualizar appRoleAssignments no Diretório Ativo Azure. |
| microsoft.directy/appRoleAssignments/delete | Eliminar as atribuições de aplicações no Diretório Ativo Azure. |
| microsoft.directy/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditlogs no Diretório Ativo Azure. |
| microsoft.diretório/policies/applicationConfiguração/criação | Criar políticas no Diretório Ativo Azure. |
| microsoft.diretório/policies/applicationConfiguração/básico/leitura | Leia políticas.applicationConfiguração propriedade em Diretório Ativo Azure. |
| microsoft.directy/policies/applicationConfiguração/básico/atualização | Atualizar políticas.applicationConfiguração propriedade em Diretório Ativo Azure. |
| microsoft.diretório/policies/applicationConfiguração/exclusão | Eliminar políticas no Diretório Ativo Azure. |
| microsoft.diretório/policies/applicationConfiguração/proprietários/leitura | Leia políticas.applicationConfiguração propriedade em Diretório Ativo Azure. |
| microsoft.directy/policies/applicationConfigura/owners/update | Atualizar políticas.applicationConfiguração propriedade em Diretório Ativo Azure. |
| microsoft.directy/policies/applicationConfiguração/policyAppliedTo/read | Leia políticas.applicationConfiguração propriedade em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/appRoleAssignedTo/update | Atualizar serviçoSPrincipais.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/appRoleAssignments/update | Atualizar serviçoSPrincipais.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/audience/update | Atualizar serviçoSPrincipais.propriedade do público em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/autenticação/atualização | Atualizar serviçoSPrincipais.propriedade de autenticação em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/basic/update | Atualizar propriedades básicas em serviçoPrincipais em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/criar | Crie serviços Diretores em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/credenciais/atualização | Atualizar serviçoSPrincipais.credenciais propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/delete | Elimine os principais de serviço no Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/proprietários/atualização | Atualizar serviçoSPrincipais.proprietários propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/permissões/atualização | Atualizar serviçoSPrincipais.permissions propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/policies/update | Atualizar serviçoSPrincipais.policies propriedade em Azure Ative Directory. |
| microsoft.directy/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="cloud-device-administrator-permissions"></a>Permissões do Administrador de Dispositivos cloud

Acesso total para gerir dispositivos em Azure AD.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditlogs no Diretório Ativo Azure. |
| microsoft.directy/devices/bitLockerRecoveryKeys/read | Leia a propriedade devices.bitLockerRecoveryKeys no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/eliminar | Eliminar dispositivos no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/desativar | Desative os dispositivos no Diretório Ativo Azure. |
| microsoft.directy/devices/enable | Ativar dispositivos no Diretório Ativo Azure. |
| microsoft.directy/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |

### <a name="company-administrator-permissions"></a>Permissões do Administrador da Empresa

Pode gerir todos os aspetos dos serviços Azure AD e Microsoft que utilizam identidades Azure AD. Este papel também é conhecido como o papel de Administrador Global. 

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize propriedades padrão em microsoft.aad.cloudAppSecurity. |
| microsoft.directy/administrativeUnits/allProperties/allTasks | Crie e elimine unidades administrativas e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/applications/allProperties/allTasks | Crie e elimine aplicações e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/appRoleAssignments/allProperties/allTasks | Crie e elimine as appRoleAssignments e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditlogs no Diretório Ativo Azure. |
| microsoft.directy/contacts/allProperties/allTasks | Crie e elimine contactos e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/contracts/allProperties/allTasks | Crie e elimine contratos e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/devices/allProperties/allTasks | Crie e elimine dispositivos e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/directyRoles/allProperties/allTasks | Crie e elimine os diretórios, e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/directyRoleTemplates/allProperties/allTasks | Crie e elimine os modelos de rolea e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/domínios/allProperties/allTasks | Crie e elimine domínios e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/groups/allProperties/allTasks | Crie e elimine grupos, e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/groupSettings/allProperties/allTasks | Crie e elimine as Definições de grupo, e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/groupSettingTemplates/allProperties/allTasks | Crie e elimine modelos de definição de grupo, e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/loginTenantBranding/allProperties/allTasks | Crie e elimine loginTenantBranding, e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Crie e elimine o OAuth2PermissionGrants e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/organization/allProperties/allTasks | Crie e elimine a organização, e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/policies/allProperties/allTasks | Crie e elimine políticas e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/roleAssignments/allProperties/allTasks | Crie e elimine as roleAssignments e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/roleDefinitions/allProperties/allTasks | Crie e elimine definições de papéis e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/scopedRoleMemberships/allProperties/allTasks | Crie e elimine os ScopedRoleMemberships e leia e atualize todas as propriedades no Diretório Ativo Do Azure. |
| microsoft.directy/serviceAction/activateService | Pode realizar a ação de serviço Activateservice no Diretório Ativo Azure |
| microsoft.directy/serviceAction/disableDirectoryFeature | Pode realizar a ação de serviço de funcionalidade de desativação no Diretório Ativo Azure |
| microsoft.directy/serviceAction/enableDirectoryFeature | Pode realizar a ação de serviço Enabledirectoryfeature no Diretório Ativo Azure |
| microsoft.directy/serviceAction/getAvailableExtentionProperties | Pode realizar a ação de serviço Getavailableextentionproperties no Diretório Ativo Azure |
| microsoft.directy/servicePrincipas/allProperties/allTasks | Crie e elimine os principais de serviços e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.directy/subscritoSkus/allProperties/allTasks | Crie e elimine o Skus subscrito e leia e atualize todas as propriedades no Diretório Ativo Azure. |
| microsoft.directy/users/allProperties/allTasks | Crie e elimine os utilizadores e leia e atualize todas as propriedades no Diretório Ativo Do Azure. |
| microsoft.directorySync/allEntities/allTasks | Execute todas as ações no Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize as propriedades padrão em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Leia todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Leia todos os recursos em microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerir todos os aspetos da Proteção de Informação Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gerencie todos os aspetos da faturação do Office 365. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspetos de Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gerir todos os aspetos do Office 365 Compliance Manager |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerencie todos os aspetos do Desktop Analytics. |
| microsoft.office365.exchange/allEntities/allTasks | Gerencie todos os aspetos do Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Gerir todos os aspetos do Office 365 Customer Lockbox |
| microsoft.office365.messageCenter/messages/read | Leia mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leia segurançaMensagens em microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Gerencie todos os aspetos do Centro de Proteção do Escritório 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize as propriedades padrão em microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize as propriedades padrão em microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspetos do Skype para o Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de utilização do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerencie todos os aspetos da Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerencie todos os aspetos do Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Leia todos os recursos em microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator-permissions"></a>Permissões do Administrador de Conformidade

Pode ler e gerir a configuração de conformidade e relatórios em Azure AD e Office 365.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gerir todos os aspetos do Office 365 Compliance Manager |
| microsoft.office365.exchange/allEntities/allTasks | Gerencie todos os aspetos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize as propriedades padrão em microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspetos do Skype para o Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="compliance-data-administrator-permissions"></a>Permissões do Administrador de Dados de Conformidade

Cria e gere o conteúdo de conformidade.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Leia e configure a Segurança da Aplicação Microsoft Cloud. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerir todos os aspetos da Proteção de Informação Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gerir todos os aspetos do Office 365 Compliance Manager |
| microsoft.office365.exchange/allEntities/allTasks | Gerencie todos os aspetos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize as propriedades padrão em microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspetos do Skype para o Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="conditional-access-administrator-permissions"></a>Permissões do Administrador de Acesso Condicional

Pode gerir as capacidades de Acesso Condicional.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/policies/conditionalAccess/basic/read | Leia as políticas.condicionalAccess propriedade em Diretório Ativo Azure. |
| microsoft.directy/policies/conditionalAccess/basic/update | Atualizar políticas.condicionalAccess propriedade em Diretório Ativo Azure. |
| microsoft.directy/policies/conditionalAccess/create | Criar políticas no Diretório Ativo Azure. |
| microsoft.directy/policies/conditionalAccess/delete | Eliminar políticas no Diretório Ativo Azure. |
| microsoft.directy/policies/condicionalAccess/owners/read | Leia as políticas.condicionalAccess propriedade em Diretório Ativo Azure. |
| microsoft.directy/policies/conditionalAccess/owners/update | Atualizar políticas.condicionalAccess propriedade em Diretório Ativo Azure. |
| microsoft.directy/policies/conditionalAccess/policiesAppliedTo/read | Leia as políticas.condicionalAccess propriedade em Diretório Ativo Azure. |
| microsoft.directy/policies/conditionalAccess/tenantDefault/update | Atualizar políticas.condicionalAccess propriedade em Diretório Ativo Azure. |

### <a name="crm-service-administrator-permissions"></a>Permissões do Administrador de Serviço CRM

Pode gerir todos os aspetos do produto Dynamics 365.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerencie todos os aspetos da Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="customer-lockbox-access-approver-permissions"></a>Permissões do aprovador de acesso lockBox do cliente

Pode aprovar pedidos de suporte da Microsoft para aceder aos dados organizacionais dos clientes.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Gerir todos os aspetos do Office 365 Customer Lockbox |

### <a name="desktop-analytics-administrator-permissions"></a>Permissões do Administrador de Análise de Desktop

Pode gerir os serviços de Personalização de Desktop analytics e & Política de Escritórios. Para desktop Analytics, isto inclui a capacidade de visualizar o inventário de ativos, criar planos de implementação, visualizar a implementação e o estado de saúde. Para o serviço de personalização de & Política de Escritórios, esta função permite aos utilizadores gerir as políticas do Office.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerencie todos os aspetos do Desktop Analytics. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="device-administrators-permissions"></a>Permissões de Administradores de Dispositivos

Os utilizadores atribuídos a esta função são adicionados ao grupo de administradores locais em dispositivos ligados à AD Azure.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/groupSettings/basic/read | Leia as propriedades básicas em groupSettings no Diretório Ativo Azure. |
| microsoft.directy/groupSettingTemplates/basic/read | Leia propriedades básicas em modelos de definição de grupo em Diretório Ativo Azure. |

### <a name="directory-readers-permissions"></a>Permissões dos Leitores de Diretório
Pode ler informações básicas sobre o diretório. Para conceder acesso a aplicações, não destinado supor utilizadores.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/administrativeUnits/basic/read | Leia propriedades básicas em Unidades administrativas em Diretório Ativo Azure. |
| microsoft.directy/administrativeUnits/members/read | Leia a propriedade administrativaUnits.members em Azure Ative Directory. |
| microsoft.diretório/aplicações/básico/leitura | Leia as propriedades básicas sobre aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/proprietários/ler | Leia aplicações.proprietários em Diretório Ativo Azure. |
| microsoft.diretório/aplicações/políticas/ler | Leia aplicações.policies propriedade em Azure Ative Diretório. |
| microsoft.directy/contactos/basic/read | Leia as propriedades básicas em contactos no Diretório Ativo Azure. |
| microsoft.directy/contacts/memberOf/read | Leia contacts.memberOf propriedade em Azure Ative Diretório. |
| microsoft.diretório/contratos/básico/lido | Leia propriedades básicas sobre contratos em Azure Ative Diretório. |
| microsoft.diretório/dispositivos/básico/leitura | Leia propriedades básicas em dispositivos em Diretório Ativo Azure. |
| microsoft.directy/devices/memberOf/read | Leia dispositivos.memberOf propriedade em Azure Ative Diretório. |
| microsoft.diretório/dispositivos/registeredOwners/read | Leia dispositivos.registeredOwners propriedade em Azure Ative Directory. |
| microsoft.directy/devices/registeredUsers/read | Leia dispositivos.registeredUsers propriedade em Azure Ative Directory. |
| microsoft.directy/directyRoles/basic/read | Leia propriedades básicas em Diretórios em Diretório Ativo Azure. |
| microsoft.directyRoles/elegíveisDeputados/ler | Leia a propriedade dos membros elegíveis no Azure Ative Directory. |
| microsoft.directy/directyRoles/members/read | Leia a propriedade do directórioRoles.membros no Diretório Ativo Azure. |
| microsoft.diretório/domínios/básico/leitura | Leia propriedades básicas em domínios em Diretório Ativo Azure. |
| microsoft.directy/groups/appRoleAssignments/read | Leia a propriedade de groups.appRoleAssignments no Diretório Ativo Azure. |
| microsoft.diretório/grupos/básico/lido | Leia propriedades básicas em grupos em Azure Ative Diretório. |
| microsoft.diretório/grupos/memberOf/read | Leia grupos.memberOf propriedade em Azure Ative Diretório. |
| microsoft.diretório/grupos/membros/ler | Leia a propriedade de grupos.membros no Diretório Ativo Azure. |
| microsoft.diretório/grupos/proprietários/leitura | Leia os grupos.proprietários propriedade em Azure Ative Diretório. |
| microsoft.directy/groups/settings/read | Leia os grupos.configurações de propriedade em Azure Ative Diretório. |
| microsoft.directy/groupSettings/basic/read | Leia as propriedades básicas em groupSettings no Diretório Ativo Azure. |
| microsoft.directy/groupSettingTemplates/basic/read | Leia propriedades básicas em modelos de definição de grupo em Diretório Ativo Azure. |
| microsoft.directy/oAuth2PermissionGrants/basic/read | Leia propriedades básicas em oAuth2PermissionGrants no Diretório Ativo Azure. |
| microsoft.diretório/organização/básico/leitura | Leia propriedades básicas sobre organização em Azure Ative Diretório. |
| microsoft.directy/organization/trustedCAsForPasswordlessAuth/read | Leia a propriedade casForPasswordlessAuth em Azure Ative Directory. |
| microsoft.directy/roleAssignments/basic/read | Leia propriedades básicas sobre roleAssignments no Diretório Ativo Azure. |
| microsoft.directy/roleDefinições/básico/leitura | Leia propriedades básicas sobre roleDefinitions no Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/appRoleAssignedTo/read | Leia serviçoPrincipals.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/appRoleAssignments/read | Leia a propriedade servicePrincipas.appRoleAssignments no Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/basic/read | Leia propriedades básicas em serviçoPrincipais em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/memberOf/read | Leia serviçoPrincipais.memberOf propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/oAuth2PermissionGrants/basic/read | Leia serviçoPrincipals.oAuth2PermissionGrants propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/ownedObjects/read | Leia serviçoPrincipals.ownedObjects propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/proprietários/read | Leia serviçoSPrincipais.proprietários propriedade em Azure Ative Diretório. |
| microsoft.directy/servicePrincipas/policies/read | Leia serviçoS.policies propriedade em Azure Ative Directory. |
| microsoft.directy/subscritoSkus/basic/read | Leia as propriedades básicas no Skus subscrito no Diretório Ativo Azure. |
| microsoft.directy/users/appRoleAssignments/read | Leia a propriedade users.appRoleAssignments no Diretório Ativo Azure. |
| microsoft.directy/users/basic/read | Leia as propriedades básicas dos utilizadores no Diretório Ativo Azure. |
| microsoft.directy/users/directReports/read | Leia os utilizadores.directReports propriedade em Azure Ative Directory. |
| microsoft.directy/users/manager/read | Leia a propriedade de utilizadores.manager no Diretório Ativo Azure. |
| microsoft.directy/users/memberOf/read | Leia utilizadores.memberOf propriedade em Azure Ative Diretório. |
| microsoft.directy/users/oAuth2PermissionGrants/basic/read | Leia a propriedade users.oAuth2PermissionGrants no Azure Ative Directory. |
| microsoft.directy/users/ownedDevices/read | Leia a propriedade dos utilizadores.ownedDevices em Azure Ative Directory. |
| microsoft.directy/users/ownedObjects/read | Leia a propriedade de utilizadores.ownedObjects em Azure Ative Directory. |
| microsoft.directy/users/registeredDevices/read | Leia a propriedade users.registeredDevices no Diretório Ativo Azure. |

### <a name="directory-synchronization-accounts-permissions"></a>Autorizações de Sincronização de Diretórios

Utilizado apenas pelo serviço Azure AD Connect.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/organization/dirSync/update | Atualização organization.dirSync property in Azure Ative Directory. |
| microsoft.diretório/políticas/criar | Criar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/apagar | Eliminar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/básico/lido | Leia propriedades básicas sobre políticas no Diretório Ativo Azure. |
| microsoft.directy/policies/basic/update | Atualizar propriedades básicas sobre políticas no Diretório Ativo Azure. |
| microsoft.diretório/policies/owners/read | Leia as políticas.proprietários de propriedades em Azure Ative Diretório. |
| microsoft.diretório/policies/owners/update | Atualizar políticas.proprietários propriedade em Azure Ative Diretório. |
| microsoft.diretório/policies/policiesAppliedTo/read | Leia as políticas.policiesAppliedTo propriedade em Azure Ative Diretório. |
| microsoft.directy/policies/tenantDefault/update | Atualizar políticas.tenantDefault propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/appRoleAssignedTo/read | Leia serviçoPrincipals.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/appRoleAssignedTo/update | Atualizar serviçoSPrincipais.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/appRoleAssignments/read | Leia a propriedade servicePrincipas.appRoleAssignments no Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/appRoleAssignments/update | Atualizar serviçoSPrincipais.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/audience/update | Atualizar serviçoSPrincipais.propriedade do público em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/autenticação/atualização | Atualizar serviçoSPrincipais.propriedade de autenticação em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/basic/read | Leia propriedades básicas em serviçoPrincipais em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/basic/update | Atualizar propriedades básicas em serviçoPrincipais em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/criar | Crie serviços Diretores em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/credenciais/atualização | Atualizar serviçoSPrincipais.credenciais propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/memberOf/read | Leia serviçoPrincipais.memberOf propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/oAuth2PermissionGrants/basic/read | Leia serviçoPrincipals.oAuth2PermissionGrants propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/proprietários/read | Leia serviçoSPrincipais.proprietários propriedade em Azure Ative Diretório. |
| microsoft.directy/servicePrincipas/proprietários/atualização | Atualizar serviçoSPrincipais.proprietários propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/ownedObjects/read | Leia serviçoPrincipals.ownedObjects propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/permissões/atualização | Atualizar serviçoSPrincipais.permissions propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/policies/read | Leia serviçoS.policies propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/policies/update | Atualizar serviçoSPrincipais.policies propriedade em Azure Ative Directory. |
| microsoft.directorySync/allEntities/allTasks | Execute todas as ações no Azure AD Connect. |

### <a name="directory-writers-permissions"></a>Permissões de Escritores de Diretório

Pode ler & escrever informações básicas sobre o diretório. Para conceder acesso a aplicações, não destinado supor utilizadores.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/grupos/criar | Crie grupos no Diretório Ativo Azure. |
| microsoft.directy/groups/createAsOwner | Crie grupos no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta com a quota de 250 objetos criados pelo criador. |
| microsoft.directy/groups/appRoleAssignments/update | Update groups.appRoleAssignments property in Azure Ative Directory. |
| microsoft.directy/groups/basic/update | Atualizar propriedades básicas em grupos em Azure Ative Directory. |
| microsoft.diretório/grupos/membros/atualização | Atualizar grupos.membros propriedade em Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/atualização | Atualizar grupos.proprietários propriedade em Azure Ative Diretório. |
| microsoft.directy/groups/settings/update | Atualizar grupos.configura a propriedade no Diretório Ativo Azure. |
| microsoft.directy/groupSettings/basic/update | Atualizar propriedades básicas em groupSettings no Diretório Ativo Azure. |
| microsoft.directy/groupSettings/create | Crie definições de grupo em Diretório Ativo Azure. |
| microsoft.directy/groupSettings/delete | Eliminar definições de grupo no Diretório Ativo Azure. |
| microsoft.directy/users/appRoleAssignments/update | Atualizar utilizadores.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.directy/users/assignLicense | Gerir licenças sobre utilizadores em Diretório Ativo Azure. |
| microsoft.directy/users/basic/update | Atualizar propriedades básicas sobre utilizadores no Diretório Ativo Do Azure. |
| microsoft.directy/users/invalidateAllRefreshTokens | Invalide todas as fichas de atualização do utilizador no Diretório Ativo Azure. |
| microsoft.directy/users/manager/update | Atualize a propriedade de utilizadores.manager no Diretório Ativo Azure. |
| microsoft.directy/users/userPrincipalName/update | Atualizar utilizadores.userPrincipalName propriedade em Azure Ative Directory. |

### <a name="exchange-service-administrator-permissions"></a>Permissões do Administrador de Serviço de Intercâmbio

Pode gerir todos os aspetos do produto Exchange.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.directy/groups/uniified/appRoleAssignments/update | Atualizar grupos.propriedade unificada em Azure Ative Directory. |
| microsoft.diretório/grupos/unificado/básico/atualização | Atualizar propriedades básicas do Office 365 Groups. |
| microsoft.diretório/grupos/unificado/criar | Crie o Office 365 Groups. |
| microsoft.diretório/grupos/unificado/excluir | Eliminar o Office 365 Groups. |
| microsoft.diretório/grupos/unificado/membros/atualização | Atualização da adesão ao Office 365 Groups. |
| microsoft.diretório/grupos/unificado/proprietários/atualização | Atualizar a propriedade do Office 365 Groups. |
| microsoft.office365.exchange/allEntities/allTasks | Gerencie todos os aspetos do Exchange Online. |
| microsoft.office365.network/performance/allProperties/read | Leia as páginas de desempenho da rede no Centro de Administração M365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de utilização do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="external-identity-provider-administrator-permissions"></a>Permissões do Administrador do Fornecedor de Identidade Externa

Configure os fornecedores de identidade para utilização na federação direta.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Leia e configure fornecedores de identidade no Diretório Ativo Azure B2C. |

### <a name="global-reader-permissions"></a>Permissões do Leitor Global
Pode ler tudo o que um Administrador Global pode, mas não editar nada.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a [descrição](#global-reader) do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.commerce.billing/allEntities/read    | Leia todos os aspetos da faturação do Office 365. |
| microsoft.directy/administrativeUnits/basic/read    | Leia propriedades básicas em Unidades administrativas em Diretório Ativo Azure. |
| microsoft.directy/administrativeUnits/members/read    | Leia a propriedade administrativaUnits.members em Azure Ative Directory. |
| microsoft.diretório/aplicações/básico/leitura    | Leia as propriedades básicas sobre aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/proprietários/ler    | Leia aplicações.proprietários em Diretório Ativo Azure. |
| microsoft.diretório/aplicações/políticas/ler    | Leia aplicações.policies propriedade em Azure Ative Diretório. |
| microsoft.directy/contactos/basic/read    | Leia as propriedades básicas em contactos no Diretório Ativo Azure. |
| microsoft.directy/contacts/memberOf/read    | Leia contacts.memberOf propriedade em Azure Ative Diretório. |
| microsoft.diretório/contratos/básico/lido    | Leia propriedades básicas sobre contratos em Azure Ative Diretório. |
| microsoft.diretório/dispositivos/básico/leitura    | Leia propriedades básicas em dispositivos em Diretório Ativo Azure. |
| microsoft.directy/devices/memberOf/read    | Leia dispositivos.memberOf propriedade em Azure Ative Diretório. |
| microsoft.diretório/dispositivos/registeredOwners/read    | Leia dispositivos.registeredOwners propriedade em Azure Ative Directory. |
| microsoft.directy/devices/registeredUsers/read    | Leia dispositivos.registeredUsers propriedade em Azure Ative Directory. |
| microsoft.directy/directyRoles/basic/read    | Leia propriedades básicas em Diretórios em Diretório Ativo Azure. |
| microsoft.directyRoles/elegíveisDeputados/ler    | Leia a propriedade dos membros elegíveis no Azure Ative Directory. |
| microsoft.directy/directyRoles/members/read    | Leia a propriedade do directórioRoles.membros no Diretório Ativo Azure. |
| microsoft.diretório/domínios/básico/leitura    | Leia propriedades básicas em domínios em Diretório Ativo Azure. |
| microsoft.directy/groups/appRoleAssignments/read    | Leia a propriedade de groups.appRoleAssignments no Diretório Ativo Azure. |
| microsoft.diretório/grupos/básico/lido    | Leia propriedades básicas em grupos em Azure Ative Diretório. |
| microsoft.diretório/grupos/hiddenMembers/read    | Leia a propriedade dos grupos.hiddenMembers em Azure Ative Directory. |
| microsoft.diretório/grupos/memberOf/read    | Leia grupos.memberOf propriedade em Azure Ative Diretório. |
| microsoft.diretório/grupos/membros/ler    | Leia a propriedade de grupos.membros no Diretório Ativo Azure. |
| microsoft.diretório/grupos/proprietários/leitura    | Leia os grupos.proprietários propriedade em Azure Ative Diretório. |
| microsoft.directy/groups/settings/read    | Leia os grupos.configurações de propriedade em Azure Ative Diretório. |
| microsoft.directy/groupSettings/basic/read    | Leia as propriedades básicas em groupSettings no Diretório Ativo Azure. |
| microsoft.directy/groupSettingTemplates/basic/read    | Leia propriedades básicas em modelos de definição de grupo em Diretório Ativo Azure. |
| microsoft.directy/oAuth2PermissionGrants/basic/read    | Leia propriedades básicas em oAuth2PermissionGrants no Diretório Ativo Azure. |
| microsoft.diretório/organização/básico/leitura    | Leia propriedades básicas sobre organização em Azure Ative Diretório. |
| microsoft.directy/organization/trustedCAsForPasswordlessAuth/read    | Leia a propriedade casForPasswordlessAuth em Azure Ative Directory. |
| microsoft.diretório/policies/standard/read    | Leia as políticas padrão no Diretório Ativo Azure. |
| microsoft.directy/roleAssignments/basic/read    | Leia propriedades básicas sobre roleAssignments no Diretório Ativo Azure. |
| microsoft.directy/roleDefinições/básico/leitura    | Leia propriedades básicas sobre roleDefinitions no Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/appRoleAssignedTo/read    | Leia serviçoPrincipals.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/appRoleAssignments/read    | Leia a propriedade servicePrincipas.appRoleAssignments no Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/basic/read    | Leia propriedades básicas em serviçoPrincipais em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/memberOf/read    | Leia serviçoPrincipais.memberOf propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/oAuth2PermissionGrants/basic/read    | Leia serviçoPrincipals.oAuth2PermissionGrants propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/ownedObjects/read    | Leia serviçoPrincipals.ownedObjects propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/proprietários/read    | Leia serviçoSPrincipais.proprietários propriedade em Azure Ative Diretório. |
| microsoft.directy/servicePrincipas/policies/read    | Leia serviçoS.policies propriedade em Azure Ative Directory. |
| microsoft.directy/signInReports/allProperties/read    | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.directy/subscritoSkus/basic/read    | Leia as propriedades básicas no Skus subscrito no Diretório Ativo Azure. |
| microsoft.directy/users/appRoleAssignments/read    | Leia a propriedade users.appRoleAssignments no Diretório Ativo Azure. |
| microsoft.directy/users/basic/read    | Leia as propriedades básicas dos utilizadores no Diretório Ativo Azure. |
| microsoft.directy/users/directReports/read    | Leia os utilizadores.directReports propriedade em Azure Ative Directory. |
| microsoft.directy/users/manager/read    | Leia a propriedade de utilizadores.manager no Diretório Ativo Azure. |
| microsoft.directy/users/memberOf/read    | Leia utilizadores.memberOf propriedade em Azure Ative Diretório. |
| microsoft.directy/users/oAuth2PermissionGrants/basic/read    | Leia a propriedade users.oAuth2PermissionGrants no Azure Ative Directory. |
| microsoft.directy/users/ownedDevices/read    | Leia a propriedade dos utilizadores.ownedDevices em Azure Ative Directory. |
| microsoft.directy/users/ownedObjects/read    | Leia a propriedade de utilizadores.ownedObjects em Azure Ative Directory. |
| microsoft.directy/users/registeredDevices/read    | Leia a propriedade users.registeredDevices no Diretório Ativo Azure. |
| microsoft.directy/users/strongAuthentication/read    | Leia propriedades de autenticação forte como informações de credenciais MFA. |
| microsoft.office365.exchange/allEntities/read    | Leia todos os aspetos do Exchange Online. |
| microsoft.office365.messageCenter/messages/read    | Leia mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read    | Leia segurançaMensagens em microsoft.office365.messageCenter. |
| microsoft.office365.network/performance/allProperties/read | Leia as páginas de desempenho da rede no Centro de Administração M365. |
| microsoft.office365.protectionCenter/allEntities/read    | Leia todos os aspetos do Centro de Proteção do Escritório 365. |
| microsoft.office365.securityComplianceCenter/allEntities/read    | Leia todas as propriedades padrão em microsoft.office365.securityComplianceCenter. |
| microsoft.office365.usageReports/allEntities/read    | Leia os relatórios de utilização do Office 365. |
| microsoft.office365.webPortal/allEntities/standard/read    | Leia as propriedades padrão em todos os recursos em microsoft.office365.webPortal. |

### <a name="groups-administrator-permissions"></a>Permissões de administrador de grupos
Pode gerir todos os aspetos de grupos e configurações de grupo, como políticas de nomeação e expiração.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.diretório/grupos/básico/lido | Leia as propriedades padrão em Grupos no Diretório Ativo Azure.  |
| microsoft.directy/groups/basic/update | Atualizar propriedades básicas em grupos em Azure Ative Directory. |
| microsoft.diretório/grupos/criar | Crie grupos no Diretório Ativo Azure. |
| microsoft.directy/groups/createAsOwner | Crie grupos no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta com a quota de 250 objetos criados pelo criador. |
| microsoft.diretório/grupos/eliminar | Eliminar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/hiddenMembers/read | Leia a propriedade dos grupos.hiddenMembers em Azure Ative Directory. |
| microsoft.diretório/grupos/membros/atualização | Atualizar grupos.membros propriedade em Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/atualização | Atualizar grupos.proprietários propriedade em Azure Ative Diretório. |
| microsoft.diretório/grupos/restauro | Restaurar grupos no Diretório Ativo Azure. |
| microsoft.directy/groups/settings/update | Atualizar grupos.configura a propriedade no Diretório Ativo Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.messageCenter/messages/read | Leia mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="guest-inviter-permissions"></a>Permissões convidadas
Pode convidar utilizadores convidados independentemente da definição de "membros podem convidar os hóspedes".

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/users/appRoleAssignments/read | Leia a propriedade users.appRoleAssignments no Diretório Ativo Azure. |
| microsoft.directy/users/basic/read | Leia as propriedades básicas dos utilizadores no Diretório Ativo Azure. |
| microsoft.directy/users/directReports/read | Leia os utilizadores.directReports propriedade em Azure Ative Directory. |
| microsoft.directy/users/inviteGuest | Convide os utilizadores convidados no Azure Ative Directory. |
| microsoft.directy/users/manager/read | Leia a propriedade de utilizadores.manager no Diretório Ativo Azure. |
| microsoft.directy/users/memberOf/read | Leia utilizadores.memberOf propriedade em Azure Ative Diretório. |
| microsoft.directy/users/oAuth2PermissionGrants/basic/read | Leia a propriedade users.oAuth2PermissionGrants no Azure Ative Directory. |
| microsoft.directy/users/ownedDevices/read | Leia a propriedade dos utilizadores.ownedDevices em Azure Ative Directory. |
| microsoft.directy/users/ownedObjects/read | Leia a propriedade de utilizadores.ownedObjects em Azure Ative Directory. |
| microsoft.directy/users/registeredDevices/read | Leia a propriedade users.registeredDevices no Diretório Ativo Azure. |

### <a name="helpdesk-administrator-permissions"></a>Permissões do Administrador de Helpdesk

Pode redefinir palavras-passe para não administradores e administradores de helpdesk.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/devices/bitLockerRecoveryKeys/read | Leia a propriedade devices.bitLockerRecoveryKeys no Diretório Ativo Azure. |
| microsoft.directy/users/invalidateAllRefreshTokens | Invalide todas as fichas de atualização do utilizador no Diretório Ativo Azure. |
| microsoft.directy/users/password/update | Atualizar palavras-passe para todos os utilizadores no Diretório Ativo do Azure. Consulte a documentação online para mais detalhes. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="hybrid-identity-administrator-permissions"></a>Permissões de Administrador de Identidade Híbrida

Ativar, implementar, configurar, gerir, monitorizar e resolver serviços de fornecimento e autenticação em nuvem. 

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.directy/applications/audience/update  | Atualizar aplicações.propriedade do público em Azure Ative Directory. |
| microsoft.directy/applications/autenticação/atualização | Atualizar aplicações.propriedade de autenticação no Diretório Ativo Azure.  |
| microsoft.diretório/aplicações/básico/atualização | Atualizar propriedades básicas sobre aplicações no Diretório Ativo Azure. |
| microsoft.directy/applications/create | Criar aplicações no Diretório Ativo Azure. |
| microsoft.directy/applications/credentials/update | Atualizar aplicações.credenciais propriedade em Azure Ative Directory. |
| microsoft.directy/applications/delete | Eliminar aplicações no Diretório Ativo Azure. |
| microsoft.diretório/aplicações/proprietários/atualização | Atualizar aplicações.proprietários propriedade em Azure Ative Diretório. |
| microsoft.directy/applications/permissions/update | Atualizar aplicações.permissões propriedade em Azure Ative Directory. |
| microsoft.directy/applications/policies/update | Atualizar aplicações.policies property in Azure Ative Directory. |
| microsoft.diretório/aplicaçãoTemplates/instantiate | Aplicações de galeria instantânea a partir de modelos de aplicação. |
| microsoft.directy/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditlogs no Diretório Ativo Azure. |
| microsoft.directy/cloudProvisioning/allProperties/allTasks | Leia e configure todas as propriedades do serviço de fornecimento de nuvem Azure AD. |
| microsoft.directy/federatedAuthentication/allProperties/allTasks | Gerencie todos os aspetos dos Serviços Federados de Diretório Ativo (ADFS) ou 3º provedor da federação de terceiros em Azure AD. |
| microsoft.directy/organization/dirSync/update | Atualização organization.dirSync property in Azure Ative Directory. |
| microsoft.directy/passwordHashSync/allProperties/allTasks | Gerencie todos os aspetos do Password Hash Sync (PHS) em Azure AD. |
| microsoft.directy/passThroughAuthentication/allProperties/allTasks | Gerencie todos os aspetos da Autenticação Pass-through (PTA) em Azure AD. |
| microsoft.directy/seamlessSSO/allProperties/allTasks | Gerencie todos os aspetos do único sign-on sem emenda (SSO) em Azure AD. |
| microsoft.directy/servicePrincipas/audience/update | Atualizar serviçoSPrincipais.propriedade do público em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/autenticação/atualização | Atualizar serviçoSPrincipais.propriedade de autenticação em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/basic/update | Atualizar propriedades básicas em serviçoPrincipais em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/criar | Crie serviços Diretores em Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/credenciais/atualização | Atualizar serviçoSPrincipais.credenciais propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/delete | Elimine os principais de serviço no Diretório Ativo Azure. |
| microsoft.directy/servicePrincipas/proprietários/atualização | Atualizar serviçoSPrincipais.proprietários propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/permissões/atualização | Atualizar serviçoSPrincipais.permissions propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/policies/update | Atualizar serviçoSPrincipais.policies propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/sincronizaçãoJobs/manage | Gerir todos os aspetos dos trabalhos de sincronização em Azure AD. |
| microsoft.directy/servicePrincipas/sincronizaçãoSchema/manage | Gerencie todos os aspetos do esquema de sincronização em Azure AD. |
| microsoft.directy/servicePrincipais/sincronizaçãoCredenciais/gerir | Gerencie todos os aspetos das credenciais de sincronização em Azure AD. |
| microsoft.directy/servicePrincipas/tag/update | Atualizar serviçoSPrincipais.tag propriedade em Azure Ative Directory. |
| microsoft.directy/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.office365.messageCenter/messages/read | Leia mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |


### <a name="intune-service-administrator-permissions"></a>Intune Service Administrator permissões

Pode gerir todos os aspetos do produto Intune.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/contacts/basic/update | Atualizar propriedades básicas sobre contactos em Diretório Ativo Azure. |
| microsoft.directy/contacts/create | Crie contactos no Diretório Ativo Azure. |
| microsoft.directy/contacts/delete | Eliminar contactos no Diretório Ativo Azure. |
| microsoft.directy/devices/basic/update | Atualizar propriedades básicas em dispositivos em Azure Ative Directory. |
| microsoft.directy/devices/bitLockerRecoveryKeys/read | Leia a propriedade devices.bitLockerRecoveryKeys no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/criar | Crie dispositivos no Diretório Ativo Azure. |
| microsoft.diretório/dispositivos/eliminar | Eliminar dispositivos no Diretório Ativo Azure. |
| microsoft.directy/devices/registeredOwners/update | Atualizar dispositivos.registeredOwners propriedade em Azure Ative Directory. |
| microsoft.directy/devices/registeredUsers/update | Atualizar dispositivos.registeredUsers propriedade em Azure Ative Directory. |
| microsoft.directy/groups/appRoleAssignments/update | Update groups.appRoleAssignments property in Azure Ative Directory. |
| microsoft.directy/groups/basic/update | Atualizar propriedades básicas em grupos em Azure Ative Directory. |
| microsoft.diretório/grupos/criar | Crie grupos no Diretório Ativo Azure. |
| microsoft.directy/groups/createAsOwner | Crie grupos no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta com a quota de 250 objetos criados pelo criador. |
| microsoft.diretório/grupos/eliminar | Eliminar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/hiddenMembers/read | Leia a propriedade dos grupos.hiddenMembers em Azure Ative Directory. |
| microsoft.diretório/grupos/membros/atualização | Atualizar grupos.membros propriedade em Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/atualização | Atualizar grupos.proprietários propriedade em Azure Ative Diretório. |
| microsoft.diretório/grupos/restauro | Restaurar grupos no Diretório Ativo Azure. |
| microsoft.directy/groups/settings/update | Atualizar grupos.configura a propriedade no Diretório Ativo Azure. |
| microsoft.directy/users/appRoleAssignments/update | Atualizar utilizadores.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.directy/users/basic/update | Atualizar propriedades básicas sobre utilizadores no Diretório Ativo Do Azure. |
| microsoft.directy/users/manager/update | Atualize a propriedade de utilizadores.manager no Diretório Ativo Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspetos de Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="kaizala-administrator-permissions"></a>Permissão do Administrador Kaizala

Pode gerir as definições para o Microsoft Kaizala.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia o Escritório 365 centro de administração. |

### <a name="license-administrator-permissions"></a>Permissões do Administrador de Licença

Pode gerir licenças de produtos em utilizadores e grupos.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/users/assignLicense | Gerir licenças sobre utilizadores em Diretório Ativo Azure. |
| microsoft.directy/users/usageLocation/update | Atualizar utilizadores.usageLocation propriedade em Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |

### <a name="lync-service-administrator-permissions"></a>Permissões do Administrador de Serviço Lync

Pode gerir todos os aspetos do produto Skype for Business.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspetos do Skype para o Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |
| microsoft.office365.usageReports/allEntities/read    | Leia os relatórios de utilização do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |


### <a name="message-center-privacy-reader-permissions"></a>Permissões do Leitor de Privacidade do Centro de Mensagens

Pode ler posts do Message Center, mensagens de privacidade de dados, grupos, domínios e subscrições.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Leia mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leia segurançaMensagens em microsoft.office365.messageCenter. |

### <a name="message-center-reader-permissions"></a>Permissões do leitor do Centro de Mensagens
Pode ler mensagens e atualizações para a sua organização apenas no Office 365 Message Center. 

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Leia mensagens em microsoft.office365.messageCenter. |

### <a name="network-administrator-permissions"></a>Permissões do Administrador de Rede
Pode gerir as localizações da rede e rever as ideias de design de rede da empresa para o Microsoft 365 Software como aplicações de Serviço.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.network/performance/allProperties/read | Leia as páginas de desempenho da rede no Centro de Administração M365.  |
| microsoft.office365.network/locations/allProperties/allTasks | Leia e configure propriedades de localização de rede para cada local. |

### <a name="office-apps-administrator-permissions"></a>Permissões de administrador de apps de escritório
Pode gerir os serviços de cloud das aplicações do Office, incluindo a gestão de políticas e configurações, e gerir a capacidade de selecionar, desseleccionar e publicar conteúdo de funcionalidade "novidade" para dispositivos de utilizador final.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.messageCenter/messages/read | Leia mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de utilização do Office 365. |
| microsoft.office365.userCommunication/allEntities/allTasks | Leia e atualize a visibilidade das novas mensagens. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="partner-tier1-support-permissions"></a>Permissões de suporte de nível 1 de parceiro

Não utilize - não destinado a uso geral.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/contacts/basic/update | Atualizar propriedades básicas sobre contactos em Diretório Ativo Azure. |
| microsoft.directy/contacts/create | Crie contactos no Diretório Ativo Azure. |
| microsoft.directy/contacts/delete | Eliminar contactos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/criar | Crie grupos no Diretório Ativo Azure. |
| microsoft.directy/groups/createAsOwner | Crie grupos no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta com a quota de 250 objetos criados pelo criador. |
| microsoft.diretório/grupos/membros/atualização | Atualizar grupos.membros propriedade em Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/atualização | Atualizar grupos.proprietários propriedade em Azure Ative Diretório. |
| microsoft.directy/users/appRoleAssignments/update | Atualizar utilizadores.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.directy/users/assignLicense | Gerir licenças sobre utilizadores em Diretório Ativo Azure. |
| microsoft.directy/users/basic/update | Atualizar propriedades básicas sobre utilizadores no Diretório Ativo Do Azure. |
| microsoft.directy/users/delete | Elimine os utilizadores no Diretório Ativo Azure. |
| microsoft.directy/users/invalidateAllRefreshTokens | Invalide todas as fichas de atualização do utilizador no Diretório Ativo Azure. |
| microsoft.directy/users/manager/update | Atualize a propriedade de utilizadores.manager no Diretório Ativo Azure. |
| microsoft.directy/users/password/update | Atualizar palavras-passe para todos os utilizadores no Diretório Ativo do Azure. Consulte a documentação online para mais detalhes. |
| microsoft.directy/users/restore | Restaurar os utilizadores eliminados no Diretório Ativo Azure. |
| microsoft.directy/users/userPrincipalName/update | Atualizar utilizadores.userPrincipalName propriedade em Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="partner-tier2-support-permissions"></a>Permissões de suporte de nível parceiro 2

Não utilize - não destinado a uso geral.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/contacts/basic/update | Atualizar propriedades básicas sobre contactos em Diretório Ativo Azure. |
| microsoft.directy/contacts/create | Crie contactos no Diretório Ativo Azure. |
| microsoft.directy/contacts/delete | Eliminar contactos no Diretório Ativo Azure. |
| microsoft.directy/domínios/allTasks | Crie e elimine domínios e leia e atualize as propriedades padrão no Diretório Ativo Azure. |
| microsoft.diretório/grupos/criar | Crie grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/eliminar | Eliminar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/membros/atualização | Atualizar grupos.membros propriedade em Azure Ative Directory. |
| microsoft.diretório/grupos/restauro | Restaurar grupos no Diretório Ativo Azure. |
| microsoft.directy/organization/basic/update | Atualizar propriedades básicas sobre organização em Azure Ative Directory. |
| microsoft.directy/users/appRoleAssignments/update | Atualizar utilizadores.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.directy/users/assignLicense | Gerir licenças sobre utilizadores em Diretório Ativo Azure. |
| microsoft.directy/users/basic/update | Atualizar propriedades básicas sobre utilizadores no Diretório Ativo Do Azure. |
| microsoft.directy/users/delete | Elimine os utilizadores no Diretório Ativo Azure. |
| microsoft.directy/users/invalidateAllRefreshTokens | Invalide todas as fichas de atualização do utilizador no Diretório Ativo Azure. |
| microsoft.directy/users/manager/update | Atualize a propriedade de utilizadores.manager no Diretório Ativo Azure. |
| microsoft.directy/users/password/update | Atualizar palavras-passe para todos os utilizadores no Diretório Ativo do Azure. Consulte a documentação online para mais detalhes. |
| microsoft.directy/users/restore | Restaurar os utilizadores eliminados no Diretório Ativo Azure. |
| microsoft.directy/users/userPrincipalName/update | Atualizar utilizadores.userPrincipalName propriedade em Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="password-administrator-permissions"></a>Permissões do Administrador de Password

Pode redefinir palavras-passe para não administradores e administradores de palavras-passe.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/users/password/update | Atualizar palavras-passe para todos os utilizadores no Diretório Ativo do Azure. Consulte a documentação online para mais detalhes. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="power-bi-service-administrator-permissions"></a>Permissões do Administrador de Serviço Power BI

Pode gerir todos os aspetos do produto Power BI.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerencie todos os aspetos do Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |


### <a name="power-platform-administrator-permissions"></a>Permissões do Administrador da Plataforma de Energia

Pode criar e gerir todos os aspetos do Microsoft Dynamics 365, PowerApps e Microsoft Flow. 

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.dynamics365/allEntities/allTasks | Gerencie todos os aspetos da Dynamics 365. |
| microsoft.flow/allEntities/allTasks | Gerencie todos os aspetos do Microsoft Flow. |
| microsoft.powerApps/allEntities/allTasks | Gerir todos os aspetos do PowerApps. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="printer-administrator-permissions"></a>Permissões do Administrador da Impressora

Pode gerir todos os aspetos das impressoras e conectores da impressora.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.print/allEntities/allProperties/allTasks | Crie e elimine impressoras e conectores e leia e atualize todas as propriedades na Microsoft Print. |

### <a name="printer-technician-permissions"></a>Permissões do Técnico de Impressora

Pode registar e desregistar impressoras e atualizar o estado da impressora.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.print/conectores/allProperties/read | Leia todas as propriedades dos conectores na Microsoft Print. |
| microsoft.azure.print/printers/allProperties/read | Leia todas as propriedades das impressoras na Microsoft Print. |
| microsoft.azure.print/impressoras/basic/update | Atualizar propriedades básicas das impressoras na Microsoft Print. |
| microsoft.azure.print/impressoras/register | Registe as impressoras na Microsoft Print. |
| microsoft.azure.print/printers/unregister | Desregistre as impressoras na Microsoft Print. |

### <a name="privileged-authentication-administrator-permissions"></a>Autorizações de Administrador de Autenticação Privilegiada

Permitido visualizar, definir e repor informações do método de autenticação para qualquer utilizador (administrador ou não administrador).

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/users/invalidateAllRefreshTokens | Invalide todas as fichas de atualização do utilizador no Diretório Ativo Azure. |
| microsoft.directy/users/strongAuthentication/update | Atualizar propriedades de autenticação forte como informações de credenciais MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |
| microsoft.directy/users/password/update | Atualizar palavras-passe para todos os utilizadores da organização Office 365. Consulte a documentação online para mais detalhes. |

### <a name="privileged-role-administrator-permissions"></a>Permissões de Administrador de Funções Privilegiadas

Pode gerir atribuições de papéis em Azure AD, e todos os aspetos da Gestão de Identidade Privilegiada.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize as propriedades padrão em microsoft.aad.privilegedIdentityManagement. |
| microsoft.directy/servicePrincipas/appRoleAssignedTo/allTasks | Leia e configure serviçoSPrincipais.appRoleAssignedTo propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/oAuth2PermissionGrants/allTasks | Leia e configure serviçoSPrincipais.oAuth2PermissionGrants propriedade em Azure Ative Directory. |
| microsoft.directy/administrativeUnits/allProperties/allTasks | Criar e gerir unidades administrativas (incluindo membros) |
| microsoft.directy/roleAssignments/allProperties/allTasks | Criar e gerir atribuições de papéis. |
| microsoft.directy/roleDefinitions/allProperties/allTasks | Criar e gerir definições de papéis. |

### <a name="reports-reader-permissions"></a>Relatórios Permissões do Leitor

Pode ler relatórios de inscrição e auditoria.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditlogs no Diretório Ativo Azure. |
| microsoft.directy/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de utilização do Office 365. |

### <a name="search-administrator-permissions"></a>Permissões do Administrador de Pesquisa

Pode criar e gerir todos os aspetos das definições de Pesquisa do Microsoft.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Leia mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Crie e elimine todos os recursos e leia e atualize todas as propriedades em microsoft.office365.search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="search-editor-permissions"></a>Permissões do Editor de Pesquisa

Pode criar e gerir os conteúdos editoriais, tais como marcadores, Q e As, localizações, plana de piso.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Leia mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Crie e elimine conteúdos e leia e atualize todas as propriedades em microsoft.office365.search. |

### <a name="security-administrator-permissions"></a>Permissões do Administrador de Segurança

Pode ler informações e relatórios de segurança e gerir a configuração em Azure AD e Office 365.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/applications/policies/update | Atualizar aplicações.policies property in Azure Ative Directory. |
| microsoft.directy/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditlogs no Diretório Ativo Azure. |
| microsoft.directy/devices/bitLockerRecoveryKeys/read | Leia a propriedade devices.bitLockerRecoveryKeys no Diretório Ativo Azure. |
| microsoft.directy/policies/basic/update | Atualizar propriedades básicas sobre políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/criar | Criar políticas no Diretório Ativo Azure. |
| microsoft.diretório/políticas/apagar | Eliminar políticas no Diretório Ativo Azure. |
| microsoft.diretório/policies/owners/update | Atualizar políticas.proprietários propriedade em Azure Ative Diretório. |
| microsoft.directy/policies/tenantDefault/update | Atualizar políticas.tenantDefault propriedade em Azure Ative Directory. |
| microsoft.directy/servicePrincipas/policies/update | Atualizar serviçoSPrincipais.policies propriedade em Azure Ative Directory. |
| microsoft.directy/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.aad.identityProtection/allEntities/read | Leia todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Atualize todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Leia todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Leia todos os aspetos do Centro de Proteção do Escritório 365. |
| microsoft.office365.protectionCenter/allEntities/update | Atualize todos os recursos em microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |

### <a name="security-operator-permissions"></a>Permissões do Operador de Segurança

Cria e gere eventos de segurança.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Leia e configure a Segurança da Aplicação Microsoft Cloud. |
| microsoft.aad.identityProtection/allEntities/read | Leia todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Leia todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Leia e configure a Proteção avançada de ameaças da Azure AD. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspetos de Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Leia e configure o Centro de Conformidade & segurança. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Leia e configure a Proteção avançada de ameaças do Windows Defender. |

### <a name="security-reader-permissions"></a>Permissões do Leitor de Segurança

Pode ler informações de segurança e relatórios em Azure AD e Office 365.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/auditLogs/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) em auditlogs no Diretório Ativo Azure. |
| microsoft.directy/devices/bitLockerRecoveryKeys/read | Leia a propriedade devices.bitLockerRecoveryKeys no Diretório Ativo Azure. |
| microsoft.directy/policies/conditionalAccess/basic/read | Leia as políticas.condicionalAccess propriedade em Diretório Ativo Azure. |
| microsoft.directy/signInReports/allProperties/read | Leia todas as propriedades (incluindo propriedades privilegiadas) no signInReports in Azure Ative Directory. |
| microsoft.aad.identityProtection/allEntities/read | Leia todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Leia todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Leia todos os aspetos do Centro de Proteção do Escritório 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |

### <a name="service-support-administrator-permissions"></a>Permissões do Administrador de Suporte de Serviço

Pode ler informações de saúde de serviço e gerir bilhetes de apoio.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

### <a name="sharepoint-service-administrator-permissions"></a>Permissões do Administrador de Serviço SharePoint

Pode gerir todos os aspetos do serviço SharePoint.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.directy/groups/uniified/appRoleAssignments/update | Atualizar grupos.propriedade unificada em Azure Ative Directory. |
| microsoft.diretório/grupos/unificado/básico/atualização | Atualizar propriedades básicas do Office 365 Groups. |
| microsoft.diretório/grupos/unificado/criar | Crie o Office 365 Groups. |
| microsoft.diretório/grupos/unificado/excluir | Eliminar o Office 365 Groups. |
| microsoft.diretório/grupos/unificado/membros/atualização | Atualização da adesão ao Office 365 Groups. |
| microsoft.diretório/grupos/unificado/proprietários/atualização | Atualizar a propriedade do Office 365 Groups. |
| microsoft.office365.network/performance/allProperties/read | Leia as páginas de desempenho da rede no Centro de Administração M365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Crie e elimine todos os recursos e leia e atualize as propriedades padrão em microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |
| microsoft.office365.usageReports/allEntities/read    | Leia os relatórios de utilização do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="teams-communications-administrator-permissions"></a>Permissões do Administrador de Comunicações de Equipas

Pode gerir as funcionalidades de chamadas e reuniões dentro do serviço Microsoft Teams.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de utilização do Office 365. |

### <a name="teams-communications-support-engineer-permissions"></a>Permissões do Engenheiro de Apoio às Comunicações de Equipas

Pode resolver problemas de comunicações dentro das Equipas usando ferramentas avançadas.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |

### <a name="teams-communications-support-specialist-permissions"></a>Permissões especializadas em apoio às comunicações de equipas

Pode resolver problemas de comunicações dentro das Equipas usando ferramentas básicas.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |

### <a name="teams-service-administrator-permissions"></a>Permissões do Administrador de Serviço de Equipas

Pode gerir o serviço Microsoft Teams.

> [!NOTE]
> Este papel tem permissões adicionais fora do Azure Ative Directory. Para mais informações, consulte a descrição do papel acima.
>
>

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.diretório/grupos/hiddenMembers/read | Leia a propriedade dos grupos.hiddenMembers em Azure Ative Directory. |
| microsoft.directy/groups/uniified/appRoleAssignments/update | Atualizar grupos.propriedade unificada em Azure Ative Directory. |
| microsoft.diretório/grupos/unificado/básico/atualização | Atualizar propriedades básicas do Office 365 Groups. |
| microsoft.diretório/grupos/unificado/criar | Crie o Office 365 Groups. |
| microsoft.diretório/grupos/unificado/excluir | Eliminar o Office 365 Groups. |
| microsoft.diretório/grupos/unificado/membros/atualização | Atualização da adesão ao Office 365 Groups. |
| microsoft.diretório/grupos/unificado/proprietários/atualização | Atualizar a propriedade do Office 365 Groups. |
| microsoft.office365.network/performance/allProperties/read | Leia as páginas de desempenho da rede no Centro de Administração M365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |
| microsoft.office365.usageReports/allEntities/read | Leia os relatórios de utilização do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="user-administrator-permissions"></a>Permissões do Administrador do Utilizador
Pode gerir todos os aspetos dos utilizadores e grupos, incluindo a reposição de senhas para administradores limitados.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.directy/appRoleAssignments/create | Crie appRoleAssignments no Diretório Ativo Azure. |
| microsoft.directy/appRoleAssignments/delete | Eliminar as atribuições de aplicações no Diretório Ativo Azure. |
| microsoft.directy/appRoleAssignments/update | Atualizar appRoleAssignments no Diretório Ativo Azure. |
| microsoft.directy/contacts/basic/update | Atualizar propriedades básicas sobre contactos em Diretório Ativo Azure. |
| microsoft.directy/contacts/create | Crie contactos no Diretório Ativo Azure. |
| microsoft.directy/contacts/delete | Eliminar contactos no Diretório Ativo Azure. |
| microsoft.directy/groups/appRoleAssignments/update | Update groups.appRoleAssignments property in Azure Ative Directory. |
| microsoft.directy/groups/basic/update | Atualizar propriedades básicas em grupos em Azure Ative Directory. |
| microsoft.diretório/grupos/criar | Crie grupos no Diretório Ativo Azure. |
| microsoft.directy/groups/createAsOwner | Crie grupos no Diretório Ativo Azure. O criador é adicionado como o primeiro proprietário, e o objeto criado conta com a quota de 250 objetos criados pelo criador. |
| microsoft.diretório/grupos/eliminar | Eliminar grupos no Diretório Ativo Azure. |
| microsoft.diretório/grupos/hiddenMembers/read | Leia a propriedade dos grupos.hiddenMembers em Azure Ative Directory. |
| microsoft.diretório/grupos/membros/atualização | Atualizar grupos.membros propriedade em Azure Ative Directory. |
| microsoft.diretório/grupos/proprietários/atualização | Atualizar grupos.proprietários propriedade em Azure Ative Diretório. |
| microsoft.diretório/grupos/restauro | Restaurar grupos no Diretório Ativo Azure. |
| microsoft.directy/groups/settings/update | Atualizar grupos.configura a propriedade no Diretório Ativo Azure. |
| microsoft.directy/users/appRoleAssignments/update | Atualizar utilizadores.appRoleAssignments propriedade em Azure Ative Directory. |
| microsoft.directy/users/assignLicense | Gerir licenças sobre utilizadores em Diretório Ativo Azure. |
| microsoft.directy/users/basic/update | Atualizar propriedades básicas sobre utilizadores no Diretório Ativo Do Azure. |
| microsoft.directy/users/create | Crie utilizadores no Azure Active Directory. |
| microsoft.directy/users/delete | Elimine os utilizadores no Diretório Ativo Azure. |
| microsoft.directy/users/invalidateAllRefreshTokens | Invalide todas as fichas de atualização do utilizador no Diretório Ativo Azure. |
| microsoft.directy/users/manager/update | Atualize a propriedade de utilizadores.manager no Diretório Ativo Azure. |
| microsoft.directy/users/password/update | Atualizar palavras-passe para todos os utilizadores no Diretório Ativo do Azure. Consulte a documentação online para mais detalhes. |
| microsoft.directy/users/restore | Restaurar os utilizadores eliminados no Diretório Ativo Azure. |
| microsoft.directy/users/userPrincipalName/update | Atualizar utilizadores.userPrincipalName propriedade em Azure Ative Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Leia e configure a Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Crie e gereos bilhetes de apoio Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Leia e configure o Gabinete 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Crie e gerencie os bilhetes de apoio do Office 365. |

## <a name="role-template-ids"></a>IDs de modelo de função

Os IDs do modelo de função são utilizados principalmente pelos utilizadores da Microsoft Graph API ou PowerShell.

Nome de exibição de gráfico | Nome de exibição do portal Azure | direccionalRoleTemplateId
----------------- | ------------------------- | -------------------------
Administrador da Aplicação | Administrador de candidatura | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Programador de Aplicações | Desenvolvedor de aplicações | CF1C38E5-3621-4004-A7CB-879624DCED7C
Administrador de Autenticação | Administrador de autenticação | c4e39bd9-1100-46d3-8c65-fb160da071f
Administrador azure DevOps | Administrador da Azure DevOps | e3973bdf-4987-49ae-837a-ba8e231c7286
Administrador de Proteção de Informação Azure | Administrador de Proteção de Informação Azure | 7495fdc4-34c4-4d15-a289-98788ce399fd
Administrador de fluxo de utilizador B2C | Administrador de fluxo de utilizador B2C | 6e591065-9bad-43ed-90f3-e9424366d2f0
Administrador de atributo de fluxo de utilizador B2C | Administrador de atributo de fluxo de utilizador B2C | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Administrador de teclas B2C IEF | Administrador de teclas B2C IEF | aaf43236-0c0d-4d5f-883a-6955382ac081
Administrador de Política B2C IEF | Administrador de Política B2C IEF | 3edaf663-341e-4475-9f94-5c398ef6c070
Administrador de Faturação | Administrador de faturação | b0f54661-2d74-4c50-afa3-1ec803f12efe
Administrador de Aplicações na Cloud | Administrador de aplicação em nuvem | 158c047a-c907-4556-b7ef-446551a6b5f7
Administrador de dispositivos de nuvem | Administrador de dispositivos de nuvem | 7698a772-787b-4ac8-901f-60d6b08affd2
Administrador da Empresa | Administrador global | 62e90394-69f5-4237-9190-01217145e10
Administrador de Conformidade | Administrador de conformidade | 17315797-102d-40b4-93e0-432062caca18
Administrador de Dados de Conformidade | Administrador de dados de conformidade | e6d1a23a-da11-4be4-9570-befc86d067a7
Administrador de Acesso Condicional | Administrador de Acesso Condicional | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Administrador de serviço crm | Administrador da Dinâmica 365 | 44367163-eba1-44c3-98af-f578779f96a
Aprovador de acesso ao Bloqueio do Cliente | Aprovador de acesso ao bloqueio do cliente | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Administrador de Análise de Desktop | Administrador de Análise de Desktop | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Administradores de Dispositivos | Administradores de dispositivos | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Aderamento do dispositivo | Adesão ao dispositivo | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Gestores de Dispositivos | Gestores de dispositivos | 2b499bcd-da44-4968-8aec-78e167444d
Utilizadores de dispositivos | Utilizadores de dispositivos | d405c6df-0af8-4e3b-95e4-4d06e542189e
Leitores de Diretório | Leitores de diretório | 88d8e3e3-8f55-4a1e-953a-9b9898b876b
Contas de Sincronização de Diretório | Contas de sincronização de diretórios | d29b2b05-8046-44ba-8758-1e26182fcf32
Escritores de Diretório | Escritores de diretórios | 9360feb5-f418-4baa-8175-e2a00bac4301
Administrador de Serviço de Intercâmbio | Administrador do Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administrador de Fornecedor de Identidade Externa | Administrador de Fornecedor de Identidade Externa | be2f45a1-457d-42af-a067-6ec1fa63bc45
Leitor Global | Leitor global | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Administrador de Grupos | Administrador de grupos | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Convidado convidado | Convidado convidado | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Administrador de Helpdesk | Administrador de helpdesk | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administrador de Identidade Híbrida | Administrador de identidade híbrido | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Administrador de Serviços do Intune | Administrador do Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Administrador kaizala | Administrador kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Administrador de Licença | Administrador de licença | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Administrador de Serviço Lync | Administrador do Skype para Empresas | 75941009-915a-4869-abe7-691bff18279e
Leitor de privacidade do Centro de Mensagens | Leitor de privacidade do centro de mensagens | ac16e43d-7b2d-40e0-ac05-243ff356ab5b5b
Leitor de centro de mensagens | Leitor de centro de mensagens | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Administrador de Rede | Administrador de rede | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Administrador de aplicações de escritório | Administrador de aplicações de escritório | 2b745bdf-0803-4d80-aa65-822c4493daac
Suporte de parceiro tier1 | Suporte de nível parceiro1 | 4ba39ca4-527c-499a-b93d-d9b492c50246
Suporte de parceiro tier2 | Suporte de nível parceiro2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Administrador de palavra-passe | Administrador de palavras-passe | 966707d0-3269-4727-9be2-8c3a10f19b9d
Administrador do Serviço Power BI | Administrador power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Administrador da Plataforma de Energia | Administrador de plataforma de energia | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Administrador de impressora | Administrador de impressora | 644ef478-e28f-4e28-b9dc-3fdde9a0b1f
Técnico de impressora | Técnico de impressora | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
Administrador de Autenticação Privilegiada | Administrador de autenticação privilegiado | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administrador privilegiado | Administrador privilegiado | e8611ab8-c189-46e8-94e1-60213ab1f814
Relatórios Leitor | Leitor de relatórios | 4a5d8f65-41da-4de4-8968-e035b65339cf
Administrador de Pesquisa | Administrador de pesquisa | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Editor de Pesquisa | Editor de pesquisa | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Administrador de Segurança | Administrador de segurança | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operador de Segurança | Operador de segurança | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Leitor de Segurança | Leitor de segurança | 5d6b6bb7-de71-4623-b4af-96380a352509
Administrador de Suporte ao Serviço | Administrador de suporte de serviços | f023fd81-a637-4b56-95fd-791ac0226033
Administrador de serviço sharePoint | Administrador do SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Administrador de Comunicações de Equipas | Administrador de Comunicações de Equipas | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Engenheiro de Apoio às Comunicações de Equipas | Engenheiro de Apoio às Comunicações de Equipas | f70938a0-fc10-4177-9e90-2178f8765737
Especialista em Apoio às Comunicações de Equipas | Especialista em Apoio às Comunicações de Equipas | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Administrador de Serviço de Equipas | Administrador de Serviço de Equipas | 69091246-20e8-4a56-aa4d-066075b2a7a8
Utilizador | Utilizador | a0b1b346-4d3e-4e8b-98f8-753987be4970
Administrador da Conta do Utilizador | Administrador de utilizadores | fe930be7-5e62-47db-91af-98c3a49a38b1
Dispositivo de local de trabalho Aderir | Dispositivo de local de trabalho aderir | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>Papéis depreciados

As seguintes funções não devem ser utilizadas. Foram depreciados e serão removidos da AD Azure no futuro.

* Administrador de licença AdHoc
* Aderamento do dispositivo
* Gestores de Dispositivos
* Utilizadores de dispositivos
* E-mail Verificado Criador de Utilizadores
* Administrador de caixa de correio
* Dispositivo de local de trabalho Aderir

## <a name="roles-not-shown-in-the-portal"></a>Papéis não mostrados no portal

Nem todas as funções devolvidas pela PowerShell ou mS Graph API são visíveis no portal Azure. A tabela seguinte organiza essas diferenças.

Nome API | Nome do portal Azure | Notas
-------- | ------------------- | -------------
Administrador da Empresa | Administrador Global | [Nome alterado para uma melhor clareza](directory-assign-admin-roles.md#role-template-ids)
Administrador de serviço crm | Administrador da Dinâmica 365 | [Reflete a marca atual do produto](directory-assign-admin-roles.md#role-template-ids)
Aderamento do dispositivo | Preterido | [Documentação de papéis depreciados](directory-assign-admin-roles.md#deprecated-roles)
Gestores de Dispositivos | Preterido | [Documentação de papéis depreciados](directory-assign-admin-roles.md#deprecated-roles)
Utilizadores de dispositivos | Preterido | [Documentação de papéis depreciados](directory-assign-admin-roles.md#deprecated-roles)
Contas de Sincronização de Diretório | Não mostrado porque não deve ser usado | [Documentação das Contas de Sincronização do Diretório](directory-assign-admin-roles.md#directory-synchronization-accounts)
Escritores de Diretório | Não mostrado porque não deve ser usado | [Documentação de Escritores de Diretório](directory-assign-admin-roles.md#directory-writers)
Utilizador Convidado | Não mostrado porque não pode ser usado  | ND
Administrador de Serviço Lync | Administrador do Skype para Empresas | [Reflete a marca atual do produto](directory-assign-admin-roles.md#role-template-ids)
Suporte de nível 1 do parceiro | Não mostrado porque não deve ser usado | [Documentação de suporte de nível 1 do parceiro](directory-assign-admin-roles.md#partner-tier1-support)
Suporte de nível parceiro 2 | Não mostrado porque não deve ser usado | [Documentação de suporte de nível parceiro2](directory-assign-admin-roles.md#partner-tier2-support)
Administrador de impressora | Trabalho em curso | Trabalho em curso
Técnico de impressora | Trabalho em curso | Trabalho em curso
Utilizador de hóspedes restrito | Não mostrado porque não pode ser usado | ND
Utilizador | Não mostrado porque não pode ser usado | ND
Dispositivo de local de trabalho Aderir | Preterido | [Documentação de papéis depreciados](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como atribuir um utilizador como administrador de uma subscrição do Azure, consulte [gerir o acesso utilizando funções Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)
* Para saber mais sobre como o acesso a recursos é controlado no Microsoft Azure, consulte [Compreender as diferentes funções](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para obter mais informações sobre como o Azure Active Directory está relacionado com a sua subscrição do Azure, veja [Como as subscrições do Azure estão associadas ao Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
