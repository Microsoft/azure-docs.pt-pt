---
title: Problemas de sessão numa aplicação da Microsoft Microsoft Docs
description: Problemas de resolução de problemas comuns enfrentados ao assinar na microsoft applications de primeira parte usando AD Azure (como office 365)
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
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee8802aeb2a760e255ab4f5e99010dfedc45e0d
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "67108305"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problemas de sessão numa aplicação da Microsoft

As aplicações da Microsoft (como office 365 Exchange, SharePoint, Yammer, etc.) são atribuídas e geridas de forma um pouco diferente das aplicações SaaS de terceiros ou outras aplicações que integra com a Azure AD para um único sinal.

Existem três formas principais de um utilizador ter acesso a uma aplicação publicada pela Microsoft.

-   Para aplicações no Office 365 ou outras suites pagas, os utilizadores têm acesso através da atribuição de **licenças** diretamente à sua conta de utilizador, ou através de um grupo que utiliza a nossa capacidade de atribuição de licenças baseada em grupo.

-   Para aplicações que a Microsoft ou um Terceiro publiquelivremente para qualquer pessoa usar, os utilizadores podem ter acesso através do consentimento do **utilizador**. Isto significa que eles assinam a aplicação com a sua conta Azure AD Work ou School e permitem-lhe ter acesso a algum conjunto limitado de dados na sua conta.

-   Para aplicações que a Microsoft ou uma terceira parte publica livremente para qualquer pessoa usar, os utilizadores também podem ter acesso através do **consentimento do administrador**. Isto significa que um administrador determinou que o pedido pode ser usado por todos na organização, pelo que assina mato no pedido com uma conta de Administrador Global e concede acesso a todos na organização.

Para resolver o problema, comece pelas [Áreas Gerais de Problemas com Acesso a Aplicações a considerar](#general-problem-areas-with-application-access-to-consider) e, em seguida, leia o Walkthrough: Steps to troubleshoot Microsoft Application access to get in the details.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Áreas de problemas gerais com acesso a aplicação a considerar

Segue-se uma lista das áreas problemáticas gerais em que pode perfurar se tiver uma ideia de por onde começar, mas recomendamos que leia o walkthrough para começar rapidamente: Walkthrough: Steps to troubleshoot Microsoft Application access.

-   [Problemas com a conta do utilizador](#problems-with-the-users-account)

-   [Problemas com grupos](#problems-with-groups)

-   [Problemas com políticas de acesso condicional](#problems-with-conditional-access-policies)

-   [Problemas com o consentimento da aplicação](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Passos para resolver o acesso à Aplicação da Microsoft

Seguem-se alguns problemas comuns que as pessoas encontram quando os seus utilizadores não podem iniciar sessão numa aplicação da Microsoft.

- Questões gerais para verificar primeiro

  * Certifique-se de que o utilizador está a iniciar sessão no **URL correto** e não num URL de aplicação local.

  * Certifique-se de que a conta do utilizador não está **bloqueada.**

  * Certifique-se de que a **conta do utilizador existe** no Diretório Ativo Azure. [Verifique se existe uma conta de utilizador no Diretório Ativo do Azure](#problems-with-the-users-account)

  * Certifique-se de que a conta do utilizador está **ativada** para iniciar sessão. Consulte o [estado da conta de um utilizador](#problems-with-the-users-account)

  * Certifique-se de que a palavra-passe do utilizador **não está caducada ou esquecida.** [Redefinir a palavra-passe de um utilizador](#reset-a-users-password) ou ativar o reset da [palavra-passe de autosserviço](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

  * Certifique-se de que a **autenticação multi-factor** não está a bloquear o acesso ao utilizador. [Verifique o estado de autenticação de vários fatores de um utilizador](#check-a-users-multi-factor-authentication-status) ou verifique as [informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info)

  * Certifique-se de que uma política de **acesso condicional** ou política de **proteção de identidade** não está a bloquear o acesso dos utilizadores. [Verifique uma política específica](#problems-with-conditional-access-policies) de acesso condicional ou verifique a política de [acesso condicional de uma aplicação específica](#check-a-specific-applications-conditional-access-policy) ou [desative uma política específica](#disable-a-specific-conditional-access-policy) de acesso condicional

  * Certifique-se de que as informações de contacto de **autenticação** de um utilizador estão atualizadas para permitir a aplicação das políticas de autenticação multi-factor ou acesso condicional. [Verifique o estado de autenticação de vários fatores de um utilizador](#check-a-users-multi-factor-authentication-status) ou verifique as [informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info)

- Para aplicações da **Microsoft** **que requerem uma licença** (como o Office365), aqui estão alguns problemas específicos para verificar uma vez que você tenha excluído os problemas gerais acima:

  * Certifique-se de que o utilizador ou tem uma **licença atribuída.** Verifique as [licenças atribuídas a um utilizador](#check-a-users-assigned-licenses) ou verifique as [licenças atribuídas por um grupo](#check-a-groups-assigned-licenses)

  * Se a licença for **atribuída a um** grupo **estático,** **certifique-se** de que o utilizador é membro desse grupo. [Verifique os membros do grupo de um utilizador](#check-a-users-group-memberships)

  * Se a licença for **atribuída a um** grupo **dinâmico,** certifique-se de que a regra do **grupo dinâmico é corretamente definida**. [Verifique os critérios de adesão de um grupo dinâmico](#check-a-dynamic-groups-membership-criteria)

  * Se a licença for **atribuída a um** grupo **dinâmico,** certifique-se de que o grupo dinâmico terminou de **processar** a sua adesão e que o utilizador **é membro** (isto pode demorar algum tempo). [Verifique os membros do grupo de um utilizador](#check-a-users-group-memberships)

  *  Assim que se certificar de que a licença está atribuída, certifique-se de que a licença **não está caducada**.

  *  Certifique-se de que a licença é **para a aplicação** a que estão a aceder.

- Para aplicações da **Microsoft** **que não necessitam de licença,** aqui estão outras coisas para verificar:

  * Se a aplicação estiver a solicitar **permissões ao nível do utilizador** (por exemplo,"Aceda à caixa de correio deste utilizador"), **certifique-se** de que o utilizador assinou a aplicação e realizou uma operação de consentimento ao nível do utilizador para permitir que a aplicação aceda aos seus dados.

  * Se a aplicação estiver a solicitar **permissões** ao nível do administrador (por exemplo,"Aceda a todas as caixas de correio do utilizador"), **certifique-se** de que um Administrador Global realizou uma operação de consentimento ao nível do administrador em nome de todos os utilizadores da organização.

## <a name="problems-with-the-users-account"></a>Problemas com a conta do utilizador

O acesso à aplicação pode ser bloqueado devido a um problema com um utilizador que está atribuído à aplicação. Seguem-se algumas formas de resolver problemas com os utilizadores e as definições da sua conta:

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

Para permitir o reset da palavra-passe de autosserviço, siga os passos de implementação abaixo:

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

7. Assim que o portal da Administração de **Autenticação Multifactor** carregar, certifique-se de que está no separador **Utilizadores.**

8. Encontre o utilizador na lista de utilizadores pesquisando, filtrando ou classificando.

9. Selecione o utilizador da lista de utilizadores e **Ative,** **Desativar**ou **Impor** a autenticação de vários fatores conforme desejado.

   * **Nota:** Se um utilizador estiver em estado **de aplicação,** poderá defini-los temporariamente para **Desativados** para os deixar voltar à sua conta. Uma vez que eles estão de volta, você pode então mudar o seu estado para **Enabled** novamente para exigir que eles reregistrem suas informações de contato durante o seu próximo sinal de entrada. Em alternativa, pode seguir os passos no [Check a autenticação de informações de contacto de um utilizador](#check-a-users-authentication-contact-info) para verificar ou definir estes dados para os mesmos.

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

## <a name="problems-with-groups"></a>Problemas com grupos

O acesso à aplicação pode ser bloqueado devido a um problema com um grupo que está atribuído à aplicação. Seguem-se algumas formas de resolver problemas com grupos e membros do grupo:

-   [Verifique a adesão de um grupo](#check-a-groups-membership)

-   [Verifique os critérios de adesão de um grupo dinâmico](#check-a-dynamic-groups-membership-criteria)

-   [Verifique as licenças atribuídas a um grupo](#check-a-groups-assigned-licenses)

-   [Reprocessar as licenças de um grupo](#reprocess-a-groups-licenses)

-   [Atribuir uma licença a um grupo](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Verifique a adesão de um grupo

Para verificar a adesão de um grupo, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique **em Todos os grupos**.

6.  **Procure** o grupo em que está interessado e **clique na linha** para selecionar.

7.  clique em **Membros** para rever a lista de utilizadores atribuídos a este grupo.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Verifique os critérios de adesão de um grupo dinâmico 

Para verificar os critérios de adesão de um grupo dinâmico, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique **em Todos os grupos**.

6.  **Procure** o grupo em que está interessado e **clique na linha** para selecionar.

7.  clique em **regras de adesão dinâmicas.**

8.  Reveja a regra **simples** ou **avançada** definida para este grupo e certifique-se de que o utilizador que pretende ser membro deste grupo satisfaz estes critérios.

### <a name="check-a-groups-assigned-licenses"></a>Verifique as licenças atribuídas a um grupo

Para verificar as licenças atribuídas a um grupo, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique **em Todos os grupos**.

6.  **Procure** o grupo em que está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais as licenças atribuídas pelo grupo.

### <a name="reprocess-a-groups-licenses"></a>Reprocessar as licenças de um grupo

Para reprocessar as licenças atribuídas por um grupo, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique **em Todos os grupos**.

6. **Procure** o grupo em que está interessado e **clique na linha** para selecionar.

7. clique em **Licenças** para ver quais as licenças atribuídas pelo grupo.

8. clique no botão **Reprocessar** para garantir que as licenças atribuídas aos membros deste grupo estão atualizadas. Isto pode demorar muito tempo, dependendo do tamanho e complexidade do grupo.

   >[!NOTE]
   >Para o fazer mais rapidamente, considere a atribuição temporária de uma licença ao utilizador diretamente. [Atribuir uma licença](#problems-with-application-consent)a um utilizador .
   >
   >

### <a name="assign-a-group-a-license"></a>Atribuir uma licença a um grupo

Para atribuir uma licença a um grupo, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique **em Todos os grupos**.

6. **Procure** o grupo em que está interessado e **clique na linha** para selecionar.

7. clique em **Licenças** para ver quais as licenças atribuídas pelo grupo.

8. clique no botão **Atribuir.**

9. Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Clique opcional** mente no item das **opções** de atribuição para atribuir granularly produtos. Clique em **Ok** quando isto estiver concluído.

11. Clique no botão **Atribuir** para atribuir estas licenças a este grupo. Isto pode demorar muito tempo, dependendo do tamanho e complexidade do grupo.

    >[!NOTE]
    >Para o fazer mais rapidamente, considere a atribuição temporária de uma licença ao utilizador diretamente. [Atribuir uma licença](#problems-with-application-consent)a um utilizador .
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problemas com políticas de acesso condicional

### <a name="check-a-specific-conditional-access-policy"></a>Verifique uma política específica de acesso condicional

Para verificar ou validar uma única política de acesso condicional:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique em **aplicações da Enterprise** no menu de navegação.

5. clique no item de navegação de **Acesso Condicional.**

6. clique na política que está interessado em inspecionar.

7. Reveja que não existem condições específicas, atribuições ou outras definições que possam estar a bloquear o acesso ao utilizador.

   >[!NOTE]
   >Pode desativar temporariamente esta política para garantir que não está a afetar as inscrições. Para isso, detete a **tecla ativar** a tecla **'Não'** e clique no botão **Guardar.**
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Verifique a política de acesso condicional de uma aplicação específica

Para verificar ou validar a política de acesso condicional de uma única aplicação:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **aplicações da Enterprise** no menu de navegação.

5.  clique em **Todas as aplicações**.

6.  Procure a aplicação em que está interessado, ou o utilizador está a tentar iniciar sessão através do nome do ecrã da aplicação ou do ID da aplicação.

     >[!NOTE]
     >Se não vir a aplicação que procura, clique no botão **Filter** e expanda o âmbito da lista para **Todas as aplicações**. Se quiser ver mais colunas, clique no botão **Colunas** para adicionar detalhes adicionais para as suas aplicações.
     >
     >

7.  clique no item de navegação de **Acesso Condicional.**

8.  clique na política que está interessado em inspecionar.

9.  Reveja que não existem condições específicas, atribuições ou outras definições que possam estar a bloquear o acesso ao utilizador.

     >[!NOTE]
     >Pode desativar temporariamente esta política para garantir que não está a afetar as inscrições. Para isso, detete a **tecla ativar** a tecla **'Não'** e clique no botão **Guardar.**
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Desativar uma política específica de acesso condicional

Para verificar ou validar uma única política de acesso condicional:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique em **aplicações da Enterprise** no menu de navegação.

5.  clique no item de navegação de **Acesso Condicional.**

6.  clique na política que está interessado em inspecionar.

7.  Desative a política definindo a **tecla Ativa** rema da política para **Não** e clique no botão **Guardar.**

## <a name="problems-with-application-consent"></a>Problemas com o consentimento da aplicação

O acesso à aplicação pode ser bloqueado porque a operação de consentimento de permissões adequadas não ocorreu. Seguem-se algumas formas de resolver problemas e resolver problemas de consentimento da aplicação:

-   [Realizar uma operação de consentimento ao nível do utilizador](#perform-a-user-level-consent-operation)

-   [Executar operação de consentimento ao nível do administrador para qualquer pedido](#perform-administrator-level-consent-operation-for-any-application)

-   [Executar o consentimento de nível de administrador para um pedido de inquilino único](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Executar o consentimento de nível de administrador para um pedido de multi-inquilino](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Realizar uma operação de consentimento ao nível do utilizador

-   Para qualquer aplicação ativada pelo Open ID Connect que solicite permissões, navegar para o sinal da aplicação no ecrã executa um consentimento de nível de utilizador para a aplicação para o utilizador inscrito.

-   Se desejar fazer isto programáticamente, consulte o pedido de [consentimento individual do utilizador](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Executar operação de consentimento ao nível do administrador para qualquer pedido

-   Para **aplicações desenvolvidas apenas utilizando o modelo de aplicação V1,** pode forçar este consentimento de nível de administrador a ocorrer adicionando "**?prompt=admin\_consent**" ao fim do sinal de uma aplicação em URL.

-   Para qualquer aplicação desenvolvida utilizando o modelo de **aplicação V2,** pode impor este consentimento de nível de administrador para ocorrer seguindo as instruções ao abrigo do **Pedido as permissões de uma** secção de administração de diretório de [Utilização do ponto final](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)do consentimento do administrador .

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Executar o consentimento de nível de administrador para um pedido de inquilino único

-   Para **aplicações de inquilino único** que solicitem permissões (como as que está a desenvolver ou possua na sua organização), pode realizar uma operação de consentimento a **nível administrativo** em nome de todos os utilizadores, inserindo-se como Administrador Global e clicando no botão de **permissões da Concessão** no topo do Registo de **Aplicações -&gt; Todas as Aplicações -&gt; Selecione uma app -&gt; Painel de Permissões Obrigatórias.**

-   Para qualquer aplicação desenvolvida utilizando o modelo de **aplicação V1 ou V2,** pode impor este consentimento de nível de administrador para ocorrer seguindo as instruções ao **abrigo do Pedido as permissões de uma** secção de administração de diretório de [Utilização do ponto final](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)do consentimento do administrador .

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Executar o consentimento de nível de administrador para um pedido de multi-inquilino

-   Para **aplicações multi-inquilinos** que solicitam permissões (como uma aplicação que um terceiro, ou Microsoft, desenvolve), você pode realizar uma operação de consentimento de **nível administrativo.** Inscreva-se como Administrador Global e clique no botão de **permissões da Concessão** no âmbito das **Aplicações empresariais -&gt; Todas as Aplicações -&gt; Selecione uma app -&gt; Painel de permissões** (disponível em breve).

-   Também pode impor este consentimento de nível de administrador para ocorrer seguindo as instruções sob o **Pedido as permissões de uma** secção de administrador de diretório de [Utilização do ponto final](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)do consentimento do administrador .

## <a name="next-steps"></a>Passos seguintes
[Utilização do ponto final do consentimento da administração](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

