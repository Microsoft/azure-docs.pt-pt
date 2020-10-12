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
ms.openlocfilehash: 295b9fc842d926d0a8b264ed5200b60ac7bd2261
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465150"
---
Navegue para a conta de armazenamento para a qual gostaria de restringir todo o acesso ao ponto final público. Na tabela de conteúdos para a conta de armazenamento, selecione **Firewalls e redes virtuais**.

No topo da página, selecione o botão de rádio **de redes selecionados.** Isto irá desconsendo uma série de configurações para controlar a restrição do ponto final público. Check **Allow trusted Microsoft services to access this service account** to allow trusted first party Microsoft services, tais como Azure File Sync, para aceder à conta de armazenamento.

[![Screenshot das firewalls e da lâmina de redes virtuais com os restritos apropriados no lugar](media/storage-files-networking-endpoints-public-disable-portal/disable-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-disable-portal/disable-public-endpoint-0.png#lightbox)