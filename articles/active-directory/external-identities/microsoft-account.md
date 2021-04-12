---
title: Provedor de identidade microsoft Account (MSA) em Azure AD
description: Utilize o Azure AD para permitir que um utilizador externo (convidado) inscreva-se nas suas aplicações AD Azure com a sua conta Microsoft (MSA).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d3380277d0e653fd5cb03069b7d91cb363dd95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693267"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>Provedor de identidade da Conta Microsoft (MSA) para identidades externas (pré-visualização)

> [!NOTE]
> O fornecedor de identidade da Conta Microsoft é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os seus utilizadores de hóspedes B2B podem utilizar as suas contas pessoais da Microsoft para a colaboração B2B sem mais configurações. Os utilizadores convidados podem resgatar os seus convites de colaboração B2B ou completar os fluxos de utilizador de inscrição usando a sua conta pessoal da Microsoft.

As contas da Microsoft são criadas por um utilizador para ter acesso a produtos e serviços na nuvem orientados para o consumidor, tais como Outlook, OneDrive, Xbox LIVE ou Microsoft 365. A conta é criada e armazenada no sistema de conta de identidade de consumidor da Microsoft que é gerido pela Microsoft.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Sindução de hóspedes usando contas microsoft

A Conta Microsoft está disponível na lista de fornecedores de identidades externas por padrão. Não é necessária nenhuma configuração adicional para permitir que os utilizadores convidados entrem com a sua conta Microsoft usando o fluxo de convite ou um fluxo de utilizador de inscrição de autosserviço.

![Conta Microsoft na lista de fornecedores de identidade](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>Conta Microsoft no fluxo de convites

Quando [convida um utilizador convidado](add-users-administrator.md) para a colaboração B2B, pode especificar a sua conta Microsoft como o endereço de e-mail que utilizarão para iniciar scontabilidade.

![Convidar a utilizar uma conta microsoft](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>Conta Microsoft em fluxos de utilizador de inscrição de autosserviço

A Conta Microsoft é uma opção de fornecedor de identidade para os fluxos de utilizador de inscrição de autosserviço. Os utilizadores podem inscrever-se nas suas aplicações utilizando as suas próprias contas Microsoft. Primeiro, terá de permitir a [inscrição de autosserviço](self-service-sign-up-user-flow.md) para o seu inquilino. Em seguida, pode configurar um fluxo de utilizador para a aplicação e selecionar a Conta Microsoft como uma das opções de inscrição.

![Conta Microsoft num fluxo de utilizador de inscrição de autosserviço](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>Passos seguintes

- [Adicionar utilizadores de colaboração Azure Ative Directory B2B](add-users-administrator.md)
- [Adicione inscrição de self-service a uma aplicação](self-service-sign-up-user-flow.md)