---
title: Como exibir a entidade de serviço de uma identidade gerenciada no portal do Azure
description: Instruções passo a passo para exibir a entidade de serviço de uma identidade gerenciada no portal do Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a8db0254f85980022115cab5d73f7f329b57001
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827559"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Exibir a entidade de serviço de uma identidade gerenciada no portal do Azure

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, você aprenderá a exibir a entidade de serviço de uma identidade gerenciada usando o portal do Azure.

 > [!NOTE] 
 > As entidades de serviço são aplicativos empresariais. 

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md).
- Se você ainda não tiver uma conta do Azure, [Inscreva-se para obter uma conta gratuita](https://azure.microsoft.com/free/).
- Habilite [a identidade atribuída pelo sistema em uma máquina virtual](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) ou [aplicativo](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Exibir a entidade de serviço

Este procedimento demonstra como exibir a entidade de serviço de uma VM com a identidade atribuída pelo sistema habilitada (as mesmas etapas se aplicam a um aplicativo).

1. Clique em **Azure Active Directory** e em **aplicativos empresariais**.
2. Em **tipo de aplicativo**, escolha **todos os aplicativos**.
3. Na caixa filtro de pesquisa, digite o nome da VM ou do aplicativo que tem identidade gerenciada habilitada ou escolha-a na lista apresentada.

   ![Exibir entidade de serviço de identidade gerenciada no portal](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Passos seguintes

[Identidades geridas para os recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview)

