---
title: Inscrição única baseada em palavra-passe (SSO) no Painel de Acesso [ Microsoft Docs
description: Discute áreas problemáticas que fornecem orientações para problemas relacionados com a assinatura nas aplicações da Azure AD Gallery configuradas para a inscrição individual de senha.
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca192c28757df189e531aee0ba2d8da288ba7e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68381232"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problemas de inscrição numa aplicação da Galeria AD Azure configurada para inscrição individual de senha

O Painel de Acesso é um portal baseado na web que permite a um utilizador que tenha uma conta de trabalho ou escola no Azure Ative Directory (Azure AD) para visualizar e lançar aplicações baseadas na nuvem a que o administrador da AD Azure lhes tenha dado acesso. Um utilizador que tenha edições Azure AD também pode usar capacidades de autosserviço e gestão de aplicações através do Painel de Acesso. O Painel de Acesso está separado do portal Azure e não exige que os utilizadores tenham uma subscrição Azure.

Para utilizar um único sinal de acesso baseado em palavra-passe (SSO) no Painel de Acesso, a extensão do Painel de Acesso deve ser instalada no navegador do utilizador. Esta extensão é descarregada automaticamente quando um utilizador seleciona uma aplicação configurada para SSO baseada em palavra-passe.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Cumprir os requisitos do navegador para o Painel de Acesso

O Painel de Acesso requer um navegador que suporta o JavaScript e tem CSS ativado. Para utilizar um único sinal de acesso baseado em palavra-passe (SSO) no Painel de Acesso, a extensão do Painel de Acesso deve ser instalada no navegador do utilizador. Esta extensão é descarregada automaticamente quando um utilizador seleciona uma aplicação configurada para SSO baseada em palavra-passe.

Para o SSO baseado em palavras-passe, os navegadores do utilizador final podem ser:

-   Internet Explorer 8, 9, 10, 11 - no Windows 7 ou mais tarde

-   Chrome - no Windows 7 ou mais tarde, e no MacOS X ou mais tarde

-   Firefox 26.0 ou mais tarde -- no Windows XP SP2 ou mais tarde, e no Mac OS X 10.6 ou mais tarde

>[!NOTE]
>A extensão SSO baseada em palavras-passe fica disponível para o Microsoft Edge no Windows 10 quando as extensões do navegador forem suportadas para o Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do navegador do Painel de Acesso

Para instalar a extensão do Navegador do Painel de Acesso, siga os passos abaixo:

1.  Abra o Painel de [Acesso](https://myapps.microsoft.com) num dos navegadores suportados e inscreva-se como **utilizador** no seu Azure AD.

2.  clique numa **aplicação password-SSO** no Painel de Acesso.

3.  Na solicitação imediata de instalar o software, selecione **Instalar Agora**.

4.  Com base no seu navegador, você será direcionado para o link de descarregamento. **Adicione** a extensão ao seu navegador.

5.  Se o seu navegador perguntar, selecione para **ativar** ou **permitir** a extensão.

6.  Uma vez instalado, **reinicie** a sua sessão de navegador.

7.  Inicie sessão no Painel de Acesso e veja se pode **lançar** as suas aplicações password-SSO

Também pode descarregar a extensão para Chrome e Firefox a partir dos links diretos abaixo:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Criação de uma política de grupo para o Internet Explorer

Pode configurar uma política de grupo que lhe permite instalar remotamente a extensão do Painel de Acesso para o Internet Explorer nas máquinas dos seus utilizadores.

Os pré-requisitos incluem:

-   Criou serviços de domínio de [diretório ativo](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e juntou-se às máquinas dos seus utilizadores para o seu domínio.

-   Deve ter a permissão "Editar configurações" para editar o Objeto de Política de Grupo (GPO). Por padrão, os membros dos seguintes grupos de segurança têm esta permissão: Administradores de Domínio, Administradores de Empresas e Proprietários de Criadores de Políticas de Grupo. [Saiba mais](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Siga o tutorial [Como implementar a extensão do painel de acesso para o Internet Explorer utilizando](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) a Política do Grupo para instruções passo a passo sobre como configurar a política do grupo e implementá-la aos utilizadores.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Resolução de problemas no Painel de Acesso no Internet Explorer

Siga o manual de resolução de [problemas do painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) de acesso para o guia do Internet Explorer para aceder a uma ferramenta de diagnóstico e instruções passo a passo sobre a configuração da extensão para IE.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o início de inscrição individual de senha para uma aplicação não-galeria

Para configurar uma aplicação da galeria Azure AD é necessário:

-   [Adicione uma aplicação não-galeria](#add-a-non-gallery-application)

-   [Configure o pedido de inscrição individual de senha](#configure-the-application-for-password-single-sign-on)

-   [Atribuir utilizadores à aplicação](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Adicione uma aplicação não-galeria

Para adicionar uma aplicação da Galeria AD Azure, siga os passos abaixo:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministrador**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5.  clique no botão **Adicionar** no canto superior direito no painel **de aplicações da empresa.**

6.  clique na **aplicação não-galeria.**

7.  Insira o nome da sua aplicação na caixa de texto **Nome.** Selecione **Adicionar.**

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configure o pedido de inscrição individual de senha

Para configurar um único sinal para uma aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende configurar um único sinal

7. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8. Selecione o modo **De Acesso baseado em palavras-passe.**

9. Introduza o **URL de inscrição**. Este é o URL onde os utilizadores introduzem o seu nome de utilizador e senha para iniciar sessão. Certifique-se de que o sinal nos campos está visível no URL.

10. Atribuir utilizadores à aplicação.

11. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando nas Credenciais de **Atualização** e inserindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais no próprio momento do lançamento.

### <a name="assign-users-to-the-application"></a>Atribuir utilizadores à aplicação

Para atribuir um ou mais utilizadores a uma aplicação diretamente, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende atribuir a um utilizador da lista.

7. Assim que a aplicação for carregada, clique em **Utilizadores e Grupos** a partir do menu de navegação à esquerda da aplicação.

8. Clique no botão **Adicionar** em cima da lista de **Utilizadores e Grupos** para abrir o painel de atribuição de **adicionar.**

9. clique nos Utilizadores e no seletor de **grupos** a partir do painel **adicionar atribuição.**

10. Digite o **nome completo** ou endereço de **e-mail** do utilizador que está interessado em atribuir na caixa de pesquisa de endereços de correio eletrónico **Pesquisar pelo nome ou pelo endereço de e-mail.**

11. Passe por cima do **utilizador** na lista para revelar uma caixa de **verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do utilizador para adicionar o seu utilizador à lista **Selecionada.**

12. **Opcional:** Se quiser **adicionar mais do que um utilizador**, digite outro nome **completo** ou endereço de **e-mail** na caixa de pesquisa de endereços de pesquisa de nome ou endereço de **e-mail,** e clique na caixa de verificação para adicionar este utilizador à lista **Selecionada.**

13. Quando terminar de selecionar os utilizadores, clique no botão **Select** para adicioná-los à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **de funções seletor** no painel **de atribuição** de adicionar para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique no botão **Atribuir** para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os utilizadores selecionados poderão lançar estas aplicações no Painel de Acesso.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolverem a questão

abrir um bilhete de apoio com as seguintes informações, se disponível:

-   Id de erro de correlação

-   UPN (endereço de e-mail do utilizador)

-   TenantID

-   Tipo de browser

-   Ofuso horário e tempo/tempo durante o erro ocorre

-   Traços de violinista

## <a name="next-steps"></a>Passos seguintes
[Forneça um único sinal às suas apps com Procuração de Aplicação](application-proxy-configure-single-sign-on-with-kcd.md)

