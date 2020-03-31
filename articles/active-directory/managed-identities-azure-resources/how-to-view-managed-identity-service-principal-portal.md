---
title: Ver serviço principal de uma identidade gerida no portal Azure - Azure AD
description: Instruções passo a passo para visualizar o principal de serviço de uma identidade gerida no portal Azure.
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
ms.openlocfilehash: c12f15cc79d5329d028239ade4e18a853000bf01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298602"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Ver o principal de serviço de uma identidade gerida no portal Azure

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida automaticamente no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaD Azure, sem ter credenciais no seu código. 

Neste artigo, aprende-se a ver o principal de serviço de uma identidade gerida utilizando o portal Azure.

 > [!NOTE] 
 > Os diretores de serviço são aplicações empresariais. 

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md)
- Se ainda não tem uma conta Azure, [inscreva-se para uma conta gratuita.](https://azure.microsoft.com/free/)
- Ativar [o sistema de identidade atribuída numa máquina](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) virtual ou [aplicação](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Ver o diretor de serviço

Este procedimento demonstra como visualizar o diretor de serviço de um VM com identidade atribuída ao sistema (as mesmas etapas aplicam-se a uma aplicação).

1. Clique em **Azure Ative Directory** e, em seguida, clique em **aplicações da Enterprise**.
2. No tipo de **aplicação,** escolha **todas as aplicações** e clique em **Aplicar**.
3. Na caixa de filtro de pesquisa, digite o nome do VM ou aplicação que tenha gerido a identidade ativada ou escolha-a na lista apresentada.

   ![Ver serviço de identidade gerido principal no portal](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Passos seguintes

[Identidades geridas para os recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview)

