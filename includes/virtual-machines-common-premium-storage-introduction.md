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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74005566"
---
Este artigo fornece orientações para a construção de aplicações de alto desempenho utilizando o Armazenamento Premium Azure. Pode utilizar as instruções fornecidas neste documento combinadas com as melhores práticas de desempenho aplicáveis às tecnologias utilizadas pela sua aplicação. Para ilustrar as diretrizes, usamos o SQL Server a correr no Armazenamento Premium como exemplo ao longo deste documento.

Enquanto abordamos cenários de desempenho para a camada de Armazenamento neste artigo, você precisará otimizar a camada de aplicação. Por exemplo, se estiver a hospedar uma Quinta SharePoint no Armazenamento Premium Azure, pode utilizar os exemplos do SQL Server deste artigo para otimizar o servidor de base de dados. Além disso, otimize o servidor web e servidor de aplicações da SharePoint Farm para obter o maior desempenho.

Este artigo ajudará a responder a perguntas comuns sobre a otimização do desempenho da aplicação no Armazenamento Premium Azure,

* Como medir o desempenho da sua candidatura?  
* Por que não vê um desempenho esperado?  
* Quais os fatores que influenciam o desempenho da sua aplicação no Armazenamento Premium?  
* Como é que estes fatores influenciam o desempenho da sua aplicação no Armazenamento Premium?  
* Como pode otimizar iOPS, Largura de Banda e Latência?  

Fornecemos estas diretrizes especificamente para o Armazenamento Premium porque as cargas de trabalho em funcionamento no Armazenamento Premium são altamente sensíveis ao desempenho. Demos exemplos, se for caso disso. Também pode aplicar algumas destas diretrizes a aplicações em execução em VMs IaaS com discos de Armazenamento Padrão.