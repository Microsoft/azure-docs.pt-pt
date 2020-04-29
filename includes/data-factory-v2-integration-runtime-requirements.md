---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68966364"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se a sua loja de dados estiver configurada de uma das seguintes formas, precisa de configurar um Tempo de [Integração Auto-hospedado](../articles/data-factory/create-self-hosted-integration-runtime.md) para se ligar a esta loja de dados:

- A loja de dados está localizada dentro de uma rede no local, dentro da Rede Virtual Azure, ou dentro da Amazon Virtual Private Cloud.
- A loja de dados é um serviço de dados em nuvem gerido onde o acesso é restrito a IPs listados nas regras de firewall.
