---
title: incluir ficheiro
description: incluir ficheiro
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027482"
---
 Os clusters de computação Azure Machine Learning também suportam [identidades geridas](../articles/active-directory/managed-identities-azure-resources/overview.md) para autenticar o acesso aos recursos do Azure sem incluir credenciais no seu código. Existem dois tipos de identidades geridas:

* Uma **identidade gerida atribuída pelo sistema** é ativada diretamente no cluster de cálculo de Aprendizagem de Máquinas Azure. O ciclo de vida de uma identidade atribuída ao sistema está diretamente ligado ao cluster compute. Se o cluster de cálculo for eliminado, o Azure limpa automaticamente as credenciais e a identidade em Azure AD.
* Uma **identidade gerida atribuída ao utilizador** é um recurso autónomo da Azure fornecido através do serviço de Identidade Gerida Azure. Pode atribuir uma identidade gerida atribuída ao utilizador a vários recursos, e persiste durante o tempo que quiser.