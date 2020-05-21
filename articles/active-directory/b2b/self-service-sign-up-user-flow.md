---
title: Adicione um fluxo de utilizador de inscrição auto-útil - Azure AD
description: Federate com o Facebook para permitir que utilizadores externos (hóspedes) inscrevam-se nas suas aplicações Azure AD com as suas próprias contas de Facebook.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd94fffded8c0e5d7b120993f069b042c2b19b6c
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83712354"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app-preview"></a>Adicione um fluxo de utilizador de inscrição de autosserviço a uma aplicação (Pré-visualização)
|     |
| --- |
| A inscrição em self-service é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Pode criar fluxos de utilizadores para apps que são construídas pela sua organização. Associar o fluxo do utilizador a uma aplicação permite-lhe ativar a inscrição nessa aplicação. Pode escolher mais do que uma aplicação para estar associada ao fluxo do utilizador. Uma vez associado o fluxo do utilizador a uma ou mais aplicações, os utilizadores que visitarem essa aplicação poderão inscrever-se e obter uma conta de hóspedes utilizando as opções configuradas no fluxo do utilizador.

> [!NOTE]
> Pode associar fluxos de utilizadores a aplicações construídas pela sua organização. Os fluxos de utilizadores não podem ser utilizados para aplicações da Microsoft, como o SharePoint ou Teams.

## <a name="before-you-begin"></a>Antes de começar

### <a name="add-social-identity-providers-optional"></a>Adicionar fornecedores de identidade social (opcional)

A Azure AD é o fornecedor de identidade padrão para a inscrição de self-service. Isto significa que os utilizadores são capazes de se inscrever por padrão com uma conta Azure AD. Os fornecedores de identidade social também podem ser incluídos nestes fluxos de inscrição para suportar as contas do Google e do Facebook.

- [Adicione o Facebook à sua lista de fornecedores de identidade social](facebook-federation.md)
- [Adicione o Google à sua lista de fornecedores de identidade social](google-federation.md)

> [!NOTE]
> Na pré-visualização atual, se um fluxo de utilizador de autosserviço estiver associado a uma aplicação e enviar um convite a um utilizador para essa aplicação, o utilizador não poderá utilizar uma conta do Gmail para resgatar o convite. Como uma suver, o utilizador pode passar pelo processo de inscrição de self-service. Ou podem resgatar o convite acedendo a uma aplicação diferente ou utilizando o portal My Apps em https://myapps.microsoft.com .

### <a name="define-custom-attributes-optional"></a>Definir atributos personalizados (opcional)

Os atributos do utilizador são valores recolhidos do utilizador durante o autosserviço de inscrição. O Azure AD vem com um conjunto incorporado de atributos, mas pode criar atributos personalizados para uso no fluxo do utilizador. Também pode ler e escrever estes atributos utilizando a API do Microsoft Graph. Ver [Definir atributos personalizados para fluxos de utilizador](user-flow-add-custom-attributes.md).

## <a name="enable-self-service-sign-up-for-your-tenant"></a>Ativar inscrição de self-service para o seu inquilino

Antes de poder adicionar um fluxo de utilizador de inscrição de autosserviço às suas aplicações, tem de ativar a funcionalidade para o seu inquilino. Depois de ativado, os controlos ficam disponíveis no fluxo do utilizador que lhe permitem associar o fluxo do utilizador a uma aplicação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Sob **os serviços azure,** selecione **Azure Ative Directory**.
3. Selecione **as definições do Utilizador**, e, em seguida, em utilizadores **externos,** selecione **Gerir as definições**de colaboração externa .
4. Detete o **autosserviço do hóspede Enable através dos fluxos do utilizador (Pré-visualização)** para **Sim**.

   ![Ativar o autosserviço do hóspede](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)

## <a name="create-the-user-flow-for-self-service-sign-up"></a>Criar o fluxo do utilizador para a inscrição de autosserviço

Em seguida, irá criar o fluxo de utilizador para a inscrição de self-service e adicioná-lo a uma aplicação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Sob **os serviços azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Selecione **os fluxos de utilizador (Pré-visualização)** e, em seguida, selecione **o novo fluxo do utilizador**.

   ![Adicione um novo botão de fluxo do utilizador](media/self-service-sign-up-user-flow/new-user-flow.png)

5. Na página **Criar,** introduza um **Nome** para o fluxo do utilizador. Note que o nome é automaticamente pré-fixado com **B2X_1_**.
6. Na lista de fornecedores de **Identidade,** selecione um ou mais fornecedores de identidade que os seus utilizadores externos possam utilizar para iniciar sessão na sua aplicação. **O Azure Ative Directory Sign up** é selecionado por defeito. (Ver [Antes de começar](#before-you-begin) mais cedo neste artigo para aprender a adicionar fornecedores de identidade.)
7. Sob os **atributos do Utilizador,** escolha os atributos que pretende recolher do utilizador. Para atributos adicionais, selecione **Mostrar mais**. Por exemplo, selecione **Mostrar mais**, e depois escolher atributos e reivindicações para **País/Região,** **Nome de Exibição**, e **Código Postal**. Selecione **OK**.

   ![Criar uma nova página de fluxo de utilizador](media/self-service-sign-up-user-flow/create-user-flow.png)

8. Selecione **Criar**.
9. O novo fluxo de utilizador aparece na lista de fluxos de **utilizador (Pré-visualização).** Se necessário, refresque a página.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>Selecione o layout do formulário de recolha de atributos

Pode escolher a ordem na qual os atributos são apresentados na página de inscrição. 

1. No [portal Azure,](https://portal.azure.com)selecione **Azure Ative Directory**.
2. Selecione **Identidades Externas,** selecione **fluxos de utilizador (Pré-visualização)**.
3. Selecione o fluxo de utilizador de inscrição de autosserviço da lista.
4. Em **Personalizar,** selecione **layouts de página.**
5. Os atributos que escolheu para recolher estão listados. Para alterar a ordem do visor, selecione um atributo e, em seguida, selecione **Mover para cima,** **mover-se para baixo,** **mover-se para a parte superior**, ou **mover-se para baixo**.
6. Selecione **Guardar**.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>Adicione aplicações ao fluxo de utilizador de inscrição auto-útil

Agora pode associar aplicações ao fluxo do utilizador.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Sob **os serviços azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Em **função de autosserviço, selecione** **fluxos de utilizador (Pré-visualização)**.
5. Selecione o fluxo de utilizador de inscrição de autosserviço da lista.
6. No menu esquerdo, em **Utilização,** selecione **Aplicações**.
7. Selecione **Adicionar aplicação**.

   ![Atribuir uma aplicação ao fluxo do utilizador](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. Selecione a aplicação da lista. Ou use a caixa de pesquisa para encontrar a aplicação e, em seguida, selecione-a.
9. Clique em **Selecionar**.

## <a name="next-steps"></a>Próximos passos

- [Adicione o Google à sua lista de fornecedores de identidade social](google-federation.md)
- [Adicione o Facebook à sua lista de fornecedores de identidade social](facebook-federation.md)
