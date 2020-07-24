---
title: Comparação baseada em recursos dos escalões de Gestão da API da Azure Microsoft Docs
description: Este artigo compara os níveis de Gestão da API com base nas funcionalidades que oferecem.
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
ms.openlocfilehash: 7ec89d985c025814d903ae9a16f9b0f5123b3571
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056435"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Comparação baseada em recursos dos escalões de Gestão API da Azure

Cada [nível de preços da](https://aka.ms/apimpricing) API Management oferece um conjunto distinto de funcionalidades e por [capacidade unitária.](api-management-capacity.md) A tabela seguinte resume as principais funcionalidades disponíveis em cada um dos níveis. Algumas funcionalidades podem funcionar de forma diferente ou ter capacidades diferentes dependendo do nível. Nestes casos, as diferenças são convocadas nos artigos de documentação que descrevem estas características individuais.

> [!IMPORTANT]
> Por favor, note que o nível de Desenvolvedor é para casos e avaliações de utilização não-produção. Não oferece SLA.

| Funcionalidade                                                                                      | Consumo | Programador | Básica | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Integração AD AZure<sup>1</sup>                                                             | No          | Yes       | No    | Sim      | Sim     |
| Suporte de Rede Virtual (VNet)                                                               | No          | Yes       | Não    | Não       | Yes     |
| Implementação em várias regiões                                                                      | Não          | Não        | Não    | Não       | Yes     |
| Vários nomes de domínio personalizado                                                                 | Não          | Não        | Não    | Não       | Yes     |
| Portal do desenvolvedor<sup>2</sup>                                                                 | No          | Sim       | Sim   | Sim      | Sim     |
| Cache embutido                                                                               | No          | Sim       | Sim   | Sim      | Sim     |
| Análise incorporada                                                                           | No          | Sim       | Sim   | Sim      | Sim     |
| [Gateway auto-hospedado](self-hosted-gateway-overview.md)<sup>3</sup>                           | No          | Yes       | Não    | Não       | Yes     |
| [Definições de TLS](api-management-howto-manage-protocols-ciphers.md)                             | Sim         | Sim       | Sim   | Sim      | Sim     |
| [Cache externo](https://aka.ms/apimbyoc)                                                    | Sim         | Sim       | Sim   | Sim      | Sim     |
| [Autenticação de certificado de cliente](api-management-howto-mutual-certificates-for-clients.md) | Sim         | Sim       | Sim   | Sim      | Sim     |
| [Criar Cópias de Segurança e Restaurar](api-management-howto-disaster-recovery-backup-restore.md)               | No          | Sim       | Sim   | Sim      | Sim     |
| [Gestão sobre Git](api-management-configuration-repository-git.md)                        | No          | Sim       | Sim   | Sim      | Sim     |
| API de gestão direta                                                                        | No          | Sim       | Sim   | Sim      | Sim     |
| Registos e métricas do Monitor Azure                                                               | Sim         | Sim       | Sim   | Sim      | Sim     |
| IP estático                                                                                    | Não          | Sim       | Sim   | Sim      | Sim     |

<sup>1</sup> Permite a utilização do Azure AD (e Azure AD B2C) como fornecedor de identidade para o utilizador iniciar súbdis no portal do desenvolvedor.<br/>
<sup>2</sup> Incluindo funcionalidades relacionadas, por exemplo, utilizadores, grupos, problemas, aplicações e modelos de e-mail e notificações.<br/>
<sup>3</sup> No nível de desenvolvimento, os gateways auto-hospedados estão limitados a um único nó de gateway.<br/>
