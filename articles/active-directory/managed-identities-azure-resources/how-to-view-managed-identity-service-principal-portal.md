---
title: Ver diretor de serviço de uma identidade gerida no portal Azure - Azure AD
description: Instruções passo a passo para visualizar o principal de serviço de uma identidade gerida no portal Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 296dae5d2c628de66c72155661586624e02f81be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608369"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Ver o principal de serviço de uma identidade gerida no portal Azure

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código. 

Neste artigo, você aprende a ver o principal de serviço de uma identidade gerida usando o portal Azure.

 > [!NOTE] 
 > Os diretores de serviço são aplicações empresariais. 

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md)
- Se ainda não tem uma conta Azure, [inscreva-se numa conta gratuita.](https://azure.microsoft.com/free/)
- Ativar a identidade atribuída ao sistema numa máquina ou [aplicação](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) [virtual.](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)

## <a name="view-the-service-principal"></a>Ver o principal de serviço

Este procedimento demonstra como ver o principal de serviço de um VM com a identidade atribuída ao sistema (os mesmos passos se aplicam a uma aplicação).

1. Clique **em Azure Ative Directory** e, em seguida, clique em **aplicações enterprise**.
2. No **Tipo de Aplicação**, escolha **todas as aplicações** e, em seguida, clique em **Aplicar**.
3. Na caixa de filtro de pesquisa, digite o nome do VM ou da aplicação que tenha gerido a identidade ativada ou escolha-a na lista apresentada.

   ![Ver diretor de serviço de identidade gerido no portal](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Próximos passos

[Identidades geridas para os recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview)

