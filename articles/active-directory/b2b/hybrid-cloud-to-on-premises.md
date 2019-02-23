---
title: Utilizadores B2B de concessão de acesso às suas aplicações no local - Azure Active Directory | Documentos da Microsoft
description: Mostra como conceder a cloud aos utilizadores B2B acesso a aplicações no local com a colaboração B2B do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: d353fa49fe054a28a913cef631cc500b5d6e937c
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673601"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Os utilizadores de concessão B2B no Azure AD acedem às suas aplicações no local

Como uma organização que utiliza recursos de colaboração B2B do Azure Active Directory (Azure AD) para convidar utilizadores de organizações parceiras com o Azure AD, pode agora fornecer estes utilizadores B2B acesso a aplicações no local. Estas aplicações no local podem utilizar a autenticação baseada no SAML ou autenticação integrada do Windows (IWA) com a delegação restringida de Kerberos (KCD).

## <a name="access-to-saml-apps"></a>Acesso a aplicações SAML

Se a sua aplicação no local utiliza autenticação baseada no SAML, pode facilmente disponibilizar estas aplicações para os utilizadores de colaboração B2B do Azure AD através do portal do Azure.

Terá de efetuar os seguintes elementos:

- Integrar a aplicação SAML, utilizando o modelo de aplicativo externas à galeria, conforme descrito em [configurar o início de sessão único para aplicações que não estão na Galeria de aplicações do Azure Active Directory](../manage-apps/configure-single-sign-on-non-gallery-applications.md). Certifique-se de observar o que utilizar para o **URL de início de sessão** valor.
-  Utilizar o Proxy de aplicações do Azure AD para publicar a aplicação no local, com **do Azure Active Directory** configurado como origem de autenticação. Para obter instruções, consulte [publicar aplicações com o Proxy de aplicações do Azure AD](../manage-apps/application-proxy-publish-azure-portal.md). 

   Quando configura a **Url interno** definir, utilize o URL de início de sessão que especificou no modelo de aplicativo externas à galeria. Dessa forma, os utilizadores podem aceder a aplicação a partir de fora do limite da organização. Proxy da aplicação executa o SAML início de sessão único para a aplicação no local.
 
   ![Mostra o URL interno de definições de aplicações no local e a autenticação](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Acesso a aplicações IWA e KCD

Para fornecer utilizadores B2B acesso a aplicações no local que são protegidas com a autenticação integrada do Windows e delegação restrita de Kerberos, terá dos seguintes componentes:

- **Autenticação através do Proxy de aplicações do Azure AD**. Utilizadores B2B tem de conseguir autenticar a aplicação no local. Para tal, tem de publicar a aplicação no local através do Proxy de aplicações do Azure AD. Para obter mais informações, consulte [introdução ao Proxy da aplicação e instalar o conector](../manage-apps/application-proxy-enable.md) e [publicar aplicações com o Proxy de aplicações do Azure AD](../manage-apps/application-proxy-publish-azure-portal.md).
- **Autorização por meio de um objeto de utilizador B2B no diretório no local**. O aplicativo deve ser capaz de realizar verificações de acesso de utilizador e conceder acesso aos recursos corretos. IWA e KCD exigem um objeto de utilizador no local Windows Server Active Directory para concluir esta autorização. Conforme descrito em [como início de sessão único com KCD funciona](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), Proxy de aplicações tem este objeto de utilizador para representar o utilizador e obter um token de Kerberos para a aplicação. 

   Para o cenário de utilizador B2B, existem dois métodos disponíveis que pode utilizar para criar os objetos de utilizador convidado que são necessários para autorização no diretório no local:

   - Microsoft Identity Manager (MIM) e o agente de gestão de MIM para o Microsoft Graph. 
   - [Um script do PowerShell](#create-b2b-guest-user-objects-through-a-script-preview). Usando o script é uma solução mais simples que não necessita de MIM. 

O diagrama seguinte fornece uma visão geral do Proxy de aplicações como o Azure AD e a geração do objeto de utilizador B2B no local directory funcionam em conjunto para conceder a utilizadores B2B acesso às suas aplicações IWA e KCD no local. Os passos numerados são descritos em detalhe o diagrama abaixo.

![Diagrama de soluções de script MIM e B2B](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Um utilizador a partir de uma organização de parceiro (o inquilino da Fabrikam) é convidado para o inquilino da Contoso.
2.  Um objeto de utilizador convidado é criado no inquilino Contoso (por exemplo, um objeto de utilizador com um UPN de guest_fabrikam.com#EXT#@contoso.onmicrosoft.com).
3.  O convidado Fabrikam é importado da Contoso através de MIM ou o script do PowerShell de B2B.
4.  Uma representação ou "espaço" do objeto de utilizador Fabrikam convidados (convidados #EXT #) é criado no diretório no local, Contoso.com, por meio de MIM ou o script do PowerShell de B2B.
5.  A aplicação de acessos no local do utilizador convidado, app.contoso.com.
6.  O pedido de autenticação está autorizado através do Proxy de aplicações, utilizando a delegação restringida de Kerberos. 
7.  Porque o objeto de utilizador convidado existe localmente, a autenticação é efetuada com êxito.

### <a name="lifecycle-management-policies"></a>Políticas de gestão do ciclo de vida

Pode gerenciar os objetos de utilizador B2B no local através de políticas de gestão do ciclo de vida. Por exemplo:

- Pode configurar políticas de autenticação multifator (MFA) para o utilizador convidado para que a MFA é utilizado durante a autenticação de Proxy de aplicações. Para obter mais informações, consulte [acesso condicional para utilizadores de colaboração B2B](conditional-access.md).
- Qualquer patrocínios, revisões de acesso, verificações de conta, etc., que são executadas na cloud B2B utilizador aplica-se aos utilizadores no local. Por exemplo, se o utilizador de nuvem é eliminado por meio de suas políticas de gestão do ciclo de vida, o utilizador no local também é eliminado por sincronização de MIM ou através do Azure AD Connect. Para obter mais informações, consulte [revisões de acesso de convidado de gerir com o acesso do Azure AD](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Criar objetos de utilizador B2B convidado através de MIM

Para obter informações sobre como utilizar o MIM 2016 Service Pack 1 e o agente de gestão de MIM para o Microsoft Graph para criar os objetos de utilizador convidado no diretório no local, consulte [colaboração de empresa-empresa (B2B) do Azure AD com o Microsoft Identity Manager (MIM) 2016 SP1 com o Proxy de aplicações do Azure](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Criar objetos de utilizador B2B convidado através de um script (pré-visualização)

Existe um script de exemplo do PowerShell disponível que pode utilizar como ponto de partida para criar os objetos de utilizador convidado no Active Directory no local.

Pode transferir o script e o ficheiro Leia-me os [Centro de transferências](https://www.microsoft.com/download/details.aspx?id=51495). Escolha o **Script e leia-me para efetuar pull B2B do Azure AD os utilizadores do prem.zip** ficheiro.

Antes de utilizar o script, certifique-se de que revê os pré-requisitos e as considerações importantes para o ficheiro Leia-me associado. Além disso, compreenda que o script é disponibilizado apenas como um exemplo. Sua equipe de desenvolvimento ou um parceiro tem de personalizar e rever o script antes de executá-lo.

## <a name="license-considerations"></a>Considerações sobre a licença

Certifique-se de que tem as licenças de acesso cliente de correto (CALs) para utilizadores convidados externos que acederem a aplicações no local. Para obter mais informações, consulte a secção "Conectores externos" [licenças de acesso de cliente e licenças de gestão](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Consulte o seu representante da Microsoft ou revendedor local relativamente à suas necessidades específicas de licenciamento.

## <a name="next-steps"></a>Passos Seguintes

- [Colaboração do Azure Active Directory B2B para organizações híbridas](hybrid-organizations.md)

- Para uma descrição geral do Azure AD Connect, consulte [integrar seus diretórios no local com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

