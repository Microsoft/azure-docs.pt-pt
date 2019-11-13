---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005566"
---
Este artigo fornece diretrizes para a criação de aplicativos de alto desempenho usando o armazenamento Premium do Azure. Você pode usar as instruções fornecidas neste documento combinadas com as práticas recomendadas de desempenho aplicáveis às tecnologias usadas pelo seu aplicativo. Para ilustrar as diretrizes, usamos SQL Server em execução no armazenamento Premium como um exemplo em todo este documento.

Enquanto abordamos os cenários de desempenho da camada de armazenamento neste artigo, você precisará otimizar a camada de aplicativo. Por exemplo, se você estiver hospedando um farm do SharePoint no armazenamento Premium do Azure, poderá usar os exemplos de SQL Server deste artigo para otimizar o servidor de banco de dados. Além disso, otimize o servidor Web do farm do SharePoint e o servidor de aplicativos para obter o máximo de desempenho.

Este artigo ajudará a responder a seguir perguntas comuns sobre como otimizar o desempenho do aplicativo no armazenamento Premium do Azure,

* Como medir o desempenho do aplicativo?  
* Por que você não está vendo o alto desempenho esperado?  
* Quais fatores influenciam o desempenho do aplicativo no armazenamento Premium?  
* Como esses fatores influenciam o desempenho do seu aplicativo no armazenamento Premium?  
* Como você pode otimizar para IOPS, largura de banda e latência?  

Fornecemos essas diretrizes especificamente para armazenamento Premium porque as cargas de trabalho em execução no armazenamento Premium são altamente sensíveis ao desempenho. Fornecemos exemplos quando apropriado. Você também pode aplicar algumas dessas diretrizes a aplicativos executados em VMs IaaS com discos de armazenamento padrão.