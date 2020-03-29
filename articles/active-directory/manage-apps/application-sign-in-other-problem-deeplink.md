---
title: Problemas de inscrição numa aplicação usando um deeplink Microsoft Docs
description: Como resolver problemas de acesso a uma aplicação a partir de um URL deeplink usando AD Azure
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
ms.openlocfilehash: 44825f32a13db0a221252c042dc9f23ec43a9c8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825414"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problemas de sessão numa aplicação usando um deeplink

O Painel de Acesso é um portal baseado na web que permite a um utilizador com uma conta de trabalho ou escola no Azure Ative Directory (Azure AD) visualizar e iniciar aplicações baseadas na nuvem a que o administrador da AD Azure lhes concedeu acesso. 

Estas aplicações estão configuradas em nome do utilizador no portal Azure AD. A aplicação deve ser configurada corretamente e atribuída ao utilizador ou a um grupo do qual o utilizador é membro para ver a aplicação no Painel de Acesso.

Links profundos ou URLs de acesso ao Utilizador são links que os seus utilizadores podem usar para aceder às suas aplicações de password-SSO diretamente das suas barras de URL dos seus navegadores. Ao navegar para este link, os utilizadores são automaticamente inscritos na aplicação sem terem de ir primeiro ao Painel de Acesso. Esta é a mesma ligação que os utilizadores usam para aceder a estas aplicações a partir do lançador de aplicações do Office 365.

## <a name="general-issues-to-check-first"></a>Questões gerais para verificar primeiro

-   Certifique-se de que utiliza um **browser** que satisfaça os requisitos mínimos para o Painel de Acesso.

-   Certifique-se de que o navegador do utilizador adicionou o URL da aplicação aos seus **sites fidedignos.**

-   Certifique-se de que a aplicação está **corretamente configurada.**

-   Certifique-se de que a conta do utilizador está **ativada** para iniciar sessão.

-   Certifique-se de que a conta do utilizador não está **bloqueada.**

-   Certifique-se de que a palavra-passe do utilizador **não está caducada ou esquecida.**

-   Certifique-se de que a **autenticação multi-factor** não está a bloquear o acesso ao utilizador.

-   Certifique-se de que uma política de **acesso condicional** ou política de **proteção de identidade** não está a bloquear o acesso dos utilizadores.

-   Certifique-se de que as informações de contacto de **autenticação** de um utilizador estão atualizadas para permitir a aplicação das políticas de autenticação multi-factor ou acesso condicional.

-   Certifique-se de também tentar limpar os cookies do seu navegador e tentar iniciar sessão novamente.

## <a name="checking-the-deeplink"></a>Verificando o deeplink

Para verificar se tem o deeplink correto, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

7. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

8. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

9. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

10. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

11. Selecione a aplicação que deseja verificar o deeplink.

12. Encontre o URL de acesso ao **utilizador**da etiqueta . O seu deeplink deve coincidir com este URL.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do navegador do Painel de Acesso

Para instalar a extensão do Navegador do Painel de Acesso, siga estes passos:

1.  Abra o Painel de [Acesso](https://myapps.microsoft.com) num dos navegadores suportados e inscreva-se como **utilizador** no seu Azure AD.

2.  clique numa **aplicação password-SSO** no Painel de Acesso.

3.  Na solicitação imediata de instalar o software, selecione **Instalar Agora**.

4.  Com base no seu navegador, você é direcionado para o link de descarregamento. **Adicione** a extensão ao seu navegador.

5.  Se o seu navegador perguntar, selecione para **ativar** ou **permitir** a extensão.

6.  Uma vez instalado, **reinicie** a sua sessão de navegador.

7.  Inicie sessão no Painel de Acesso e veja se pode **lançar** as suas aplicações password-SSO

Também pode descarregar a extensão para Chrome e Firefox a partir destas ligações diretas:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar um único sinal de senha para uma aplicação de galeria Azure AD

Para configurar uma aplicação da galeria Azure AD, deve:

-   [Adicione uma aplicação da galeria Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configure o pedido de inscrição individual de senha](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicione uma aplicação da galeria Azure AD

Para adicionar uma aplicação da Galeria AD Azure, siga estes passos:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministrador**.

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5.  clique no botão **Adicionar** no canto superior direito no painel **de aplicações da empresa.**

6.  Na caixa de **texto de nome Insira um nome** a partir do Add da secção **galeria,** digite o nome da aplicação.

7.  Selecione a aplicação que pretende configurar para um único início de sessão.

8.  Antes de adicionar a aplicação, pode alterar o seu nome a partir da caixa de texto **Nome.**

9.  Para adicionar a aplicação, clique em **Adicionar**.

Após um curto período de tempo, é possível ver o painel de configuração da aplicação.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configure o pedido de inscrição individual de senha

Para configurar um único início de inscrição para uma aplicação, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende configurar um único sinal.

7. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8. Selecione o modo **De Acesso baseado em palavras-passe.**

9. [Atribuir utilizadores à aplicação](#how-to-assign-a-user-to-an-application-directly).

10. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando nas Credenciais de **Atualização** e inserindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais no próprio momento do lançamento.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o início de inscrição individual de senha para uma aplicação não-galeria

Para configurar uma aplicação da galeria Azure AD, deve:

-   [Adicione uma aplicação não-galeria](#add-a-non-gallery-application)

-   [Configure o pedido de inscrição individual de senha](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Adicione uma aplicação não-galeria

Para adicionar uma aplicação da Galeria AD Azure, siga estes passos:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministrador**.

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5.  clique no botão **Adicionar** no canto superior direito no painel **de aplicações da empresa.**

6.  clique na **aplicação não-galeria.**

7.  Insira o nome da sua aplicação na caixa de texto **Nome.** Selecione **Adicionar.**

Após um curto período de tempo, é possível ver o painel de configuração da aplicação.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configure o pedido de inscrição individual de senha

Para configurar um único início de inscrição para uma aplicação, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5.  clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    1.  Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6.  Selecione a aplicação que pretende configurar um único sinal.

7.  Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8.  Selecione o modo **De Acesso baseado em palavras-passe.**

9.  Introduza o **URL de iniciar sessão,** o URL onde os utilizadores introduzem o seu nome de utilizador e palavra-passe para iniciar sessão. Certifique-se de que os campos de inscrição estão visíveis no URL.

10. Atribuir utilizadores à aplicação.

11. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando nas Credenciais de **Atualização** e inserindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais no próprio momento do lançamento.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir um utilizador a uma aplicação diretamente

Para atribuir diretamente a um ou mais utilizadores uma aplicação, siga estes passos:

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

11. Passe por cima do **utilizador** na lista para revelar uma caixa de **verificação**. Para adicionar o seu utilizador à lista **Selecionada,** clique na caixa de verificação ao lado da foto ou logotipo do perfil do utilizador.

12. **Opcional:** Se quiser **adicionar mais do que um utilizador**, digite outro nome **completo** ou endereço de **e-mail** na caixa de pesquisa de endereços de pesquisa de nome ou endereço de **e-mail,** e clique na caixa de verificação para adicionar este utilizador à lista **Selecionada.**

13. Quando terminar de selecionar os utilizadores, clique no botão **Select** para adicioná-los à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **de funções seletor** no painel **de atribuição** de adicionar para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique no botão **Atribuir** para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os utilizadores selecionados poderão lançar estas aplicações no Painel de Acesso.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolverem a questão. 

abrir um bilhete de apoio com as seguintes informações, se disponível:

-   Id de erro de correlação

-   UPN (endereço de e-mail do utilizador)

-   TenantID

-   Tipo de browser

-   Ofuso horário e tempo/tempo durante o erro ocorre

-   Traços de violinista

## <a name="next-steps"></a>Passos seguintes
[Forneça um único sinal às suas apps com Procuração de Aplicação](application-proxy-configure-single-sign-on-with-kcd.md)
