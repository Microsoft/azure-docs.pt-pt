---
title: Problemas de URL de SAS de comuns e correções para o Azure Marketplace
description: Lista problemas comuns em torno de usando URIs de assinatura de acesso partilhado e possíveis soluções.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 4f2770312624e1ca4c939ade458a451eb03f9d20
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938384"
---
# <a name="common-sas-url-issues-and-fixes"></a>Problemas de URL de SAS e correções comuns

A tabela seguinte lista alguns dos problemas comuns encontrados ao trabalhar com assinaturas de acesso partilhado (que são utilizadas para identificar e partilhar os VHDs carregados para a sua solução), juntamente com resoluções sugeridas.

| **Problema** | **Mensagem de falha** | **Fix** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Falha na cópia de imagens* |  |  |
| "?" não foi encontrado no URL de SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | O URL de SAS através de atualização recomendado de ferramentas. |
| parâmetros de "st" e "se" não está no URL de SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URL de SAS com proper **data de início** e **data de fim** valores no mesmo. | 
| "sp = rl" não está no URL de SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Atualizar o URL de SAS com as permissões definidas como `Read` e `List`. | 
| URL de SAS tem espaços em branco no nome do VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URL de SAS para remover espaços em branco. |
| Erro de autorização de URL de SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Reveja e corrija o formato de URI de SAS. Voltar a gerar se necessário. |
| URL de SAS "st" e "se" parâmetros não têm especificação completa de data e hora | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | URL de SAS **data de início** e **data de fim** parâmetros (`st` e `se` subcadeias de carateres) devem ter o formato de datetime completa, tais como `11-02-2017T00:00:00Z`. Abreviada versões não são válidas. (Alguns comandos na CLI do Azure podem gerar valores abreviadas por predefinição.) | 
|  |  |  |

Para obter mais informações, consulte [Using partilhado assinaturas de acesso (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
