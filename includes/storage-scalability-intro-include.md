---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "75477151"
---
Esta referência detalha a escalabilidade e os alvos de desempenho para o Armazenamento Azure. Os objetivos de escalabilidade e desempenho aqui listados são alvos de alta qualidade, mas são alcançáveis. Em todos os casos, a taxa de pedido e largura de banda alcançada saem do tamanho dos objetos armazenados, dos padrões de acesso utilizados e do tipo de carga de trabalho que a sua aplicação realiza.

Certifique-se de testar o seu serviço para determinar se o seu desempenho satisfaz os seus requisitos. Se possível, evite picos súbitos na taxa de tráfego e certifique-se de que o tráfego está bem distribuído através das divisórias.

Quando a sua aplicação atinge o limite do que uma divisória pode lidar com a sua carga de trabalho, o Armazenamento Azure começa a devolver as respostas do código de erro 503 (Server Busy) ou do código de erro 500 (Tempo limite de funcionamento). Se ocorrerem 503 erros, considere modificar a sua aplicação para utilizar uma política exponencial de backoff para tentativas de repescagem. O backoff exponencial permite que a carga na divisória diminua e alivie os picos de tráfego para essa divisória.
