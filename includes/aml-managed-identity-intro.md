---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: d8ee7b61897a9718d7078fc16eddc177fefbff43
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147606"
---
 Os clusters de computação Azure Machine Learning também suportam [identidades geridas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o acesso aos recursos do Azure sem incluir credenciais no seu código. Existem dois tipos de identidades geridas:

* Uma **identidade gerida atribuída pelo sistema** é ativada diretamente no cluster de cálculo de Aprendizagem de Máquinas Azure. O ciclo de vida de uma identidade atribuída ao sistema está diretamente ligado ao cluster compute. Se o cluster de cálculo for eliminado, o Azure limpa automaticamente as credenciais e a identidade em Azure AD.
* Uma **identidade gerida atribuída ao utilizador** é um recurso autónomo da Azure fornecido através do serviço de Identidade Gerida Azure. Pode atribuir uma identidade gerida atribuída ao utilizador a vários recursos, e persiste durante o tempo que quiser.