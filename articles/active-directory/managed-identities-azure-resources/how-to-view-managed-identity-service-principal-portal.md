---
title: Como ver o principal de serviço de uma identidade gerida no portal do Azure
description: Obter instruções passo a passo para visualizar o principal de serviço de uma identidade gerida no portal do Azure.
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
ms.openlocfilehash: 5f6139062c4d302284cc653606ae838206d3691a
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444990"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Ver o principal de serviço de uma identidade gerida no portal do Azure

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, irá aprender a ver o principal de serviço de uma identidade gerida no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md).
- Se ainda não tiver uma conta do Azure, [Inscreva-se numa conta gratuita](https://azure.microsoft.com/free/).
- Ativar [identidade do sistema atribuído numa máquina virtual](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) ou [aplicação](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Ver o principal de serviço

Este procedimento explica como ver o principal de serviço de uma VM com a identidade de sistema atribuído ativada (os mesmos passos aplicam-se para uma aplicação).

1. Clique em **do Azure Active Directory** e, em seguida, clique em **aplicações empresariais**.
2. Sob **tipo de aplicação**, escolha **todos os aplicativos**.
3. Na caixa de filtro de pesquisa, escreva o nome da VM ou aplicação que tem a identidade gerida ativada ou escolhê-lo na lista apresentada.

   ![Ver o principal de serviço de identidade gerida no portal](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Passos Seguintes

[Identidades geridas para os recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview)

