---
title: Instalar a extensão de browser de painel de acesso de aplicações - Azure | Documentos da Microsoft
description: Corrigi erros comuns encontrados ao instalar a extensão de browser do painel de acesso.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/4/2018
ms.author: celested
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 216568ac43c8e1b04c91d9a8f611a0ceb2e430af
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674009"
---
# <a name="install-the-access-panel-browser-extension"></a>Instalar a extensão de browser do painel de acesso

O painel de acesso é um portal baseado na web. Se tiver uma conta escola ou no Azure Active Directory (Azure AD), pode utilizar o painel de acesso para ver e iniciar os aplicativos baseados na nuvem que o administrador do Azure AD lhe concedeu que acesso a. 

Se estiver a utilizar edições do Azure AD, pode também utilizar grupos self-service e capacidades de gestão de aplicações através do painel de acesso. 

O painel de acesso está separado do portal do Azure. Não requer que tenha uma subscrição do Azure.

## <a name="web-browser-requirements"></a>Requisitos de navegador da Web

No mínimo, o painel de acesso requer um browser que suporte a JavaScript e CSS ativou o. Para entrar para aplicações através do SSO de palavra-passe de mensagens em fila no painel de acesso, tem de ter a extensão do painel de acesso instalada no seu browser. A extensão é transferida automaticamente ao selecionar uma aplicação que está configurada para SSO baseado em palavra-passe.

Para SSO de palavra-passe, pode utilizar qualquer um dos seguintes browsers:

- **Microsoft Edge**: Windows 10 Anniversary Edition ou posterior. 
- **Chrome**: no Windows 7 ou posterior e no MacOS X ou posterior.
- **Firefox 26.0 ou posterior**: no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior.

## <a name="install-the-access-panel-browser-extension"></a>Instalar a extensão de browser do painel de acesso

Para instalar a extensão de browser do painel de acesso, efetue o seguinte:

1.  Em um dos browsers suportados, abra a [painel de acesso](https://myapps.microsoft.com)e, em seguida, inicie sessão como um utilizador na sua conta do Azure AD.

2.  Selecione um aplicativo de SSO baseado em palavra-passe.

3.  Quando lhe for pedido, selecione **instalar agora**.  
    Será direcionado para a ligação de transferência para o seu navegador selecionado. 
    
4.  Selecione **Adicionar**.

5.  Se for a pedido, seja **habilitar** ou **permitir** a extensão.

6.  Após a instalação estiver concluída, reinicie o navegador.

7.  Entrar para o painel de acesso e a verificação para ver se é possível iniciar seus aplicativos de SSO baseado em palavra-passe.

Também pode transferir a extensão para o Chrome e o Microsoft Edge diretamente a partir de sites a seguir:

- [Extensão do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Extensão do Microsoft Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Utilize a extensão de início de sessão de seguro das minhas aplicações
* Se estiver a utilizar um URL de aplicações My diferente de `https://myapps.microsoft.com`, configurar o URL predefinido ao fazer o seguinte:
   1. Enquanto estiver *não* entrar para a extensão, com o botão direito no ícone de extensão.
   2. No menu, selecione **URL de aplicações My**.
   3. Selecione o URL predefinido.
   4. Selecione o ícone de extensão.
   5. Para iniciar sessão para a extensão, selecione **iniciar sessão começar a utilizar**.

* Para iniciar sessão diretamente a uma aplicação a partir do browser, faça o seguinte:
   1. Depois de instalar a extensão, iniciar sessão na mesma selecionando **iniciar sessão começar a utilizar**.
   2. Inicie sessão na aplicação com o URL de início de sessão.  
       O URL de início de sessão é, normalmente, o URL da aplicação que exibe o formulário de início de sessão.
      A extensão deve alterar o estado e permitem-lhe saber que uma palavra-passe está disponível.
   3. Para iniciar sessão, selecione o ícone de extensão.

* Para iniciar uma aplicação a partir da extensão, faça o seguinte:
   1. Depois de instalar a extensão, iniciar sessão na mesma selecionando **iniciar sessão começar a utilizar**.
   2. Selecione o ícone de extensão para abrir respetivo menu.
   3. Procurar uma aplicação que está disponível no portal minhas aplicações.
   4. Na lista de resultados da pesquisa, selecione a aplicação.  
       As últimas três aplicações que já utilizou são apresentadas no **utilizados recentemente** lista de atalho.
       
* Para utilizar URLs internos da empresa ao remoto, faça o seguinte:
    1. [Configurar o Proxy de aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) no seu inquilino
    2. [Publicar a aplicação](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) e URL através do Proxy de aplicações
    3. Instalar a extensão e iniciar sessão na mesma ao selecionar o início de sessão para começar a utilizar
    4. Agora, pode navegar para o URL interna da empresa, mesmo durante o remoto

> [!NOTE]
> As opções anteriores estão disponíveis apenas para o Microsoft Edge, Chrome e Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Configurar uma política de grupo para o Internet Explorer

Pode configurar uma política de grupo permite-lhe instalar remotamente a extensão do painel de acesso para o Internet Explorer nos computadores dos seus utilizadores.

Antes de configurar uma política de grupo, certifique-se de que:

-   Configurou [serviços de domínio do Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), e associar máquinas dos seus utilizadores ao seu domínio.

-   Para editar o objeto de política de grupo (GPO), tem de ter *editar as definições de* permissões. Por predefinição, esta permissão é concedida aos membros dos grupos de segurança seguintes: os administradores do domínio, administradores da empresa e proprietários de criador de políticas de grupo.

Para instruções passo a passo sobre como configurar a política de grupo e implementar a política aos utilizadores, consulte [implementar a extensão do painel de acesso para o Internet Explorer utilizando a política de grupo](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Resolver problemas relacionados com a extensão do painel de acesso no Internet Explorer

Para ter acesso a uma ferramenta de diagnóstico e informações sobre como configurar a extensão para o Internet Explorer, consulte [resolver problemas relacionados com a extensão do painel de acesso para o Internet Explorer](manage-access-panel-browser-extension.md).

> [!NOTE]
> Internet Explorer está no suporte limitado e deixará de receber atualizações de software novo. Microsoft Edge é o navegador recomendado.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Se os passos anteriores não resolverem o problema

Abra um pedido de suporte com as informações seguintes, se estiver disponível:

-   ID de correlação de erro
-   UPN (endereço de e-mail do utilizador)
-   TenantID
-   Tipo de navegador
-   Fuso horário e a hora ou intervalo de tempo quando ocorreu o erro
-   Rastreios do fiddler

## <a name="next-steps"></a>Passos Seguintes
[What is application access and single sign-on with Azure Active Directory?](what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
