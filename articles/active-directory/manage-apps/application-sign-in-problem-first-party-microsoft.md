---
title: Problemas ao iniciar sessão para uma aplicação da Microsoft | Documentos da Microsoft
description: Resolver problemas comuns enfrentados ao iniciar sessão Applications original da Microsoft com o Azure AD (como o Office 365)
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09f1ef137bbad50e72771c4368346716336c4faf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447103"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problemas ao iniciar sessão para uma aplicação da Microsoft

Applications da Microsoft (como o Office 365 Exchange, SharePoint, Yammer, etc.) são atribuídos e geridos de forma um pouco diferente do que o 3º aplicações de SaaS de terceiros ou outros aplicativos que integrar com o Azure AD para início de sessão único no.

Existem três formas principais que um utilizador pode obter acesso a uma aplicação publicada pela Microsoft.

-   Para aplicativos do Office 365 ou outros conjuntos de aplicações pagos, os utilizadores que terão acesso através de **atribuição de licença** diretamente à conta de utilizador ou através de um grupo com a nossa capacidade de atribuição de licença baseada em grupo.

-   Para aplicações que a Microsoft ou de uma entidade externa publica livremente para qualquer pessoa a utilizar, podem ser concedido acesso aos utilizadores através de **consentimento do utilizador**. This0 significa que iniciar sessão na aplicação com a respetiva conta escolar ou profissional do Azure AD e permitir que ele tem acesso a um conjunto limitado de dados na respetiva conta.

-   Para aplicações que a Microsoft ou um 3rd party publica livremente para qualquer pessoa a utilizar, podem também ser concedido acesso aos utilizadores através de **consentimento de administrador**. Isso significa que um administrador determinou que a aplicação pode ser utilizada por todas as pessoas na organização, para que inicie sessão para a aplicação com uma conta de Administrador Global e concedem acesso a todas as pessoas na organização.

Para resolver o seu problema, comece com o [áreas com problemas gerais com acesso a aplicações a serem considerados](#general-problem-areas-with-application-access-to-consider) e, em seguida, leia o passo a passo: Passos para resolver problemas de acesso do Microsoft Application para entrar em detalhes.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Áreas com problemas gerais com acesso à aplicação a considerar

Segue-se uma lista das áreas de problema geral que pode pormenorizar se tiver uma idéia de como iniciar, mas recomendamos que leia o passo a passo para começar rapidamente: Descrição Passo a Passo: Passos para resolver problemas de acesso do Microsoft Application.

-   [Problemas com a conta de utilizador](#problems-with-the-users-account)

-   [Problemas com grupos](#problems-with-groups)

-   [Problemas com políticas de acesso condicional](#problems-with-conditional-access-policies)

-   [Problemas com o consentimento da aplicação](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Passos para resolver problemas de acesso do Microsoft Application

Seguem-se alguns problemas comuns pessoal se deparar com seus usuários que não é possível iniciar sessão para uma aplicação da Microsoft.

-   Problemas gerais para verificar primeiro

  * Certifique-se de que o utilizador está a iniciar sessão para o **corrija URL** e não um URL de aplicação local.

  * Certifique-se a conta de utilizador é **não bloqueadas.**

  * Certifique-se de que o **conta de utilizador existe** no Azure Active Directory. [Verifique se existe uma conta de utilizador no Azure Active Directory](#problems-with-the-users-account)

  * Certifique-se a conta de utilizador está **ativada** para inícios de sessão. [Verifique o estado de conta de um utilizador](#problems-with-the-users-account)

  * Certificar-se de que o usuário **palavra-passe não está expirado ou esquecido.** [Repor palavra-passe de um usuário](#reset-a-users-password) ou [ativar a reposição de palavra-passe self-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

   * Certifique-se **multi-factor Authentication** não está a bloquear o acesso dos utilizadores. [Verificar o estado da autenticação multifator de um usuário](#check-a-users-multi-factor-authentication-status) ou [verificar informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info)

   * Certifique-se de um **política de acesso condicional** ou **Identity Protection** política não está a bloquear o acesso de utilizador. [Verificar uma política de acesso condicional específico](#problems-with-conditional-access-policies) ou [Verifique a política de acesso condicional de uma aplicação específica](#check-a-specific-applications-conditional-access-policy) ou [desativar uma política de acesso condicional específico](#disable-a-specific-conditional-access-policy)

   * Certifique-se de que um usuário **informações de contacto de autenticação** está atualizada para permitir que as políticas de acesso condicional ou de multi-factor Authentication sejam impostas. [Verificar o estado da autenticação multifator de um usuário](#check-a-users-multi-factor-authentication-status) ou [verificar informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info)

-   Para **Microsoft** **aplicativos que exigem uma licença** (como o Office 365), aqui estão alguns problemas específicos para verificar assim que tiver descartado os problemas gerais acima:

   * Certifique-se o utilizador ou tem um **licença atribuída.** [Verificar as licenças atribuídas de um usuário](#check-a-users-assigned-licenses) ou [verificar as licenças atribuídas de um grupo](#check-a-groups-assigned-licenses)

   * Se a licença for **atribuídos a um** **grupo estático**, certifique-se de que o **utilizador é membro** desse grupo. [Verifique as associações de grupo de um utilizador](#check-a-users-group-memberships)

   * Se a licença for **atribuídos a um** **grupo dinâmico**, certifique-se de que o **regra de grupo dinâmica está definida corretamente**. [Verificar os critérios de associação de um grupo dinâmico](#check-a-dynamic-groups-membership-criteria)

   * Se a licença for **atribuídos a um** **grupo dinâmico**, certifique-se de que o grupo dinâmico tem **termina o processamento** e que a respetiva associação a **utilizador é membro**  (pode demorar algum tempo). [Verifique as associações de grupo de um utilizador](#check-a-users-group-memberships)

   *  Depois de efetuar-se de que a licença é atribuída, certificar-se de que está a licença **não expirado**.

   *  Certifique-se a licença é **para a aplicação** estão a aceder.

-   Para **Microsoft** **aplicações que não requerem uma licença**, aqui estão algumas outras coisas para verificar:

   * Se a aplicação está a solicitar **permissões ao nível do usuário** (por exemplo "aceder a caixa de correio deste utilizador"), certifique-se de que o utilizador tem sessão iniciada para a aplicação e efetuou uma **deoperaçãodeconsentimentoaoníveldoutilizador** para permitir que a aplicação aceder aos seus dados.

   * Se a aplicação está a solicitar **permissões ao nível do administrador** (por exemplo "aceder a caixas de correio de todos os utilizadores"), certifique-se de que um Administrador Global efetuou uma **operação de consentimento de nível de administrador no nome de todos os utilizadores** na organização.

## <a name="problems-with-the-users-account"></a>Problemas com a conta de utilizador

Acesso de aplicação pode ser bloqueado devido a um problema com um utilizador que está atribuído à aplicação. Seguem-se algumas formas de resolver problemas e resolver problemas com os usuários e suas configurações de conta:

-   [Verifique se existe uma conta de utilizador no Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Verifique o estado de conta de um utilizador](#check-a-users-account-status)

-   [Repor palavra-passe de um utilizador](#reset-a-users-password)

-   [Ativar a reposição de palavras-passe self-service](#enable-self-service-password-reset)

-   [Verifique o estado de autenticação multifator de um utilizador](#check-a-users-multi-factor-authentication-status)

-   [Verificar informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info)

-   [Verifique as associações de grupo de um utilizador](#check-a-users-group-memberships)

-   [Verificar as licenças atribuídas de um utilizador](#check-a-users-assigned-licenses)

-   [Atribuir uma licença de um utilizador](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Verifique se existe uma conta de utilizador no Azure Active Directory

Para verificar se a conta de um utilizador estiver presente, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Verifique as propriedades do objeto de utilizador para ter certeza de que eles procuram conforme esperado e não existem dados estão em falta.

### <a name="check-a-users-account-status"></a>Verifique o estado de conta de um utilizador

Para verificar o estado de conta de um utilizador, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **perfil**.

8.  Sob **configurações** Certifique-se de que **bloquear início de sessão** está definida como **não**.

### <a name="reset-a-users-password"></a>Repor palavra-passe de um utilizador

Para repor a palavra-passe de um utilizador, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique nas **Repor palavra-passe** botão na parte superior do painel do utilizador.

8.  Clique nas **Repor palavra-passe** botão a **Repor palavra-passe** painel que aparece.

9.  Copiar o **palavra-passe temporária** ou **introduza uma nova palavra-passe** para o utilizador.

10. Comunicar esta nova palavra-passe para o usuário, sejam necessárias para alterar esta palavra-passe durante o próximo início de sessão no Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service

Para ativar a reposição de palavra-passe self-service, siga os passos de implementação abaixo:

-   [Permitir que os utilizadores reponham as palavras-passe do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Permitir que os utilizadores reponham ou alterem as respetivas palavras-passe do Active Directory no local](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Verifique o estado de autenticação multifator de um utilizador

Para verificar o estado de autenticação multifator de um utilizador, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  Clique nas **multi-factor Authentication** botão na parte superior do painel.

7.  Uma vez a **portal de administração do multi-factor Authentication** cargas, certifique-se estiver a utilizar o **utilizadores** separador.

8.  Localize o utilizador na lista de utilizadores por procurar, filtrar ou ordenar.

9.  Selecione o utilizador a partir da lista de utilizadores e **habilitar**, **desativar**, ou **impor** autenticação multifator conforme pretendido.

  * **Nota**: Se um utilizador estiver num **imposto** de estado, pode defini-los como **desativado** temporariamente para informá-los novamente para a respetiva conta. Assim que estão de volta, pode alterar seu estado para **ativado** novamente para que eles para voltar a registar as informações de contacto durante o próximo início de sessão. Em alternativa, pode seguir os passos a [verificar informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info) para verificar ou definir esses dados para eles.

### <a name="check-a-users-authentication-contact-info"></a>Verificar informações de contacto de autenticação de um utilizador

Para verificar a informação de contacto de autenticação de um utilizador utilizada para autenticação multifator, acesso condicional, proteção de identidade e de reposição de palavra-passe, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **perfil**.

8.  Desloque para baixo até **informações de contacto de autenticação**.

9.  **Revisão** os dados registados para o utilizador e a atualização, conforme necessário.

### <a name="check-a-users-group-memberships"></a>Verifique as associações de grupo de um utilizador

Para verificar as associações de grupo de um utilizador, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **grupos** ver a quais grupos o usuário é membro.

### <a name="check-a-users-assigned-licenses"></a>Verificar as licenças atribuídas de um utilizador

Para verificar as licenças atribuídas de um utilizador, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **licenças** ver quais licenças atualmente o utilizador tenha atribuído.

### <a name="assign-a-user-a-license"></a>Atribuir uma licença de um utilizador 

Para atribuir uma licença a um utilizador, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **licenças** ver quais licenças atualmente o utilizador tenha atribuído.

8.  Clique nas **atribuir** botão.

9.  Selecione **um ou mais produtos** na lista de produtos disponíveis.

10. **Opcional** clique a **opções de atribuição** item para atribuir especificamente com produtos. Clique em **Ok** quando for concluído.

11. Clique nas **atribuir** botão para atribuir essas licenças para este utilizador.

## <a name="problems-with-groups"></a>Problemas com grupos

Acesso de aplicação pode ser bloqueado devido a um problema com um grupo que está atribuído à aplicação. Seguem-se algumas formas pode resolver problemas e resolver problemas com grupos e associações de grupo:

-   [Verifique a associação a um grupo](#check-a-groups-membership)

-   [Verificar os critérios de associação de um grupo dinâmico](#check-a-dynamic-groups-membership-criteria)

-   [Verificar as licenças atribuídas de um grupo](#check-a-groups-assigned-licenses)

-   [Reprocessar licenças de um grupo](#reprocess-a-groups-licenses)

-   [Atribuir uma licença um grupo](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Verifique a associação a um grupo

Para verificar uma associação de grupo, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os grupos**.

6.  **Pesquisa** para o grupo de interesse e **clique na linha** para selecionar.

7.  Clique em **membros** para consultar a lista de utilizadores atribuídos a este grupo.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Verificar os critérios de associação de um grupo dinâmico 

Para verificar os critérios de associação de um grupo dinâmico, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os grupos**.

6.  **Pesquisa** para o grupo de interesse e **clique na linha** para selecionar.

7.  Clique em **regras de associação dinâmica.**

8.  Reveja os **simples** ou **avançadas** definida para este grupo de regra e certifique-se de que o utilizador que pretende ser um membro deste grupo atende a esses critérios.

### <a name="check-a-groups-assigned-licenses"></a>Verificar as licenças atribuídas de um grupo

Para verificar as licenças atribuídas de um grupo, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os grupos**.

6.  **Pesquisa** para o grupo de interesse e **clique na linha** para selecionar.

7.  Clique em **licenças** ver quais licenças atualmente o grupo tiver atribuído.

### <a name="reprocess-a-groups-licenses"></a>Reprocessar licenças de um grupo

Reprocessar licenças atribuídas de um grupo, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os grupos**.

6.  **Pesquisa** para o grupo de interesse e **clique na linha** para selecionar.

7.  Clique em **licenças** ver quais licenças atualmente o grupo tiver atribuído.

8.  Clique nas **Reprocessar** botão para se certificar de que as licenças atribuídas aos membros deste grupo estão atualizadas. Isto pode demorar muito tempo, dependendo do tamanho e a complexidade do grupo.

   >[!NOTE]
   >Para fazer isso com mais rapidez, considere atribuir temporariamente uma licença para o usuário diretamente. [Atribuir um utilizador uma licença](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Atribuir uma licença um grupo

Para atribuir uma licença a um grupo, siga estes passos:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os grupos**.

6.  **Pesquisa** para o grupo de interesse e **clique na linha** para selecionar.

7.  Clique em **licenças** ver quais licenças atualmente o grupo tiver atribuído.

8.  Clique nas **atribuir** botão.

9.  Selecione **um ou mais produtos** na lista de produtos disponíveis.

10. **Opcional** clique a **opções de atribuição** item para atribuir especificamente com produtos. Clique em **Ok** quando for concluído.

11. Clique nas **atribuir** botão para atribuir essas licenças para este grupo. Isto pode demorar muito tempo, dependendo do tamanho e a complexidade do grupo.

   >[!NOTE]
   >Para fazer isso com mais rapidez, considere atribuir temporariamente uma licença para o usuário diretamente. [Atribuir um utilizador uma licença](#problems-with-application-consent).
   > 
   >

## <a name="problems-with-conditional-access-policies"></a>Problemas com políticas de acesso condicional

### <a name="check-a-specific-conditional-access-policy"></a>Verificar uma política de acesso condicional específico

Para verificar ou validar uma política de acesso condicional único:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação.

5.  Clique nas **acesso condicional** item de navegação.

6.  Clique em política estiver interessado em inspecionar.

7.  Revisão que existem sem condições específicas, atribuições ou outras definições que podem estar a bloquear o acesso dos utilizadores.

   >[!NOTE]
   >Pode pretender desativar temporariamente esta política para garantir que ele não está afetando inícios de sessão. Para tal, defina o **ativar política** alternar para **não** e clique no **guardar** botão.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Verifique a política de acesso condicional de uma aplicação específica

Para verificar ou validar um único aplicativo atualmente configurado a política de acesso condicional:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação.

5.  Clique em **todos os aplicativos**.

6.  Procure a aplicação que está interessado, ou o utilizador está a tentar iniciar sessão nome de exibição do aplicativo ou o id da aplicação.

     >[!NOTE]
     >Se não vir a aplicação que procura, clique nas **filtro** botão e expanda o escopo da lista para **todos os aplicativos**. Se quiser ver mais colunas, clique nas **colunas** botão para adicionar detalhes adicionais para as suas aplicações.
     >
     >

7.  Clique nas **acesso condicional** item de navegação.

8.  Clique em política estiver interessado em inspecionar.

9.  Revisão que existem sem condições específicas, atribuições ou outras definições que podem estar a bloquear o acesso dos utilizadores.

     >[!NOTE]
     >Pode pretender desativar temporariamente esta política para garantir que ele não está afetando inícios de sessão. Para tal, defina o **ativar política** alternar para **não** e clique no **guardar** botão.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Desativar uma política de acesso condicional específico

Para verificar ou validar uma política de acesso condicional único:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação.

5.  Clique nas **acesso condicional** item de navegação.

6.  Clique em política estiver interessado em inspecionar.

7.  Desativar a política, definindo a **ativar política** alternar para **não** e clique no **guardar** botão.

## <a name="problems-with-application-consent"></a>Problemas com o consentimento da aplicação

Acesso da aplicação pode ser bloqueado porque não ocorreu a operação de consentimento de permissões adequadas. Seguem-se algumas formas pode resolver problemas e resolução de problemas de consentimento da aplicação:

-   [Executar uma operação de consentimento ao nível do utilizador](#perform-a-user-level-consent-operation)

-   [Efetuar a operação de consentimento de nível de administrador para qualquer aplicação](#perform-administrator-level-consent-operation-for-any-application)

-   [Executar o consentimento de nível de administrador para uma aplicação de inquilino único](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Executar o consentimento de nível de administrador para uma aplicação multi-inquilino](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Executar uma operação de consentimento ao nível do utilizador

-   Para qualquer aplicação com capacidade para abrir ID Connect que solicita as permissões, navegando até o ecrã de início de sessão do aplicativo executa um consentimento ao nível do utilizador para a aplicação para o utilizador com sessão iniciada.

-   Se desejar fazer isso por meio de programação, consulte [pedir consentimento do usuário individual](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Efetuar a operação de consentimento de nível de administrador para qualquer aplicação

-   Para **apenas os aplicativos desenvolvidos com o modelo de aplicativo V1**, pode forçar esta consentimento de nível de administrador para ocorrer ao adicionar "**? = prompt de administrador\_consentimento**" ao final de um início de sessão da aplicação no URL.

-   Para **qualquer aplicativo desenvolvido usando o modelo de aplicação V2**, pode impor este consentimento de nível de administrador para ocorrer ao seguir as instruções no **solicitar as permissões de um administrador do diretório** secção do [utilizando o ponto de extremidade de consentimento do admin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Executar o consentimento de nível de administrador para uma aplicação de inquilino único

-   Para **aplicações de inquilino único** pedir que as permissões (como aqueles que está desenvolvendo ou proprietário na sua organização), pode realizar uma **consentimento de nível administrativo** operação em nome de todos utilizadores por iniciar sessão como um Administrador Global e clicando na **conceder permissões** botão na parte superior do **Registro do aplicativo -&gt; todas as aplicações -&gt; Selecione uma aplicação -&gt; Permissões obrigatórias** painel.

-   Para **qualquer aplicativo desenvolvido usando o modelo de aplicação V1 ou V2**, pode impor este consentimento de nível de administrador para ocorrer ao seguir as instruções no **solicitar as permissões de um administrador do diretório**  secção do [utilizando o ponto de extremidade de consentimento do admin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Executar o consentimento de nível de administrador para uma aplicação multi-inquilino

-   Para **aplicativos de multilocação** esse pedir permissões (como uma aplicação de uma aplicação de terceiros ou Microsoft, desenvolve), pode realizar uma **consentimento de nível administrativo** operação. Inicie sessão como um Administrador Global e clicar no **conceder permissões** botão sob a **aplicações empresariais -&gt; todas as aplicações -&gt; selecionar uma aplicação -&gt; permissões**  painel (disponível em breve).

-   Também pode impor este consentimento de nível de administrador para ocorrer ao seguir as instruções no **solicitar as permissões de um administrador do** secção [utilizando o ponto de extremidade de consentimento do admin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Passos Seguintes
[Utilizar o ponto final de consentimento de administrador](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

