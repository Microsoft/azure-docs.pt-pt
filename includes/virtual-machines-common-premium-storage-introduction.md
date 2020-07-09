---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74005566"
---
Este artigo fornece diretrizes para a construção de aplicações de alto desempenho utilizando o Azure Premium Storage. Pode utilizar as instruções fornecidas neste documento combinadas com as melhores práticas de desempenho aplicáveis às tecnologias utilizadas pela sua aplicação. Para ilustrar as diretrizes, utilizamos o SQL Server a funcionar no Premium Storage como exemplo ao longo deste documento.

Enquanto abordamos os cenários de desempenho para a camada de Armazenamento neste artigo, você precisará otimizar a camada de aplicação. Por exemplo, se estiver a hospedar uma SharePoint Farm no Azure Premium Storage, pode utilizar os exemplos do SQL Server deste artigo para otimizar o servidor de base de dados. Além disso, otimize o servidor web e o servidor de aplicação da SharePoint Farm para obter o maior desempenho.

Este artigo ajudará a responder após perguntas comuns sobre a otimização do desempenho da aplicação no Azure Premium Storage,

* Como medir o desempenho da sua candidatura?  
* Por que não estás a ver o desempenho esperado?  
* Quais os fatores que influenciam o desempenho da sua aplicação no Armazenamento Premium?  
* Como é que estes fatores influenciam o desempenho da sua aplicação no Premium Storage?  
* Como pode otimizar para IOPS, Largura de Banda e Latência?  

Fornecemos estas diretrizes especificamente para o Armazenamento Premium, porque as cargas de trabalho em execução no Armazenamento Premium são altamente sensíveis ao desempenho. Demos exemplos sempre que adequado. Também pode aplicar algumas destas diretrizes a aplicações em execução em IaaS VMs com discos de armazenamento padrão.