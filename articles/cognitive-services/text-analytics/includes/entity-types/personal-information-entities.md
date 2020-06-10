---
title: Entidades nomeadas para informações pessoais
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/30/2020
ms.author: aahi
ms.openlocfilehash: 04fb080d09b1945add7340db081ab49dd017731d
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637549"
---
> [!NOTE]
> Para `PHI` detetar, utilize o parâmetro e a `domain=phi` versão do modelo ou `2020-04-01` posteriormente.
>
> Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi&model-version=2020-04-01`
 
As seguintes categorias de entidades são devolvidas ao enviar pedidos para o `/v3.1-preview.1/entities/recognition/pii` ponto final.

| Categoria   | Subcategory | Descrição                          | Versão do modelo inicial | Notas |
|------------|-------------|--------------------------------------|------------------------|---|
| Pessoa     | N/D         | Nomes de pessoas.  | `2019-10-01`  | Também devolvido `domain=phi` com. |
| PersonType | N/D         | Tipos de emprego ou funções detidas por uma pessoa. | `2020-02-01` | |
| PhoneNumber | N/D | Números de telefone (apenas números de telefone dos EUA e da UE). | `2019-10-01` | Também devolvido com`domain=phi` |
|Organização  | N/D | Empresas, grupos políticos, bandas musicais, clubes desportivos, organismos governamentais e organizações públicas.  | `2019-10-01` | As nacionalidades e religiões não estão incluídas neste tipo de entidade.  |
|Organização | Médico | Empresas médicas e grupos. | `2020-04-01` | Também devolvido `domain=phi` com. |
|Organização | Bolsa de valores | Grupos de bolsa. | `2020-04-01` | Também devolvido `domain=phi` com. |
| Organização | Desportos | Organizações relacionadas com o desporto. | `2020-04-01` | Também devolvido `domain=phi` com. |
| Endereço | N/D | Endereços de correio completos.  | `2020-04-01` | Também devolvido `domain=phi` com. |
| Coordenadas DE GPS da UE | N/D | Coordenadas GPS para locais na União Europeia.  | `2019-10-01` |  |
| E-mail | N/D | Endereços de e-mail. | `2019-10-01` | Também devolvido `domain=phi` com.   |
| URL | N/D | URLs para sites. | `2019-10-01` | Também devolvido `domain=phi` com. |
| IP | N/D | Endereços IP de rede. | `2019-10-01` | |
| DateTime | N/D | Datas e horas do dia. | `2019-10-01` |  | 
| DateTime | Data | Datas de Calender. | `2019-10-01` | Também devolvido `domain=phi` com. |
| Quantidade | N/D | Números e quantidades numéricas. | `2019-10-01` |  |
| Quantidade | Idade | Idades. | `2019-10-01` | | |
| Classificação Internacional das Doenças (ICD-10-CM) | N/D | Entidades relacionadas com a Classificação Internacional de Doenças, 9ª Revisão.   | `2020-04-01` | |
| Classificação Internacional das Doenças (ICD-10-CM) | N/D | Entidades relacionadas com a Classificação Internacional de Doenças, Décima Revisão.    | `2020-04-01` | |

## <a name="azure-information"></a>Informação azul

Esta categoria de entidade inclui informações identificáveis do Azure, incluindo informações de autenticação e cadeias de conexão. Disponível a partir da versão `2019-10-01` modelo. Não voltou com o `domain=phi` parâmetro.

| Subcategory                           | Descrição                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Chave Azure DocumentDB Auth             | Chave de autorização para um servidor Azure DocumentDB.                           |
| Cadeia de conexão de base de dados Azure IAAS e cadeia de conexão Azure SQL | Cadeia de ligação para uma Infraestrutura Azure como base de dados de serviço (IaaS) e cadeia de conexão SQL. |
| Cadeia de conexão Azure SQL           | Cadeia de ligação para uma base de dados Azure SQL.                                |
| Cadeia de conexão Azure IoT           | Cadeia de ligação para Azure Internet das coisas (IoT).                        |
| Azure Publicar Senha de Definição        | Palavra-passe para as definições de Azure Publish.                                        |
| Cadeia de conexão Azure Redis Cache   | Fio de ligação para uma cache Azure para Redis.                             |
| Azure SAS                             | Cadeia de ligação para Azure Software como um Serviço (SAS).                     |
| Cadeia de conexão de ônibus de serviço Azure   | Fio de ligação para um ônibus de serviço Azure.                                 |
| Chave da conta de armazenamento Azure             | Chave de conta para uma conta de armazenamento Azure.                                   |
| Chave da conta de armazenamento Azure (genérico)   | Chave de conta genérica para uma conta de armazenamento Azure.                           |
| Cadeia de conexão do servidor SQL          | Cadeia de ligação para um servidor SQL.                                         |

## <a name="identification"></a>Identificação

[!INCLUDE [supported identification entities](./identification-entities.md)]
