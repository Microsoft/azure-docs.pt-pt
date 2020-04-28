---
title: Instalar extensão do navegador do painel de acesso a aplicações - Azure AD
description: Corrija erros comuns encontrados quando instala a extensão do navegador do painel de acesso.
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
ms.openlocfilehash: 771ba79f067cbff1ab8bbfece64f4028b4ca50b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74275862"
---
# <a name="install-the-access-panel-browser-extension"></a>Instale a extensão do navegador do painel de acesso

O painel de acesso é um portal baseado na web. Se tiver uma conta de trabalho ou escola no Azure Ative Directory (Azure AD), pode utilizar o painel de acesso para visualizar e iniciar aplicações baseadas na nuvem a que um administrador da AD Azure lhe concedeu acesso. 

Se estiver a usar edições AD Azure, também pode utilizar capacidades de autosserviço e gestão de aplicações através do painel de acesso. 

O painel de acesso é separado do portal Azure. Não é necessário ter uma subscrição Azure.

## <a name="web-browser-requirements"></a>Requisitos de Web browsers

No mínimo, o painel de acesso requer um navegador que suporta o JavaScript e tem CSS ativado. Para ser inscrito nas aplicações através do SSO baseado em palavras-passe no painel de acesso, tem de ter a extensão do painel de acesso instalada no seu navegador. A extensão é descarregada automaticamente quando seleciona uma aplicação configurada para SSO baseada em palavras-passe.

Para o SSO baseado em palavras-passe, pode utilizar qualquer um dos seguintes navegadores:

- **Microsoft Edge**: na Edição de Aniversário do Windows 10 ou mais tarde. 
- **Chrome**: no Windows 7 ou posterior, e no MacOS X ou posteriormente.
- **Firefox 26.0 ou posterior**: no Windows XP SP2 ou posterior, e no Mac OS X 10.6 ou posterior.

## <a name="install-the-access-panel-browser-extension"></a>Instale a extensão do navegador do painel de acesso

Para instalar a extensão do navegador do painel de acesso, faça o seguinte:

1.  Num dos navegadores suportados, abra o painel de [acesso](https://myapps.microsoft.com)e depois inscreva-se como utilizador na sua conta Azure AD.

2.  Selecione uma aplicação SSO baseada em palavra-passe.

3.  Quando for solicitado, selecione **Instalar Agora**.  
    Você está direcionado para o link de descarregamento para o seu navegador selecionado. 
    
4.  Selecione **Adicionar**.

5.  Se for solicitado, **ou ativar** ou **permitir** a extensão.

6.  Depois de concluída a instalação, reinicie o seu navegador.

7.  Inicie sessão no painel de acesso e verifique se pode iniciar as suas aplicações SSO baseadas em palavras-passe.

Também pode descarregar a extensão para Chrome e Microsoft Edge diretamente a partir de seguintes sites:

- [Extensão do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Extensão do Microsoft Edge](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Extensão firefox](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Use a extensão de inscrição segura das minhas aplicações
* Se estiver a utilizar um `https://myapps.microsoft.com`URL my apps diferente , configure o seu URL predefinido fazendo o seguinte:
   1. Enquanto *não* estiver inscrito na extensão, clique no ícone de extensão.
   2. No menu, selecione **My Apps URL**.
   3. Selecione o URL padrão.
   4. Selecione o ícone de extensão.
   5. Para iniciar o seu início, selecione Iniciar sessão **.**

* Para iniciar sessão diretamente numa aplicação do navegador, faça o seguinte:
   1. Depois de instalar a extensão, inicie o seu insto selecionando **o Signin para começar**.
   2. Inscreva-se na aplicação com o URL de inscrição.  
       O URL de inscrição é geralmente o URL da aplicação que exibe o formulário de inscrição.
      A extensão deve alterar o estado e informá-lo de que está disponível uma senha.
   3. Para iniciar sessão, selecione o ícone de extensão.

* Para iniciar uma aplicação a partir da extensão, faça o seguinte:
   1. Depois de instalar a extensão, inicie o seu insto selecionando **o Signin para começar**.
   2. Selecione o ícone de extensão para abrir o menu.
   3. Procure uma aplicação disponível no portal My Apps.
   4. Na lista de resultados da pesquisa, selecione a aplicação.  
       As três últimas aplicações que usou estão apresentadas na lista de **atalhos recentemente utilizados.**
       
* Para utilizar URLs internos da empresa durante o controlo remoto, faça o seguinte:
    1. [Configure procuração](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) de aplicação no seu inquilino
    2. [Publique a aplicação](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) e o URL através do Proxy de Aplicação
    3. Instale a extensão e inscreva-se nela selecionando O Signin para começar
    4. Agora pode navegar para o URL da empresa interna mesmo quando remoto

> [!NOTE]
> As opções anteriores estão disponíveis apenas para Microsoft Edge, Chrome e Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Criar uma política de grupo para o Internet Explorer

Pode configurar uma política de grupo que lhe permite instalar remotamente a extensão do painel de acesso para o Internet Explorer nas máquinas dos seus utilizadores.

Antes de criar uma política de grupo, certifique-se de que:

-   Criou serviços de domínio de [diretório ativo](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e juntou-se às máquinas dos seus utilizadores para o seu domínio.

-   Para editar o Objeto de Política de Grupo (GPO), tem de ter permissões de *definições de Edição.* Por predefinição, esta permissão é concedida aos membros dos seguintes grupos de segurança: administradores de domínio, administradores de empresas e proprietários de políticas de grupo.

Para instruções passo a passo sobre configurar a política do grupo e implementá-la para os utilizadores, consulte [implementar a extensão do painel de acesso para o Internet Explorer utilizando](deploy-access-panel-browser-extension.md)a política do grupo .

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Resolução de problemas na extensão do painel de acesso no Internet Explorer

Para acesso a uma ferramenta de diagnóstico e informações sobre a configuração da extensão para o Internet Explorer, consulte [Troubleshoot a extensão do painel](manage-access-panel-browser-extension.md)de acesso para o Internet Explorer .

> [!NOTE]
> O Internet Explorer está em suporte limitado e já não recebe novas atualizações de software. O Microsoft Edge é o navegador recomendado.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Se os passos anteriores não resolverem a questão

Abra um bilhete de apoio com as seguintes informações, se estiver disponível:

-   Id de erro de correlação
-   UPN (endereço de e-mail do utilizador)
-   TenantID
-   Tipo de browser
-   Fuso horário e o prazo ou prazo quando ocorreu o erro
-   Traços de violinista

## <a name="next-steps"></a>Passos seguintes
[What is application access and single sign-on with Azure Active Directory?](what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
