---
title: Problema de sessão no site do painel de acesso Microsoft Docs
description: Orientação para problemas que pode encontrar ao tentar iniciar sessão para usar o Painel de Acesso
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c6a9c3f26c8939176197a2ecf2fcd6026e9928
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784314"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Problema de sessão no site do painel de acesso

O Painel de Acesso é um portal baseado na web que permite a um utilizador que tenha uma conta de trabalho ou escola no Azure Ative Directory (Azure AD) para visualizar e lançar aplicações baseadas na nuvem a que o administrador da AD Azure lhes tenha dado acesso. Um utilizador que tenha edições Azure AD também pode usar capacidades de autosserviço e gestão de aplicações através do Painel de Acesso. O Painel de Acesso está separado do portal Azure e não exige que os utilizadores tenham uma subscrição Azure.

Os utilizadores podem iniciar sessão no Painel de Acesso se tiverem uma conta de trabalho ou escola em Azure AD.

-   Os utilizadores podem ser autenticados diretamente pela Azure AD.

-   Os utilizadores podem ser autenticados utilizando serviços da Federação de Diretórios Ativos (AD FS).

-   Os utilizadores podem ser autenticados pelo Diretório Ativo do Windows Server.

Se um utilizador tiver uma subscrição para o Azure ou office 365 e tiver vindo a utilizar o portal Azure ou uma aplicação do Office 365, poderá utilizar o Painel de Acesso sem necessidade de voltar a inscrever-se. Os utilizadores que não sejam autenticados são solicitados a iniciar sessão utilizando o nome de utilizador e a palavra-passe para a sua conta em AD Azure. Se a organização tiver configurado a federação, escrever o nome de utilizador é suficiente.

## <a name="general-issues-to-check-first"></a>Questões gerais para verificar primeiro 

-   Certifique-se de que o utilizador está a iniciar sessão no **URL correto:**<https://myapps.microsoft.com>

-   Certifique-se de que o navegador do utilizador adicionou o URL aos seus **sites fidedignos**

-   Certifique-se de que a conta do utilizador está **ativada** para iniciar sessão.

-   Certifique-se de que a conta do utilizador não está **bloqueada.**

-   Certifique-se de que a palavra-passe do utilizador **não está caducada ou esquecida.**

-   Certifique-se de que a **autenticação multi-factor** não está a bloquear o acesso ao utilizador.

-   Certifique-se de que uma política de **acesso condicional** ou política de **proteção de identidade** não está a bloquear o acesso dos utilizadores.

-   Certifique-se de que as informações de contacto de **autenticação** de um utilizador estão atualizadas para permitir a aplicação das políticas de autenticação de vários fatores ou de acesso condicional.

-   Certifique-se de também tentar limpar os cookies do seu navegador e tentar iniciar sessão novamente.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Cumprir os requisitos do navegador para o Painel de Acesso

O Painel de Acesso requer um navegador que suporta o JavaScript e tem CSS ativado. Para utilizar um único sinal de acesso baseado em palavra-passe (SSO) no Painel de Acesso, a extensão do Painel de Acesso deve ser instalada no navegador do utilizador. Esta extensão é descarregada automaticamente quando um utilizador seleciona uma aplicação configurada para SSO baseada em palavra-passe.

Para o SSO baseado em palavras-passe, os navegadores do utilizador final podem ser:

-   Internet Explorer 8, 9, 10, 11 - no Windows 7 ou mais tarde

-   Microsoft Edge na Edição de Aniversário do Windows 10 ou mais tarde 

-   Chrome - no Windows 7 ou mais tarde, e no MacOS X ou mais tarde

-   Firefox 26.0 ou mais tarde -- no Windows XP SP2 ou mais tarde, e no Mac OS X 10.6 ou mais tarde


## <a name="problems-with-the-users-account"></a>Problemas com a conta do utilizador

O acesso ao Painel de Acesso pode ser bloqueado devido a um problema na conta do utilizador. Seguem-se algumas formas de resolver problemas com os utilizadores e as definições da sua conta:

-   [Verifique se existe uma conta de utilizador no Diretório Ativo do Azure](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Verifique o estado da conta de um utilizador](#check-a-users-account-status)

-   [Redefinir a palavra-passe de um utilizador](#reset-a-users-password)

-   [Ativar a reposição de palavras-passe self-service](#enable-self-service-password-reset)

-   [Verifique o estado de autenticação de vários fatores de um utilizador](#check-a-users-multi-factor-authentication-status)

-   [Verifique as informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info)

-   [Verifique os membros do grupo de um utilizador](#check-a-users-group-memberships)

-   [Verifique as licenças atribuídas por um utilizador](#check-a-users-assigned-licenses)

-   [Atribuir uma licença a um utilizador](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Verifique se existe uma conta de utilizador no Diretório Ativo do Azure

Para verificar se a conta de um utilizador está presente, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  Verifique as propriedades do objeto do utilizador para se certificar de que eles parecem como espera e não faltam dados.

### <a name="check-a-users-account-status"></a>Verifique o estado da conta de um utilizador

Para verificar o estado da conta de um utilizador, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique em **Perfil**.

8.  Em **Definições,** certifique-se de que o **sinal de bloco** está definido para **O**.

### <a name="reset-a-users-password"></a>Redefinir a palavra-passe de um utilizador

Para redefinir a palavra-passe de um utilizador, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique no botão **de palavra-passe Reset** na parte superior do painel do utilizador.

8.  clique no botão de **palavra-passe Reset** no painel de **palavras-passe Reset** que aparece.

9.  Copie a **palavra-passe temporária** ou **introduza uma nova senha** para o utilizador.

10. Comunicar esta nova palavra-passe ao utilizador, sendo-a obrigada a alterar esta palavra-passe durante o seu próximo início de sessão no Diretório Ativo do Azure.

### <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service

Para permitir o reset da palavra-passe de autosserviço, siga estes passos de implementação:

-   [Ativar os utilizadores para redefinir as suas palavras-passe do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Ativar os utilizadores para redefinir ou alterar as suas palavras-passe do Diretório Ativo no local](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Verifique o estado de autenticação de vários fatores de um utilizador

Para verificar o estado de autenticação de vários fatores de um utilizador, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. clique no botão de **autenticação multi-factor** na parte superior do painel.

7. Assim que o Portal de Administração de **Autenticação Multifactor** carregar, certifique-se de que está no separador **Utilizadores.**

8. Encontre o utilizador na lista de utilizadores pesquisando, filtrando ou classificando.

9. Selecione o utilizador da lista de utilizadores e **Ative,** **Desativar**ou **Impor** a autenticação de vários fatores conforme desejado.

   >[!NOTE]
   >Se um utilizador estiver em estado **de Aplicação,** pode defini-los temporariamente para **Deficientes** para deixá-los voltar à sua conta. Uma vez que eles estão de volta, você pode então mudar o seu estado para **Enabled** novamente para exigir que eles reregistrem suas informações de contacto durante o seu próximo registo. Em alternativa, pode seguir os passos no [Check a autenticação de informações de contacto de um utilizador](#check-a-users-authentication-contact-info) para verificar ou definir estes dados para os mesmos.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Verifique as informações de contacto de autenticação de um utilizador

Para verificar as informações de contacto de autenticação de um utilizador utilizadas para a autenticação de vários fatores, acesso condicional, proteção de identidade e reset de palavra-passe, siga estas etapas:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique em **Perfil**.

8.  Desloque-se para baixo para **informações de contacto**de autenticação .

9.  **Reveja** os dados registados para o utilizador e atualize-os conforme necessário.

### <a name="check-a-users-group-memberships"></a>Verifique os membros do grupo de um utilizador

Para verificar os membros do grupo de um utilizador, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique **em Grupos** para ver em que grupos o utilizador é membro.

### <a name="check-a-users-assigned-licenses"></a>Verifique as licenças atribuídas por um utilizador

Para verificar as licenças atribuídas por um utilizador, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais as licenças que o utilizador tem atualmente atribuído.

### <a name="assign-a-user-a-license"></a>Atribuir uma licença a um utilizador 

Para atribuir uma licença a um utilizador, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais as licenças que o utilizador tem atualmente atribuído.

8.  clique no botão **Atribuir.**

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Clique opcional** mente no item das **opções** de atribuição para atribuir granularly produtos. Clique em **Ok** quando isto estiver concluído.

11. Clique no botão **Atribuir** para atribuir estas licenças a este utilizador.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolverem a questão

abrir um bilhete de apoio com as seguintes informações, se disponível:

-   Id de erro de correlação

-   UPN (endereço de e-mail do utilizador)

-   ID do inquilino

-   Tipo de browser

-   Ofuso horário e tempo/tempo durante o erro ocorre

-   Traços de violinista

## <a name="next-steps"></a>Passos seguintes
[Forneça um único sinal às suas apps com Procuração de Aplicação](application-proxy-configure-single-sign-on-with-kcd.md)
