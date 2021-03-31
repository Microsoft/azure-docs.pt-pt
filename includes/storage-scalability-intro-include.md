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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75477151"
---
Esta referência detalha a escalabilidade e os objetivos de desempenho para o Azure Storage. Os objetivos de escalabilidade e desempenho aqui listados são alvos de alta qualidade, mas são exequíveis. Em todos os casos, a taxa de pedido e largura de banda alcançada pela sua conta de armazenamento depende do tamanho dos objetos armazenados, dos padrões de acesso utilizados e do tipo de carga de trabalho que a sua aplicação realiza.

Certifique-se de testar o seu serviço para determinar se o seu desempenho satisfaz os seus requisitos. Se possível, evite picos súbitos na taxa de tráfego e certifique-se de que o tráfego está bem distribuído através de divisórias.

Quando a sua aplicação atinge o limite do que uma partição pode lidar para a sua carga de trabalho, o Azure Storage começa a devolver respostas do código de erro 503 (Server Busy) ou do código de erro 500 (Operação Timeout). Se ocorrerem 503 erros, considere modificar a sua aplicação para utilizar uma política de backoff exponencial para retróssões. O recuo exponencial permite que a carga na partição diminua, e para aliviar os picos de tráfego para essa partição.
