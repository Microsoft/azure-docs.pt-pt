---
title: Questões e correções comuns da SAS URI - Azure Marketplace
description: Questões comuns encontradas e sugeridas resoluções ao trabalhar com assinaturas de acesso partilhado.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: 4eb3c3e893a276aed10807a13a0f2d6d3bc4e71d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87316843"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Problemas e correções comuns da SAS URI

Seguem-se questões comuns encontradas ao trabalhar com assinaturas de acesso partilhado (que são usadas para identificar e partilhar os VHDs carregados para a sua solução), juntamente com as resoluções sugeridas.

| **Problema** | **Mensagem de falha** | **Correção** |
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
