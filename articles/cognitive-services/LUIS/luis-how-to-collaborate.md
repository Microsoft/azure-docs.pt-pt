---
title: Colaborar com outras pessoas
titleSuffix: Language Understanding - Azure Cognitive Services
description: Um proprietário da aplicação pode adicionar os funcionários para a aplicação. Estes colaboradores podem modificar o modelo, formar e publicar a aplicação.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/12/2019
ms.author: diberry
ms.openlocfilehash: f23212a854fb37dda89fd2bf6b223cf0dc69526b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60198775"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Como gerir os autores e colaboradores 

Um proprietário da aplicação pode adicionar os funcionários para a aplicação. Estes colaboradores podem modificar o modelo, formar e publicar a aplicação. 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>Adicionar acesso do funcionário

Uma aplicação tem um autor único, o proprietário, mas pode ter muitos funcionários. Para permitir que os funcionários para editar a sua aplicação LUIS, tem de adicionar o e-mail que utilizam para aceder ao portal de LUIS para a lista de colaboradores. Depois que serem adicionados, a aplicação é apresentada no seu portal de LUIS.

1. Selecione **Manage** a partir do menu superior direito, em seguida, selecione **colaboradores** no menu à esquerda.

2. Selecione **adicionar acesso do funcionário** da barra de ferramentas.

    [![Adicionar acesso do funcionário](./media/luis-how-to-collaborate/add-collaborator.png "adicionar acesso do funcionário")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. Introduza o endereço de e-mail, que o acesso do funcionário utiliza para iniciar sessão portal do LUIS.

    ![Adicionar endereço de e-mail do colaborador](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>Transferência de propriedade

Enquanto o LUIS atualmente não suporta a transferência de propriedade, pode exportar a sua aplicação e outro utilizador de LUIS pode importar a aplicação. Pode haver pequenas diferenças na pontuações de LUIS entre as duas aplicações. 

## <a name="azure-active-directory-resources"></a>Recursos do Azure Active Directory

Se usar [do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) na sua organização, compreensão de idiomas (LUIS) necessita da permissão para as informações sobre o acesso dos seus utilizadores quando pretendem utilizar o LUIS. Os recursos de que necessita de LUIS são mínimos. 

Veja a descrição detalhada quando tentar inscrever-se com uma conta que tem o consentimento de administrador ou não necessita de consentimento de administrador, como o consentimento de administrador:

* Permite-lhe iniciar sessão na aplicação com a sua conta organizacional e permitir que a aplicação leia o seu perfil. Também permite que a aplicação Leia informações básicas da empresa. Isso dá a permissão de LUIS para ler dados de perfil básicas, como ID de utilizador, e-mail, nome
* Permite que a aplicação ver e atualizar os seus dados, mesmo quando não está a utilizar a aplicação. É necessária a permissão para atualizar o token de acesso do utilizador.


## <a name="azure-active-directory-tenant-user"></a>Utilizador de inquilino do Azure Active Directory

LUIS usa o fluxo de consentimento do padrão do Azure Active Directory (Azure AD). 

O administrador de inquilinos deve trabalhar diretamente com o utilizador que precisa de acesso concedido a utilizar o LUIS no Azure AD. 

* Em primeiro lugar, o utilizador inicia sessão LUIS e vê a caixa de diálogo pop-up que precisam de aprovação de administrador. O usuário entra em contacto com o administrador de inquilino antes de continuar. 
* Em segundo lugar, o administrador de inquilino iniciar sessão numa LUIS e vê um diálogo de pop-up de fluxo de consentimento. Esta é a caixa de diálogo que o administrador tem de conceder permissão para o utilizador. Assim que o administrador de aceita a permissão, o utilizador é continuar com os LUIS. Se o administrador de inquilino não irá iniciar sessão no LUIS, o administrador pode aceder [consentimento](https://account.activedirectory.windowsazure.com/r#/applications) para LUIS, mostrado na captura de ecrã seguinte. Tenha em atenção a lista está filtrada para itens que incluem o nome `LUIS`.

![Permissão do Azure active directory ao Web site da aplicação](./media/luis-how-to-collaborate/tenant-permissions.png)

Se o administrador de inquilinos apenas desejar determinados utilizadores para utilizar o LUIS, existem duas soluções possíveis:
* Dar "consentimento do admin" (de consentir a todos os utilizadores do Azure AD), mas, em seguida, definido como "Sim" a "atribuição utiliz. necessária" nas propriedades de aplicativo empresarial e, finalmente atribuir/adicione apenas os utilizadores desejados para a aplicação. Com esse método, o administrador ainda está a fornecer o "consentimento do admin" para a aplicação, no entanto, é possível controlar os utilizadores que possam acessá-la.
* Uma segunda solução, é através de [Azure AD Graph API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) para fornecer consentimento a cada utilizador específico. 

Saiba mais sobre os utilizadores do Active Directory do Azure e o consentimento: 
* [Restringir a sua aplicação](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) para um conjunto de utilizadores

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Contas de utilizador com várias mensagens de e-mail para funcionários

Se adicionar colaboradores para uma aplicação do LUIS, está a especificar o endereço de e-mail exatamente que um funcionário tem de utilizar o LUIS como um colaborador. Enquanto o Azure Active Directory (Azure AD) permite um único utilizador ter mais de uma conta de e-mail utilizada alternadamente, LUIS exige que o utilizador iniciar sessão com o endereço de e-mail especificado na lista do funcionário.

