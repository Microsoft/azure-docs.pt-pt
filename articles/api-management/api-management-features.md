---
title: Comparação baseada em recursos dos níveis de Gestão da API Azure | Microsoft Docs
description: Compare os níveis de Gestão da API com base nas funcionalidades que oferecem. Consulte uma tabela que resume as principais funcionalidades disponíveis em cada nível de preços.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: f111729d7d7707ed4f40ce8f89ce76975fb47400
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536448"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Comparação baseada em recursos dos escalões de Gestão API da Azure

Cada [nível de preços da](https://aka.ms/apimpricing) API Management oferece um conjunto distinto de funcionalidades e por [capacidade unitária.](api-management-capacity.md) A tabela seguinte resume as principais funcionalidades disponíveis em cada um dos níveis. Algumas funcionalidades podem funcionar de forma diferente ou ter capacidades diferentes dependendo do nível. Nestes casos, as diferenças são convocadas nos artigos de documentação que descrevem estas características individuais.

> [!IMPORTANT]
> Por favor, note que o nível de Desenvolvedor é para casos e avaliações de utilização não-produção. Não oferece SLA.

| Funcionalidade                                                                                      | Consumo | Programador | Básica | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Integração AD AZure<sup>1</sup>                                                             | No          | Yes       | No    | Yes      | Yes     |
| Suporte de Rede Virtual (VNet)                                                               | No          | Yes       | No    | No       | Yes     |
| Implementação em várias regiões                                                                      | No          | No        | No    | No       | Yes     |
| Zonas de disponibilidade                                                                           | No          | No        | No    | No       | Yes     |
| Vários nomes de domínio personalizado                                                                 | No          | Yes        | No    | No       | Yes     |
| Portal do desenvolvedor<sup>2</sup>                                                                 | No          | Yes       | Yes   | Yes      | Yes     |
| Cache embutido                                                                               | No          | Yes       | Yes   | Yes      | Yes     |
| Análise incorporada                                                                           | No          | Yes       | Yes   | Yes      | Yes     |
| [Gateway auto-hospedado](self-hosted-gateway-overview.md)<sup>3</sup>                           | No          | Yes       | No    | No       | Yes     |
| [Definições de TLS](api-management-howto-manage-protocols-ciphers.md)                             | Yes         | Yes       | Yes   | Yes      | Yes     |
| [Cache externo](./api-management-howto-cache-external.md)                                                    | Yes         | Yes       | Yes   | Yes      | Yes     |
| [Autenticação de certificado de cliente](api-management-howto-mutual-certificates-for-clients.md) | Yes         | Yes       | Yes   | Yes      | Yes     |
| [Backup e restauro](api-management-howto-disaster-recovery-backup-restore.md)               | No          | Yes       | Yes   | Yes      | Yes     |
| [Gestão sobre Git](api-management-configuration-repository-git.md)                        | No          | Yes       | Yes   | Yes      | Yes     |
| API de gestão direta                                                                        | No          | Yes       | Yes   | Yes      | Yes     |
| Registos e métricas do Monitor Azure                                                               | No          | Yes       | Yes   | Yes      | Yes     |
| IP estático                                                                                    | No          | Yes       | Yes   | Yes      | Yes     |

<sup>1</sup> Permite a utilização do Azure AD (e Azure AD B2C) como fornecedor de identidade para o utilizador iniciar súbdis no portal do desenvolvedor.<br/>
<sup>2</sup> Incluindo funcionalidades relacionadas, por exemplo, utilizadores, grupos, problemas, aplicações e modelos de e-mail e notificações.<br/>
<sup>3</sup> No nível de desenvolvimento, os gateways auto-hospedados estão limitados a um único nó de gateway.<br/>