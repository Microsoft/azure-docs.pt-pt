---
title: Suporte para Azure Active Directory B2C | Microsoft Docs
description: Como arquivar solicitações de suporte para Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a4bb82665ecd9d04c097a127f46814bb7198f393
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227182"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: Solicitações de suporte de arquivo
Você pode arquivar solicitações de suporte para Azure Active Directory (Azure AD) B2C no portal do Azure usando as seguintes etapas:

1. Mude de seu locatário B2C para outro locatário que tenha uma assinatura do Azure associada a ele. Normalmente, o último é seu locatário do funcionário ou o locatário padrão criado para você quando você se inscreveu para uma assinatura do Azure. Para saber mais, veja [como uma assinatura do Azure está relacionada ao Azure ad](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    ![portal do Azure com seleção de locatário realçada](./media/active-directory-b2c-support/support-switch-dir.png)

1. Depois de alternar os locatários, clique em **ajuda + suporte**.

    ![Bloco de ajuda + suporte realçado em portal do Azure](./media/active-directory-b2c-support/support-support.png)

1. Clique em **nova solicitação de suporte**.

    ![Novo bloco de solicitação de suporte realçado em portal do Azure](./media/active-directory-b2c-support/support-new.png)

1. Na folha **noções básicas** , use esses detalhes e clique em **Avançar**.

    * O **tipo de problema** é **técnico**.
    * Escolha a **assinatura**apropriada.
    * O **serviço** está **Active Directory**.
    * Escolha o **plano de suporte**apropriado. Se você não tiver um, poderá se inscrever para obter um [aqui](https://azure.microsoft.com/support/plans/).

     ![Página noções básicas com o botão Avançar realçado em portal do Azure](./media/active-directory-b2c-support/support-basics.png)

1. Na folha **problema** , use esses detalhes e clique em **Avançar**.

    * Escolha o nível de **severidade** apropriado.
    * O **tipo de problema** é **B2C**.
    * Escolha a **categoria**apropriada.
    * Descreva seu problema no campo de **detalhes** . Forneça detalhes como o nome do locatário B2C, a descrição do problema, as mensagens de erro, as IDs de correlação (se disponível) e assim por diante.
    * No campo período de **tempo** , forneça a data e a hora (incluindo o fuso horário) que o problema ocorreu.
    * Em **upload de arquivo**, carregue todas as capturas de tela e arquivos que você acredita ajudar a resolver o problema.

     ![Página do problema com o botão Avançar realçado em portal do Azure](./media/active-directory-b2c-support/support-problem.png)

1. Na folha **informações de contato** , adicione suas informações de contato. Clique em **Criar**.

    ![Página de informações de contato com o botão criar realçado no portal](./media/active-directory-b2c-support/support-contact.png)

1. Depois de enviar sua solicitação de suporte, você pode monitorá-la clicando em **ajuda + suporte** no quadro inicial e, em seguida, **Gerenciar solicitações de suporte**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Problema conhecido: Arquivando uma solicitação de suporte no contexto de um locatário B2C

Se você tiver perdido a etapa 2 descrita acima e tentar criar uma solicitação de suporte no contexto de seu locatário B2C, você verá o erro a seguir.

> [!IMPORTANT]
> Não tente se inscrever para uma nova assinatura do Azure em seu locatário B2C.

![Você não tem um erro de assinatura exibido no portal do Azure](./media/active-directory-b2c-support/support-no-sub.png)
