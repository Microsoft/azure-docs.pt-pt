---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0b03957178af0578d2c6cd91d7377c93f413cec3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629507"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se a sua loja de dados estiver configurada de uma das seguintes formas, tem de configurar um [tempo de integração auto-hospedado](../articles/data-factory/create-self-hosted-integration-runtime.md) para se ligar à loja de dados:

- A loja de dados está localizada dentro de uma rede no local, dentro de uma rede virtual Azure, ou dentro da Amazon Virtual Private Cloud.
- A loja de dados é um serviço de dados em nuvem gerido onde o acesso é restrito a IPs que estão listados em branco nas regras de firewall.
