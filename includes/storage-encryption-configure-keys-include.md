---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/16/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 3076065d23d4701711c0ecb60349e77ee5c9b7c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61593536"
---
O armazenamento do Azure suporta a encriptação em repouso com chaves geridas pela Microsoft ou com chaves geridas pelo cliente. Chaves geridas pelo cliente permitem-lhe criar, girar, desativar e revogar os controlos de acesso.

Utilize o Azure Key Vault para gerir as chaves e auditar a utilização de chave. Pode criar suas próprias chaves e armazená-las num cofre de chaves, ou pode utilizar as APIs de Cofre de chaves do Azure para gerar chaves. A conta de armazenamento e o Cofre de chaves têm de estar na mesma região, mas podem estar em diferentes subscrições. Para obter mais informações sobre o Azure Key Vault, consulte [o que é o Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
