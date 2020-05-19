---
title: Adicione atributos personalizados para fluxos de utilizadores de AD Azure
description: Aprenda a personalizar os atributos para os fluxos de utilizador de inscrição de autosserviço.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f187cc47d9c64c8257cc097734fa41e10629f1c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597451"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>Definir atributos personalizados para fluxos de utilizador (Pré-visualização)
|     |
| --- |
| A funcionalidade de atributos personalizados do utilizador é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Para cada pedido, pode ter diferentes requisitos para a informação que pretende recolher durante o sessão. A Azure AD vem com um conjunto incorporado de informação armazenada em atributos, tais como Nome Dado, Apelido, Cidade e Código Postal. Com o Azure AD, pode estender o conjunto de atributos armazenados numa conta de hóspedes quando o utilizador externo se inscreve através de um fluxo de utilizador.

Pode criar atributos personalizados no portal Azure e usá-los nos fluxos de utilizador de inscrição de autosserviço. Também pode ler e escrever estes atributos utilizando o [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api). O Microsoft Graph API suporta a criação e atualização de um utilizador com atributos de extensão. Os atributos de extensão na API do gráfico são nomeados utilizando a convenção `extension_<Application-client-id>_attributename` . Por exemplo:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Pode encontrar a `<Application-client-id>` página de **registos** da App ao lado do ID da **Aplicação (cliente).** Esta identificação é específica para o seu inquilino.

## <a name="create-a-custom-attribute"></a>Criar um atributo personalizado

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Sob **os serviços azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Selecione **atributos personalizados do utilizador (Pré-visualização)**. Os atributos de utilizador disponíveis estão listados.

   ![Selecione atributos de utilizador para inscrição](media/user-flow-add-custom-attributes/user-attributes.png)

5. Para adicionar um atributo, selecione **Adicionar**.
6. No **painel adicionar um atributo,** introduza os seguintes valores:

   - **Nome** - Forneça um nome para o atributo personalizado (por exemplo, "Shoesize").
   - **Tipo** de dados - Escolha um tipo de dados **(String,** **Boolean,** ou **Int**).
   - **Descrição** - Opcionalmente, introduza uma descrição do atributo personalizado para uso interno. Esta descrição não é visível para o utilizador.

   ![Adicione um atributo](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Selecione **Criar**.

O atributo personalizado está agora disponível na lista de atributos do utilizador e para utilização nos fluxos de utilizador. Um atributo personalizado só é criado na primeira vez que é usado em qualquer fluxo de utilizador, e não quando o adiciona à lista de atributos do utilizador.

Uma vez criado um novo utilizador utilizando um fluxo de utilizador que utiliza o atributo personalizado recém-criado, o objeto pode ser consultado no [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Deverá agora ver **shoeSize** na lista de atributos recolhidos durante a jornada de inscrição e vê-lo no token enviado de volta para a sua aplicação. Para obter informações sobre como incluir estas reclamações no token enviado de volta para a sua aplicação, consulte [configurar pedidos opcionais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-directory-extension-optional-claims) de extensão de diretório

## <a name="next-steps"></a>Passos seguintes

[Adicione um fluxo de utilizador de inscrição de autosserviço a uma aplicação](self-service-sign-up-user-flow.md)