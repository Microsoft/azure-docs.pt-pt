---
title: incluir ficheiro
description: incluir ficheiro
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 24a541080b580eab967987fa7c92cea64b99d65c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389514"
---
| Nomes de domínio                                          | Portas de saída | Description                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| Nuvem Pública: `*.servicebus.windows.net` <br> Governo de Azure: `*.servicebus.usgovcloudapi.net` <br> China: `*.servicebus.chinacloudapi.cn`   | 443            | Exigido pelo tempo de integração auto-hospedado para a autoria interativa. |
| Nuvem Pública: `{datafactory}.{region}.datafactory.azure.net`<br> ou `*.frontend.clouddatahub.net` <br> Governo de Azure: `{datafactory}.{region}.datafactory.azure.us` <br> China: `{datafactory}.{region}.datafactory.azure.cn` | 443            | Requerido pelo tempo de integração auto-hospedado para ligar ao serviço Data Factory. <br>Para a nova Fábrica de Dados criada em nuvem pública, encontre o FQDN a partir da sua chave de runtime de integração auto-hospedada que está em formato {datafactory}. {region}.datafactory.azure.net. Para a antiga fábrica de dados, se não vir o FQDN na sua chave de integração auto-hospedada, utilize *.frontend.clouddatahub.net em vez disso. |
| `download.microsoft.com`    | 443            | Requerido pelo tempo de integração auto-hospedado para descarregar as atualizações. Se tiver desativado a atualização automática, pode saltar para configurar este domínio. |
| URL do Cofre de Chaves | 443           | Requerido pelo Azure Key Vault se armazenar a credencial no Key Vault. |
