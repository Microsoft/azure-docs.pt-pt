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
ms.openlocfilehash: b1287f9c7e946c7b4d035b2ad6301947ffad3cea
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717133"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Armazenamento premium do Azure: conceber o elevado desempenho

Este artigo fornece diretrizes para a criação de aplicativos de alto desempenho usando o armazenamento Premium do Azure. Pode utilizar as instruções fornecidas neste documento, combinado com melhores práticas de desempenho aplicáveis para as tecnologias utilizadas pela sua aplicação. Para ilustrar as diretrizes, usamos SQL Server em execução no armazenamento Premium como um exemplo em todo este documento.

Enquanto estamos a resolver cenários de desempenho para a camada de armazenamento neste artigo, precisará otimizar a camada de aplicativo. Por exemplo, se estiver a alojar um Farm do SharePoint no armazenamento Premium do Azure, pode utilizar os exemplos do SQL Server deste artigo para otimizar o servidor de base de dados. Além disso, Otimize o servidor Web e servidor de aplicações para obter a maior parte do desempenho do Farm do SharePoint.

Este artigo o ajudará a resposta a perguntas comuns sobre a otimização de desempenho de aplicações no armazenamento do Azure Premium, a seguir

* Como medir o desempenho da aplicação?  
* Por que motivo está a ver não esperado de alto desempenho?  
* Que fatores influenciam o desempenho da aplicação no armazenamento Premium?  
* Como é que esses fatores influenciam o desempenho da sua aplicação no armazenamento Premium?  
* Como pode otimizar para IOPS, largura de banda e latência?  

Nós fornecemos estas diretrizes especificamente para o armazenamento Premium como cargas de trabalho em execução no armazenamento Premium são altamente confidencial do desempenho. Nós fornecemos exemplos quando apropriado. Também pode aplicar alguns destas diretrizes para aplicativos em execução em VMs de IaaS com discos de armazenamento Standard.