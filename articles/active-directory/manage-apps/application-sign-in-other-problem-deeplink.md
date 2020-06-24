---
title: Problemas de adesão a uma aplicação utilizando um deeplink Microsoft Docs
description: Como resolver problemas de acesso a uma aplicação a partir de um URL de deeplink usando Azure AD
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
ms.openlocfilehash: 3c68143dabd9b047a8d6cc37ccac770b7d954656
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2020
ms.locfileid: "84759492"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problemas de inscrição numa aplicação utilizando um deeplink

O Painel de Acesso é um portal baseado na Web que permite a um utilizador com uma conta de trabalho ou escola em Azure Ative Directory (Azure AD) visualizar e iniciar aplicações baseadas na nuvem a que o administrador da AD Azure lhes concedeu acesso. 

Estas aplicações estão configuradas em nome do utilizador no portal AZure AD. A aplicação deve ser configurada corretamente e atribuída ao utilizador ou a um grupo do qual o utilizador é membro para ver a aplicação no Painel de Acesso.

Links profundos ou URLs de acesso ao utilizador são links que os seus utilizadores podem usar para aceder às suas aplicações SSO de palavra-passe diretamente das barras de URL dos seus navegadores. Ao navegar para este link, os utilizadores são automaticamente inscritos na aplicação sem terem de ir primeiro ao Painel de Acesso. Este é o mesmo link que os utilizadores utilizam para aceder a estas aplicações a partir do lançador de aplicações Office 365.

## <a name="general-issues-to-check-first"></a>Questões gerais para verificar primeiro

-   Certifique-se de que utiliza um **browser** que satisfaça os requisitos mínimos para o Painel de Acesso.

-   Certifique-se de que o navegador do utilizador adicionou o URL da aplicação aos seus **sites fidedignos.**

-   Certifique-se de que a aplicação está **configurada** corretamente.

-   Certifique-se de que a conta do utilizador está **ativada** para iniciar sposições.

-   Certifique-se de que a conta do utilizador não está **bloqueada.**

-   Certifique-se de que a palavra-passe do utilizador **não está caducada ou esquecida.**

-   Certifique-se de que **a autenticação multi-factor** não bloqueia o acesso do utilizador.

-   Certifique-se de que uma **política de acesso condicional** ou uma política de **proteção de identidade** não estão a bloquear o acesso do utilizador.

-   Certifique-se de que as informações de contacto de **autenticação** de um utilizador estão atualizadas para permitir a aplicação das políticas de Autenticação Multi-Factor ou acesso condicional.

-   Certifique-se de também tentar limpar os cookies do seu navegador e tentar entrar novamente.

## <a name="checking-the-deeplink"></a>Verificando o deeplink

Para verificar se tem o deeplink correto, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

7. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

8. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

9. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

10. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

11. Selecione a aplicação para a seguinte a verificação do deeplink.

12. Encontre o **URL de Acesso ao Utilizador da**etiqueta . O seu deeplink deve coincidir com este URL.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do Navegador do Painel de Acesso

Para instalar a extensão do Navegador do Painel de Acesso, siga estes passos:

1.  Abra o [Painel de Acesso](https://myapps.microsoft.com) num dos navegadores suportados e inscreva-se como **utilizador** no seu AD Azure.

2.  clique numa **aplicação password-SSO** no Painel de Acesso.

3.  Na instrução pedindo para instalar o software, **selecione Instalar Agora**.

4.  Com base no seu navegador, você está direcionado para o link de descarregamento. **Adicione** a extensão ao seu navegador.

5.  Se o seu navegador perguntar, selecione para **Ativar** ou **Permitir** a extensão.

6.  Uma vez instalado, **reinicie** a sessão do seu navegador.

7.  Inicie seduca no Painel de Acesso e veja se consegue **lançar** as suas aplicações password-SSO

Também pode descarregar a extensão para Chrome e Firefox a partir destes links diretos:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o sign-on único da palavra-passe para uma aplicação de galeria AZure AD

Para configurar uma aplicação da galeria AD Azure, deve:

-   [Adicione uma aplicação da galeria AD AZure](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar o pedido de assinatura única de assinatura](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicione uma aplicação da galeria AD AZure

Para adicionar uma aplicação da Galeria AD Azure, siga estes passos:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministração**.

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5.  clique no botão **Adicionar** no canto superior direito no painel **de Aplicações da Empresa.**

6.  Na **caixa de** texto de nome Introduzir a partir da secção **de galeria, digite** o nome da aplicação.

7.  Selecione a aplicação que pretende configurar para uma única sação.

8.  Antes de adicionar a aplicação, pode alterar o seu nome a partir da caixa de texto **Name.**

9.  Para adicionar a aplicação, clique em **Adicionar**.

Após um curto período de tempo, é possível ver o painel de configuração da aplicação.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o pedido de assinatura única de assinatura

Para configurar um único sinal de inscrição para uma aplicação, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação desejada para configurar um único sinal de inscrição.

7. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

8. Selecione o modo **de acesso baseado em palavras-passe.**

9. [Atribuir os utilizadores à aplicação.](#how-to-assign-a-user-to-an-application-directly)

10. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando em **Credenciais de Atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais por si mesmos no lançamento.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o sign-on único da palavra-passe para uma aplicação não-galeria

Para configurar uma aplicação da galeria AD Azure, deve:

-   [Adicione uma aplicação não-galeria](#add-a-non-gallery-application)

-   [Configurar o pedido de assinatura única de assinatura](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Adicione uma aplicação não-galeria

Para adicionar uma aplicação da Galeria AD Azure, siga estes passos:

1.  Abra o [portal Azure](https://portal.azure.com) e inscreva-se como **Administrador Global** ou **Coadministração**.

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5.  clique no botão **Adicionar** no canto superior direito no painel **de Aplicações da Empresa.**

6.  clique **em Aplicação não-galeria.**

7.  Insira o nome da sua candidatura na caixa de texto **'Nome'.** **Selecione Adicionar.**

Após um curto período de tempo, é possível ver o painel de configuração da aplicação.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o pedido de assinatura única de assinatura

Para configurar um único sinal de inscrição para uma aplicação, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5.  clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    1.  Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6.  Selecione a aplicação desejada para configurar um único sinal de inscrição.

7.  Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.

8.  Selecione o modo **de acesso baseado em palavras-passe.**

9.  Introduza o **URL de entrada,** URL onde os utilizadores introduzem o seu nome de utilizador e palavra-passe para iniciar sposição. Certifique-se de que os campos de inscrição estão visíveis na URL.

10. Atribua os utilizadores à aplicação.

11. Além disso, também pode fornecer credenciais em nome do utilizador, selecionando as linhas dos utilizadores e clicando em **Credenciais de Atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, os utilizadores são solicitados a introduzir as credenciais por si mesmos no lançamento.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir um utilizador a uma aplicação diretamente

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga estes passos:

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

11. Passe por cima do **utilizador** na lista para revelar uma **caixa de verificação**. Para adicionar o seu utilizador à lista **Selecionada,** clique na caixa de verificação ao lado da foto ou logotipo do perfil do utilizador.

12. **Opcional:** Se pretender **adicionar mais do que um utilizador**, digite outro nome **completo** ou endereço **de e-mail** na caixa de pesquisa **de endereço de nome ou endereço de e-mail** e clique na caixa de verificação para adicionar este utilizador à lista **Selecionada.**

13. Quando terminar de selecionar utilizadores, clique no botão **Selecionar** para os adicionar à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **'Escolha's Role** in the **Add Assignment(Sessão)** para selecionar uma função a atribuir aos utilizadores selecionados.

15. Clique no botão **Atribuir** para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os utilizadores selecionados poderão lançar estas aplicações no Painel de Acesso.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolverem a questão. 

abrir um bilhete de apoio com as seguintes informações, se disponível:

-   ID de erro de correlação

-   UPN (endereço de e-mail do utilizador)

-   TenantID

-   Tipo de browser

-   Fuso horário e tempo/prazo durante o erro ocorre

-   Traços de violino

## <a name="next-steps"></a>Passos seguintes
[Fornecer um único s-on às suas apps com Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)
