---
title: incluir ficheiro
description: incluir ficheiro
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 03/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2459cf6c5055dcde83dccf37addc160aeeaa64ad
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198665"
---
Os domínios do evento são mais facilmente explicados usando um exemplo. Digamos que você dirige a Máquina de Construção Contoso, onde fabrica tratores, equipamentos de escavação, e outras máquinas pesadas. Como parte da gestão do negócio, você empurra informações em tempo real para os clientes sobre manutenção de equipamentos, saúde de sistemas e atualizações de contratos. Toda esta informação vai para vários pontos finais, incluindo a sua app, pontos finais do cliente e outras infraestruturas que os clientes criaram.

Os domínios do evento permitem-lhe modelar a Maquinaria de Construção Contoso como uma única entidade de eventos. Cada um dos seus clientes é representado como um tópico dentro do domínio. A autenticação e a autorização são tratadas utilizando o Diretório Ativo Azure. Cada um dos seus clientes pode subscrever o seu tópico e entregar-lhes os seus eventos. O acesso gerido através do domínio do evento garante que só podem aceder ao seu tópico.

Também lhe dá um único ponto final, ao qual pode publicar todos os eventos do seu cliente. A Event Grid cuidará de garantir que cada tópico só está ciente dos eventos que se assemcam ao seu inquilino.

:::image type="content" source="./media/event-grid-domain-example-use-case/contoso-construction-example.png" alt-text="Exemplo de construção de Contoso" lightbox="./media/event-grid-domain-example-use-case/contoso-construction-example.png":::