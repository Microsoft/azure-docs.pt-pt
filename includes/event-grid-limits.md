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
ms.openlocfilehash: e084256d9c2043d4382ca180ef3178175b301367
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745688"
---
Os seguintes limites aplicam-se aos tópicos da Grelha de **Eventos** Azure (tópicos de sistema, personalizados e parceiros). 

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por subscrição do Azure | 100 |
| Assinaturas de eventos por tópico | 500 |
| Taxa de publicação para um tema personalizado ou parceiro (ingress) | 5.000 eventos/seg ou 1 MB/seg (o que for recebido primeiro)<br/>Não se aplica a tópicos do sistema. |
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


