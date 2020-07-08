---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74895262"
---
O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por predefinição, os dados são encriptados com as teclas geridas pela Microsoft. Para controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para usar para encriptação de dados de blob e ficheiros.

As chaves geridas pelo cliente devem ser guardadas num cofre de chaves Azure. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. A conta de armazenamento e o cofre-chave devem estar na mesma região, mas podem estar em diferentes subscrições. Para obter mais informações sobre encriptação do Azure Storage e gestão de chaves, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](../articles/storage/common/storage-service-encryption.md). Para mais informações sobre o Azure Key Vault, veja [o que é o Cofre da Chave Azure?](../articles/key-vault/key-vault-overview.md)
