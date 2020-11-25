---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023202"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Se a sua loja de dados estiver localizada dentro de uma rede no local, de uma rede virtual Azure ou da Amazon Virtual Private Cloud, é necessário configurar um [tempo de integração auto-hospedado](../articles/data-factory/create-self-hosted-integration-runtime.md) para se ligar a ela.

Em alternativa, se a sua loja de dados for um serviço de dados em nuvem gerido, pode utilizar o tempo de execução da integração do Azure. Se o acesso for restrito aos IPs aprovados nas regras de firewall, pode adicionar IPs de [Runtime de Integração Azure](../articles/data-factory/azure-integration-runtime-ip-addresses.md) na lista de permitis. 

Para obter mais informações sobre os mecanismos e opções de segurança da rede suportados pela Data Factory, consulte [as estratégias de acesso aos dados.](../articles/data-factory/data-access-strategies.md)
