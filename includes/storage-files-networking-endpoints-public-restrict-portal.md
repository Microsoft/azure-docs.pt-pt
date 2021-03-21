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
ms.openlocfilehash: 3c76988557bfa338bcfb606f568036422a536c1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98798532"
---
Navegue para a conta de armazenamento para a qual gostaria de restringir o ponto final público a redes virtuais específicas. Na tabela de conteúdos para a conta de armazenamento, **selecione Networking**. 

No topo da página, selecione o botão de rádio **de redes selecionados.** Isto irá desconsendo uma série de configurações para controlar a restrição do ponto final público. Clique **+Adicionar a rede virtual existente** para selecionar a rede virtual específica que deve ser autorizada a aceder à conta de armazenamento através do ponto final público. Isto requer a seleção de uma rede virtual e de uma sub-rede para essa rede virtual. 

Check **Allow trusted Microsoft services to access this service account** to allow trusted first party Microsoft services, tais como Azure File Sync, para aceder à conta de armazenamento.

[![Screenshot da lâmina de rede com uma rede virtual específica permitiu aceder à conta de armazenamento através do ponto final público](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)