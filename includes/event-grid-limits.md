---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b884bc72381c98af77f2f49336f3dd5762c68734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859868"
---
Os seguintes limites aplicam-se aos tópicos da Grelha de **Eventos** Azure (tópicos de sistema, personalizados e parceiros). 

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por subscrição do Azure | 100 |
| Assinaturas de eventos por tópico | 500 |
| Taxa de publicação para um tema personalizado ou parceiro (ingress) | 5.000 eventos/seg ou 1 MB/seg (o que for recebido primeiro) |
| Tamanho do evento | 1 MB  |
| Conexões de ponto final privados por tópico  | 64 | 
| Regras ip firewall por tópico | 16 | 

Os seguintes limites aplicam-se aos domínios da Grelha de **Eventos**Azure . 

| Recurso | Limite |
| --- | --- |
| Tópicos por domínio de evento | 100.000 |
| Subscrições de eventos por tópico dentro de um domínio | 500 |
| Subscrições de eventos de âmbito de domínio | 50 |
| Taxa de publicação para um domínio de evento (entrada) | 5.000 eventos/seg ou 1 MB/seg (o que for recebido primeiro) |
| Domínios de eventos por subscrição de Azure | 100 |
| Ligações privadas de ponto final por domínio | 64 | 
| Regras ip firewall por domínio | 16 | 


