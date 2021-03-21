---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 192a16d6f7bb39608b040ac28fe2bedb7a2e2dc3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98798644"
---
Navegue para a conta de armazenamento para a qual gostaria de restringir todo o acesso ao ponto final público. Na tabela de conteúdos para a conta de armazenamento, **selecione Networking**.

No topo da página, selecione o botão de rádio **de redes selecionados.** Isto irá desconsendo uma série de configurações para controlar a restrição do ponto final público. Check **Allow trusted Microsoft services to access this service account** to allow trusted first party Microsoft services, tais como Azure File Sync, para aceder à conta de armazenamento.

[![Screenshot da lâmina de rede com os restritos apropriados no lugar](media/storage-files-networking-endpoints-public-disable-portal/disable-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-disable-portal/disable-public-endpoint-0.png#lightbox)