---
title: Problemas de sessão numa aplicação da Microsoft | Microsoft Docs
description: Resolver problemas comuns enfrentados ao iniciar sessão em aplicações microsoft de primeira parte usando Azure AD (como o Microsoft 365).
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79c8d6d072379853d6eca561d372f61dbb8acc8a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99260016"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problemas de sessão numa aplicação da Microsoft

As aplicações da Microsoft (como Exchange, SharePoint, Yammer, etc.) são atribuídas e geridas de forma um pouco diferente das aplicações SaaS de terceiros ou outras aplicações que integra com a AZure AD para um único sinal.

Existem três formas principais de um utilizador ter acesso a uma aplicação publicada pela Microsoft.

-   Para aplicações no Microsoft 365 ou outras suites pagas, os utilizadores têm acesso através de **uma atribuição de licenças** diretamente à sua conta de utilizador, ou através de um grupo que utiliza a nossa capacidade de atribuição de licenças baseada no grupo.

-   Para aplicações que a Microsoft ou um Terceiro publica livremente para qualquer pessoa utilizar, os utilizadores podem ter acesso através do **consentimento do utilizador.** Isto significa que eles assinam a aplicação com a sua conta Azure AD Work ou Escola e permitem-lhe ter acesso a algum conjunto limitado de dados na sua conta.

-   Para aplicações que a Microsoft ou uma terceira parte publica livremente para qualquer pessoa usar, os utilizadores também podem ter acesso através do **consentimento do administrador**. Isto significa que um administrador determinou que o pedido pode ser usado por todos na organização, por isso eles assinam o pedido com uma conta de Administrador Global e concedem acesso a todos na organização.

Para resolver o seu problema, comece com as [Áreas Gerais de Problema com Acesso à Aplicação a considerar](#general-problem-areas-with-application-access-to-consider) e depois leia o Walkthrough: Passos para resolver problemas no acesso à Aplicação da Microsoft para entrar nos detalhes.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Áreas de Problema Geral com Acesso à Aplicação a considerar

Segue-se uma lista das áreas gerais de problemas em que pode perfurar se tiver uma ideia de onde começar, mas recomendamos que leia a passagem para começar rapidamente: Walkthrough: Passos para resolver o acesso à Aplicação microsoft.

-   [Problemas na conta do utilizador](#problems-with-the-users-account)

-   [Problemas com grupos](#problems-with-groups)

-   [Problemas com políticas de acesso condicional](#problems-with-conditional-access-policies)

-   [Problemas com o consentimento da candidatura](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Passos para resolver problemas no acesso à Aplicação microsoft

Seguem-se alguns problemas comuns que as pessoas encontram quando os seus utilizadores não conseguem entrar numa aplicação da Microsoft.

- Questões gerais para verificar primeiro

  * Certifique-se de que o utilizador está a iniciar sessão no **URL correto** e não num URL de aplicação local.

  * Certifique-se de que a conta do utilizador não está **bloqueada.**

  * Certifique-se de que a **conta do utilizador existe** no Azure Ative Directory. [Verifique se existe uma conta de utilizador no Azure Ative Directory](#problems-with-the-users-account)

  * Certifique-se de que a conta do utilizador está **ativada** para iniciar sposições. [Verifique o estado da conta de um utilizador](#problems-with-the-users-account)

  * Certifique-se de que a palavra-passe do utilizador **não está caducada ou esquecida.** [Redefinir a palavra-passe de um utilizador](#reset-a-users-password) ou ativar o reset da [palavra-passe de autosserviço](../authentication/tutorial-enable-sspr.md)

  * Certifique-se de que **a autenticação multi-factor** não bloqueia o acesso do utilizador. [Verifique o estado de autenticação de vários fatores do utilizador](#check-a-users-multi-factor-authentication-status) ou [verifique a informação de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info)

  * Certifique-se de que uma **política de acesso condicional** ou uma política de **proteção de identidade** não estão a bloquear o acesso do utilizador. [Consulte uma política específica de acesso condicional](#problems-with-conditional-access-policies) ou verifique a política específica de acesso condicional de uma [aplicação](#check-a-specific-applications-conditional-access-policy) ou [desative uma política específica de acesso condicional](#disable-a-specific-conditional-access-policy)

  * Certifique-se de que as informações de contacto de **autenticação** de um utilizador estão atualizadas para permitir a aplicação das políticas de Autenticação Multi-Factor ou acesso condicional. [Verifique o estado de autenticação de vários fatores do utilizador](#check-a-users-multi-factor-authentication-status) ou [verifique a informação de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info)

- Para aplicações **da Microsoft** **que requerem uma licença** (como o Office365), eis alguns problemas específicos para verificar uma vez que descartou as questões gerais acima:

  * Certifique-se de que o utilizador ou tem uma **licença atribuída.** [Verifique as licenças atribuídas a um utilizador](#check-a-users-assigned-licenses) ou [verifique as licenças atribuídas por um grupo](#check-a-groups-assigned-licenses)

  * Se a licença for **atribuída a um** grupo **estático,** certifique-se de que o **utilizador é membro** desse grupo. [Verifique os membros do grupo de um utilizador](#check-a-users-group-memberships)

  * Se a licença for **atribuída a um** grupo **dinâmico,** certifique-se de que a **regra do grupo dinâmico está corretamente definida**. [Consulte os critérios de adesão de um grupo dinâmico](#check-a-dynamic-groups-membership-criteria)

  * Se a licença for **atribuída a um** grupo **dinâmico,** certifique-se de que o grupo dinâmico **terminou o processamento** da sua adesão e que o utilizador é **membro** (isto pode demorar algum tempo). [Verifique os membros do grupo de um utilizador](#check-a-users-group-memberships)

  *  Assim que se certificar de que a licença é atribuída, certifique-se de que a licença **não expira**.

  *  Certifique-se de que a licença é **para a aplicação** a que estão a aceder.

- Para aplicações da **Microsoft** **que não requerem licença,** aqui estão outras coisas para verificar:

  * Se a aplicação estiver a solicitar **permissões ao nível** do utilizador (por exemplo, "Aceda à caixa de correio deste utilizador"), **certifique-se** de que o utilizador assinou a aplicação e realizou uma operação de consentimento ao nível do utilizador para permitir que a aplicação aceda aos seus dados.

  * Se a aplicação estiver a solicitar **permissões ao nível do administrador** (por exemplo, "Aceder a todas as caixas de correio do utilizador"), certifique-se de que um Administrador Global realizou uma **operação de consentimento ao nível do administrador em nome de todos os utilizadores** da organização.

## <a name="problems-with-the-users-account"></a>Problemas na conta do utilizador

O acesso à aplicação pode ser bloqueado devido a um problema com um utilizador que está atribuído à aplicação. Seguem-se algumas formas de resolver problemas e resolver problemas com os utilizadores e as definições da sua conta:

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

10. Comunique esta nova palavra-passe ao utilizador, sendo-lhes necessária a alteração desta palavra-passe durante o seu próximo súmis no Azure Ative Directory.

### <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service

Para ativar o reset da palavra-passe de autosserviço, siga os passos de implantação abaixo:

-   [Permitir que os utilizadores repusem as suas palavras-passe do Azure Ative Directory](../authentication/tutorial-enable-sspr.md)

-   [Permitir que os utilizadores reiniciem ou alterem as suas palavras-passe no Ative Directory no local](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Verifique o estado de autenticação de vários fatores do utilizador

Para verificar o estado de autenticação de vários fatores do utilizador, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. clique no botão **de autenticação multi-factor** na parte superior do painel.

7. Assim que o **portal da Administração de Autenticação Multi-Factor** estiver carregado, certifique-se de que está no separador **Utilizadores.**

8. Encontre o utilizador na lista de utilizadores pesquisando, filtrando ou classificando.

9. Selecione o utilizador da lista de utilizadores e **Ative,** **desativar** ou **impor a** autenticação de vários fatores conforme desejado.

   * **Nota:** Se um utilizador estiver num estado **forçado,** pode alterá-lo temporariamente para **desativar** para os deixar voltar à sua conta. Uma vez que estejam de volta, pode então alterar o seu estado para **Enabled** novamente para exigir que eles re-registrem os seus dados de contato durante o seu próximo sinal. Em alternativa, pode seguir os passos na [informação de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info) para verificar ou definir estes dados para os mesmos.

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

## <a name="problems-with-groups"></a>Problemas com grupos

O acesso à aplicação pode ser bloqueado devido a um problema com um grupo que está atribuído à aplicação. Seguem-se algumas formas de resolver problemas e resolver problemas com grupos e membros do grupo:

-   [Verifique a adesão de um grupo](#check-a-groups-membership)

-   [Consulte os critérios de adesão de um grupo dinâmico](#check-a-dynamic-groups-membership-criteria)

-   [Verifique as licenças atribuídas por um grupo](#check-a-groups-assigned-licenses)

-   [Reprocessar as licenças de um grupo](#reprocess-a-groups-licenses)

-   [Atribuir uma licença a um grupo](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Verifique a adesão de um grupo

Para verificar a adesão de um grupo, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os grupos**.

6.  **Procure** o grupo em que está interessado e **clique na linha** para selecionar.

7.  clique em **Membros** para rever a lista de utilizadores atribuídos a este grupo.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Consulte os critérios de adesão de um grupo dinâmico 

Para verificar os critérios de adesão de um grupo dinâmico, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os grupos**.

6.  **Procure** o grupo em que está interessado e **clique na linha** para selecionar.

7.  clique em **Regras de adesão dinâmicas.**

8.  Reveja a regra **simples** ou **avançada** definida para este grupo e certifique-se de que o utilizador que pretende ser membro deste grupo cumpre estes critérios.

### <a name="check-a-groups-assigned-licenses"></a>Verifique as licenças atribuídas por um grupo

Para verificar as licenças atribuídas por um grupo, siga estes passos:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **Utilizadores e grupos** no menu de navegação.

5.  clique em **Todos os grupos**.

6.  **Procure** o grupo em que está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais as licenças que o grupo atualmente atribuiu.

### <a name="reprocess-a-groups-licenses"></a>Reprocessar as licenças de um grupo

Para reprocessar as licenças atribuídas por um grupo, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os grupos**.

6. **Procure** o grupo em que está interessado e **clique na linha** para selecionar.

7. clique em **Licenças** para ver quais as licenças que o grupo atualmente atribuiu.

8. clique no botão **Reprocessamento** para garantir que as licenças atribuídas aos membros deste grupo estão atualizadas. Isto pode demorar muito tempo, dependendo do tamanho e complexidade do grupo.

   >[!NOTE]
   >Para o fazer mais rapidamente, considere atribuir temporariamente uma licença ao utilizador diretamente. [Atribua a um utilizador uma licença.](#problems-with-application-consent)
   >
   >

### <a name="assign-a-group-a-license"></a>Atribuir uma licença a um grupo

Para atribuir uma licença a um grupo, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os grupos**.

6. **Procure** o grupo em que está interessado e **clique na linha** para selecionar.

7. clique em **Licenças** para ver quais as licenças que o grupo atualmente atribuiu.

8. clique no botão **Atribuir.**

9. Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Clique opcionalmente** no item das **opções de atribuição** para atribuir granularmente produtos. Clique **em Ok** quando isto estiver concluído.

11. Clique no botão **Atribuir** estas licenças a este grupo. Isto pode demorar muito tempo, dependendo do tamanho e complexidade do grupo.

    >[!NOTE]
    >Para o fazer mais rapidamente, considere atribuir temporariamente uma licença ao utilizador diretamente. [Atribua a um utilizador uma licença.](#problems-with-application-consent)
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problemas com políticas de acesso condicional

### <a name="check-a-specific-conditional-access-policy"></a>Consulte uma política específica de acesso condicional

Para verificar ou validar uma única política de acesso condicional:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **aplicações Enterprise** no menu de navegação.

5. clique no item de navegação **de acesso condicional.**

6. clique na política que está interessado em inspecionar.

7. Reveja que não existem condições, atribuições ou outras configurações específicas que possam estar a bloquear o acesso ao utilizador.

   >[!NOTE]
   >Pode desejar desativar temporariamente esta política para garantir que não está a afetar as inscrições. Para isso, desace a **política "Ativar"** para **Não** e clique no botão **Guardar.**
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Consulte a política específica de acesso condicional de uma aplicação

Para verificar ou validar a política de acesso condicional configurada de uma única aplicação:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **aplicações Enterprise** no menu de navegação.

5.  clique em **Todas as aplicações**.

6.  Procure a aplicação em que está interessado ou o utilizador está a tentar iniciar sômposição pelo nome de exibição de aplicações ou ID da aplicação.

     >[!NOTE]
     >Se não vir a aplicação que procura, clique no botão **Filtro** e expanda o âmbito da lista para **todas as aplicações.** Se quiser ver mais colunas, clique no botão **Colunas** para adicionar detalhes adicionais para as suas aplicações.
     >
     >

7.  clique no item de navegação **de acesso condicional.**

8.  clique na política que está interessado em inspecionar.

9.  Reveja que não existem condições, atribuições ou outras configurações específicas que possam estar a bloquear o acesso ao utilizador.

     >[!NOTE]
     >Pode desejar desativar temporariamente esta política para garantir que não está a afetar as inscrições. Para isso, desace a **política "Ativar"** para **Não** e clique no botão **Guardar.**
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Desativar uma política específica de acesso condicional

Para verificar ou validar uma única política de acesso condicional:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4.  clique em **aplicações Enterprise** no menu de navegação.

5.  clique no item de navegação **de acesso condicional.**

6.  clique na política que está interessado em inspecionar.

7.  Desative a política definindo a **política "Ativar"** para **não** e clique no botão **Guardar.**

## <a name="problems-with-application-consent"></a>Problemas com o consentimento da candidatura

O acesso à aplicação pode ser bloqueado porque a operação de consentimento das permissões adequadas não ocorreu. Seguem-se algumas formas de resolver problemas e resolver problemas de consentimento da aplicação:

-   [Realizar uma operação de consentimento ao nível do utilizador](#perform-a-user-level-consent-operation)

-   [Executar operação de consentimento ao nível do administrador para qualquer aplicação](#perform-administrator-level-consent-operation-for-any-application)

-   [Executar consentimento ao nível do administrador para um pedido de um único inquilino](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Executar consentimento ao nível do administrador para uma aplicação multi-inquilino](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Realizar uma operação de consentimento ao nível do utilizador

-   Para qualquer aplicação ativada por ID Connect aberta que solicite permissões, navegar no sinal da aplicação no ecrã executa um consentimento de nível de utilizador para a aplicação para o utilizador inscrito.

-   Se desejar fazê-lo programáticamente, consulte [solicitação de consentimento individual do utilizador.](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent)

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Executar operação de consentimento ao nível do administrador para qualquer aplicação

-   Para **apenas aplicações desenvolvidas utilizando o modelo de aplicação V1,** pode forçar este consentimento de nível de administrador a ocorrer adicionando "**?prompt=admin \_ consent**" ao final do sinal de uma aplicação em URL.

-   Para **qualquer aplicação desenvolvida utilizando o modelo de aplicação V2,** pode aplicar este consentimento ao nível do administrador para ocorrer seguindo as instruções ao abrigo do Pedido as permissões de uma secção de administração de **diretórios** de [Utilização do ponto final de consentimento administrativo](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Executar consentimento ao nível do administrador para um pedido de um único inquilino

-   Para **aplicações de inquilino único** que solicitem permissões (como as que está a desenvolver ou possui na sua organização), pode realizar uma operação de consentimento de nível administrativo em nome de todos os utilizadores, inscrevendo-se como Administrador Global e clicando no botão de **permissões grant** no topo do Registo de **Aplicações &gt; - Todas as Aplicações - &gt; Selecione uma app - &gt; Permissões Necessárias.** 

-   Para **qualquer aplicação desenvolvida utilizando o modelo de aplicação V1 ou V2,** pode aplicar este consentimento ao nível do administrador para ocorrer seguindo as instruções ao abrigo **do Pedido as permissões de uma** secção de administração de diretório de [Utilização do ponto de final de consentimento administrativo](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Executar consentimento ao nível do administrador para uma aplicação multi-inquilino

-   Para **aplicações multi-inquilinos** que solicitam permissões (como uma aplicação de terceiros, ou Microsoft, desenvolve), você pode executar uma operação **de consentimento de nível administrativo.** Inscreva-se como Administrador Global e clique no botão **de permissões grant** sob as **Aplicações da Empresa - Todas as &gt; Aplicações - &gt; Selecione uma App - &gt; Permissões** (disponível em breve).

-   Pode também aplicar este consentimento ao nível do administrador, seguindo as instruções ao abrigo do **Pedido, as permissões de uma** secção de administração de diretórios de [Utilização do ponto final](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)de consentimento de administração .

## <a name="next-steps"></a>Passos seguintes
[Usando o ponto final de consentimento administrativo](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)