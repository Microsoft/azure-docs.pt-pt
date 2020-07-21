---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544669"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se a sua loja de dados estiver localizada dentro de uma rede no local, de uma rede virtual Azure ou da Amazon Virtual Private Cloud, precisa de configurar um [tempo de integração auto-hospedado](../articles/data-factory/create-self-hosted-integration-runtime.md) para se ligar a ela.

Se a sua loja de dados for um serviço de dados em nuvem gerido, onde o acesso é restrito a IPs que estão listados em branco nas regras de firewall, pode utilizar o tempo de execução da integração Azure e adicionar [os seus IPs](../articles/data-factory/azure-integration-runtime-ip-addresses.md) na lista de autorizações. 

Consulte [as estratégias de acesso](../articles/data-factory/data-access-strategies.md) aos dados para obter informações sobre os mecanismos de segurança da rede suportados pela Data Factory para aceder às lojas de dados em geral.
