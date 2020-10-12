---
title: Adicione atributos personalizados aos fluxos de inscrição de self-service - Azure AD
description: Saiba como personalizar os atributos para os fluxos de utilizador de inscrição de autosserviço.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a186b682a3a506f0f373776dd66e6592bc6036ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87908938"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>Definir atributos personalizados para fluxos de utilizador (Pré-visualização)

> [!NOTE]
> A funcionalidade de atributos personalizados do utilizador é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Para cada aplicação, poderá ter diferentes requisitos para a informação que pretende recolher durante a inscrição. A Azure AD vem com um conjunto incorporado de informações armazenadas em atributos, tais como Nome Dado, Apelido, Cidade e Código Postal. Com o Azure AD, pode estender o conjunto de atributos armazenados numa conta de hóspedes quando o utilizador externo se inscreve através de um fluxo de utilizador.

Pode criar atributos personalizados no portal Azure e usá-los nos fluxos de utilizador de inscrição de autosserviço. Também pode ler e escrever estes atributos utilizando a [API](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api)do Gráfico microsoft . A Microsoft Graph API suporta a criação e atualização de um utilizador com atributos de extensão. Os atributos de extensão na API do gráfico são nomeados através da convenção `extension_<extensions-app-id>_attributename` . Por exemplo:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

O `<extensions-app-id>` é específico para o seu inquilino. Para encontrar este identificador, navegue para o Azure Ative Directory > Registos de App > Todas as aplicações. Procure a aplicação que começa com "aad-extensions-app" e selecione-a. Na página geral da aplicação, note o ID da Aplicação (cliente).

## <a name="create-a-custom-attribute"></a>Criar um atributo personalizado

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Ao abrigo **dos serviços Azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Selecione **atributos de utilizador personalizados (pré-visualização)**. Os atributos disponíveis do utilizador estão listados.

   ![Selecione os atributos do utilizador para a inscrição](media/user-flow-add-custom-attributes/user-attributes.png)

5. Para adicionar um atributo, **selecione Adicionar**.
6. No painel **de atributos Adicionar,** introduza os seguintes valores:

   - **Nome** - Forneça um nome para o atributo personalizado (por exemplo, "Shoesize").
   - **Tipo de dados** - Escolha um tipo de dados **(String,** **Boolean,** ou **Int).**
   - **Descrição** - Opcionalmente, introduza uma descrição do atributo personalizado para uso interno. Esta descrição não é visível para o utilizador.

   ![Adicionar um atributo](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Selecione **Criar**.

O atributo personalizado está agora disponível na lista de atributos do utilizador e para utilização nos fluxos do seu utilizador. Um atributo personalizado só é criado na primeira vez que é utilizado em qualquer fluxo de utilizador, e não quando o adiciona à lista de atributos do utilizador.

Uma vez criado um novo utilizador utilizando um fluxo de utilizador que utiliza o atributo personalizado recém-criado, o objeto pode ser consultado no [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Deverá agora ver **o ShoeSize** na lista de atributos recolhidos durante a jornada de inscrição no objeto do utilizador. Pode ligar para a API do gráfico da sua aplicação para obter os dados deste atributo depois de adicionado ao objeto do utilizador.

## <a name="next-steps"></a>Passos seguintes

[Adicione um fluxo de utilizador de inscrição de autosserviço a uma aplicação](self-service-sign-up-user-flow.md)
