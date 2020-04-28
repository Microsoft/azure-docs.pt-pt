---
title: Conceder aos utilizadores B2B acesso às suas aplicações no local - Azure AD
description: Mostra como dar aos utilizadores da cloud B2B acesso a aplicações no local com colaboração Azure AD B2B.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74272804"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Conceder aos utilizadores B2B no Acesso a AD Azure às suas aplicações no local

Como uma organização que utiliza capacidades de colaboração Do Azure Ative Directory (Azure AD) B2B para convidar utilizadores convidados de organizações parceiras para o seu Azure AD, pode agora fornecer a estes utilizadores B2B acesso a aplicações no local. Estas aplicações no local podem utilizar a autenticação baseada em SAML ou autenticação integrada do Windows (IWA) com a delegação limitada da Kerberos (KCD).

## <a name="access-to-saml-apps"></a>Acesso a aplicativos SAML

Se a sua aplicação no local utilizar a autenticação baseada em SAML, pode facilmente disponibilizar estas aplicações aos utilizadores de colaboração Azure AD B2B através do portal Azure.

Deve fazer os dois seguintes:

- Integre a aplicação SAML utilizando o modelo de aplicação não-galeria, conforme descrito na Configuração de [um único sign-on para aplicações que não estão na galeria de aplicações do Azure Ative Directory.](../manage-apps/configure-single-sign-on-non-gallery-applications.md) Certifique-se de que nota o que utiliza para o valor **de URL de início de sessão.**
-  Utilize o Proxy de Aplicação AD Azure para publicar a aplicação no local, com o **Diretório Ativo Azure** configurado como fonte de autenticação. Para obter instruções, consulte [Publicar aplicações utilizando procuração de aplicação AD Azure](../manage-apps/application-proxy-publish-azure-portal.md). 

   Quando configurar a definição de **Url Interno,** utilize o URL de início de sessão que especificou no modelo de aplicação não-galeria. Desta forma, os utilizadores podem aceder à aplicação de fora do limite da organização. Application Proxy executa o único sign-on SAML para a aplicação no local.
 
   ![Mostra no local configurações de URL interno e autenticação](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Acesso a aplicativos IWA e KCD

Para fornecer aos utilizadores B2B acesso a aplicações no local que estejam protegidas com autenticação integrada do Windows e delegação limitada kerberos, você precisa dos seguintes componentes:

- Autenticação através de Procuração de **Aplicação AD Azure**. Os utilizadores B2B devem poder autenticar a aplicação no local. Para isso, tem de publicar a aplicação no local através do Procurador de Aplicação AD Azure. Para mais informações, consulte Iniciar com procuração de aplicação e instalar as aplicações [de conector](../manage-apps/application-proxy-enable.md) e publicar utilizando o Proxy de [Aplicação AD Azure](../manage-apps/application-proxy-publish-azure-portal.md).
- Autorização através de um objeto de **utilizador B2B no diretório no local**. A aplicação deve poder efetuar controlos de acesso ao utilizador e dar acesso aos recursos corretos. A IWA e a KCD requerem um objeto de utilizador no Diretório Ativo do Windows Server no local para completar esta autorização. Tal como descrito em Como funciona uma única inscrição com a [KCD,](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works)o Application Proxy precisa deste objeto de utilizador para personificar o utilizador e obter um símbolo kerberos para a aplicação. 

   Para o cenário de utilizador B2B, existem dois métodos disponíveis que pode utilizar para criar os objetos de utilizador convidado que são necessários para autorização no diretório no local:

   - Microsoft Identity Manager (MIM) e o agente de gestão MIM para o Microsoft Graph. 
   - [Um script PowerShell.](#create-b2b-guest-user-objects-through-a-script-preview) Usar o script é uma solução mais leve que não requer MIM. 

O diagrama seguinte fornece uma visão geral de alto nível de como o Proxy de Aplicação AD Azure e a geração do objeto de utilizador B2B no diretório no local trabalham em conjunto para dar aos utilizadores B2B acesso às suas aplicações IWA e KCD no local. Os passos numerados são descritos em detalhe abaixo do diagrama.

![Diagrama de soluções de script MIM e B2B](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Um utilizador de uma organização parceira (o inquilino Fabrikam) é convidado para o inquilino de Contoso.
2.  Um objeto de utilizador convidado é criado no inquilino Contoso (por exemplo, um objeto@contoso.onmicrosoft.comde utilizador com uma UPN de guest_fabrikam.com#EXT# ).
3.  O hóspede Fabrikam é importado de Contoso através de MIM ou através do script B2B PowerShell.
4.  Uma representação ou "pegada" do objeto de utilizador convidado Fabrikam (Guest#EXT#) é criada no diretório no local, Contoso.com, através de MIM ou através do script B2B PowerShell.
5.  O utilizador hóspede acede à aplicação no local, app.contoso.com.
6.  O pedido de autenticação é autorizado através da Application Proxy, utilizando a delegação limitada kerberos. 
7.  Como o objeto de utilizador hóspede existe localmente, a autenticação é bem sucedida.

### <a name="lifecycle-management-policies"></a>Políticas de gestão do ciclo de vida

Pode gerir os objetos de utilizador B2B no local através de políticas de gestão de ciclos de vida. Por exemplo:

- Pode configurar políticas de autenticação de vários fatores (MFA) para o utilizador Convidado para que o MFA seja utilizado durante a autenticação do Proxy de Aplicação. Para mais informações, consulte Acesso Condicional para utilizadores de [colaboração B2B.](conditional-access.md)
- Quaisquer patrocínios, avaliações de acesso, verificações de conta, etc. que sejam realizadas na nuvem B2B utilizador aplica-se aos utilizadores no local. Por exemplo, se o utilizador da nuvem for eliminado através das suas políticas de gestão do ciclo de vida, o utilizador no local também é eliminado pelo MIM Sync ou através da sincronização Azure AD Connect. Para mais informações, consulte Gerir o acesso dos [hóspedes com avaliações de acesso a Anúncio sinuosos.](../governance/manage-guest-access-with-access-reviews.md)

### <a name="create-b2b-guest-user-objects-through-mim"></a>Crie objetos de utilizador convidado B2B através de MIM

Para obter informações sobre como usar o MIM 2016 Service Pack 1 e o agente de gestão MIM para o Microsoft Graph para criar os objetos de utilizador convidado no diretório no local, consulte a [colaboração Azure AD business-to-business (B2B) com o Microsoft Identity Manager (MIM) 2016 SP1 com o Azure Application Proxy.](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario)

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Criar objetos de utilizador convidado B2B através de um script (Pré-visualização)

Existe um script de amostra PowerShell disponível que pode usar como ponto de partida para criar os objetos de utilizador convidado no seu Diretório Ativo no local.

Pode descarregar o script e o ficheiro Readme do [Centro de Descarregamento](https://www.microsoft.com/download/details.aspx?id=51495). Escolha o **Script e o Readme para puxar os utilizadores Azure AD B2B em-prem.zip.**

Antes de utilizar o script, certifique-se de que revê os pré-requisitos e considerações importantes no ficheiro Readme associado. Além disso, entenda que o script é disponibilizado apenas como uma amostra. A sua equipa de desenvolvimento ou um parceiro deve personalizar e rever o script antes de o executar.

## <a name="license-considerations"></a>Considerações de licença

Certifique-se de que tem as licenças de acesso ao cliente (CALs) corretas para utilizadores externos que acedem a aplicações no local. Para mais informações, consulte a secção "Conectores Externos" das Licenças de Acesso ao Cliente e Licenças de [Gestão.](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx) Consulte o seu representante da Microsoft ou revendedor local sobre as suas necessidades específicas de licenciamento.

## <a name="next-steps"></a>Passos seguintes

- [Colaboração Azure Ative Directory B2B para organizações híbridas](hybrid-organizations.md)

- Para uma visão geral do Azure AD Connect, consulte [Integrar os seus diretórios no local com o Diretório Ativo Azure](../hybrid/whatis-hybrid-identity.md).

