---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 6c348b3dd0005eeab154aa2d74abc617cbd1d0cb
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529398"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se a sua loja de dados estiver localizada dentro de uma rede no local, de uma rede virtual Azure ou da Amazon Virtual Private Cloud, precisa de configurar um [tempo de integração auto-hospedado](../articles/data-factory/create-self-hosted-integration-runtime.md) para se ligar a ela.

Se a sua loja de dados for um serviço de dados em nuvem gerido, pode utilizar o tempo de execução da integração do Azure. Se o acesso for restrito a IPs que estejam listados em branco nas regras de firewall, pode optar por adicionar IPs de [Runtime de Integração Azure](../articles/data-factory/azure-integration-runtime-ip-addresses.md) na lista de autorizações. 

Para obter mais informações sobre os mecanismos e opções de segurança da rede suportados pela Data Factory, consulte [as estratégias de acesso aos dados.](../articles/data-factory/data-access-strategies.md)
