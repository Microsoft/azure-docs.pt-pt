---
title: Problema de sessão na app da galeria Azure AD configurada para SSO Microsoft Docs
description: Como resolver problemas com uma aplicação da Azure AD Gallery que está configurada para uma assinatura individual de senha.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9fd17d9e066be6a1abff5165436a09b8921184e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68381305"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problemas de entrada com uma aplicação de galeria Azure AD configurada para SSO

O Painel de Acesso é um portal baseado na web. Permite aos utilizadores que tenham o Azure Ative Directory (Azure AD) a trabalhar ou contas escolares para acederem a aplicações baseadas na nuvem para as suas permissões. Os utilizadores que possuem edições AD Azure também podem usar capacidades de autosserviço e gestão de aplicações através do Painel de Acesso.

O Painel de Acesso está separado do portal Azure. Os utilizadores não precisam de uma subscrição Azure para utilizar o Painel de Acesso.

Para utilizar um único sinal de acesso (SSO) baseado em palavra-passe no Painel de Acesso, a extensão do Painel de Acesso deve ser instalada no seu navegador. A extensão descarrega automaticamente quando seleciona uma aplicação configurada para SSO baseado em palavras-passe.

## <a name="browser-requirements-for-access-panel"></a>Requisitos do navegador para Painel de Acesso

O Painel de Acesso requer um navegador que suporte o JavaScript e tenha CSS ativado.

Os seguintes navegadores suportam SSO baseado em palavra-passe:

- Internet Explorer 8, 9, 10 e 11 no Windows 7 ou mais tarde

- Chrome no Windows 7 ou mais tarde ou no MacOS X ou mais tarde

- Firefox 26.0 ou mais tarde no Windows XP SP2 ou mais tarde ou no Mac OS X 10.6 ou posterior

>[!NOTE]
>A extensão SSO baseada em palavras-passe fica disponível para o Microsoft Edge no Windows 10 quando o suporte para extensões de navegador foi adicionado ao Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Instalar a extensão do navegador do Painel de Acesso

Siga estes passos.

1. Open [Access Panel](https://myapps.microsoft.com) em um navegador suportado, e inscreva-se como um utilizador em Azure AD.

2. Selecione uma aplicação ativada por palavra-passe SSO no Painel de Acesso.

3. Quando for solicitado, selecione **Instalar Agora**.

4. Será direcionado para um link de descarregamento com base no seu navegador. Selecione **Adicionar** para instalar a extensão do navegador.

5. Se for solicitado, selecione **Ativar** ou **permitir**.

6. Após a instalação, reinicie o seu navegador.

7.  Inicie sessão no Painel de Acesso e veja se consegue iniciar as suas aplicações ativadas por palavra-passe SSO.

Também pode descarregar diretamente as extensões para Chrome e Firefox através destas links:

-   [Extensão do Painel de Acesso Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do Painel de Acesso firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Criar uma política de grupo para o Internet Explorer

Pode configurar uma política de grupo que lhe permite instalar remotamente a extensão do Painel de Acesso para o Internet Explorer nas máquinas dos seus utilizadores.

Estes são os pré-requisitos:

-   [Os Serviços](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) de Domínio de Diretório Ativo devem ser configurados e as máquinas dos seus utilizadores devem ser unidas ao seu domínio.

-   Tem permissão de "Editar definições" para editar o Objeto de Política de Grupo (GPO). Por padrão, os membros dos seguintes grupos de segurança têm esta permissão: Administradores de Domínio, Administradores de Empresas e Proprietários de Criadores de Políticas de Grupo. [Saiba mais](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Para configurar a política do grupo e implementá-la para os utilizadores, consulte [como implementar a extensão do Painel de Acesso para o Internet Explorer utilizando](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)a política do grupo .

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Painel de acesso de resolução de problemas no Internet Explorer

Para aceder a uma ferramenta de diagnóstico e instruções para configurar a extensão, consulte [Troubleshoot a extensão do Painel de Acesso para o Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Configure a palavra-passe SSO para uma aplicação de galeria Azure AD

Para configurar uma aplicação da galeria Azure AD, precisa de fazer estas coisas:

-   Adicione a app da galeria Azure AD
-   [Configure a aplicação para um único sign-on de senha](#configure-the-app-for-password-sso)
-   [Atribuir utilizadores à aplicação](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Adicione a app da galeria Azure AD

Siga estes passos.

1. Abra o [portal Azure](https://portal.azure.com) e inscreva-se como administrador global ou coadministrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão Azure AD.

3. Digite o **Diretório Ativo Do** tipo Azure na caixa de pesquisa de filtros e, em seguida, selecione **Azure Ative Directory**.

4. Selecione **Aplicações Empresariais** a partir do painel de navegação Azure AD.

5. **Selecione Adicionar** no canto superior direito do painel **de aplicações da empresa.**

6. No Add da secção **galeria,** digite o nome da app na caixa **de nomes Enter.**

7. Selecione a aplicação que pretende configurar para SSO.

8. *Opcional:* Antes de adicionar a aplicação, pode alterar o seu nome na caixa **Nome.**

9. Clique em **Adicionar** para adicionar a aplicação.

   Depois de um breve atraso, poderá ver o painel de configuração da aplicação.

### <a name="configure-the-app-for-password-sso"></a>Configure a aplicação para siso de senha

Siga estes passos.

1. Abra o [portal Azure](https://portal.azure.com/) e inscreva-se como administrador global ou coadministrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão Azure AD.

3. Digite o **Diretório Ativo Do** tipo Azure na caixa de pesquisa de filtros e, em seguida, selecione **Azure Ative Directory**.

4. Selecione **Aplicações Empresariais** no painel de navegação Azure AD.

5. Selecione **Todas as Aplicações** para ver uma lista das suas apps.

   > [!NOTE]
   > Se não vir a aplicação que deseja, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações**. Detete a opção **Show** para "Todas as Aplicações".

6. Selecione a aplicação que pretende configurar para SSO.

7. Depois de a aplicação carregar, selecione **um único sinal no** painel do lado esquerdo da aplicação.

8. Selecione o modo **de acesso baseado em palavra-passe.**

9. Atribuir utilizadores à aplicação.

10. Também pode fornecer credenciais para os utilizadores. (Caso contrário, os utilizadores serão solicitados a introduzir credenciais no arranque da aplicação.) Para tal, selecione as linhas dos utilizadores. Em seguida, selecione **'Credenciais de actualização'** e introduza os seus nomes de utilizador e palavras-passe.

### <a name="assign-users-to-the-app"></a>Atribuir utilizadores à aplicação

Para atribuir os utilizadores a uma aplicação diretamente, siga estes passos:

1. Abra o [portal Azure](https://portal.azure.com/) e inscreva-se como administrador global.

2. Selecione **todos os serviços** na dor de navegação do lado esquerdo para abrir a extensão Azure AD.

3. Digite o **Diretório Ativo Do** tipo Azure na caixa de pesquisa de filtros e, em seguida, selecione **Azure Ative Directory**.

4. Selecione **Aplicações Empresariais** no painel de navegação Azure AD.

5. Selecione **Todas as Aplicações** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que deseja, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações**. Detete a opção **Show** para "Todas as Aplicações".

6. A partir da lista, selecione a aplicação a que pretende atribuir um utilizador.

7. Depois de a aplicação carregar, selecione **Utilizadores e Grupos** do painel de navegação da aplicação no lado esquerdo.

8. Selecione **Adicionar** no topo da lista de **Utilizadores e Grupos** para abrir o painel de atribuição de **adicionar.**

9. Selecione **Utilizadores e grupos** no painel **adicionar atribuição.**

10. Na caixa de endereços De pesquisa ou endereço de **e-mail,** escreva o nome completo ou o endereço de e-mail do utilizador que pretende atribuir.

11. Paire sobre o utilizador da lista. Selecione a caixa de verificação ao lado da foto ou logotipo do perfil do utilizador para adicionar esse utilizador à lista **Selecionada.**

12. *Opcional:* Para adicionar outro utilizador, digite outro nome ou endereço de e-mail na caixa de **endereços de pesquisa ou e-mail** e, em seguida, selecione a caixa de verificação para adicionar esse utilizador à lista **Selecionada.**

13. Quando terminar de selecionar os utilizadores, clique em **Select** para adicioná-los à lista de utilizadores e grupos que estão atribuídos à aplicação.

14. *Opcional:* Clique em **Selecionar a Função** no painel **de atribuição de adicionar** para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Selecione **Atribuir** para atribuir a app aos utilizadores selecionados.

    Após um breve atraso, os utilizadores poderão aceder a essas aplicações a partir do Painel de Acesso.

## <a name="request-support"></a>Solicitação de apoio 
Se receber uma mensagem de erro quando configurar o SSO e atribuir utilizadores, abra um bilhete de suporte. Incluir o máximo possível das seguintes informações:

-   Id de erro de correlação
-   UPN (endereço de e-mail do utilizador)
-   TenantID
-   Tipo de browser
-   Fuso horário e prazo/hora quando ocorreu o erro
-   Traços de violinista

## <a name="next-steps"></a>Passos seguintes
[Forneça um único sinal às suas apps com Procuração de Aplicação](application-proxy-configure-single-sign-on-with-kcd.md)
