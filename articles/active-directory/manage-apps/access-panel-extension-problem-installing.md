---
title: Instalar extensão do navegador do painel de acesso a aplicações - Azure AD
description: Corrija erros comuns encontrados quando instala a extensão do navegador do painel de acesso.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/04/2018
ms.author: kenwith
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2046c24220079a604792d07f3ebc3f6ef11e9c8a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84761090"
---
# <a name="install-the-access-panel-browser-extension"></a>Instale a extensão do navegador do painel de acesso

O painel de acesso é um portal baseado na web. Se tiver uma conta de trabalho ou escola no Azure Ative Directory (Azure AD), pode utilizar o painel de acesso para visualizar e iniciar aplicações baseadas na nuvem a que um administrador da AD Azure lhe concedeu acesso. 

Se estiver a utilizar edições AD Azure, também pode utilizar capacidades de gestão de grupos de self-service e de gestão de aplicações através do painel de acesso. 

O painel de acesso é separado do portal Azure. Não requer que tenha uma assinatura Azure.

## <a name="web-browser-requirements"></a>Requisitos de Web browsers

No mínimo, o painel de acesso requer um navegador que suporte o JavaScript e tenha CSS ativado. Para ser assinado nas aplicações através de SSO baseado em palavra-passe no painel de acesso, tem de ter a extensão do painel de acesso instalada no seu navegador. A extensão é descarregada automaticamente quando seleciona uma aplicação configurada para SSO baseado em palavras-passe.

Para SSO baseado em palavras-passe, pode utilizar qualquer um dos seguintes navegadores:

- **Microsoft Edge**: na Edição de Aniversário do Windows 10 ou posteriormente. 
- **Chrome**: no Windows 7 ou mais tarde, e no MacOS X ou mais tarde.
- **Firefox 26.0 ou posterior**: no Windows XP SP2 ou mais tarde, e no Mac OS X 10.6 ou posterior.

## <a name="install-the-access-panel-browser-extension"></a>Instale a extensão do navegador do painel de acesso

Para instalar a extensão do navegador do painel de acesso, faça o seguinte:

1.  Num dos navegadores suportados, abra o [painel de acesso](https://myapps.microsoft.com)e, em seguida, inscreva-se como utilizador na sua conta AZure AD.

2.  Selecione uma aplicação SSO baseada em palavras-passe.

3.  Quando lhe for solicitado, **selecione Instalar Agora**.  
    Você está direcionado para o link de descarregamento para o seu navegador selecionado. 
    
4.  Selecione **Adicionar**.

5.  Se for solicitado, **ative** ou **permita** a extensão.

6.  Após a instalação estar concluída, reinicie o seu navegador.

7.  Inicie seduca no painel de acesso e verifique se pode iniciar as suas aplicações SSO baseadas em palavras-passe.

Também pode descarregar a extensão para Chrome e Microsoft Edge diretamente a partir dos seguintes sites:

- [Extensão do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Extensão Microsoft Edge](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Extensão firefox](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Use a extensão de inscrição segura das minhas apps
* Se estiver a utilizar um URL my apps `https://myapps.microsoft.com` diferente, configure o url padrão fazendo o seguinte:
   1. Embora *não* esteja inscrito na extensão, clique com o botão direito no ícone de extensão.
   2. No menu, selecione **My Apps URL**.
   3. Selecione o URL padrão.
   4. Selecione o ícone de extensão.
   5. Para iniciar sôms na extensão, **selecione Iniciar sôs para começar**.

* Para iniciar sôms diretamente numa aplicação a partir do navegador, faça o seguinte:
   1. Depois de instalar a extensão, inicie a sua singing **Para iniciar**o teste .
   2. Inscreva-se na aplicação com o URL de inscrição.  
       O URL de inscrição é normalmente o URL da aplicação que exibe o formulário de inscrição.
      A extensão deve alterar o estado e informá-lo de que uma senha está disponível.
   3. Para iniciar sindes, selecione o ícone de extensão.

* Para iniciar uma aplicação a partir da extensão, faça o seguinte:
   1. Depois de instalar a extensão, inicie a sua singing **Para iniciar**o teste .
   2. Selecione o ícone de extensão para abrir o seu menu.
   3. Procure uma aplicação disponível no portal My Apps.
   4. Na lista de resultados de pesquisa, selecione a aplicação.  
       As últimas três aplicações que utilizou são apresentadas na lista de atalhos **recentemente utilizados.**
       
* Para utilizar URLs internos da empresa enquanto remoto, faça o seguinte:
    1. [Configure pedido proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) no seu inquilino
    2. [Publique a aplicação](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) e URL através do Application Proxy
    3. Instale a extensão e inicie-a selecionando Iniciar singing para começar
    4. Agora pode navegar para o URL da empresa interna mesmo que remoto

> [!NOTE]
> As opções anteriores estão disponíveis apenas para Microsoft Edge, Chrome e Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Criar uma política de grupo para o Internet Explorer

Pode configurar uma política de grupo que lhe permita instalar remotamente a extensão do painel de acesso para o Internet Explorer nas máquinas dos seus utilizadores.

Antes de estabelecer uma política de grupo, certifique-se de que:

-   Criou os [Serviços de Domínio do Diretório Ativo](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e juntou as máquinas dos seus utilizadores ao seu domínio.

-   Para editar o Objeto de Política de Grupo (GPO), tem de ter *permissões de configuração de Edição.* Por padrão, esta permissão é concedida aos membros dos seguintes grupos de segurança: administradores de domínio, administradores de empresas e proprietários de criadores de políticas de grupo.

Para obter instruções passo a passo sobre a configuração da política de grupo e a sua implantação para os utilizadores, consulte [implementar a extensão do painel de acesso para o Internet Explorer utilizando a política de grupo](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Resolução de problemas da extensão do painel de acesso no Internet Explorer

Para aceder a uma ferramenta de diagnóstico e informações sobre a configuração da extensão para o Internet Explorer, consulte [Troubleshoot a extensão do painel de acesso para o Internet Explorer](manage-access-panel-browser-extension.md).

> [!NOTE]
> O Internet Explorer está em suporte limitado e já não recebe novas atualizações de software. O Microsoft Edge é o navegador recomendado.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Se os passos anteriores não resolverem a questão

Abra um bilhete de apoio com as seguintes informações, se estiver disponível:

-   ID de erro de correlação
-   UPN (endereço de e-mail do utilizador)
-   TenantID
-   Tipo de browser
-   Fuso horário e o tempo ou o prazo quando ocorreu o erro
-   Traços de violino

## <a name="next-steps"></a>Próximos passos
[What is application access and single sign-on with Azure Active Directory?](what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
