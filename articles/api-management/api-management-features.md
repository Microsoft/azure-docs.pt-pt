---
title: Comparação baseada em recursos dos escalões de Gestão da API da Azure Microsoft Docs
description: Compare os níveis de Gestão da API com base nas funcionalidades que oferecem. Consulte uma tabela que resume as principais funcionalidades disponíveis em cada nível de preços.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/15/2020
ms.author: apimpm
ms.openlocfilehash: 262d12e1b0ff74570b44b7794f8b55619a9afa97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87905147"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Comparação baseada em recursos dos escalões de Gestão API da Azure

Cada [nível de preços da](https://aka.ms/apimpricing) API Management oferece um conjunto distinto de funcionalidades e por [capacidade unitária.](api-management-capacity.md) A tabela seguinte resume as principais funcionalidades disponíveis em cada um dos níveis. Algumas funcionalidades podem funcionar de forma diferente ou ter capacidades diferentes dependendo do nível. Nestes casos, as diferenças são convocadas nos artigos de documentação que descrevem estas características individuais.

> [!IMPORTANT]
> Por favor, note que o nível de Desenvolvedor é para casos e avaliações de utilização não-produção. Não oferece SLA.

| Funcionalidade                                                                                      | Consumo | Programador | Básica | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Integração AD AZure<sup>1</sup>                                                             | Não          | Sim       | Não    | Sim      | Sim     |
| Suporte de Rede Virtual (VNet)                                                               | Não          | Sim       | Não    | Não       | Sim     |
| Implementação em várias regiões                                                                      | Não          | Não        | Não    | Não       | Sim     |
| Vários nomes de domínio personalizado                                                                 | Não          | Sim        | Não    | Não       | Sim     |
| Portal do desenvolvedor<sup>2</sup>                                                                 | Não          | Sim       | Sim   | Sim      | Sim     |
| Cache embutido                                                                               | Não          | Sim       | Sim   | Sim      | Sim     |
| Análise incorporada                                                                           | Não          | Sim       | Sim   | Sim      | Sim     |
| [Gateway auto-hospedado](self-hosted-gateway-overview.md)<sup>3</sup>                           | Não          | Sim       | Não    | Não       | Sim     |
| [Definições de TLS](api-management-howto-manage-protocols-ciphers.md)                             | Sim         | Sim       | Sim   | Sim      | Sim     |
| [Cache externo](https://aka.ms/apimbyoc)                                                    | Sim         | Sim       | Sim   | Sim      | Sim     |
| [Autenticação de certificado de cliente](api-management-howto-mutual-certificates-for-clients.md) | Sim         | Sim       | Sim   | Sim      | Sim     |
| [Backup e restauro](api-management-howto-disaster-recovery-backup-restore.md)               | Não          | Sim       | Sim   | Sim      | Sim     |
| [Gestão sobre Git](api-management-configuration-repository-git.md)                        | Não          | Sim       | Sim   | Sim      | Sim     |
| API de gestão direta                                                                        | Não          | Sim       | Sim   | Sim      | Sim     |
| Registos e métricas do Monitor Azure                                                               | Sim         | Sim       | Sim   | Sim      | Sim     |
| IP estático                                                                                    | Não          | Sim       | Sim   | Sim      | Sim     |

<sup>1</sup> Permite a utilização do Azure AD (e Azure AD B2C) como fornecedor de identidade para o utilizador iniciar súbdis no portal do desenvolvedor.<br/>
<sup>2</sup> Incluindo funcionalidades relacionadas, por exemplo, utilizadores, grupos, problemas, aplicações e modelos de e-mail e notificações.<br/>
<sup>3</sup> No nível de desenvolvimento, os gateways auto-hospedados estão limitados a um único nó de gateway.<br/>
