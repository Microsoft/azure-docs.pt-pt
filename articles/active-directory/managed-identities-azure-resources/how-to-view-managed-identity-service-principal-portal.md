---
title: Ver diretor de serviço de uma identidade gerida no portal Azure - Azure AD
description: Instruções passo a passo para visualizar o principal de serviço de uma identidade gerida no portal Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b25f2691646192faf4d4c133835b456038abd94
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003740"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Ver o principal de serviço de uma identidade gerida no portal Azure

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código. 

Neste artigo, você aprende a ver o principal de serviço de uma identidade gerida usando o portal Azure.

 > [!NOTE] 
 > Os diretores de serviço são aplicações empresariais. 

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md)
- Se ainda não tem uma conta Azure, [inscreva-se numa conta gratuita.](https://azure.microsoft.com/free/)
- Ativar a identidade atribuída ao sistema numa máquina ou [aplicação](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity) [virtual.](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

## <a name="view-the-service-principal"></a>Ver o principal de serviço

Este procedimento demonstra como ver o principal de serviço de um VM com a identidade atribuída ao sistema (os mesmos passos se aplicam a uma aplicação).

1. Clique **em Azure Ative Directory** e, em seguida, clique em **aplicações enterprise**.
2. No **Tipo de Aplicação**, escolha **todas as aplicações** e, em seguida, clique em **Aplicar**.
3. Na caixa de filtro de pesquisa, digite o nome do recurso Azure que tenha gerido a identidade ativada ou escolha-a na lista apresentada.

   ![Ver diretor de serviço de identidade gerido no portal](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Passos seguintes

[Identidades geridas para os recursos do Azure](./overview.md)