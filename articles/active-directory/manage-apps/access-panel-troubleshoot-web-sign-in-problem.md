---
title: Problema de inscrição no site do painel de acesso ! Microsoft Docs
description: Orientação para resolver problemas que pode encontrar ao tentar iniciar sessão para utilizar o Painel de Acesso
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
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f2ad4db231c616b3022ecafc62b12d6d81b67fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760818"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Insessão de problemas no site do painel de acesso

O Painel de Acesso é um portal baseado na Web que permite a um utilizador que tenha uma conta de trabalho ou escola no Azure Ative Directory (Azure AD) visualizar e lançar aplicações baseadas na nuvem a que o administrador da AD Azure lhes concedeu acesso. Um utilizador que tenha edições AD Azure também pode usar o grupo de self-service e as capacidades de gestão de aplicações através do Painel de Acesso. O Painel de Acesso está separado do portal Azure e não requer que os utilizadores tenham uma subscrição do Azure.

Os utilizadores podem inscrever-se no Painel de Acesso se tiverem uma conta de trabalho ou escola em Azure AD.

-   Os utilizadores podem ser autenticados diretamente pela Azure AD.

-   Os utilizadores podem ser autenticados utilizando serviços da Federação de Diretórios Ativos (AD FS).

-   Os utilizadores podem ser autenticados pelo Windows Server Ative Directory.

Se um utilizador tiver uma subscrição para o Azure ou o Office 365 e tiver vindo a utilizar o portal Azure ou uma aplicação do Office 365, poderá utilizar o Painel de Acesso sem necessidade de voltar a iniciar súbb. Os utilizadores que não estão autenticados são solicitados a iniciar scontabilidade utilizando o nome de utilizador e a palavra-passe para a sua conta em Azure AD. Se a organização tiver configurado a federação, digitar o nome de utilizador é suficiente.

## <a name="general-issues-to-check-first"></a>Questões gerais para verificar primeiro 

-   Certifique-se de que o utilizador está a iniciar sessão no **URL correto:**<https://myapps.microsoft.com>

-   Certifique-se de que o navegador do utilizador adicionou o URL aos seus **sites fidedignos**

-   Certifique-se de que a conta do utilizador está **ativada** para iniciar sposições.

-   Certifique-se de que a conta do utilizador não está **bloqueada.**

-   Certifique-se de que a palavra-passe do utilizador **não está caducada ou esquecida.**

-   Certifique-se de que **a autenticação multi-factor** não bloqueia o acesso do utilizador.

-   Certifique-se de que uma **política de acesso condicional** ou uma política de **proteção de identidade** não estão a bloquear o acesso do utilizador.

-   Certifique-se de que as informações de contacto de **autenticação** de um utilizador estão atualizadas para permitir a aplicação das políticas de autenticação ou acesso condicional de vários fatores.

-   Certifique-se de também tentar limpar os cookies do seu navegador e tentar entrar novamente.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Satisfazendo os requisitos do navegador para o Painel de Acesso

O Painel de Acesso requer um navegador que suporte o JavaScript e tenha CSS ativado. Para utilizar o único sinal de sso baseado em palavra-passe (SSO) no Painel de Acesso, a extensão do Painel de Acesso deve ser instalada no navegador do utilizador. Esta extensão é descarregada automaticamente quando um utilizador seleciona uma aplicação configurada para SSO baseado em palavra-passe.

Para sSO baseado em palavra-passe, os navegadores do utilizador final podem ser:

-   Internet Explorer 8, 9, 10, 11 - no Windows 7 ou mais tarde

-   Microsoft Edge na Edição de Aniversário do Windows 10 ou mais tarde 

-   Chrome - no Windows 7 ou mais tarde, e no MacOS X ou mais tarde

-   Firefox 26.0 ou mais tarde -- no Windows XP SP2 ou mais tarde, e no Mac OS X 10.6 ou mais tarde


## <a name="problems-with-the-users-account"></a>Problemas na conta do utilizador

O acesso ao Painel de Acesso pode ser bloqueado devido a um problema na conta do utilizador. Seguem-se algumas formas de resolver problemas e resolver problemas com os utilizadores e as definições da sua conta:

-   [Verifique se existe uma conta de utilizador no Azure Ative Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Verifique o estado da conta de um utilizador](#check-a-users-account-status)

-   [Redefinir a senha de um utilizador](#reset-a-users-password)

-   [Ativar a reposição de palavras-passe self-service](#enable-self-service-password-reset)

-   [Verifique o estado de autenticação de vários fatores do utilizador](#check-a-users-multi-factor-authentication-status)

-   [Verifique as informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info)

-   [Verifique os membros do grupo de um utilizador](#check-a-users-group-memberships)

-   [Verifique as licenças atribuídas a um utilizador](#check-a-users-assigned-licenses)

-   [Atribuir a um utilizador uma licença](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Verifique se existe uma conta de utilizador no Azure Ative Directory

Para verificar se a conta de um utilizador está presente, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  Verifique as propriedades do objeto do utilizador para ter certeza de que parecem como espera e não faltam dados.

### <a name="check-a-users-account-status"></a>Verifique o estado da conta de um utilizador

Para verificar o estado da conta de um utilizador, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique em **Perfil**.

8.  Em **Definições,** certifique-se de que **o sinal de bloqueio** está definido para **nº**.

### <a name="reset-a-users-password"></a>Redefinir a senha de um utilizador

Para redefinir a palavra-passe de um utilizador, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique no botão **de palavra-passe Reset** na parte superior do painel de utilizador.

8.  clique no botão **de palavra-passe Reset** no painel **de palavras-passe Reset** que aparece.

9.  Copie a **palavra-passe temporária** ou **introduza uma nova palavra-passe** para o utilizador.

10. Comunique esta nova palavra-passe ao utilizador, sendo-lhes necessária a alteração desta palavra-passe durante a sua próxima entrada no Azure Ative Directory.

### <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service

Para permitir o reset da palavra-passe de autosserviço, siga estes passos de implementação:

-   [Permitir que os utilizadores repusem as suas palavras-passe do Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Permitir que os utilizadores reiniciem ou alterem as suas palavras-passe no Ative Directory no local](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Verifique o estado de autenticação de vários fatores do utilizador

Para verificar o estado de autenticação de vários fatores do utilizador, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. clique no botão **de autenticação multi-factor** na parte superior do painel.

7. Assim que o Portal de **Administração de Autenticação Multi-Factor** estiver carregado, certifique-se de que está no separador **Utilizadores.**

8. Encontre o utilizador na lista de utilizadores pesquisando, filtrando ou classificando.

9. Selecione o utilizador da lista de utilizadores e **Ative,** **desativar**ou **impor a** autenticação de vários fatores conforme desejado.

   >[!NOTE]
   >Se um utilizador estiver num estado **forçado,** pode desativá-los **temporariamente** para os deixar voltar à sua conta. Uma vez que estejam de volta, pode então alterar o seu estado para **Ativado** novamente para exigir que voltem a registar os seus dados de contacto durante a sua próxima entrada. Em alternativa, pode seguir os passos na [informação de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info) para verificar ou definir estes dados para os mesmos.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Verifique as informações de contacto de autenticação de um utilizador

Para verificar as informações de contacto de autenticação de um utilizador utilizadas para autenticação de vários fatores, acesso condicional, proteção de identidade e redefinição de palavras-passe, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique em **Perfil**.

8.  Desloque-se para **informações de contacto de autenticação**.

9.  **Reveja** os dados registados para o utilizador e atualização conforme necessário.

### <a name="check-a-users-group-memberships"></a>Verifique os membros do grupo de um utilizador

Para verificar os membros do grupo de um utilizador, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique em **Grupos** para ver em que grupos o utilizador é membro.

### <a name="check-a-users-assigned-licenses"></a>Verifique as licenças atribuídas a um utilizador

Para verificar as licenças atribuídas a um utilizador, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais as licenças que o utilizador atualmente atribuiu.

### <a name="assign-a-user-a-license"></a>Atribuir a um utilizador uma licença 

Para atribuir uma licença a um utilizador, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os utilizadores**.

6.  **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais as licenças que o utilizador atualmente atribuiu.

8.  clique no botão **Atribuir.**

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Clique opcionalmente** no item das **opções de atribuição** para atribuir granularmente produtos. Clique **em Ok** quando isto estiver concluído.

11. Clique no botão **Atribuir** estas licenças a este utilizador.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolverem a questão

abrir um bilhete de apoio com as seguintes informações, se disponível:

-   ID de erro de correlação

-   UPN (endereço de e-mail do utilizador)

-   ID do inquilino

-   Tipo de browser

-   Fuso horário e tempo/prazo durante o erro ocorre

-   Traços de violino

## <a name="next-steps"></a>Próximos passos
[Fornecer um único s-on às suas apps com Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)
