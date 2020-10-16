---
title: Grant B2B acesso às suas aplicações no local - Azure AD
description: Mostra como dar aos utilizadores da cloud B2B acesso a aplicações de instalações com colaboração Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 132711b4a3fc584261cd577b4b8f1d4fb13da09d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819390"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Grant B2B utilizadores em Azure AD acesso às suas aplicações no local

Como uma organização que utiliza capacidades de colaboração B2B (Azure AD) para convidar utilizadores convidados de organizações parceiras para o seu AD Azure, pode agora fornecer a estes utilizadores B2B acesso a aplicações no local. Estas aplicações no local podem utilizar a autenticação baseada em SAML ou a Autenticação Integrada do Windows (IWA) com a delegação restrita kerberos (KCD).

## <a name="access-to-saml-apps"></a>Acesso a aplicativos SAML

Se a sua aplicação no local utilizar a autenticação baseada em SAML, pode facilmente disponibilizar estas aplicações aos utilizadores da colaboração Azure AD B2B através do portal Azure.

Deve fazer as duas coisas:

- Integre a aplicação utilizando o SAML como descrito no [sign-on único baseado em Configure SAML](../manage-apps/configure-saml-single-sign-on.md). Certifique-se de que nota o que utiliza para o valor **URL de inscrição.**
-  Utilize o Azure AD Application Proxy para publicar a aplicação no local, com **o Azure Ative Directory** configurado como fonte de autenticação. Para obter instruções, consulte [publicar aplicações utilizando o Azure AD Application Proxy](../manage-apps/application-proxy-publish-azure-portal.md). 

   Quando configurar a definição **de Url Interno,** utilize o URL de inscrição que especificou no modelo de aplicação de não galeria. Desta forma, os utilizadores podem aceder à app a partir do limite da organização. Application Proxy executa o único sign-on SAML para a aplicação no local.
 
   ![Mostra configurações de aplicativos no local URL interno e autenticação](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Acesso a aplicativos IWA e KCD

Para fornecer aos utilizadores B2B acesso a aplicações no local que estejam protegidas com autenticação integrada do Windows e delegação restrita kerberos, precisa dos seguintes componentes:

- **Autenticação através do Azure AD Application Proxy**. Os utilizadores B2B devem poder autenticar a aplicação no local. Para isso, tem de publicar a aplicação no local através do Azure AD Application Proxy. Para obter mais informações, consulte [Começar com o Application Proxy e instalar as](../manage-apps/application-proxy-enable.md) aplicações de conector e publicação utilizando o [Azure AD Application Proxy](../manage-apps/application-proxy-publish-azure-portal.md).
- **Autorização através de um objeto utilizador B2B no diretório no local**. A aplicação deve ser capaz de realizar verificações de acesso ao utilizador e conceder acesso aos recursos corretos. A IWA e a KCD requerem um objeto de utilizador no Windows Server Ative Directory para completar esta autorização. Como descrito em [Como funciona um único s-on com o KCD,](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works)o Application Proxy precisa deste objeto do utilizador para personificar o utilizador e obter um token Kerberos para a aplicação. 

   > [!NOTE]
   > Quando configurar o Proxy de aplicação AD Azure, certifique-se de que a **identidade de logon delegada** está definida para **o nome principal do utilizador** (padrão) para um único sinal de IWA.

   Para o cenário de utilizador B2B, existem dois métodos disponíveis que pode utilizar para criar os objetos de utilizador convidado que são necessários para autorização no diretório no local:

   - Microsoft Identity Manager (MIM) e o agente de gestão MIM para o Microsoft Graph. 
   - [Um script PowerShell](#create-b2b-guest-user-objects-through-a-script-preview). A utilização do script é uma solução mais leve que não requer MIM. 

O diagrama seguinte fornece uma visão geral de alto nível de como a aplicação AD Proxy e a geração do objeto de utilizador B2B no diretório no local trabalham em conjunto para garantir aos utilizadores B2B acesso às suas aplicações IWA e KCD no local. Os passos numerados são descritos em detalhe abaixo do diagrama.

![Diagrama de soluções de script MIM e B2B](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Um utilizador de uma organização parceira (o inquilino de Fabrikam) é convidado para o inquilino contoso.
2.  Um objeto de utilizador convidado é criado no inquilino Contoso (por exemplo, um objeto de utilizador com uma UPN de guest_fabrikam.com#EXT# @contoso.onmicrosoft.com ).
3.  O hóspede Fabrikam é importado de Contoso através de MIM ou através do script B2B PowerShell.
4.  Uma representação ou "pegada" do objeto de utilizador convidado Fabrikam (Guest#EXT#) é criada no diretório de instalações, Contoso.com, através de MIM ou através do script B2B PowerShell.
5.  O utilizador convidado acede à aplicação no local, app.contoso.com.
6.  O pedido de autenticação é autorizado através da Aplicação Proxy, utilizando a delegação restrita kerberos. 
7.  Como o objeto do utilizador convidado existe localmente, a autenticação é bem sucedida.

### <a name="lifecycle-management-policies"></a>Políticas de gestão do ciclo de vida

Pode gerir os objetos de utilizador B2B no local através de políticas de gestão do ciclo de vida. Por exemplo:

- Pode configurar políticas de autenticação de vários fatores (MFA) para o utilizador Convidado para que o MFA seja utilizado durante a autenticação do Application Proxy. Para obter mais informações, consulte [Acesso Condicional para utilizadores de colaboração B2B.](conditional-access.md)
- Quaisquer patrocínios, revisões de acesso, verificações de conta, etc. que sejam realizados na nuvem B2B o utilizador aplica-se aos utilizadores no local. Por exemplo, se o utilizador da nuvem for eliminado através das suas políticas de gestão do ciclo de vida, o utilizador no local também é eliminado pela MIM Sync ou pela sincronização Azure AD Connect. Para obter mais informações, consulte [Gerir o acesso dos hóspedes com comentários de acesso Azure AD](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Criar objetos de utilizador convidado B2B através da MIM

Para obter informações sobre como utilizar o Pacote de Serviço MIM 2016 e o agente de gestão MIM para o Microsoft Graph para criar os objetos de utilizador convidado no diretório de instalações, consulte [a colaboração de negócios-a-negócios (B2B) da Azure AD com o Microsoft Identity Manager (MIM) 2016 SP1 com a Azure Application Proxy](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Criar objetos de utilizador convidado B2B através de um script (Pré-visualização)

Existe um script de amostra PowerShell disponível que pode usar como ponto de partida para criar os objetos de utilizador convidados no seu Ative Directory.

Pode descarregar o script e o ficheiro Readme do Centro de [Descarregamentos.](https://www.microsoft.com/download/details.aspx?id=51495) Escolha o **Script e Readme para puxar os utilizadores Azure AD B2B on-prem.zip** ficheiro.

Antes de utilizar o script, certifique-se de que revê os pré-requisitos e considerações importantes no ficheiro Readme associado. Além disso, entenda que o script é disponibilizado apenas como uma amostra. A sua equipa de desenvolvimento ou um parceiro deve personalizar e rever o script antes de executá-lo.

## <a name="license-considerations"></a>Considerações de licença

Certifique-se de que tem as licenças de acesso ao cliente (CALs) corretas para utilizadores externos que acedem a aplicações no local. Para mais informações, consulte a secção "Conectores Externos" das Licenças de [Acesso ao Cliente e Licenças de Gestão.](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx) Consulte o seu representante da Microsoft ou revendedor local sobre as suas necessidades específicas de licenciamento.

## <a name="next-steps"></a>Passos seguintes

- [Colaboração B2B do Diretório Ativo Azure para organizações híbridas](hybrid-organizations.md)

- Para uma visão geral do Azure AD Connect, consulte [Integrar os seus diretórios no local com o Azure Ative Directory](../hybrid/whatis-hybrid-identity.md).

