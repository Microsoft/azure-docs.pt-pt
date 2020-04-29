---
title: Problemas e correções comuns da SAS URI - Azure Marketplace
description: Questões comuns encontradas e sugeridas resoluções ao trabalhar com assinaturas de acesso partilhado.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266243"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Problemas e correções comuns da SAS URI

> [!IMPORTANT]
> Estamos a mover a gestão das suas ofertas de Máquina Virtual Azure do Portal do Parceiro cloud para o Partner Center. Até que as suas ofertas sejam migradas, siga as instruções em [questões e correções](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) da Common SAS URI para gerir as suas ofertas.

Seguem-se questões comuns encontradas ao trabalhar com assinaturas de acesso partilhado (que são usadas para identificar e partilhar os VHDs carregados para a sua solução), juntamente com as resoluções sugeridas.

| **Edição** | **Mensagem de falha** | **Correção** |
| --------- | ------------------- | ------- |
| *Falha na cópia de imagens* |  |  |
| "?" não é encontrado no SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI utilizando ferramentas recomendadas. |
| Parâmetros "st" e "se" não em SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI com os valores adequados da data de **início** e **data de fim.** |
| "sp=rl" não no SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI `Read` com `List`permissões definidas como e . |
| SAS URI tem espaços brancos em nome VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o SAS URI para remover espaços brancos. |
| Erro de autorização SAS URI | `Failure: Copying Images. Not able to download blob due to authorization error.` | Reveja e corrija o formato SAS URI. Regenerar se necessário. |
| Os parâmetros SAS URI "st" e "se" não têm especificação de data-data completa | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | Os parâmetros SAS URI Data`st` `se` **de Início** e Data de **Fim** (e `11-02-2017T00:00:00Z`subcordas) devem ter formato de data-data completo, tais como . Versões encurtadas são inválidas (alguns comandos no Azure CLI podem gerar valores encurtados por padrão). |
|  |  |  |

Para mais detalhes, consulte [A utilização de assinaturas de acesso partilhado (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
