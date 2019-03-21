---
title: Problema ao iniciar sessão para o site do painel de acesso | Documentos da Microsoft
description: Documentação de orientação para resolver os problemas que podem ser encontrados ao tentar iniciar sessão e utilizar o painel de acesso
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
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f7418c16d5d5312d2a9a7913da49941ef0a7870
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090462"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Problema ao iniciar sessão para o site do painel de acesso

O painel de acesso é um portal baseado na web que permite que um utilizador que tem uma conta profissional ou escolar no Azure Active Directory (Azure AD) para ver e iniciar aplicativos de baseados na nuvem que o administrador do Azure AD tem concederia a eles acesso a. Um utilizador que tem as edições do Azure AD também pode usar grupos self-service e capacidades de gestão de aplicações através do painel de acesso. O painel de acesso separado no portal do Azure e não requer que os utilizadores tenham uma subscrição do Azure.

Os utilizadores podem iniciar sessão painel de acesso se tiverem uma conta profissional ou escolar no Azure AD.

-   Os utilizadores podem ser autenticados pelo Azure AD diretamente.

-   Os utilizadores podem ser autenticados utilizando os serviços de Federação do Active Directory (AD FS).

-   Os utilizadores podem ser autenticados pelo Active Directory do Windows Server.

Se um utilizador tem uma subscrição do Azure ou do Office 365 e utiliza o portal do Azure ou um aplicativo do Office 365, poderão usar o painel de acesso diretamente sem a necessidade de iniciar sessão novamente. Para iniciar sessão com o nome de utilizador e palavra-passe da sua conta no Azure AD, é pedido aos utilizadores que não estão autenticados. Se a organização tiver configurado a Federação, escreva o nome de utilizador é suficiente.

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro 

-   Certifique-se de que o utilizador está a iniciar sessão para o **corrija URL**: <https://myapps.microsoft.com>

-   Certifique-se de que o navegador do usuário adicionou o URL para seus **sites fidedignos**

-   Certifique-se a conta de utilizador está **ativada** para inícios de sessão.

-   Certifique-se a conta de utilizador é **não bloqueadas.**

-   Certificar-se de que o usuário **palavra-passe não está expirado ou esquecido.**

-   Certifique-se **multi-factor Authentication** não está a bloquear o acesso dos utilizadores.

-   Certifique-se de um **política de acesso condicional** ou **Identity Protection** política não está a bloquear o acesso de utilizador.

-   Certifique-se de que um usuário **informações de contacto de autenticação** está atualizada para permitir que as políticas de acesso condicional ou de multi-factor Authentication sejam impostas.

-   Certifique-se para também tente limpar os cookies de seu navegador e tentar iniciar sessão novamente.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Requisitos de browser de reunião para o painel de acesso

O painel de acesso requer um browser que suporte a JavaScript e CSS ativou o. Para utilizar com base em palavra-passe de início de sessão único (SSO) no painel de acesso, a extensão do painel de acesso tem de estar instalada no navegador do usuário. Esta extensão é transferida automaticamente quando um usuário seleciona uma aplicação que está configurada para SSO baseado em palavra-passe.

Para SSO de palavra-passe, os navegadores do usuário final podem ser:

-   Internet Explorer 8, 9, 10, 11 – no Windows 7 ou posterior

-   Microsoft Edge no Windows 10 Anniversary Edition ou posterior 

-   Cromado, No Windows 7 ou posterior e no MacOS X ou posterior

-   Firefox 26.0 ou mais tarde, no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior


## <a name="problems-with-the-users-account"></a>Problemas com a conta de utilizador

Acesso ao painel de acesso pode ser bloqueado devido a um problema com a conta de utilizador. Seguem-se algumas formas de resolver problemas e resolver problemas com os usuários e suas configurações de conta:

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

10. Comunicar esta nova palavra-passe para o usuário, sejam necessárias para alterar esta palavra-passe durante o próximo início de sessão para o Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service

Para ativar a reposição de palavra-passe self-service, siga estes passos de implementação:

-   [Permitir que os utilizadores reponham as palavras-passe do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Permitir que os utilizadores reponham ou alterem as respetivas palavras-passe do Active Directory no local](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Verifique o estado de autenticação multifator de um utilizador

Para verificar o estado de autenticação multifator de um utilizador, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **utilizadores e grupos** no menu de navegação.

5. Clique em **todos os utilizadores**.

6. Clique nas **multi-factor Authentication** botão na parte superior do painel.

7. Uma vez a **Portal de administração do multi-factor Authentication** cargas, certifique-se estiver a utilizar o **utilizadores** separador.

8. Localize o utilizador na lista de utilizadores por procurar, filtrar ou ordenar.

9. Selecione o utilizador a partir da lista de utilizadores e **habilitar**, **desativar**, ou **impor** autenticação multifator conforme pretendido.

   >[!NOTE]
   >Se um utilizador estiver num **imposto** de estado, pode defini-los como **desativado** temporariamente para informá-los novamente para a respetiva conta. Assim que estão de volta, pode alterar seu estado para **ativado** novamente para que eles para voltar a registar as informações de contacto durante o próximo início de sessão. Em alternativa, pode seguir os passos a [verificar informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info) para verificar ou definir esses dados para eles.
   >
   >

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

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolverem o problema

Abra um pedido de suporte com as seguintes informações se estiver disponível:

-   ID de correlação de erro

-   UPN (endereço de e-mail do utilizador)

-   ID do inquilino

-   Tipo de navegador

-   Fuso horário e tempo/período de tempo durante o erro ocorre

-   Rastreios do fiddler

## <a name="next-steps"></a>Passos Seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)
