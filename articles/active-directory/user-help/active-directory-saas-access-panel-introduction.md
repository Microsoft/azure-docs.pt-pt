---
title: O que é o portal MyApps - Azure Active Directory? | Microsoft Docs
description: Aprenda a usar variações do portal MyApps (navegador da web, aplicações para Android, aplicação iPhone e iPad) para aceder às aplicações SaaS.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.reviewer: asteen
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47c624a9f1d3989e9146f7c32745ca892f6467e0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113811"
---
# <a name="what-is-the-myapps-portal"></a>O que é o portal MyApps?

Se tiver uma conta escola ou no Azure Active Directory (Azure AD), pode utilizar o portal as minhas aplicações baseadas na web para ver e iniciar os aplicativos baseados na nuvem que o administrador do Azure AD lhe concedeu que acesso a. Também pode usar grupos self-service e capacidades de gestão de aplicações através do portal MyApps.

O portal MyApps está separado do portal do Azure. Não requer que tenha uma subscrição do Azure.

![MyApps portal][1] ao utilizar o portal MyApps, pode editar algumas das suas definições de perfil e efetue o seguinte procedimento:

- Altere a palavra-passe associada uma conta escolar ou profissional.

- Edite definições de reposição de palavra-passe.

- Edite definições de contacto e preferência relacionadas com a autenticação multifator (para contas que tenham sido necessário usá-lo por um administrador).

- Ver a conta alternam de detalhes, como o ID de utilizador, dispositivos, móveis e office números de telefone e e-mail.

- Ver e iniciar os aplicativos baseados na nuvem que o administrador do Azure AD lhe concedeu que acesso a. 

- Grupos de Self-gerir. Os administradores podem criar e gerir grupos de segurança e as associações de grupo de segurança de pedido no Azure AD. Para obter mais informações, consulte [gestão de grupos self-service para utilizadores no Azure AD](../users-groups-roles/groups-self-service-management.md) e [gerir os seus grupos](../fundamentals/active-directory-manage-groups.md).

## <a name="access-the-myapps-portal"></a>Aceder ao portal MyApps

Pode aceder ao portal de MyApps acedendo a `https://myapps.microsoft.com`.

Se tiver de marca personalizada configurada para sua página de início de sessão, é possível carregar a imagem corporativa, acrescentando o domínio da sua organização para o URL (por exemplo, `https://myapps.microsoft.com/<your domain>.com`).

Pode utilizar qualquer nome de domínio do Active Directory ou verificado que tenha sido configurada no portal do Azure, como mostrado aqui: ![Nome de domínio do Wingtip Toys][2]  

Distribua o URL para todos os utilizadores que iniciar sessão nas aplicações que são integradas no Azure AD.

## <a name="authentication"></a>Authentication

Para aceder ao portal MyApps, tem de ser autenticado através de uma conta profissional ou escolar no Azure AD. Pode ser autenticado para o Azure AD diretamente. Em alternativa, se uma organização tiver configurado a Federação com os serviços de Federação do Active Directory (AD FS) ou outras tecnologias, pode ser autenticada pelo Windows Server Active Directory.

Se tiver uma subscrição do Azure ou do Office 365 e tem estado a utilizar o portal do Azure ou um aplicativo do Office 365, pode ver a lista de aplicações sem iniciar sessão novamente. Se não estiver autenticado, lhe for pedido para iniciar sessão com o nome de utilizador e palavra-passe para a sua conta no Azure AD. Se sua organização configurou o Federação, escreva o nome de utilizador é suficiente.

Quando estiver autenticado, pode interagir com os aplicativos que o administrador tem integrado com o diretório. Para saber como integrar aplicações com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="web-browser-requirements"></a>Requisitos de navegador da Web

No mínimo, o portal MyApps requer um browser que suporte a JavaScript e CSS ativou a. Para entrar para aplicações através do baseado em palavra-passe de início de sessão único (SSO), tem de ter a extensão do portal MyApps instalada no seu browser. A extensão é transferida automaticamente ao selecionar uma aplicação que está configurada para SSO baseado em palavra-passe.

O instalador é específica da arquitetura. Se clicar no link de download, apenas receberá o instalador para a arquitetura de sistema operacional atualmente em execução no. Se for um administrador de implementação de aplicação, certifique-se de que visitar o link de download de um dispositivo de 64 bits e 32 bits para obter ambos os instaladores.


A extensão do portal MyApps está atualmente disponível para:
- **Microsoft Edge**: Windows 10 Anniversary Edition ou posterior. 
- **Chrome**: no Windows 7 ou posterior e no MacOS X ou posterior.
- **Firefox 26.0 ou posterior**: no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior.
- **Internet Explorer 11**: no Windows 7 ou posterior (suporte limitado).

## <a name="my-apps-secure-sign-in-extension"></a>Extensão de Início de Sessão Seguro das Minhas Aplicações
Iniciar sessão com base em palavra-passe de início de sessão único, tem de utilizar a extensão. Depois da extensão é instalada, pode iniciar sessão na mesma para ativar funcionalidades adicionais, selecionando **iniciar sessão começar a utilizar**. 

- Pode iniciar sessão a uma aplicação diretamente através da aplicação **URL de início de sessão**. Quando utiliza o URL da aplicação, a extensão detecta a ação e lhe dá a opção de iniciar sessão a partir da extensão.
- Pode iniciar qualquer uma das suas aplicações a partir do portal MyApps, utilizando o *pesquisa rápida* funcionalidade da extensão. 
- A extensão mostra-lhe as últimas três aplicações que foi iniciado no **utilizados recentemente** secção.
- Pode usar URLs internos da empresa ao remoto através de [Proxy de aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)

> [!NOTE]
> Recursos adicionais estão disponíveis apenas para o Microsoft Edge, Chrome e Firefox.
> 
> Pode transferir a extensão diretamente a partir os seguintes sites:
> - [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
> - [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
> - [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Se estiver a utilizar um URL de aplicações My diferente de `https://myapps.microsoft.com`, configurar o URL predefinido ao fazer o seguinte:
1. Enquanto estiver *não* entrar para a extensão, com o botão direito no ícone de extensão.
2. No menu, selecione **URL de aplicações My**.
3. Selecione o URL predefinido.
4. Selecione o ícone de extensão.
5. Selecione **iniciar sessão começar a utilizar**.

Para utilizar URLs internos da empresa ao remoto usando a extensão, faça o seguinte:
1. [Configurar o Proxy de aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) no seu inquilino.
2. [Publicar a aplicação](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) e URL através do Proxy de aplicações.
3. Instalar a extensão e iniciar sessão na mesma ao selecionar o início de sessão para começar a utilizar.
4. Agora pode navegar para o URL interna da empresa, mesmo durante o remoto.

> [!NOTE]
> Também pode desativar redirecionamento automático para os URLs da empresa, selecionar o ícone de engrenagem de definições no menu principal e selecionando **desativar** para a opção de redirecionamento de URL interna da empresa.


## <a name="mobile-app-support"></a>Suporte de aplicações móveis

A equipa do Azure Active Directory publica a aplicação móvel as minhas aplicações. Ao instalar a aplicação, pode iniciar sessão nas aplicações de SSO baseado em palavra-passe em dispositivos iOS e Android.

> [!NOTE]
> Pode iniciar sessão aplicações que suportam o federação com o Azure AD (incluindo o Salesforce, Google Apps, Dropbox, caixa, Concur, Workday, do Office 365 e mais de 70 outros) em praticamente qualquer navegador da web, em qualquer dispositivo, sem precisar de uma aplicação de plug-in ou móvel. A ser utilizado num dispositivo móvel, o outro [experiências de MyApps portal](https://myapps.microsoft.com/) também não necessitam de aplicação móvel das minhas aplicações.

### <a name="my-apps-for-iphone-and-ipad"></a>Minhas aplicações para iPhone e iPad

As minhas aplicações para iOS é suportada em qualquer iPhone ou iPad com versão do iOS 7 ou posterior.  

Ele está disponível na [da Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Minhas aplicações para iOS][4]    


## <a name="intune-managed-browser-for-my-apps"></a>Browser gerido do Intune para as minhas aplicações

As minhas aplicações também está integrado com o Intune Managed Browser. O Intune Managed Browser para dispositivos iOS e Android ajuda a com mais segurança exibir e navegar páginas Web que pode conter informações da empresa, ajudando a proporcionar uma experiência de navegação na web mais segura.  

Pode acessar as minhas aplicações de ambos os Managed Browser home page e de seus indicadores, que significa que existem menos cliques necessários para atingir as suas aplicações.

Browser gerido do Intune está disponível na [da Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) e [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

![Browser gerido para as minhas aplicações][5]    


## <a name="tips-for-testing-the-user-experience"></a>Dicas para a experiência do usuário de teste

Se for um administrador do Azure e tem sessão iniciada portal do Azure, utilizando uma conta no diretório, automaticamente sessão iniciada no portal MyApps como sua conta atual. Esta vista apresenta todos os aplicativos que são atribuídos ao utilizador.

Para testar num *diferentes* utilizador da conta, efetue o seguinte:

1. No canto superior direito do portal do Azure ou o portal MyApps, selecione **terminar sessão**. 
2. Vá para o [MyApps portal](https://myapps.microsoft.com).
3. Na página de início de sessão, escreva o nome de utilizador e palavra-passe para a conta no seu diretório que pretende testar.


## <a name="starting-applications"></a>A partir de aplicações

Esta secção descreve vários tipos de aplicativos que podem aparecer no MyApps portal.

### <a name="office-365-applications"></a>Aplicativos do Office 365

Se sua organização está a utilizar aplicações do Office 365 e está licenciado para si, os aplicativos do Office 365 são apresentados no seu portal MyApps.

Ao selecionar um mosaico da aplicação para uma aplicação do Office 365, é redirecionado para a aplicação e entrar automaticamente.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Aplicativos Microsoft e de terceiros configurados com o SSO baseado em Federação

O administrador pode adicionar aplicações na secção do portal do Azure Active Directory com o modo SSO definido como **do Azure AD Single Sign-On**. Pode ver estas aplicações apenas se o administrador tenha explicitamente concedido que acesso às mesmas.

Quando seleciona um mosaico para uma aplicação, são redirecionadas e automaticamente conectado a ele.

### <a name="password-based-sso-without-identity-provisioning"></a>SSO baseado em palavra-passe sem o aprovisionamento de identidade

O administrador pode adicionar aplicações na secção do portal do Azure Active Directory com o modo SSO definido como **baseado em palavra-passe de início de sessão único**. Todos os utilizadores no diretório, podem ver todas as aplicações que tenham sido configuradas neste modo.

Na primeira vez que selecione um mosaico da aplicação, lhe for pedido para instalar o plug-in de SSO de palavra-passe para o Internet Explorer ou o Chrome. A instalação poderá exigir que reinicie o navegador da web. Quando voltar para o portal MyApps e selecione o mosaico de aplicação novamente, lhe for pedido um nome de utilizador e palavra-passe para a aplicação. Quando introduzir o nome de utilizador e palavra-passe, as credenciais são armazenadas em segurança e ligadas à sua conta no Azure AD.

Da próxima vez que selecione o mosaico de aplicação, tem automaticamente sessão iniciada para a aplicação.  

Não tem que introduzir as suas credenciais novamente e ou instalar o plug-in de SSO de palavra-passe.

Se as suas credenciais foram alterados no aplicativo de terceiros de destino, tem também de atualizar as suas credenciais que estão armazenadas no Azure AD. 

Para atualizar as suas credenciais, faça o seguinte:

1. Selecione o ícone no mosaico da aplicação.
2. Selecione **atualizar as credenciais** para reintroduzir outro nome de utilizador e palavra-passe para a aplicação.


### <a name="password-based-sso-with-identity-provisioning"></a>SSO baseado em palavra-passe com o provisionamento de identidade

O administrador pode adicionar aplicações na secção do portal do Azure Active Directory com o modo SSO definido como **baseado em palavra-passe de início de sessão único**, juntamente com o aprovisionamento de identidade.

Na primeira vez que selecione um mosaico da aplicação, lhe for pedido para instalar o plug-in de SSO de palavra-passe para o Internet Explorer ou o Chrome. A instalação poderá exigir que reinicie o navegador da web.  

Quando voltar para o portal MyApps e selecione novamente o mosaico da aplicação, tem automaticamente sessão iniciada para a aplicação.

Alguns aplicativos podem implicar a alterar a palavra-passe no primeiro início de sessão-in. Se as suas credenciais foram alterados no aplicativo de terceiros de destino, tem também de atualizar as credenciais que são armazenadas no Azure AD. 

Para atualizar as suas credenciais, faça o seguinte:

1. Selecione o ícone no mosaico da aplicação.
2. Selecione **atualizar as credenciais** para reintroduzir outro nome de utilizador e palavra-passe para a aplicação.


### <a name="application-with-existing-sso-solutions"></a>Aplicações com soluções existentes de SSO

Para configurar o SSO para um aplicativo, o portal do Azure fornece uma terceira opção chamada existente início de sessão único. Esta opção permite que o administrador para criar uma ligação para um aplicativo e colocá-lo no portal MyApps para utilizadores selecionados.

Por exemplo, se um aplicativo estiver configurado para autenticar os utilizadores através da utilização do AD FS 2.0, o administrador pode utilizar a opção existente início de sessão único para criar uma ligação ao mesmo no MyApps portal. Quando acessa a ligação, são autenticados através do AD FS 2.0 ou qualquer que seja SSO solução existente que o aplicativo fornece.


## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre a gestão de aplicações, veja [gestão de aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md).
 
- Para saber como integrar uma aplicação SaaS com o Azure AD, veja a [lista de tutoriais sobre como integrar aplicações SaaS](../saas-apps/tutorial-list.md).
 
- Para saber mais sobre como gerir aplicações com o Azure AD, veja a [introdução ao acesso de aplicação única de início de sessão e a gerir com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
 
- Para saber mais sobre o aprovisionamento de utilizadores, veja [automatizar o aprovisionamento de utilizador e a aplicações SaaS de desaprovisionamento](../manage-apps/user-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
