---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966364"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se o armazenamento de dados estiver configurado de uma das maneiras a seguir, você precisará configurar um [Integration Runtime auto-hospedado](../articles/data-factory/create-self-hosted-integration-runtime.md) para se conectar a este armazenamento de dados:

- O armazenamento de dados está localizado dentro de uma rede local, dentro da rede virtual do Azure ou dentro da nuvem virtual privada da Amazon.
- O armazenamento de dados é um serviço de dados de nuvem gerenciado em que o acesso é restrito aos IPs na lista de permissões de firewall.
