---
title: Questões e correções comuns da SAS URI - Azure Marketplace
description: Questões comuns encontradas e sugeridas resoluções ao trabalhar com assinaturas de acesso partilhado.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 56db3562efdc0406e745fd38b73df0a473d0ecd5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83724603"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Problemas e correções comuns da SAS URI

Seguem-se questões comuns encontradas ao trabalhar com assinaturas de acesso partilhado (que são usadas para identificar e partilhar os VHDs carregados para a sua solução), juntamente com as resoluções sugeridas.

| **Emissão** | **Mensagem de falha** | **Correção** |
| --------- | ------------------- | ------- |
| *Falha na cópia de imagens* |  |  |
| "?" não é encontrado em SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI utilizando ferramentas recomendadas. |
| parâmetros "st" e "se" não no SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI com os **valores adequados de Data de Início** e Data de **Fim.** |
| "sp=rl" não em SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI com permissões definidas como `Read` e `List` . |
| SAS URI tem espaços brancos em nome VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI para remover espaços brancos. |
| Erro de autorização SAS URI | `Failure: Copying Images. Not able to download blob due to authorization error.` | Reveja e corrija o formato SAS URI. Regenerar se necessário. |
| Os parâmetros SAS URI "st" e "se" não têm especificação completa da data | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | Os parâmetros **sas** uri data de início e **data de fim** `st` (e `se` sublamentos) devem ter formato de data completa, tais como `11-02-2017T00:00:00Z` . As versões encurtadas são inválidas (alguns comandos no CLI Azure podem gerar valores encurtados por padrão). |
|  |  |  |

Para mais informações, consulte [a utilização de assinaturas de acesso partilhado (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
