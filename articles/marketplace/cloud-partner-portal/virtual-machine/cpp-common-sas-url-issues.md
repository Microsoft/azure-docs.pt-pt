---
title: Problemas e correções de URL SAS comuns para o Mercado Azure
description: Enumera ris comuns em torno da utilização de URIs de assinatura de acesso partilhado e possíveis soluções.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 723762695d34380b7f237fa9082dc470dafcc8df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147050"
---
# <a name="common-sas-url-issues-and-fixes"></a>Problemas e correções de URL SAS comuns

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de Máquina Virtual Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [questões e correções de URL SAS Comuns](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues) para gerir as suas ofertas migratórias.

A tabela que se segue enumera algumas das questões comuns encontradas ao trabalhar com assinaturas de acesso partilhado (que são usadas para identificar e partilhar os VHDs enviados para a sua solução), juntamente com resoluções sugeridas.

| **Edição** | **Mensagem de falha** | **Correção** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Falha na cópia de imagens* |  |  |
| "?" não é encontrado na URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URL SAS utilizando ferramentas recomendadas. |
| Parâmetros "st" e "se" não em URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URL SAS com os valores adequados da data de **início** e **da data de fim.** | 
| "sp=rl" não em URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Atualize o URL SAS `Read` com `List`permissões definidas como e . | 
| SAS URL tem espaços brancos em nome VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URL SAS para remover espaços brancos. |
| Erro de autorização de URL SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Reveja e corrija o formato SAS URI. Regenerar se necessário. |
| Os parâmetros "st" e "se" da SAS URL não têm especificação de data-data completa | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Os parâmetros de data de`st` **início** e data de **fim** (e `se` subcordas) `11-02-2017T00:00:00Z`do URL SAS são necessários para ter formato de data-data completa, tais como . Versões encurtadas não são válidas. (Alguns comandos no Azure CLI podem gerar valores encurtados por padrão.) | 
|  |  |  |

Para mais informações, consulte [A utilização de assinaturas de acesso partilhado (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
