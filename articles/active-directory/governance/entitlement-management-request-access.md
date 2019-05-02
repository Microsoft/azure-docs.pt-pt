---
title: Pedir acesso a um pacote de acesso na gestão de direitos do Azure AD (pré-visualização) - Azure Active Directory
description: Saiba como utilizar o portal de acesso My para solicitar acesso a um pacote de acesso na gestão de direitos do Azure Active Directory (pré-visualização).
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
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39a50240b4360c5b4adcd6020c2b80b0f06315f7
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541559"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Pedir acesso a um pacote de acesso na gestão de direitos do Azure AD (pré-visualização)

> [!IMPORTANT]
> Gestão de direitos do Active Directory (Azure AD) do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-the-my-access-portal"></a>Inicie sessão portal do meu acesso

A primeira etapa é iniciar sessão portal do meu acesso onde pode solicitar acesso a um pacote de acesso.

**Função de pré-requisitos:** Requerente

1. Procure uma mensagem de e-mail ou uma mensagem do Gestor de projeto ou da empresa que estiver a trabalhar com. O e-mail deve incluir uma ligação para o pacote de acesso, terá acesso a. A ligação terá início com:

    `https://myaccess.microsoft.com`

1. Abra a ligação.

1. Inicie sessão no portal do meu acesso.

    Certifique-se de que utilize a sua conta organizacional. Se não souber, verifique com seu gerente de projeto ou empresa.

## <a name="request-an-access-package"></a>Pedido de um pacote de acesso

Depois de encontrar o pacote de acesso no portal do meu acesso, pode submeter um pedido.

**Função de pré-requisitos:** Requerente

1. Clique na marca de verificação para selecionar o pacote de acesso.

    ![Meu portal de acesso - pacotes de acesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Clique em **pedir acesso** para abrir o painel de acesso do pedido.

1. Se o **justificativa comercial** é apresentada a caixa, escreva uma justificação para precisar de acesso.

1. Se **pedir para o período específico?** está ativada, selecione **Sim** ou **não**.

1. Se necessário, especifique a data de início e data de fim.

    ![Meu portal de acesso – pedir acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Quando terminar, clique em **submeter** para submeter o pedido.

1. Clique em **histórico de pedidos** para ver uma lista dos seus pedidos e o estado.

    Se o pacote de acesso necessitar de aprovação, o pedido está agora num Estado de aprovação pendentes.

## <a name="cancel-a-request"></a>Cancelar um pedido

Se enviar um pedido de acesso e o pedido é ainda na **com aprovação pendente** de estado, pode cancelar o pedido.

**Função de pré-requisitos:** Requerente

1. No portal do meu acesso, no lado esquerdo, clique em **histórico de pedidos** para ver uma lista dos seus pedidos e o estado.

1. Clique nas **vista** ligação para o pedido de que pretende cancelar.

1. Se a solicitação for ainda na **com aprovação pendente** de estado, pode clicar em **pedido de cancelamento** para cancelar o pedido.

    ![Meu portal de acesso - pedido de cancelamento](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Clique em **histórico de pedidos** para confirmar se o pedido foi cancelado.

## <a name="select-a-policy"></a>Selecionar uma política

Se está a solicitar acesso a um pacote de acesso que tem várias políticas que se aplicam, poderá ser-lhe pedido para selecionar uma política. Por exemplo, um Gestor de pacotes de acesso pode configurar um pacote de acesso com duas políticas para dois grupos de empregados internos. A primeira diretiva pode permitir o acesso durante 60 dias e exigir a aprovação. A segunda política pode permitir o acesso de 2 dias e requer aprovação. Se encontrar neste cenário, tem de selecionar a política que pretende utilizar.

**Função de pré-requisitos:** Requerente

## <a name="next-steps"></a>Passos Seguintes

- [Aprovar ou negar pedidos de acesso](entitlement-management-request-approve.md)
- [Processo e notificações de e-mail do pedido](entitlement-management-process.md)
