---
title: Diagrama de arquitetura de referência | Mercado Azure
description: Como criar um diagrama de arquitetura de referência para uma oferta no mercado comercial da Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 02/18/2021
ms.openlocfilehash: 66e4d498ff7584188d680e35c89c6f10cc43c9cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604496"
---
# <a name="reference-architecture-diagram"></a>Diagrama de arquitetura de referência

O diagrama de arquitetura de referência é um modelo que representa a infraestrutura em que a sua oferta depende. Para soluções Azure IP, o diagrama também deve mostrar como a sua oferta utiliza os serviços de cloud da Microsoft de acordo com os requisitos técnicos da CO-venda IP. Não se destina a avaliar a qualidade da arquitetura. Foi concebido para mostrar como a sua solução utiliza os serviços da Microsoft.

O diagrama de arquitetura de referência pode ser criado através de múltiplas ferramentas. Recomendamos o Microsoft Visio, uma vez que possui múltiplos stencils que retratam modelos de arquitetura Azure.

Um ponto de partida útil para a construção de diagramas de arquitetura de referência é alavancar os [modelos Azure Architecture.](/azure/architecture/browse/)

## <a name="typical-components-of-a-reference-architecture-diagram"></a>Componentes típicos de um diagrama de arquitetura de referência

- Serviços em nuvem que hospedam e interagem com a sua oferta, incluindo aqueles que consomem recursos Azure
- Conexões de dados, camadas de dados e serviços de dados consumidos pela sua oferta
- Serviços em nuvem utilizados para controlar a segurança, a autenticação e os utilizadores da oferta
- Interfaces de utilizador e outros serviços que expõem a oferta aos utilizadores
- Conectividade híbrida ou no local ou integrações ou uma combinação de ambos

Esta imagem mostra um exemplo de um diagrama de arquitetura de referência.

[![Esta imagem é um exemplo Co-vender o diagrama de arquitetura.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

Este diagrama de arquitetura de referência é para um chatbot da indústria vertical que pode ser integrado com sites intranet para ajudar com os cenários de procura de previsão através de um algoritmo de aprendizagem automática. Esta solução utiliza dados de programação da cadeia de fornecimento e de fabrico de diferentes sistemas ERP. O bot é projetado para responder a questões sobre quando um vendedor pode comprometer-se em possíveis datas de entrega para uma encomenda.

## <a name="next-steps"></a>Passos seguintes

- [Configurar Co-venda para uma oferta de mercado comercial](commercial-marketplace-co-sell.md)
