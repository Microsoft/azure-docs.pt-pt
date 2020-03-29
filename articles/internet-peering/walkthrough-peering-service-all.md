---
title: Instruções para parceiros do Peering Service
titleSuffix: Azure
description: Instruções para parceiros do Peering Service
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720253"
---
# <a name="peering-service-partner-walkthrough"></a>Instruções para parceiros do Peering Service

Esta secção explica os passos que um fornecedor precisa de seguir para permitir um desvio direto para o Serviço de Peering.

## <a name="create-direct-peering-connection-for-peering-service"></a>Criar conexão de peering direto para o Serviço de Peering
Os Prestadores de Serviços podem expandir o seu alcance geográfico criando um novo epeering direto que suporta o Serviço de Peering. Para realizar isto,
1. Torne-se um parceiro de serviço de peering se ainda não
1. Siga as instruções para [Criar ou modificar um olhar direto utilizando o portal](howto-direct-portal.md). Certifique-se de que satisfaz os requisitos de alta disponibilidade.
1. Em seguida, siga os passos para ativar o [Serviço de Peering num desvio direto utilizando o portal](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Utilize a conexão de peering direto legado para o Serviço de Peering
Se tem legado Direto que pretende usar para apoiar o Serviço de Peering,
1. Torne-se um parceiro de Serviço de Peering se ainda não.
1. Siga as instruções para converter um legado Direto para o [recurso Azure utilizando o portal](howto-legacy-direct-portal.md). Se necessário, encomende circuitos adicionais para satisfazer os requisitos de alta disponibilidade.
1. Em seguida, siga os passos para ativar o [Serviço de Peering num desvio direto utilizando o portal](howto-peering-service-portal.md).

## <a name="next-steps"></a>Passos seguintes

* Aprenda sobre a política de [pares.](https://peering.azurewebsites.net/peering)
* Para aprender sobre os passos para configurar o direct peering com a Microsoft, siga o [direct peering walkthrough](walkthrough-direct-all.md).
* Para aprender sobre os passos para configurar o Exchange peering com a Microsoft, siga o [exchange peering walkthrough](walkthrough-exchange-all.md).