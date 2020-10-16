---
title: Instruções para parceiros do Peering Service
titleSuffix: Azure
description: Instruções para parceiros do Peering Service
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 75ec1a4ededfea4f9b40461d69b6e16d947e6919
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84712484"
---
# <a name="peering-service-partner-walkthrough"></a>Instruções para parceiros do Peering Service

Esta secção explica os passos que um provedor precisa seguir para permitir um olhar direto para o Serviço de Observação.

## <a name="create-direct-peering-connection-for-peering-service"></a>Criar ligação de espreitamento direto para o serviço de peering
Os Fornecedores de Serviços podem expandir o seu alcance geográfico criando um novo espreitamento direto que suporta o Serviço de Observação. Para conseguir isto,
1. Torne-se um parceiro de Serviço de Observação, se não for já
1. Siga as instruções para [criar ou modificar um espreitamento direto utilizando o portal](howto-direct-portal.md). Certifique-se de que cumpre os requisitos de alta disponibilidade.
1. Em seguida, siga os passos para [Ativar o Serviço de Observação de Pares num espreitamento direto utilizando o portal](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Use a ligação de observação direta legado para o Serviço de Peering
Se tiver um legado, olhando diretamente que pretende utilizar para apoiar o Serviço de Peering,
1. Torne-se um parceiro de Serviço de Observação, se não for já.
1. Siga as instruções para [converter um legado Espreitar diretamente para o recurso Azure utilizando o portal](howto-legacy-direct-portal.md). Se necessário, encomende circuitos adicionais para satisfazer os requisitos de alta disponibilidade.
1. Em seguida, siga os passos para [Ativar o Serviço de Observação de Pares num espreitamento direto utilizando o portal](howto-peering-service-portal.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a política de espreitar.](https://peering.azurewebsites.net/peering)
* Para aprender sobre os passos para configurar o persigo direto com a Microsoft, siga [o walkthrrough de observação direto](walkthrough-direct-all.md).
* Para aprender sobre os passos para configurar o Exchange peering com a Microsoft, siga [o Exchange peering walkthrough](walkthrough-exchange-all.md).