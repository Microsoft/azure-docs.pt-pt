---
title: Como as aplicações são apresentadas no painel de acesso | Documentos da Microsoft
description: Resolver problemas de por que uma aplicação é apresentada no painel de acesso
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
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: fccf671edbc121501a17975be303453a798837e4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58122204"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Como as aplicações são apresentadas no painel de acesso

O painel de acesso é um portal baseado na web, que permite que um utilizador com uma conta profissional ou escolar no Azure Active Directory (Azure AD) para ver e iniciar os aplicativos baseados na nuvem que o administrador do Azure AD lhe concedeu-lhes acesso. Esses aplicativos são configurados em nome do utilizador no portal do Azure AD. O administrador pode aprovisionar a aplicação para o usuário diretamente ou a um grupo de um utilizador é parte do resultante do aplicativo que aparecem no painel de acesso do utilizador.

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Se um aplicativo tiver sido removido de um utilizador ou grupo que o utilizador é membro do, tente iniciar sessão e reduzir novamente no painel de acesso do utilizador após alguns minutos para ver se o aplicativo é removido.

-   Se uma licença tiver sido removida de um utilizador ou grupo, o utilizador é que membro de isto poderá demorar muito tempo, dependendo do tamanho e a complexidade do grupo para que as alterações sejam feitas. Permita tempo adicional antes de iniciar sessão no painel de acesso.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemas relacionados com a atribuição de aplicações para utilizadores

Um utilizador pode estar a ver uma aplicação no seu painel de acesso porque tinha sido anteriormente atribuídos ao mesmo. Seguem-se algumas formas de verificar:

-   [Verificar se um utilizador é atribuído à aplicação](#check-if-a-user-is-assigned-to-the-application)

-   [Verificar se um utilizador é sob uma licença relacionadas com a aplicação](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Verificar se um utilizador é atribuído à aplicação

Para verificar se um utilizador é atribuído à aplicação, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

6. **Pesquisa** para o nome do aplicativo em questão.

7. Clique em **utilizadores e grupos**.

8. Verifique se o utilizador é atribuído à aplicação.

   * Se pretender remover o utilizador da aplicação, **clique na linha** do utilizador e selecione **eliminar**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Verificar se um utilizador é sob uma licença relacionadas com a aplicação

Para verificar as licenças atribuídas de um utilizador, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **utilizadores e grupos** no menu de navegação.

5. Clique em **todos os utilizadores**.

6. **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7. Clique em **licenças** ver quais licenças atualmente o utilizador tenha atribuído.

   * Se o utilizador é atribuído a uma licença do Office, isso permite que aplicativos do Office de terceiros primeiro a aparecer no painel de acesso do utilizador.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemas relacionados com a atribuição de aplicações a grupos

Um utilizador pode estar a ver uma aplicação no seu painel de acesso porque fazem parte de um grupo que tenha sido atribuído a aplicação. Seguem-se algumas formas de verificar:

-   [Verifique as associações de grupo de um utilizador](#check-a-users-group-memberships)

-   [Verificar se um utilizador é um membro de um grupo atribuído a uma licença](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Verifique as associações de grupo de um utilizador

Para verificar uma associação de grupo, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **utilizadores e grupos** no menu de navegação.

5. Clique em **todos os utilizadores**.

6. **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7. Clique em **grupos.**

8. Verifique se o utilizador faz parte de um grupo atribuída à aplicação.

   * Se pretender remover o utilizador do grupo **clique na linha** da eliminação de grupo e selecione.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Verificar se um utilizador é um membro de um grupo atribuído a uma licença

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **utilizadores e grupos** no menu de navegação.

5. Clique em **todos os utilizadores**.

6. **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7. Clique em **grupos.**

8. Clique na linha de um grupo específico.

9. Clique em **licenças** ver que as licenças do grupo atribuiu a ele.

   * Se o grupo for atribuído a uma licença do Office, isso pode permitir determinados aplicativos do Office de terceiros primeiro a aparecer no painel de acesso do utilizador.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se efetuar estes passos de resolução de problemas não o resolve o problema

Abra um pedido de suporte com as seguintes informações se estiver disponível:

-   ID de correlação de erro

-   UPN (endereço de e-mail do utilizador)

-   ID do inquilino

-   Tipo de navegador

-   Fuso horário e tempo/período de tempo durante o erro ocorre

-   Rastreios do fiddler

## <a name="next-steps"></a>Passos Seguintes
[Managing Applications with Azure Active Directory](what-is-application-management.md) (Gerir Aplicações com o Azure Active Directory)
