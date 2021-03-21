---
title: Aprovar ou negar pedidos de acesso - Gestão de direitos AZure AD
description: Saiba como usar o portal My Access para aprovar ou negar pedidos a um pacote de acesso na gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: fddb3b171e5a26273cb2e0045f11e3a4dbb48c5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97655106"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Aprovar ou negar pedidos de acesso na gestão de direitos da AD Azure

Com a gestão de direitos AD Azure, pode configurar políticas para exigir aprovação para pacotes de acesso e escolher um ou mais aprovadores. Este artigo descreve como os aprovadores designados podem aprovar ou negar pedidos de pacotes de acesso.

## <a name="open-request"></a>Pedido aberto

O primeiro passo para aprovar ou negar pedidos de acesso é encontrar e abrir o pedido de acesso enquanto se aguarda a aprovação. Há duas maneiras de abrir o pedido de acesso.

**Papel pré-requisito:** Aprovador

1. Procure um e-mail da Microsoft Azure que lhe pede para aprovar ou negar um pedido. Aqui está um e-mail de exemplo:

    ![Aprovar pedido de acesso ao pacote de e-mail](./media/entitlement-management-shared/approver-request-email.png)

1. Clique no link **De aprovar ou negar** o link de pedido para abrir o pedido de acesso.

1. Inscreva-se no portal 'Acesso'.

Se não tiver o e-mail, pode encontrar os pedidos de acesso enquanto aguarda a sua aprovação seguindo estes passos.

1. Inscreva-se no portal 'O Acesso' em [https://myaccess.microsoft.com](https://myaccess.microsoft.com) .  (Para o Governo dos EUA, o domínio no link do portal My Access será `myaccess.microsoft.us` .)

1. No menu esquerdo, clique em **Aprovações** para ver uma lista de pedidos de acesso pendentes de aprovação.

1. No **separador Pendente,** encontre o pedido.

## <a name="view-requestors-answers-to-questions-preview"></a>Ver respostas do solicitador a perguntas (Pré-visualização)

1. Navegue para o **separador aprovações** no Meu Acesso.

1. Aceda ao pedido que gostaria de aprovar e clique em **detalhes.** Também pode clicar em **Aprovar** ou **Negar** se estiver pronto para tomar uma decisão.

1. Clique em **Detalhes do Pedido.**

    ![O meu portal de Acesso - Pedido de acesso- Clique nos detalhes do pedido](./media/entitlement-management-request-approve/requestor-information-request-details.png)

1. As informações fornecidas pelo solicitador estarão na parte inferior do painel.

    ![Screenshot mostra os detalhes do pedido](./media/entitlement-management-request-approve/requestor-information-requestor-answers.png)

1. Com base nas informações fornecidas pelo solicitador, pode então aprovar ou negar o pedido. Consulte os passos em Aprovar ou negar pedido de orientação.

## <a name="approve-or-deny-request"></a>Aprovar ou negar pedido

Depois de abrir um pedido de acesso pendente de aprovação, pode ver detalhes que o ajudarão a tomar uma decisão de aprovação ou negação.

**Papel pré-requisito:** Aprovador

1. Clique no link **Ver** para abrir o painel de pedidos de acesso.

1. Clique **em Detalhes** para ver detalhes sobre o pedido de acesso.

    Os detalhes incluem o nome do utilizador, organização, data de início e fim de acesso se for fornecida, justificação do negócio, quando o pedido foi apresentado, e quando o pedido expirar.

1. Clique **em Aprovar** ou **Negar**.

1. Se necessário, insira uma razão.

    ![A screenshot mostra a página onde aceita ou nega o pedido.](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Clique **em Submeter** para submeter a sua decisão.

    Se uma política estiver configurada com vários aprovadores, apenas um aprovador precisa de tomar uma decisão sobre a aprovação pendente. Depois de um aprovador ter apresentado a sua decisão ao pedido de acesso, o pedido está concluído e já não está disponível para que os restantes aprovadores aprovem ou neguem o pedido. Os outros aprovadores podem ver a decisão do pedido e o decisor no seu portal My Access. Neste momento, apenas é apoiada a aprovação de uma única fase.

    Se nenhum dos aprovadores configurados puder aprovar ou negar o pedido de acesso, o pedido expira após a duração do pedido configurado. O utilizador é notificado de que o seu pedido de acesso expirou e que precisa de reenviar o pedido de acesso.

## <a name="next-steps"></a>Passos seguintes

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Processo de pedido e notificações de e-mail](entitlement-management-process.md)
