---
title: Aprovar ou negar pedidos de acesso na gestão de direitos do Azure AD (pré-visualização) - Azure Active Directory
description: Saiba como utilizar o portal de acesso My para aprovar ou recusar pedidos para um pacote de acesso na gestão de direitos do Azure Active Directory (pré-visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b2d07638f6c6f153ee3640273fbee5e56df0ab2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541529"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Aprovar ou negar pedidos de acesso na gestão de direitos do Azure AD (pré-visualização)

> [!IMPORTANT]
> Gestão de direitos do Active Directory (Azure AD) do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Com a gestão de direitos do Azure AD, pode configurar políticas para exigir a aprovação para pacotes de acesso e escolha um ou mais aprovadores. Este artigo descreve como designados aprovadores pode aprovar ou recusar pedidos para os pacotes de acesso.

## <a name="open-request"></a>Pedido aberto

A primeira etapa para aprovar ou recusar pedidos de acesso é para encontrar e abrir o pedido de acesso com aprovação pendente. Existem duas formas de abrir o pedido de acesso.

**Função de pré-requisitos:** Aprovador

1. Procure uma mensagem de e-mail do Microsoft Azure que lhe pede para aprovar ou recusar um pedido. Eis um e-mail de exemplo:

    ![Aprovar o pedido para aceder ao e-mail do pacote](./media/entitlement-management-shared/email-approve-request.png)

1. Clique nas **aprovar ou negar o pedido** link para abrir o pedido de acesso.

1. Inicie sessão no portal do meu acesso.

Se não tiver o e-mail, pode encontrar os pedidos de acesso com aprovação pendente ao seguir estes passos.

1. Inicie sessão no portal do meu acesso em [ https://myaccess.microsoft.com ](https://myaccess.microsoft.com).

1. No menu à esquerda, clique em **aprovações** para ver uma lista de pedidos de acesso com aprovação pendente.

1. Sobre o **pendente** separador, encontrar o pedido.

## <a name="approve-or-deny-request"></a>Aprovar ou negar pedido

Depois de abrir um pedido de acesso com aprovação pendente, pode ver detalhes que irão ajudá-lo a fazer um aprovar ou negar a decisão.

**Função de pré-requisitos:** Aprovador

1. Clique nas **vista** link para abrir o painel de pedido de acesso.

1. Clique em **detalhes** para ver detalhes sobre o pedido de acesso.

    Os detalhes incluem o nome do utilizador, organização, aceder a data de início e fim se for fornecido, justificação de negócio, quando o pedido foi submetido, e quando o pedido irá expirar.

1. Clique em **aprovar** ou **negar**.

1. Se necessário, introduza um motivo.

    ![Meu portal de acesso - pedido de acesso](./media/entitlement-management-shared/my-access-approve-request.png)

1. Clique em **submeter** para submeter a sua decisão.

    Se uma política estiver configurada com vários aprovadores, apenas um aprovador precisa tomar uma decisão sobre a aprovação pendente. Depois de um aprovador submeteu seus decisão ao pedido de acesso, o pedido é concluído e já não está disponível para os outros aprovadores aprovar ou negar o pedido. Os outros aprovadores podem ver a decisão de pedido e o tomador de decisões no seu portal de acesso My. Neste momento, a aprovação de fase única só é suportada.

    Se nenhum dos aprovadores configurados conseguem aprovar ou recusar o pedido de acesso, o pedido expira após a duração do pedido configurado. O utilizador é notificado de que o seu pedido de acesso tiver expirado e que têm de voltar a submeter o pedido de acesso.

## <a name="next-steps"></a>Passos Seguintes

- [Pedir acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Processo e notificações de e-mail do pedido](entitlement-management-process.md)
