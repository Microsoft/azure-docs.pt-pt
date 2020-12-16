---
title: Adicione um fluxo de utilizador de inscrição de autosserviço - Azure AD
description: Crie fluxos de utilizadores para apps que são construídas pela sua organização. Em seguida, os utilizadores que visitarem essa aplicação podem ganhar uma conta de hóspedes usando as opções configuradas no fluxo do utilizador.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f76f4a3e5fc87420c242c693e3c48a91244641e0
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560038"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app-preview"></a>Adicione um fluxo de utilizador de inscrição de autosserviço a uma aplicação (Preview)
> [!NOTE]
> A inscrição de self-service é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode criar fluxos de utilizadores para apps que são construídas pela sua organização. Associar o fluxo do utilizador a uma aplicação permite-lhe ativar o inscrição nessa aplicação. Pode escolher mais do que uma aplicação para estar associada ao fluxo do utilizador. Uma vez associado o fluxo do utilizador a uma ou mais aplicações, os utilizadores que visitarem essa aplicação poderão inscrever-se e obter uma conta de hóspedes utilizando as opções configuradas no fluxo do utilizador.

> [!NOTE]
> Pode associar fluxos de utilizadores a aplicações construídas pela sua organização. Os fluxos de utilizadores não podem ser utilizados para aplicações da Microsoft, como o SharePoint ou o Teams.

## <a name="before-you-begin"></a>Before you begin

### <a name="add-social-identity-providers-optional"></a>Adicionar fornecedores de identidade social (opcional)

A Azure AD é o fornecedor de identidade padrão para a inscrição de autosserviço. Isto significa que os utilizadores são capazes de se inscrever por padrão com uma conta AZure AD. Os fornecedores de identidade social também podem ser incluídos nestes fluxos de inscrição para apoiar as contas do Google e do Facebook.

- [Adicione o Facebook à sua lista de fornecedores de identidade social](facebook-federation.md)
- [Adicione o Google à sua lista de fornecedores de identidade social](google-federation.md)

> [!NOTE]
> Na pré-visualização atual, se um fluxo de utilizador de inscrição de autosserviço estiver associado a uma aplicação e enviar um convite a um utilizador para essa aplicação, o utilizador não poderá utilizar uma conta Gmail para resgatar o convite. Como uma solução alternativa, o utilizador pode passar pelo processo de inscrição de autosserviço. Ou, podem resgatar o convite acedendo a uma aplicação diferente ou usando o portal My Apps em https://myapps.microsoft.com .

### <a name="define-custom-attributes-optional"></a>Defina atributos personalizados (opcional)

Os atributos do utilizador são valores recolhidos do utilizador durante a inscrição de autosserviço. O Azure AD vem com um conjunto de atributos incorporado, mas pode criar atributos personalizados para uso no fluxo do seu utilizador. Também pode ler e escrever estes atributos utilizando a API do Gráfico microsoft. Consulte [Definir atributos personalizados para fluxos de utilizador.](user-flow-add-custom-attributes.md)

## <a name="enable-self-service-sign-up-for-your-tenant"></a>Ativar o autosserviço de inscrição para o seu inquilino

Antes de poder adicionar um fluxo de utilizador de inscrição autosserviço às suas aplicações, tem de ativar a funcionalidade para o seu inquilino. Depois de ativado, os controlos ficam disponíveis no fluxo do utilizador que permite associar o fluxo do utilizador a uma aplicação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Ao abrigo **dos serviços Azure,** selecione **Azure Ative Directory**.
3. Selecione **as definições do Utilizador** e, em seguida, em **utilizadores externos**, selecione **Gerir as definições de colaboração externa**.
4. Descreva o **autosserviço do hóspede ativado através de fluxos de utilizador (Pré-visualização)** para alternar para **Sim**.

   ![Ativar o autosserviço do hóspede](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)
5. Selecione **Guardar**.
## <a name="create-the-user-flow-for-self-service-sign-up"></a>Crie o fluxo do utilizador para o autosserviço de inscrição

Em seguida, irá criar o fluxo de utilizador para inscrição de autosserviço e adicioná-lo a uma aplicação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Ao abrigo **dos serviços Azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Selecione **os fluxos do utilizador (pré-visualização)** e, em seguida, selecione **Novo fluxo de utilizador**.

   ![Adicione um novo botão de fluxo de utilizador](media/self-service-sign-up-user-flow/new-user-flow.png)

5. Na página **Criar,** insira um **Nome** para o fluxo do utilizador. Note que o nome é automaticamente pré-fixado com **B2X_1_**.
6. Na lista **de fornecedores de identidade,** selecione um ou mais fornecedores de identidade que os seus utilizadores externos podem utilizar para iniciar sessão na sua aplicação. **O Azure Ative Directory Inscrição** é selecionado por padrão. (Ver [Antes de começar](#before-you-begin) mais cedo neste artigo para aprender a adicionar fornecedores de identidade.)
7. Nos **atributos do Utilizador,** escolha os atributos que pretende recolher do utilizador. Para obter atributos adicionais, **selecione Mostrar mais**. Por exemplo, selecione **Mostrar mais**, e depois escolha atributos e reclamações para **País/Região,** **Nome de Exibição** e **Código Postal**. Selecione **OK**.

   ![Criar uma nova página de fluxo de utilizador](media/self-service-sign-up-user-flow/create-user-flow.png)

8. Selecione **Criar**.
9. O novo fluxo de utilizador aparece na lista de **fluxos do Utilizador (Pré-visualização).** Se necessário, refresque a página.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>Selecione o layout do formulário de recolha de atributos

Pode escolher a ordem na qual os atributos são apresentados na página de inscrição. 

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.
2. Selecione **identidades externas**, selecione **fluxos de utilizador (pré-visualização)**.
3. Selecione o fluxo de utilizador de inscrição automática da lista.
4. Em **Personalizar, selecione** **layouts de página**.
5. Os atributos que escolheu para recolher estão listados. Para alterar a ordem de visualização, selecione um atributo e, em seguida, **selecione Mova-se** para cima , **Mova-se para cima**, ou **mova-se para a parte inferior**. 
6. Selecione **Guardar**.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>Adicione aplicações ao fluxo de utilizador de inscrição de autosserviço

Agora pode associar aplicações ao fluxo do utilizador.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Ao abrigo **dos serviços Azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. No **self-service sign up**, selecione **Fluxos de Utilizador (Pré-visualização)**.
5. Selecione o fluxo de utilizador de inscrição automática da lista.
6. No menu esquerdo, em **Utilização**, selecione **Aplicações**.
7. Selecione **Adicionar a aplicação**.

   ![Atribuir uma aplicação ao fluxo de utilizador](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. Selecione a inscrição na lista. Ou use a caixa de pesquisa para encontrar a aplicação e, em seguida, selecione-a.
9. Clique em **Selecionar**.

## <a name="next-steps"></a>Passos seguintes

- [Adicione o Google à sua lista de fornecedores de identidade social](google-federation.md)
- [Adicione o Facebook à sua lista de fornecedores de identidade social](facebook-federation.md)
- [Utilize conectores API para personalizar e alargar os fluxos do seu utilizador através de APIs web](api-connectors-overview.md)
- [Adicione fluxo de trabalho de aprovação personalizado ao fluxo do seu utilizador](self-service-sign-up-add-approvals.md)
