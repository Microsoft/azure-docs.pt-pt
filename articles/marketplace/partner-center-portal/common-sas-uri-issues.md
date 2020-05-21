---
title: Problemas e correções comuns da SAS URI - Azure Marketplace
description: Questões comuns encontradas e sugeridas resoluções ao trabalhar com assinaturas de acesso partilhado.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 56db3562efdc0406e745fd38b73df0a473d0ecd5
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83724603"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Problemas e correções comuns da SAS URI

Seguem-se questões comuns encontradas ao trabalhar com assinaturas de acesso partilhado (que são usadas para identificar e partilhar os VHDs carregados para a sua solução), juntamente com as resoluções sugeridas.

| **Edição** | **Mensagem de falha** | **Correção** |
| --------- | ------------------- | ------- |
| *Falha na cópia de imagens* |  |  |
| "?" não é encontrado no SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI utilizando ferramentas recomendadas. |
| Parâmetros "st" e "se" não em SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI com os valores adequados da data de **início** e **data de fim.** |
| "sp=rl" não no SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI com permissões definidas como `Read` e `List` . |
| SAS URI tem espaços brancos em nome VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI para remover espaços brancos. |
| Erro de autorização SAS URI | `Failure: Copying Images. Not able to download blob due to authorization error.` | Reveja e corrija o formato SAS URI. Regenerar se necessário. |
| Os parâmetros SAS URI "st" e "se" não têm especificação de data-data completa | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | Os parâmetros SAS URI **Data de Início** e Data de **Fim** `st` (e `se` subcordas) devem ter formato de data-data completo, tais como `11-02-2017T00:00:00Z` . Versões encurtadas são inválidas (alguns comandos no Azure CLI podem gerar valores encurtados por padrão). |
|  |  |  |

Para mais detalhes, consulte [A utilização de assinaturas de acesso partilhado (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
