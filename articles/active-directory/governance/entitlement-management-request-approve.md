---
title: Aprovar ou negar pedidos de acesso - Gestão de direitos da Azure AD
description: Saiba como utilizar o portal My Access para aprovar ou negar pedidos a um pacote de acesso na gestão de direitos do Diretório Ativo azure.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/27/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec63efe16f1120ca6e7d07c8917d8ad2b3a0e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261726"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Aprovar ou negar pedidos de acesso na gestão de direitos da AD Azure

Com a gestão de direitos da Azure AD, pode configurar políticas para exigir aprovação para pacotes de acesso e escolher um ou mais aprovadores. Este artigo descreve como os aprovadores designados podem aprovar ou negar pedidos de pacotes de acesso.

## <a name="open-request"></a>Pedido aberto

O primeiro passo para aprovar ou negar pedidos de acesso é encontrar e abrir o pedido de acesso enquanto se aguarda a aprovação. Há duas maneiras de abrir o pedido de acesso.

**Papel pré-requisito:** Aprovador

1. Procure um e-mail do Microsoft Azure que lhe peça para aprovar ou negar um pedido. Aqui está um e-mail de exemplo:

    ![Aprovar pedido de acesso a email saque](./media/entitlement-management-shared/approver-request-email.png)

1. Clique no **Link Aprovar ou negar** o link de pedido para abrir o pedido de acesso.

1. Inscreva-se no portal My Access.

Se não tiver o e-mail, poderá encontrar os pedidos de acesso pendentes da sua aprovação seguindo estes passos.

1. Inscreva-se no portal [https://myaccess.microsoft.com](https://myaccess.microsoft.com)My Access em .  (Para o Governo dos EUA, o domínio `myaccess.microsoft.us`no link do portal My Access será .)

1. No menu à esquerda, clique em **Aprovações** para ver uma lista de pedidos de acesso pendente de aprovação.

1. No separador **Pendente,** encontre o pedido.

## <a name="approve-or-deny-request"></a>Aprovar ou negar pedido

Depois de abrir um pedido de acesso pendente de aprovação, pode ver detalhes que o ajudarão a tomar uma decisão de aprovação ou negar.

**Papel pré-requisito:** Aprovador

1. Clique no link **'Ver'** para abrir o painel de pedidos de acesso.

1. Clique em **Detalhes** para ver detalhes sobre o pedido de acesso.

    Os detalhes incluem o nome, organização, acesso ao início e data de fim se fornecido, justificação do negócio, quando o pedido foi apresentado, e quando o pedido expirar.

1. Clique em **Aprovar** ou **Negar**.

1. Se necessário, insira uma razão.

    ![Meu portal de acesso - Pedido de acesso](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Clique **em Submeter** para submeter a sua decisão.

    Se uma política for configurada com vários aprovadores, apenas um aprovador precisa de tomar uma decisão sobre a aprovação pendente. Depois de um aprovador ter apresentado a sua decisão ao pedido de acesso, o pedido está concluído e já não está disponível para que os outros aprovadores aprovem ou neguem o pedido. Os outros aprovadores podem ver a decisão do pedido e o decisor no seu portal My Access. Neste momento, apenas a aprovação de uma fase única é apoiada.

    Se nenhum dos aprovadores configurados puder aprovar ou negar o pedido de acesso, o pedido expira após a duração do pedido configurado. O utilizador é notificado de que o seu pedido de acesso expirou e que precisa de reenviar o pedido de acesso.

## <a name="next-steps"></a>Passos seguintes

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Processo de pedido e notificações de e-mail](entitlement-management-process.md)
