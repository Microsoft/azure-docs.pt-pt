---
title: Como as candidaturas aparecem no painel de acesso [ Microsoft Docs
description: Problemas sobre o porquê de uma aplicação aparecer no Painel de Acesso
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
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa8ea75cc7fda05326c802c25a91d025b66b5ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "65784415"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Como as aplicações aparecem no painel de acesso

O Painel de Acesso é um portal baseado na web, que permite a um utilizador com uma conta de trabalho ou escola no Azure Ative Directory (Azure AD) visualizar e iniciar aplicações baseadas na nuvem a que o administrador da AD Azure lhes concedeu acesso. Estas aplicações estão configuradas em nome do utilizador no portal Azure AD. O administrador pode fornecer a aplicação diretamente ao utilizador ou a um grupo que um utilizador faz parte do resultado que resulta na aplicação que aparece no Painel de Acesso do utilizador.

## <a name="general-issues-to-check-first"></a>Questões gerais para verificar primeiro

-   Se uma aplicação foi removida de um utilizador ou grupo do qual o utilizador é membro, tente iniciar sessão e sair novamente no Painel de Acesso do utilizador após alguns minutos para ver se a aplicação é removida.

-   Se uma licença foi removida de um utilizador ou grupo, o utilizador é membro disto pode demorar muito tempo, dependendo do tamanho e complexidade do grupo para alterações a serem feitas. Deixe um tempo extra antes de iniciar a sessão no Painel de Acesso.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemas relacionados com a atribuição de aplicações aos utilizadores

Um utilizador pode estar a ver uma aplicação no seu Painel de Acesso porque já lhe tinham sido atribuídas anteriormente. Seguem-se algumas formas de verificar:

-   [Verifique se um utilizador está atribuído à aplicação](#check-if-a-user-is-assigned-to-the-application)

-   [Verifique se um utilizador está sob uma licença relacionada com a aplicação](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Verifique se um utilizador está atribuído à aplicação

Para verificar se um utilizador está designado para a aplicação, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

6. **Procure** o nome da aplicação em questão.

7. clique em **Utilizadores e grupos**.

8. Verifique se o utilizador está atribuído à aplicação.

   * Se pretender retirar o utilizador da aplicação, **clique na linha** do utilizador e selecione **eliminar**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Verifique se um utilizador está sob uma licença relacionada com a aplicação

Para verificar as licenças atribuídas por um utilizador, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7. clique em **Licenças** para ver quais as licenças que o utilizador tem atualmente atribuído.

   * Se o utilizador for atribuído a uma licença do Office, isto permite que as aplicações do First Party Office apareçam no Painel de Acesso do utilizador.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemas relacionados com a atribuição de candidaturas a grupos

Um utilizador pode estar a ver uma aplicação no seu Painel de Acesso por fazer parte de um grupo que foi atribuído à aplicação. Seguem-se algumas formas de verificar:

-   [Verifique os membros do grupo de um utilizador](#check-a-users-group-memberships)

-   [Verifique se um utilizador é membro de um grupo atribuído a uma licença](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Verifique os membros do grupo de um utilizador

Para verificar a adesão de um grupo, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7. clique **em Grupos.**

8. Verifique se o utilizador faz parte de um Grupo atribuído à aplicação.

   * Se pretender remover o utilizador do grupo, **clique na linha** do grupo e selecione eliminar.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Verifique se um utilizador é membro de um grupo atribuído a uma licença

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique em **Utilizadores e grupos** no menu de navegação.

5. clique em **Todos os utilizadores**.

6. **Procure** o utilizador em que está interessado e **clique na linha** para selecionar.

7. clique **em Grupos.**

8. clique na linha de um grupo específico.

9. clique em **Licenças** para ver quais as licenças que o grupo lhe atribuiu.

   * Se o grupo for atribuído a uma licença do Office, isto poderá permitir que determinadas aplicações do First Party Office apareçam no Painel de Acesso do utilizador.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolverem a questão

abrir um bilhete de apoio com as seguintes informações, se disponível:

-   Id de erro de correlação

-   UPN (endereço de e-mail do utilizador)

-   ID do inquilino

-   Tipo de browser

-   Ofuso horário e tempo/tempo durante o erro ocorre

-   Traços de violinista

## <a name="next-steps"></a>Passos seguintes
[Gestão de Aplicações com Diretório Ativo Azure](what-is-application-management.md)
