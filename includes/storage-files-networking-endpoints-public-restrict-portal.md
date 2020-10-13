---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c594dbab51c46c382c21b4d87595cd7322f6036
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465128"
---
Navegue para a conta de armazenamento para a qual gostaria de restringir o ponto final público a redes virtuais específicas. Na tabela de conteúdos para a conta de armazenamento, selecione **Firewalls e redes virtuais**. 

No topo da página, selecione o botão de rádio **de redes selecionados.** Isto irá desconsendo uma série de configurações para controlar a restrição do ponto final público. Clique **+Adicionar a rede virtual existente** para selecionar a rede virtual específica que deve ser autorizada a aceder à conta de armazenamento através do ponto final público. Isto requer a seleção de uma rede virtual e de uma sub-rede para essa rede virtual. 

Check **Allow trusted Microsoft services to access this service account** to allow trusted first party Microsoft services, tais como Azure File Sync, para aceder à conta de armazenamento.

[![Screenshot das Firewalls e da lâmina de redes virtuais com uma rede virtual específica permitida para aceder à conta de armazenamento através do ponto final público](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)