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
ms.openlocfilehash: 0335481566ae3f28ac0f1e6bddce7050a65e7dc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92517004"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>O que posso conseguir com um aglomerado?

Para um cluster Event Hubs, o quanto você pode ingerir e stream depende de vários fatores, como os seus produtores, consumidores, a taxa a que você está ingerindo e processando, e muito mais. 

A tabela a seguir mostra os resultados de referência que obtivemos durante os nossos testes:

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

Após a criação, os clusters são faturados para um mínimo de 4 horas de uso. No lançamento de Pré-visualização da experiência de autosserviço, pode submeter um pedido de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support) à equipa de Centros de Eventos ao abrigo do Pedido **de** Quota Técnica para escalar para cima ou para baixo  >    >  **Cluster dedicado** para escalar o seu cluster para cima ou para baixo. Pode levar até 7 dias para completar o pedido para reduzir o seu cluster. 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>Como funciona o Geo-DR com o meu agrupamento?

Você pode geo-emparelhar um espaço de nome sob um cluster de nível dedicado com outro espaço de nome sob um cluster de nível dedicado. Não encorajamos a emparelhar um espaço de nome dedicado com um espaço de nome na nossa oferta padrão porque o limite de produção será incompatível e resultará em erros. 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Posso migrar os meus espaços de nome padrão para pertencer a um cluster de nível dedicado?
Atualmente, não apoiamos um processo de migração automatizado para migrar os dados dos centros de eventos de um espaço de nome Standard para um dedicado. 
