---
title: Problema de inscrição na app da galeria AD AD configurada para SSO Microsoft Docs
description: Como resolver problemas com uma aplicação da AZure AD Gallery que está configurada para o sign-on único da palavra-passe.
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
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6eebde3a7c6163b7faf92be193fe442cd5b74d2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84759118"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problemas de inscrição com uma app de galeria AD AZure configurada para SSO

O Painel de Acesso é um portal baseado na web. Permite que os utilizadores que tenham O Diretório Ativo (Azure AD) trabalhem ou contas escolares acedam a aplicações baseadas na nuvem para as que têm permissões. Os utilizadores que tenham edições AD Azure também podem usar o grupo de self-service e as capacidades de gestão de aplicações através do Access Panel.

O Painel de Acesso é separado do portal Azure. Os utilizadores não precisam de uma subscrição Azure para utilizar o Painel de Acesso.

Para utilizar o sign-on único baseado em palavra-passe (SSO) no Painel de Acesso, a extensão do Painel de Acesso deve ser instalada no seu navegador. A extensão descarrega automaticamente quando seleciona uma aplicação configurada para SSO baseado em palavras-passe.

## <a name="browser-requirements-for-access-panel"></a>Requisitos do navegador para o Painel de Acesso

O Painel de Acesso requer um navegador que suporte o JavaScript e tenha CSS ativado.

Os seguintes navegadores suportam SSO baseado em palavras-passe:

- Internet Explorer 8, 9, 10 e 11 no Windows 7 ou mais tarde

- Chrome no Windows 7 ou mais tarde ou no MacOS X ou mais tarde

- Firefox 26.0 ou mais tarde no Windows XP SP2 ou mais tarde ou no Mac OS X 10.6 ou mais tarde

>[!NOTE]
>A extensão SSO baseada em palavra-passe fica disponível para o Microsoft Edge no Windows 10 quando o suporte para extensões de navegador foi adicionado ao Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Instale a extensão do navegador do painel de acesso

Siga estes passos.

1. Abrir [o Painel de Acesso](https://myapps.microsoft.com) num browser suportado e iniciar sação como utilizador no Azure AD.

2. Selecione uma aplicação ativada por palavra-passe no Painel de Acesso.

3. Quando lhe for solicitado, **selecione Instalar Agora**.

4. Você será direcionado para um link de descarregamento baseado no seu navegador. **Selecione Adicionar** para instalar a extensão do navegador.

5. Se for solicitado, selecione **Ativar** ou **Permitir**.

6. Após a instalação, reinicie o seu navegador.

7.  Inicie seduca no Painel de Acesso e veja se pode iniciar as suas aplicações ativadas por password SSO.

Também pode baixar diretamente as extensões para Chrome e Firefox através destes links:

-   [Extensão do Painel de Acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do Painel de Acesso Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Criar uma política de grupo para o Internet Explorer

Pode configurar uma política de grupo que lhe permita instalar remotamente a extensão do Painel de Acesso para o Internet Explorer nas máquinas dos seus utilizadores.

Estes são os pré-requisitos:

-   [Os Serviços de Domínio do Diretório Ativo](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) devem ser configurados e as máquinas dos seus utilizadores devem ser unidas ao seu domínio.

-   Tem a permissão de "Editar definições" para editar o Objeto de Política de Grupo (GPO). Por padrão, os membros dos seguintes grupos de segurança têm esta permissão: Administradores de Domínio, Administradores empresariais e Proprietários de Criadores de Políticas de Grupo. [Saiba mais](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Para configurar a política de grupo e implantá-la para os utilizadores, consulte [como implementar a extensão do Painel de Acesso para o Internet Explorer utilizando a política de grupo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Painel de acesso de resolução de problemas no Internet Explorer

Para aceder a uma ferramenta de diagnóstico e instruções para configurar a extensão, consulte [a extensão do Painel de Acesso para o Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Configure palavra-passe SSO para uma aplicação de galeria AZure AD

Para configurar uma aplicação da galeria AD AZure, você precisa fazer estas coisas:

-   Adicione a aplicação da galeria AD AZure
-   [Configurar a aplicação para o sign-on único da palavra-passe](#configure-the-app-for-password-sso)
-   [Atribuir utilizadores à aplicação](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Adicione a aplicação da galeria AD AZure

Siga estes passos.

1. Abra o [portal Azure](https://portal.azure.com) e inscreva-se como administrador global ou coadministrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão AD AZure.

3. Digite **O Diretório Ativo Azure** na caixa de pesquisa de filtros e, em seguida, selecione **O Diretório Ativo Azure**.

4. Selecione **Aplicações Empresariais** a partir do painel de navegação AD Azure.

5. **Selecione Adicione** no canto superior direito do painel **de aplicações** da empresa.

6. Na secção Adicionar a partir da secção **de galeria,** digite o nome da aplicação na caixa **de nomes Enter.**

7. Selecione a aplicação que pretende configurar para SSO.

8. *Opcional:* Antes de adicionar a aplicação, pode alterar o seu nome na caixa **Nome.**

9. Clique em **Adicionar** para adicionar a aplicação.

   Após um breve atraso, poderá ver o painel de configuração da aplicação.

### <a name="configure-the-app-for-password-sso"></a>Configure a aplicação para palavra-passe SSO

Siga estes passos.

1. Abra o [portal Azure](https://portal.azure.com/) e inscreva-se como administrador global ou coadministrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão AD AZure.

3. Digite **O Diretório Ativo Azure** na caixa de pesquisa de filtros e, em seguida, selecione **O Diretório Ativo Azure**.

4. Selecione **Aplicações Empresariais** no painel de navegação AD Azure.

5. Selecione **Todas as Aplicações** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o controlo **do Filtro** no topo da Lista de **Todas as Aplicações.** Desa estada a opção **"Todas** as Aplicações".

6. Selecione a aplicação que pretende configurar para SSO.

7. Depois de carregar a aplicação, selecione **Single sign-on** no painel do lado esquerdo da aplicação.

8. Selecione o modo **de entrada baseada em palavra-passe.**

9. Atribua os utilizadores à aplicação.

10. Também pode fornecer credenciais para os utilizadores. (Caso contrário, os utilizadores serão solicitados a introduzir credenciais no arranque de aplicações.) Para tal, selecione as linhas dos utilizadores. Em seguida, selecione **'Update Credentials'** e insira os seus nomes de utilizador e palavras-passe.

### <a name="assign-users-to-the-app"></a>Atribuir utilizadores à aplicação

Para atribuir os utilizadores a uma aplicação diretamente, siga estes passos:

1. Abra o [portal Azure](https://portal.azure.com/) e inscreva-se como administrador global.

2. Selecione **Todos os serviços** na dor de navegação do lado esquerdo para abrir a extensão AD AZure.

3. Digite **O Diretório Ativo Azure** na caixa de pesquisa de filtros e, em seguida, selecione **O Diretório Ativo Azure**.

4. Selecione **Aplicações Empresariais** no painel de navegação AD Azure.

5. Selecione **Todas as Aplicações** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o controlo **do Filtro** no topo da Lista de **Todas as Aplicações.** Desa estada a opção **"Todas** as Aplicações".

6. A partir da lista, selecione a aplicação a que pretende atribuir um utilizador.

7. Depois das cargas da aplicação, selecione **Utilizadores e Grupos** a partir do painel de navegação da aplicação no lado esquerdo.

8. **Selecione Adicionar** no topo da lista de **Utilizadores e Grupos** para abrir o painel **de atribuição de adicionar.**

9. Selecione **Utilizadores e grupos** no painel **de atribuição de** adicionar.

10. Na **caixa de endereços de nome ou e-mail,** digite o nome completo ou o endereço de e-mail do utilizador que pretende atribuir.

11. Paire sobre o utilizador na lista. Selecione a caixa de verificação ao lado da foto ou logotipo do perfil do utilizador para adicionar esse utilizador à lista **Selecionada.**

12. *Opcional:* Para adicionar outro utilizador, digite outro nome ou endereço de e-mail na caixa **de endereços de nome ou e-mail** e, em seguida, selecione a caixa de verificação para adicionar esse utilizador à lista **Selecionada.**

13. Quando terminar de selecionar utilizadores, clique em **Select** para adicioná-los à lista de utilizadores e grupos que estão atribuídos à aplicação.

14. *Opcional:* Clique em **Selecionar Função** no painel **de atribuição de adicionar** para selecionar uma função a atribuir aos utilizadores que selecionou.

15. Selecione **Atribuir** a aplicação aos utilizadores selecionados.

    Após um breve atraso, os utilizadores poderão aceder a essas aplicações a partir do Access Panel.

## <a name="request-support"></a>Solicitar apoio 
Se receber uma mensagem de erro quando configurar sSO e atribuir utilizadores, abra um bilhete de apoio. Incluir o máximo possível de informações:

-   ID de erro de correlação
-   UPN (endereço de e-mail do utilizador)
-   TenantID
-   Tipo de browser
-   Fuso horário e tempo/período quando ocorreu o erro
-   Traços de violino

## <a name="next-steps"></a>Próximos passos
[Fornecer um único s-on às suas apps com Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)
