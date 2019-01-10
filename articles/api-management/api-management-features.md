---
title: Com base na funcionalidade de comparação das camadas de gestão de API do Azure | Documentos da Microsoft
description: Este artigo compara as camadas de gestão de API com base nos recursos que oferecem.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: eae36aa6e60e3da03c59952a1d9e035e6a773d2d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156702"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Com base na funcionalidade de comparação das camadas de gestão de API do Azure

Cada gestão de API [escalão de preço](https://aka.ms/apimpricing) oferece um conjunto distinto de recursos e por unidade [capacidade](api-management-capacity.md). A tabela seguinte resume os principais recursos disponíveis em cada uma das camadas. Algumas funcionalidades poderão funcionam de forma diferente ou ter capacidades diferentes consoante a camada. Nesses casos as diferenças são descritas nos artigos de documentação que descreve esses recursos individuais.

| Funcionalidade                                                                                      | Consumo<sup>pré-visualização</sup> | Programador      | Básica          | Standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Integração do Azure AD<sup>1</sup>                                                             | Não                            | Sim            | Não             | Sim            | Sim            |
| Suporte da virtual Network (VNet)                                                               | Não                            | Sim            | Não             | Não             | Sim            |
| Implementação em várias regiões                                                                      | Não                            | Não             | Não             | Não             | Sim            |
| Vários nomes de domínio personalizados                                                                 | Não                            | Não             | Não             | Não             | Sim            |
| Portal do programador<sup>2</sup>                                                                 | Não                            | Sim            | Sim            | Sim            | Sim            |
| Cache incorporada                                                                               | Não                            | Sim            | Sim            | Sim            | Sim            |
| Análise de dados incorporada                                                                           | Não                            | Sim            | Sim            | Sim            | Sim            |
| [Definições de SSL](api-management-howto-manage-protocols-ciphers.md)                             | Não                            | Sim            | Sim            | Sim            | Sim            |
| [Cache externo](https://aka.ms/apimbyoc)                                                    | Sim                           | Não<sup>3</sup> | Não<sup>3</sup> | Não<sup>3</sup> | Não<sup>3</sup> |
| [Autenticação de certificado de cliente](api-management-howto-mutual-certificates-for-clients.md) | Não<sup>4</sup>                | Sim            | Sim            | Sim            | Sim            |
| [Backup e restauro](api-management-howto-disaster-recovery-backup-restore.md)               | Não                            | Sim            | Sim            | Sim            | Sim            |
| [Gestão através de Git](api-management-configuration-repository-git.md)                        | Não                            | Sim            | Sim            | Sim            | Sim            |
| Direcionar a gestão de API                                                                        | Não                            | Sim            | Sim            | Sim            | Sim            |
| Métricas e registos de Monitor do Azure                                                               | Não<sup>5</sup>                | Sim            | Sim            | Sim            | Sim            |

<sup>1</sup> permite a utilização do Azure AD (e do Azure AD B2C) como fornecedor de identidade para início de sessão do utilizador no portal do programador.<br/>
<sup>2</sup> incluindo funcionalidade relacionada por exemplo, os utilizadores, grupos, problemas, aplicativos e modelos de e-mail e notificações.<br/>
<sup>3</sup> suporte de cache externo para esta camada estará disponível em breve.<br/>
<sup>4</sup> autenticação de certificado de cliente será adicionada para o escalão de consumo antes de sua disponibilidade geral.<br/>
<sup>5</sup> será adicionado suporte total do Azure Monitor para o escalão de consumo.
