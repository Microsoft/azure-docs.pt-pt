---
title: Exibir e gerenciar solicitações de um pacote do Access no gerenciamento de direitos do Azure AD (versão prévia) – Azure Active Directory
description: Saiba como exibir, reprocessar e cancelar solicitações para um pacote de acesso em Azure Active Directory gerenciamento de direitos (versão prévia).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7211fea4781904b9d97428ecf00b57970e4b57d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430265"
---
# <a name="view-and-manage-requests-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Exibir e gerenciar solicitações de um pacote do Access no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> O gerenciamento de direitos do Azure Active Directory (AD do Azure) está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

No gerenciamento de direitos do Azure AD, você pode ver quem solicitou pacotes de acesso, sua política e status. Este artigo descreve como exibir, reprocessar e cancelar solicitações para um pacote de acesso.

## <a name="view-requests"></a>Exibir solicitações

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **solicitações**.

1. Clique em uma solicitação específica para ver detalhes adicionais.

    ![Lista de solicitações para um pacote de acesso](./media/entitlement-management-access-package-requests/requests-list.png)

## <a name="view-a-requests-delivery-errors"></a>Exibir os erros de entrega de uma solicitação

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **solicitações**.

1. Selecione a solicitação que você deseja exibir.

    Se a solicitação tiver erros de entrega, o status da solicitação será sem **entrega** ou **parcialmente entregue**.

    Se houver erros de entrega, no painel de detalhes da solicitação, haverá uma contagem de erros de entrega.

1. Clique na contagem para ver todos os erros de entrega da solicitação.

## <a name="reprocess-a-request"></a>Reprocessar uma solicitação

Se uma solicitação encontrar um erro, você poderá reprocessar a solicitação para tentar novamente. Você só pode reprocessar uma solicitação que tenha um status de **entrega com falha** ou **parcialmente entregue** e uma data de conclusão inferior a uma semana.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **solicitações**.

1. Clique na solicitação que você deseja reprocessar.

1. No painel detalhes da solicitação, clique em **Reprocessar solicitação**.

    ![Reprocessar uma solicitação com falha](./media/entitlement-management-access-package-requests/reprocess-request.png)

## <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente

Você só pode cancelar uma solicitação pendente que ainda não foi entregue ou cuja entrega falhou.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **solicitações**.

1. Clique na solicitação que você deseja cancelar.

1. No painel detalhes da solicitação, clique em **Cancelar solicitação**.

## <a name="next-steps"></a>Passos seguintes

- [Alterar as configurações de solicitação e aprovação para um pacote de acesso](entitlement-management-access-package-request-policy.md)
- [Exibir, adicionar e remover atribuições para um pacote de acesso](entitlement-management-access-package-assignments.md)