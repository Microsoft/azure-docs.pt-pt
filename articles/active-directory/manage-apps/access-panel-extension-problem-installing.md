---
title: Instalar a extensão do navegador do painel de acesso do aplicativo – Azure | Microsoft Docs
description: Corrija os erros comuns encontrados ao instalar a extensão do navegador do painel de acesso.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 580207bb10680e84cfda7d4b1874f2b460602973
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082169"
---
# <a name="install-the-access-panel-browser-extension"></a>Instalar a extensão do navegador do painel de acesso

O painel de acesso é um portal baseado na Web. Se você tiver uma conta corporativa ou de estudante no Azure Active Directory (Azure AD), poderá usar o painel de acesso para exibir e iniciar aplicativos baseados em nuvem aos quais um administrador do Azure AD concedeu acesso. 

Se você estiver usando as edições do Azure AD, também poderá usar o grupo de autoatendimento e os recursos de gerenciamento de aplicativo por meio do painel de acesso. 

O painel de acesso é separado da portal do Azure. Ele não exige que você tenha uma assinatura do Azure.

## <a name="web-browser-requirements"></a>Requisitos do navegador da Web

No mínimo, o painel de acesso requer um navegador com suporte para JavaScript e com CSS habilitado. Para entrar em aplicativos por meio de SSO baseado em senha no painel de acesso, você deve ter a extensão do painel de acesso instalada no navegador. A extensão é baixada automaticamente quando você seleciona um aplicativo configurado para SSO baseado em senha.

Para SSO baseado em senha, você pode usar qualquer um dos seguintes navegadores:

- **Microsoft Edge**: na edição de aniversário do Windows 10 ou posterior. 
- **Chrome**: no Windows 7 ou posterior e no MacOS X ou posterior.
- **Firefox 26,0 ou posterior**: no Windows XP SP2 ou posterior e no Mac os X 10,6 ou posterior.

## <a name="install-the-access-panel-browser-extension"></a>Instalar a extensão do navegador do painel de acesso

Para instalar a extensão do navegador do painel de acesso, faça o seguinte:

1.  Em um dos navegadores com suporte, abra o [painel de acesso](https://myapps.microsoft.com)e, em seguida, entre como um usuário em sua conta do Azure AD.

2.  Selecione um aplicativo SSO baseado em senha.

3.  Quando solicitado, selecione **instalar agora**.  
    Você será direcionado para o link de download do navegador selecionado. 
    
4.  Selecione **Adicionar**.

5.  Se for solicitado, **habilite** ou **permita** a extensão.

6.  Após a conclusão da instalação, reinicie o navegador.

7.  Entre no painel de acesso e verifique se você pode iniciar seus aplicativos de SSO baseados em senha.

Você também pode baixar a extensão para o Chrome e o Microsoft Edge diretamente dos seguintes sites:

- [Extensão do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Extensão do Microsoft Edge](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Extensão do Firefox](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Usar a extensão de entrada segura de meus aplicativos
* Se você estiver usando uma URL de meus aplicativos que não seja `https://myapps.microsoft.com`, configure a URL padrão fazendo o seguinte:
   1. Enquanto você *não* estiver conectado à extensão, clique com o botão direito do mouse no ícone de extensão.
   2. No menu, selecione **a URL de meus aplicativos**.
   3. Selecione a URL padrão.
   4. Selecione o ícone de extensão.
   5. Para entrar na extensão, selecione **entrar para começar**.

* Para entrar diretamente em um aplicativo do navegador, faça o seguinte:
   1. Depois de instalar a extensão, entre nela selecionando **entrar para**começar.
   2. Entre no aplicativo com a URL de logon.  
       A URL de logon geralmente é a URL do aplicativo que exibe o formulário de entrada.
      A extensão deve alterar o estado e informar que uma senha está disponível.
   3. Para entrar, selecione o ícone de extensão.

* Para iniciar um aplicativo a partir da extensão, faça o seguinte:
   1. Depois de instalar a extensão, entre nela selecionando **entrar para**começar.
   2. Selecione o ícone de extensão para abrir o menu.
   3. Procure um aplicativo que esteja disponível no portal meus aplicativos.
   4. Na lista de resultados da pesquisa, selecione o aplicativo.  
       Os três últimos aplicativos que você usou são exibidos na lista de atalhos **usados recentemente** .
       
* Para usar URLs internas da empresa enquanto estiver remoto, faça o seguinte:
    1. [Configurar o proxy de aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) em seu locatário
    2. [Publicar o aplicativo e a](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) URL por meio do proxy de aplicativo
    3. Instale a extensão e entre nela selecionando entrar para começar
    4. Agora você pode navegar para a URL da empresa interna mesmo que seja remoto

> [!NOTE]
> As opções anteriores estão disponíveis somente para o Microsoft Edge, o Chrome e o Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Configurar uma política de grupo para o Internet Explorer

Você pode configurar uma política de grupo que permite instalar remotamente a extensão do painel de acesso para o Internet Explorer nos computadores dos usuários.

Antes de configurar uma política de grupo, verifique se:

-   Você configurou [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e ingressou nos computadores dos usuários em seu domínio.

-   Para editar o objeto de Política de Grupo (GPO), você deve ter permissões de *Editar configurações* . Por padrão, essa permissão é concedida aos membros dos seguintes grupos de segurança: administradores de domínio, administradores de empresa e proprietários criadores de diretiva de grupo.

Para obter instruções passo a passo sobre como configurar a diretiva de grupo e implantá-la para os usuários, consulte [implantar a extensão do painel de acesso para o Internet Explorer usando a política de grupo](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Solucionar problemas da extensão do painel de acesso no Internet Explorer

Para obter acesso a uma ferramenta de diagnóstico e informações sobre como configurar a extensão para o Internet Explorer, consulte [solucionar problemas da extensão do painel de acesso para o Internet Explorer](manage-access-panel-browser-extension.md).

> [!NOTE]
> O Internet Explorer está em suporte limitado e não recebe mais novas atualizações de software. O Microsoft Edge é o navegador recomendado.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Se as etapas anteriores não resolverem o problema

Abra um tíquete de suporte com as seguintes informações, se estiver disponível:

-   ID do erro de correlação
-   UPN (endereço de email do usuário)
-   TenantID
-   Tipo de navegador
-   Fuso horário e hora ou período de tempo em que o erro ocorreu
-   Rastreamentos do Fiddler

## <a name="next-steps"></a>Passos seguintes
[What is application access and single sign-on with Azure Active Directory?](what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
