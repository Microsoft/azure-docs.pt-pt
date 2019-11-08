---
title: Problemas de URL de SAS comuns e correções para o Azure Marketplace
description: Listar problemas comuns em relação ao uso de URIs de assinatura de acesso compartilhado e soluções possíveis.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 502ba1a65f9b0740a51c7a4da219cc87af494f27
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813317"
---
# <a name="common-sas-url-issues-and-fixes"></a>Problemas e correções de URL de SAS comuns

A tabela a seguir lista alguns dos problemas comuns encontrados ao trabalhar com assinaturas de acesso compartilhado (que são usadas para identificar e compartilhar os VHDs carregados para sua solução), juntamente com as resoluções sugeridas.

| **Lo** | **Mensagem de falha** | **Soluciona** | 
| --------- | ------------------- | ------- | 
| *falha de &emsp;ao copiar imagens* |  |  |
| "?" não foi encontrado na URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize a URL SAS usando as ferramentas recomendadas. |
| parâmetros "St" e "se" não estão na URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize a URL SAS com os valores de **data de início** e **data de término** adequados. | 
| "SP = RL" não está na URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Atualize a URL SAS com permissões definidas como `Read` e `List`. | 
| A URL SAS tem espaços em branco no nome do VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize a URL da SAS para remover espaços em branco. |
| Erro de autorização de URL SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Examine e corrija o formato de URI SAS. Gerar novamente se necessário. |
| Os parâmetros "St" e "se" da URL SAS não têm especificação de data e hora completa | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Os parâmetros **data de início** e data de **término** da URL SAS (`st` e `se` subcadeias de caracteres) devem ter o formato datetime completo, como `11-02-2017T00:00:00Z`. As versões reduzidas não são válidas. (Alguns comandos no CLI do Azure podem gerar valores reduzidos por padrão.) | 
|  |  |  |

Para obter mais informações, consulte [usando SAS (assinaturas de acesso compartilhado)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
