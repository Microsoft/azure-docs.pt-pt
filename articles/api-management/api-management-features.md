---
title: Comparação baseada em recursos das camadas de gerenciamento de API do Azure | Microsoft Docs
description: Este artigo compara as camadas de gerenciamento de API com base nos recursos que elas oferecem.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2019
ms.author: apimpm
ms.openlocfilehash: 72e186bf44b8dffb99c345566b084ccd1c0d6013
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472387"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Comparação baseada em recursos das camadas de gerenciamento de API do Azure

Cada tipo de [preço](https://aka.ms/apimpricing) do gerenciamento de API oferece um conjunto distinto de recursos e [capacidade](api-management-capacity.md)por unidade. A tabela a seguir resume os principais recursos disponíveis em cada uma das camadas. Alguns recursos podem funcionar de maneira diferente ou ter recursos diferentes, dependendo da camada. Nesses casos, as diferenças são chamadas nos artigos de documentação que descrevem esses recursos individuais.

> [!IMPORTANT]
> Observe que a camada de desenvolvedor é para casos de uso e avaliações de não produção. Ele não oferece SLA.

| Funcionalidade                                                                                      | Consumo | Programador | Básica | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Integração do Azure AD<sup>1</sup>                                                             | Não          | Sim       | Não    | Sim      | Sim     |
| Suporte à VNet (rede virtual)                                                               | Não          | Sim       | Não    | Não       | Sim     |
| Implementação em várias regiões                                                                      | Não          | Não        | Não    | Não       | Sim     |
| Vários nomes de domínio personalizados                                                                 | Não          | Não        | Não    | Não       | Sim     |
| Portal do desenvolvedor<sup>2</sup>                                                                 | Não          | Sim       | Sim   | Sim      | Sim     |
| Cache interno                                                                               | Não          | Sim       | Sim   | Sim      | Sim     |
| Análise de dados incorporada                                                                           | Não          | Sim       | Sim   | Sim      | Sim     |
| [Gateway auto-hospedado](self-hosted-gateway-overview.md)<sup>3</sup>                           | Não          | Sim       | Não    | Não       | Sim     |
| [Configurações de SSL](api-management-howto-manage-protocols-ciphers.md)                             | Sim         | Sim       | Sim   | Sim      | Sim     |
| [Cache externo](https://aka.ms/apimbyoc)                                                    | Sim         | Sim       | Sim   | Sim      | Sim     |
| [Autenticação de certificado de cliente](api-management-howto-mutual-certificates-for-clients.md) | Sim         | Sim       | Sim   | Sim      | Sim     |
| [Cópia de segurança e restauro](api-management-howto-disaster-recovery-backup-restore.md)               | Não          | Sim       | Sim   | Sim      | Sim     |
| [Gerenciamento no git](api-management-configuration-repository-git.md)                        | Não          | Sim       | Sim   | Sim      | Sim     |
| API de gerenciamento direto                                                                        | Não          | Sim       | Sim   | Sim      | Sim     |
| Azure Monitor logs e métricas                                                               | Não          | Sim       | Sim   | Sim      | Sim     |
| IP estático                                                                                    | Não          | Sim       | Sim   | Sim      | Sim     |

<sup>1</sup> habilita o uso do Azure AD (e Azure ad B2C) como um provedor de identidade para entrada do usuário no portal do desenvolvedor.<br/>
<sup>2</sup> incluindo funcionalidades relacionadas, por exemplo, usuários, grupos, problemas, aplicativos e modelos de email e notificações.<br/>
<sup>3</sup> limitado a uma única implantação de gateway de hospedagem interna com um único nó de gateway.<br/>
