---
title: Faça o download de uma lista de grupos no portal Azure Ative Directory Microsoft Docs
description: Descarregue propriedades de grupo a granel no centro de administração Azure em Azure Ative Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c0fccf965f4a4c9d05d82601d75e314aba7d73
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421693"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Descarregue em massa uma lista de grupos no Azure Ative Directory

Utilizando o portal Azure Ative Directory (Azure AD), pode descarregar em massa a lista de todos os grupos da sua organização para um ficheiro de valores separados por vírgula (CSV).

## <a name="to-download-a-list-of-groups"></a>Para baixar uma lista de grupos

1. Inscreva-se [no portal Azure](https://portal.azure.com) com uma conta de administrador na organização.
1. Em Azure AD, selecione **Grupos**  >  **Descarregar grupos**.
1. Na página **de descarregamento de Grupos,** selecione **Iniciar** a receber um ficheiro CSV listando os seus grupos.

   ![O comando de grupos de descarregamento está na página de Todos os grupos](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Verifique o estado do descarregamento

Pode ver o estado de todos os seus pedidos em massa pendentes na página de resultados da **operação Em Massa.**

[![Verifique o estado na página resultados das operações a granel.](media/groups-bulk-download/bulk-center.png)](media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limites de serviço de descarregamento a granel

Cada atividade a granel para descarregar uma lista de grupo pode ser executada por até uma hora. Isto permite-lhe descarregar uma lista de pelo menos 300.000 grupos.

## <a name="next-steps"></a>Próximos passos

- [A granel remove membros do grupo](groups-bulk-remove-members.md)
- [Descarregue membros de um grupo](groups-bulk-download-members.md)
