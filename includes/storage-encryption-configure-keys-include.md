---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74895262"
---
O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por padrão, os dados são encriptados com chaves geridas pela Microsoft. Para um controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para usar para encriptação de dados blob e ficheiros.

As chaves geridas pelo cliente devem ser armazenadas num Cofre de Chaves Azure. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. A conta de armazenamento e o cofre chave devem estar na mesma região, mas podem estar em assinaturas diferentes. Para obter mais informações sobre encriptação de Armazenamento Azure e gestão de chaves, consulte [encriptação de Armazenamento Azure para obter dados em repouso](../articles/storage/common/storage-service-encryption.md). Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre chave Azure?](../articles/key-vault/key-vault-overview.md)
