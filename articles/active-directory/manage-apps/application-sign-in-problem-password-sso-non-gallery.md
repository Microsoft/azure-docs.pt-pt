---
title: Sign-on único baseado em palavra-passe (SSO) no Painel de Acesso / Microsoft Docs
description: Discute áreas problemáticas que fornecem orientações para resolver problemas relacionados com a inscrição nas aplicações da AZure AD Gallery configuradas para o sign-on único da palavra-passe.
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
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4a645038dfb49e7c79995b90b159bdc07bea4f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763776"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problemas de inscrição numa aplicação da Galeria AD Azure configurada para o sign-on único da palavra-passe

O Painel de Acesso é um portal baseado na Web que permite a um utilizador que tenha uma conta de trabalho ou escola no Azure Ative Directory (Azure AD) visualizar e lançar aplicações baseadas na nuvem a que o administrador da AD Azure lhes concedeu acesso. Um utilizador que tenha edições AD Azure também pode usar o grupo de self-service e as capacidades de gestão de aplicações através do Painel de Acesso. O Painel de Acesso está separado do portal Azure e não requer que os utilizadores tenham uma subscrição do Azure.

Para utilizar o único sinal de sso baseado em palavra-passe (SSO) no Painel de Acesso, a extensão do Painel de Acesso deve ser instalada no navegador do utilizador. Esta extensão é descarregada automaticamente quando um utilizador seleciona uma aplicação configurada para SSO baseado em palavra-passe.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Satisfazendo os requisitos do navegador para o Painel de Acesso

O Painel de Acesso requer um navegador que suporte o JavaScript e tenha CSS ativado. Para utilizar o único sinal de sso baseado em palavra-passe (SSO) no Painel de Acesso, a extensão do Painel de Acesso deve ser instalada no navegador do utilizador. Esta extensão é descarregada automaticamente quando um utilizador seleciona uma aplicação configurada para SSO baseado em palavra-passe.

Para sSO baseado em palavra-passe, os navegadores do utilizador final podem ser:

-   Internet Explorer 8, 9, 10, 11 - no Windows 7 ou mais tarde

-   Chrome - no Windows 7 ou mais tarde, e no MacOS X ou mais tarde

-   Firefox 26.0 ou mais tarde -- no Windows XP SP2 ou mais tarde, e no Mac OS X 10.6 ou mais tarde

>[!NOTE]
>A extensão SSO baseada em palavra-passe fica disponível para o Microsoft Edge no Windows 10 quando as extensões do navegador ficarem suportadas pelo Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do Navegador do Painel de Acesso

Para instalar a extensão do Navegador do Painel de Acesso, siga os passos abaixo:

1.  Abra o [Painel de Acesso](https://myapps.microsoft.com) num dos navegadores suportados e inscreva-se como **utilizador** no seu AD Azure.

2.  clique numa **aplicação password-SSO** no Painel de Acesso.

3.  Na instrução pedindo para instalar o software, **selecione Instalar Agora**.

4.  Com base no seu navegador, você será direcionado para o link de descarregamento. **Adicione** a extensão ao seu navegador.

5.  Se o seu navegador perguntar, selecione para **Ativar** ou **Permitir** a extensão.

6.  Uma vez instalado, **reinicie** a sessão do seu navegador.

7.  Inicie seduca no Painel de Acesso e veja se consegue **lançar** as suas aplicações password-SSO

Também pode descarregar a extensão para Chrome e Firefox a partir dos links diretos abaixo:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Criação de uma política de grupo para o Internet Explorer

Pode configurar uma política de grupo que lhe permita instalar remotamente a extensão do Painel de Acesso para o Internet Explorer nas máquinas dos seus utilizadores.

Os pré-requisitos incluem:

-   Criou os [Serviços de Domínio do Diretório Ativo](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e juntou as máquinas dos seus utilizadores ao seu domínio.

-   Tem de ter a permissão de "Editar definições" para editar o Objeto de Política de Grupo (GPO). Por padrão, os membros dos seguintes grupos de segurança têm esta permissão: Administradores de Domínio, Administradores empresariais e Proprietários de Criadores de Políticas de Grupo. [Saiba mais](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Siga o tutorial [Como implementar a extensão do painel de acesso para o Internet Explorer utilizando a Política de Grupo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) para instruções passo a passo sobre como configurar a política de grupo e implantá-la para os utilizadores.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Resolução de problemas do Painel de Acesso no Internet Explorer

Siga a [resolução de problemas da extensão do painel de acesso para](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) o Internet Explorer para aceder a uma ferramenta de diagnóstico e instruções passo a passo sobre a configuração da extensão para IE.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o sign-on único da palavra-passe para uma aplicação não-galeria

Para configurar uma aplicação da galeria AD Azure, você precisa:

-   [Adicione uma aplicação não-galeria](#add-a-non-gallery-application)

-   [Configurar o pedido de assinatura única de assinatura](#configure-the-application-for-password-single-sign-on)

-   [Atribuir utilizadores à aplicação](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Adicione uma aplicação não-galeria

Para adicionar uma aplicação da Galeria AD Azure, siga os passos abaixo:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministrador**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5.  clique no botão **Adicionar** no canto superior direito no painel **de Aplicações da Empresa.**

6.  clique **em Aplicação não-galeria.**

7.  Insira o nome da sua candidatura na caixa de texto **'Nome'.** **Selecione Adicionar.**

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o pedido de assinatura única de assinatura

Para configurar um único sinal de inscrição para uma aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação que pretende configurar um único sinal de sação

7. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

8. Selecione o modo **de acesso baseado em palavras-passe.**

9. Introduza o **URL de inscrição**. Este é o URL onde os utilizadores introduzem o seu nome de utilizador e palavra-passe para iniciar seduca. Certifique-se de que o sinal nos campos está visível na URL.

10. Atribua os utilizadores à aplicação.

11. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando em **Credenciais de Atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais por si mesmos no lançamento.

### <a name="assign-users-to-the-application"></a>Atribuir utilizadores à aplicação

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação a que pretende atribuir um utilizador da lista.

7. Assim que a aplicação estiver carregada, clique em **Utilizadores e Grupos** a partir do menu de navegação à esquerda da aplicação.

8. Clique no botão **Adicionar** no topo da lista **de Utilizadores e Grupos** para abrir o painel **de atribuição de adicionar.**

9. clique no seletor **de Utilizadores e grupos** a partir do painel **de atribuição de adicionar.**

10. Digite o **nome completo** ou endereço de **e-mail** do utilizador que está interessado em atribuir na caixa de pesquisa **de endereço de e-mail por nome ou e-mail.**

11. Passe por cima do **utilizador** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do utilizador para adicionar o seu utilizador à lista **Selecionada.**

12. **Opcional:** Se pretender **adicionar mais do que um utilizador**, digite outro nome **completo** ou endereço **de e-mail** na caixa de pesquisa **de endereço de nome ou endereço de e-mail** e clique na caixa de verificação para adicionar este utilizador à lista **Selecionada.**

13. Quando terminar de selecionar utilizadores, clique no botão **Selecionar** para os adicionar à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **'Escolha's Role** in the **Add Assignment(Sessão)** para selecionar uma função a atribuir aos utilizadores selecionados.

15. Clique no botão **Atribuir** para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os utilizadores selecionados poderão lançar estas aplicações no Painel de Acesso.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolver a questão

abrir um bilhete de apoio com as seguintes informações, se disponível:

-   ID de erro de correlação

-   UPN (endereço de e-mail do utilizador)

-   TenantID

-   Tipo de browser

-   Fuso horário e tempo/prazo durante o erro ocorre

-   Traços de violino

## <a name="next-steps"></a>Próximos passos
[Fornecer um único s-on às suas apps com Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)

