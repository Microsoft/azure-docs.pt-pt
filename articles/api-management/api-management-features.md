---
title: Comparação baseada em recursos dos níveis de Gestão da API Azure [ Microsoft Docs
description: Este artigo compara os níveis de Gestão API com base nas funcionalidades que oferecem.
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
ms.openlocfilehash: 2e84138419986ef1033ab076b3745187812e91b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335892"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Comparação baseada em recursos dos níveis de Gestão aPI Azure

Cada [nível](https://aka.ms/apimpricing) de preços de gestão da API oferece um conjunto distinto de funcionalidades e por [capacidade](api-management-capacity.md)unitária. A tabela seguinte resume as principais características disponíveis em cada um dos níveis. Algumas funcionalidades podem funcionar de forma diferente ou têm capacidades diferentes dependendo do nível. Nesses casos, as diferenças são chamadas nos artigos de documentação que descrevem estas características individuais.

> [!IMPORTANT]
> Por favor, note que o nível de Desenvolvimento é para casos e avaliações de uso não produtivo. Não oferece SLA.

| Funcionalidade                                                                                      | Consumo | Programador | Básico | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Integração azure AD<sup>1</sup>                                                             | Não          | Sim       | Não    | Sim      | Sim     |
| Suporte da Rede Virtual (VNet)                                                               | Não          | Sim       | Não    | Não       | Sim     |
| Implementação em várias regiões                                                                      | Não          | Não        | Não    | Não       | Sim     |
| Vários nomes de domínio personalizado                                                                 | Não          | Não        | Não    | Não       | Sim     |
| Portal<sup>de</sup> desenvolvimento 2                                                                 | Não          | Sim       | Sim   | Sim      | Sim     |
| Cache embutido                                                                               | Não          | Sim       | Sim   | Sim      | Sim     |
| Análise incorporada                                                                           | Não          | Sim       | Sim   | Sim      | Sim     |
| [Gateway auto-hospedado](self-hosted-gateway-overview.md)<sup>3</sup>                           | Não          | Sim       | Não    | Não       | Sim     |
| [Definições tls](api-management-howto-manage-protocols-ciphers.md)                             | Sim         | Sim       | Sim   | Sim      | Sim     |
| [Cache externa](https://aka.ms/apimbyoc)                                                    | Sim         | Sim       | Sim   | Sim      | Sim     |
| [Autenticação do certificado de cliente](api-management-howto-mutual-certificates-for-clients.md) | Sim         | Sim       | Sim   | Sim      | Sim     |
| [Backup e restauro](api-management-howto-disaster-recovery-backup-restore.md)               | Não          | Sim       | Sim   | Sim      | Sim     |
| [Gestão sobre Git](api-management-configuration-repository-git.md)                        | Não          | Sim       | Sim   | Sim      | Sim     |
| API de gestão direta                                                                        | Não          | Sim       | Sim   | Sim      | Sim     |
| Registos e métricas do Monitor Azure                                                               | Não          | Sim       | Sim   | Sim      | Sim     |
| IP estático                                                                                    | Não          | Sim       | Sim   | Sim      | Sim     |

<sup>1</sup> Permite a utilização do Azure AD (e Azure AD B2C) como fornecedor de identidade para o utilizador iniciar sessão no portal do desenvolvedor.<br/>
<sup>2</sup> Incluindo funcionalidades relacionadas, por exemplo, utilizadores, grupos, problemas, aplicações e modelos de e-mail e notificações.<br/>
<sup>3</sup> Limitado a uma única implantação de gateway auto-hospedada com um único nó de gateway.<br/>
