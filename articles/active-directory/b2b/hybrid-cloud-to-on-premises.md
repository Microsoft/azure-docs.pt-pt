---
title: Conceder aos usuários B2B acesso aos seus aplicativos locais-Azure AD
description: Mostra como fornecer aos usuários B2B de nuvem acesso a aplicativos locais com a colaboração B2B do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 098f464b6af5f10866403e1cd1549d571d883ac1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272804"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Conceder aos usuários B2B no Azure AD acesso aos seus aplicativos locais

Como uma organização que utiliza recursos de colaboração B2B do Azure Active Directory (Azure AD) para convidar utilizadores de organizações parceiras com o Azure AD, pode agora fornecer estes utilizadores B2B acesso a aplicações no local. Estas aplicações no local podem utilizar a autenticação baseada no SAML ou autenticação integrada do Windows (IWA) com a delegação restringida de Kerberos (KCD).

## <a name="access-to-saml-apps"></a>Acesso a aplicativos SAML

Se seu aplicativo local usa a autenticação baseada em SAML, você pode facilmente disponibilizar esses aplicativos para seus usuários de colaboração B2B do Azure AD por meio do portal do Azure.

Você deve fazer o seguinte:

- Integre o aplicativo SAML usando o modelo de aplicativo inexistente na Galeria, conforme descrito em [Configurando o logon único para aplicativos que não estão na Galeria de aplicativos do Azure Active Directory](../manage-apps/configure-single-sign-on-non-gallery-applications.md). Certifique-se de observar o que você usa para o valor da **URL de logon** .
-  Use o Proxy de Aplicativo do AD do Azure para publicar o aplicativo local, com **Azure Active Directory** configurado como a origem de autenticação. Para obter instruções, consulte [publicar aplicativos usando o Azure proxy de aplicativo do AD](../manage-apps/application-proxy-publish-azure-portal.md). 

   Ao definir a configuração de **URL interna** , use a URL de logon que você especificou no modelo de aplicativo inexistente na galeria. Dessa forma, os usuários podem acessar o aplicativo de fora do limite da organização. O proxy de aplicativo executa o logon único do SAML para o aplicativo local.
 
   ![Mostra a URL interna e a autenticação das configurações do aplicativo local](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Acesso a aplicativos IWA e KCD

Para fornecer aos usuários B2B acesso a aplicativos locais que são protegidos com a autenticação integrada do Windows e a delegação restrita de Kerberos, você precisa dos seguintes componentes:

- **Autenticação por meio do Azure proxy de aplicativo do AD**. Os usuários B2B devem ser capazes de se autenticar no aplicativo local. Para fazer isso, você deve publicar o aplicativo local por meio do Proxy de Aplicativo do AD do Azure. Para obter mais informações, consulte Introdução [ao proxy de aplicativo e instalar o conector](../manage-apps/application-proxy-enable.md) e [publicar aplicativos usando o Azure proxy de aplicativo do AD](../manage-apps/application-proxy-publish-azure-portal.md).
- **Autorização por meio de um objeto de usuário B2B no diretório local**. O aplicativo deve ser capaz de executar verificações de acesso do usuário e conceder acesso aos recursos corretos. IWA e KCD exigem um objeto de usuário no Windows Server local Active Directory para concluir essa autorização. Conforme descrito em [como o logon único com o KCD funciona](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), o proxy de aplicativo precisa desse objeto de usuário para representar o usuário e obter um token Kerberos para o aplicativo. 

   Para o cenário de usuário B2B, há dois métodos disponíveis que você pode usar para criar os objetos de usuário convidado que são necessários para a autorização no diretório local:

   - O MIM (Microsoft Identity Manager) e o agente de gerenciamento do MIM para Microsoft Graph. 
   - [Um script do PowerShell](#create-b2b-guest-user-objects-through-a-script-preview). O uso do script é uma solução mais leve que não exige o MIM. 

O diagrama a seguir fornece uma visão geral de alto nível de como o Azure Proxy de Aplicativo do AD e a geração do objeto de usuário B2B no diretório local trabalham em conjunto para conceder aos usuários B2B acesso aos seus aplicativos IWA e KCD locais. As etapas numeradas são descritas em detalhes abaixo do diagrama.

![Diagrama de soluções de script MIM e B2B](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Um usuário de uma organização parceira (o locatário da Fabrikam) é convidado para o locatário da contoso.
2.  Um objeto de usuário convidado é criado no locatário da Contoso (por exemplo, um objeto de usuário com um UPN de guest_fabrikam. com # EXT #@contoso.onmicrosoft.com).
3.  O convidado da Fabrikam é importado da Contoso por meio do MIM ou do script do PowerShell B2B.
4.  Uma representação ou "superfície" do objeto de usuário convidado da Fabrikam (Guest # EXT #) é criada no diretório local, Contoso.com, via MIM ou por meio do script do PowerShell B2B.
5.  O usuário convidado acessa o aplicativo local, app.contoso.com.
6.  A solicitação de autenticação é autorizada por meio do proxy de aplicativo, usando a delegação restrita de Kerberos. 
7.  Como o objeto de usuário convidado existe localmente, a autenticação é bem-sucedida.

### <a name="lifecycle-management-policies"></a>Políticas de gerenciamento do ciclo de vida

Você pode gerenciar os objetos de usuário B2B locais por meio de políticas de gerenciamento do ciclo de vida. Por exemplo:

- Você pode configurar políticas de MFA (autenticação multifator) para o usuário convidado para que a MFA seja usada durante a autenticação do proxy de aplicativo. Para obter mais informações, consulte [acesso condicional para usuários de colaboração B2B](conditional-access.md).
- Quaisquer dos patrocínios, revisões de acesso, verificações de conta etc. que são executadas no usuário B2B de nuvem se aplicam aos usuários locais. Por exemplo, se o usuário de nuvem for excluído por meio de suas políticas de gerenciamento do ciclo de vida, o usuário local também será excluído pela sincronização do MIM ou por meio de sincronização de Azure AD Connect. Para obter mais informações, consulte [gerenciar o acesso de convidado com revisões de acesso do Azure ad](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Criar objetos de usuário convidado B2B por meio do MIM

Para obter informações sobre como usar o MIM 2016 Service Pack 1 e o agente de gerenciamento do MIM para Microsoft Graph para criar os objetos de usuário convidado no diretório local, consulte [colaboração entre empresas (B2B) do Azure AD com o Microsoft Identity Manager (mim) 2016 SP1 com aplicativo Azure proxy](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Criar objetos de usuário convidado B2B por meio de um script (visualização)

Há um script de exemplo do PowerShell disponível que você pode usar como ponto de partida para criar os objetos de usuário convidado em seu Active Directory local.

Você pode baixar o script e o arquivo Leiame no [centro de download](https://www.microsoft.com/download/details.aspx?id=51495). Escolha o **script e Leiame para extrair os usuários do on-Prem. zip do Azure ad B2B** .

Antes de usar o script, certifique-se de revisar os pré-requisitos e considerações importantes no arquivo Leiame associado. Além disso, entenda que o script é disponibilizado apenas como exemplo. Sua equipe de desenvolvimento ou um parceiro deve personalizar e revisar o script antes de executá-lo.

## <a name="license-considerations"></a>Considerações sobre licenças

Verifique se você tem as CALs (licenças de acesso para cliente) corretas para usuários convidados externos que acessam aplicativos locais. Para obter mais informações, consulte a seção "conectores externos" de [licenças de acesso para cliente e licenças de gerenciamento](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Consulte seu representante da Microsoft ou revendedor local sobre suas necessidades de licenciamento específicas.

## <a name="next-steps"></a>Passos seguintes

- [Azure Active Directory colaboração B2B para organizações híbridas](hybrid-organizations.md)

- Para obter uma visão geral do Azure AD Connect, consulte [integrar seus diretórios locais com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

