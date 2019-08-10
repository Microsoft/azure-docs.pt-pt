---
title: Colaborar com outras pessoas – LUIS
titleSuffix: Azure Cognitive Services
description: Um proprietário da aplicação pode adicionar os funcionários para a aplicação. Estes colaboradores podem modificar o modelo, formar e publicar a aplicação.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 2aaedb27d6875335b34c757505981f1a58462541
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932825"
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

Se você usar [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) em sua organização, reconhecimento vocal (Luis) precisará de permissão para as informações sobre o acesso dos usuários quando quiserem usar o Luis. Os recursos de que necessita de LUIS são mínimos. 

Veja a descrição detalhada quando tentar inscrever-se com uma conta que tem o consentimento de administrador ou não necessita de consentimento de administrador, como o consentimento de administrador:

* Permite-lhe iniciar sessão na aplicação com a sua conta organizacional e permitir que a aplicação leia o seu perfil. Também permite que a aplicação Leia informações básicas da empresa. Isso concede ao LUIS permissão para ler dados básicos de perfil, como ID de usuário, email, nome
* Permite que a aplicação ver e atualizar os seus dados, mesmo quando não está a utilizar a aplicação. A permissão é necessária para atualizar o token de acesso do usuário.


## <a name="azure-active-directory-tenant-user"></a>Utilizador de inquilino do Azure Active Directory

LUIS usa o fluxo de consentimento do padrão do Azure Active Directory (Azure AD). 

O administrador de inquilinos deve trabalhar diretamente com o utilizador que precisa de acesso concedido a utilizar o LUIS no Azure AD. 

* Em primeiro lugar, o utilizador inicia sessão LUIS e vê a caixa de diálogo pop-up que precisam de aprovação de administrador. O usuário entra em contacto com o administrador de inquilino antes de continuar. 
* Em segundo lugar, o administrador de inquilino iniciar sessão numa LUIS e vê um diálogo de pop-up de fluxo de consentimento. Esta é a caixa de diálogo que o administrador tem de conceder permissão para o utilizador. Assim que o administrador de aceita a permissão, o utilizador é continuar com os LUIS. Se o administrador de locatários não entrar no LUIS, o administrador poderá acessar o [consentimento](https://account.activedirectory.windowsazure.com/r#/applications) do Luis, mostrado na captura de tela a seguir. Observe que a lista é filtrada para itens que incluem `LUIS`o nome.

![Permissão do Azure active directory ao Web site da aplicação](./media/luis-how-to-collaborate/tenant-permissions.png)

Se o administrador de locatários quiser que apenas determinados usuários usem LUIS, haverá algumas soluções possíveis:
* Fornecendo o "consentimento do administrador" (consentimento para todos os usuários do Azure AD), mas, em seguida, defina como "Sim" a "atribuição de usuário necessária" em Propriedades do aplicativo empresarial e, por fim, atribua/adicione somente os usuários desejados ao aplicativo. Com esse método, o administrador ainda está fornecendo "consentimento do administrador" para o aplicativo, no entanto, é possível controlar os usuários que podem acessá-lo.
* Uma segunda solução é usar o [Azure AD API do Graph](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) para fornecer consentimento para cada usuário específico. 

Saiba mais sobre os usuários e o consentimento do Azure Active Directory: 
* [Restringir seu aplicativo](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) a um conjunto de usuários

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Contas de utilizador com várias mensagens de e-mail para funcionários

Se adicionar colaboradores para uma aplicação do LUIS, está a especificar o endereço de e-mail exatamente que um funcionário tem de utilizar o LUIS como um colaborador. Enquanto o Azure Active Directory (Azure AD) permite um único utilizador ter mais de uma conta de e-mail utilizada alternadamente, LUIS exige que o utilizador iniciar sessão com o endereço de e-mail especificado na lista do funcionário.

