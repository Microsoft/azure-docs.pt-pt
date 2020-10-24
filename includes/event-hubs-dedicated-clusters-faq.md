---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 345454557c1bd0df3b4e7210229c81f0149af0f3
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495111"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>O que posso conseguir com um aglomerado?

Para um cluster Event Hubs, quanto você pode ingerir e stream depende de vários fatores, como os seus produtores, consumidores, a taxa a que você está ingerindo e transformando, e muito mais. 

A tabela seguinte mostra os resultados de referência que conseguimos durante os nossos testes:

| Forma de carga útil | Recetores | Largura de banda ingress| Mensagens ingress | Largura de banda da Egress | Mensagens de egress | TUs totais | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/seg | 400k mensagens/seg | 800 MB/seg | 800k mensagens/seg | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/seg | 66.6k mensagens/seg | 1,33 GB/seg | Mensagens de 133k/seg | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/seg | Mensagens de 34k /seg | 1,05 GB/seg | Mensagens de 34k/seg | 1000 TUs | 250 TUs |

No ensaio, foram utilizados os seguintes critérios:

- Foi utilizado um cluster de centros de eventos de nível dedicado com quatro unidades de capacidade (CUs). 
- O centro de eventos usado para a ingestão tinha 200 divisórias. 
- Os dados que foram ingeridos foram recebidos por duas aplicações recetoras recebidas de todas as divisórias.

### <a name="can-i-scale-updown-my-cluster"></a>Posso escalar para cima/para baixo o meu aglomerado?

Após a criação, os clusters são faturados para um mínimo de 4 horas de uso. No lançamento de Pré-visualização da experiência de autosserviço, pode submeter um pedido de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support) à equipa de Centros de Eventos ao abrigo *do > Técnico Quota > Pedido de Escala para Cima ou Escala Para Baixo Cluster dedicado* para escalar o seu cluster para cima ou para baixo. Pode levar até 7 dias para completar o pedido para reduzir o seu cluster. 

### <a name="how-will-geo-dr-work-with-my-cluster"></a>Como é que a Geo-DR vai trabalhar com o meu agrupamento?

Você pode geo-emparelhar um espaço de nome sob um cluster de nível dedicado com outro espaço de nome sob um cluster de nível dedicado. Não encorajamos a emparelhar um espaço de nome dedicado com um espaço de nome na nossa oferta Standard, uma vez que o limite de produção será incompatível, o que resultará em erros. 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Posso migrar os meus espaços de nome padrão para pertencer a um cluster de nível dedicado?
Não apoiamos atualmente um processo de migração automatizado para migrar os dados dos centros de eventos de um espaço de nome Standard para um Dedicado. 
