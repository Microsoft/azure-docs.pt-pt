---
title: Como as aplicações aparecem no painel de acesso / Microsoft Docs
description: Resolução de problemas por que uma aplicação está a aparecer no Painel de Acesso
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
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22ba0709f4c5ca2294f515bdf1a96bff661b7293
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760835"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Como as aplicações aparecem no painel de acesso

O Painel de Acesso é um portal baseado na Web, que permite a um utilizador com uma conta de trabalho ou escola em Azure Ative Directory (Azure AD) visualizar e iniciar aplicações baseadas na nuvem a que o administrador da AD Azure lhes concedeu acesso. Estas aplicações estão configuradas em nome do utilizador no portal AZure AD. O administrador pode providenciar a aplicação ao utilizador diretamente ou a um grupo que um utilizador faz parte de resultar na aplicação que aparece no Painel de Acesso do utilizador.

## <a name="general-issues-to-check-first"></a>Questões gerais para verificar primeiro

-   Se uma aplicação foi removida de um utilizador ou grupo, o utilizador é membro, tente entrar e sair novamente no Painel de Acesso do utilizador após alguns minutos para ver se a aplicação é removida.

-   Se uma licença foi removida de um utilizador ou grupo, o utilizador é membro deste pode demorar muito tempo, dependendo do tamanho e complexidade do grupo para que as alterações sejam feitas. Deixe um tempo extra antes de iniciar a sessão no Painel de Acesso.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemas relacionados com a atribuição de aplicações aos utilizadores

Um utilizador pode estar a ver uma aplicação no seu Painel de Acesso por ter sido previamente atribuída à sua designação. Seguem-se algumas formas de verificar:

-   [Verifique se um utilizador está atribuído à aplicação](#check-if-a-user-is-assigned-to-the-application)

-   [Verifique se um utilizador está sob uma licença relacionada com a aplicação](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Verifique se um utilizador está atribuído à aplicação

Para verificar se um utilizador é atribuído à aplicação, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

6. **Procure** o nome do requerimento em questão.

7. clique em **Utilizadores e grupos**.

8. Verifique se o seu utilizador está atribuído à aplicação.

   * Se pretender remover o utilizador da aplicação, **clique na linha** do utilizador e selecione **eliminar**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Verifique se um utilizador está sob uma licença relacionada com a aplicação

Para verificar as licenças atribuídas a um utilizador, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7. clique em **Licenças** para ver quais as licenças que o utilizador atualmente atribuiu.

   * Se o utilizador for designado para uma licença do Office, isto permite que as aplicações do First Party Office apareçam no Painel de Acesso do utilizador.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemas relacionados com a atribuição de candidaturas a grupos

Um utilizador pode estar a ver uma aplicação no seu Painel de Acesso porque faz parte de um grupo que foi atribuído à aplicação. Seguem-se algumas formas de verificar:

-   [Verifique os membros do grupo de um utilizador](#check-a-users-group-memberships)

-   [Verifique se um utilizador é membro de um grupo designado para uma licença](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Verifique os membros do grupo de um utilizador

Para verificar a adesão de um grupo, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7. clique em **Grupos.**

8. Verifique se o seu utilizador faz parte de um Grupo atribuído à aplicação.

   * Se pretender remover o utilizador do grupo, **clique na linha** do grupo e selecione eliminar.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Verifique se um utilizador é membro de um grupo designado para uma licença

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7. clique em **Grupos.**

8. clique na linha de um grupo específico.

9. clique em **Licenças** para ver quais as licenças que o grupo lhe atribuiu.

   * Se o grupo for designado para uma licença do Office, isto pode permitir que determinadas aplicações do First Party Office apareçam no Painel de Acesso do utilizador.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolver a questão

abrir um bilhete de apoio com as seguintes informações, se disponível:

-   ID de erro de correlação

-   UPN (endereço de e-mail do utilizador)

-   ID do inquilino

-   Tipo de browser

-   Fuso horário e tempo/prazo durante o erro ocorre

-   Traços de violino

## <a name="next-steps"></a>Passos seguintes
[Gestão de Aplicações com Diretório Ativo Azure](what-is-application-management.md)
