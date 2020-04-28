---
title: Arquitetura Azure FarmBeats
description: Descreve a arquitetura dos Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d64c2175072d9979cfda2ea5f75beb34d3ad0d6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75482377"
---
# <a name="integration-patterns"></a>Padrões de integração

A Azure FarmBeats é uma oferta de negócios a negócios, disponível no Azure Marketplace. FarmBeats permite a gregação de conjuntos de dados agrícolas entre fornecedores, e geração de insights atuais através da construção de modelos de Inteligência Artificial (IA) ou Machine Learning (ML), fundindo os conjuntos de dados.

![Batidas da Fazenda do Projeto](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

As seguintes secções descrevem o padrão de integração para O Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Porquê integrar-se com a Azure FarmBeats?

Esta secção está focada em parceiros que querem integrar os seus sistemas de dados (como sensores, drones, estações meteorológicas) no Azure FarmBeats.

A Azure FarmBeats é uma oferta extensível, que permite às empresas agrícolas adicionar os seus diferentes conjuntos de dados agrícolas históricos e em tempo real numa única plataforma. A Azure FarmBeats ajuda uma empresa agrícola a normalizar, contextualizar e a agregar os seus dados no contexto de uma exploração.

Ao tornar-se um parceiro de dados com a Azure FarmBeats, pode abrir os seus sistemas para uma adoção mais ampla e chegar a mais clientes com as suas ofertas de dados. O Azure FarmBeats fornece uma camada de API extensível chamada Datahub, que o ajuda a ingerir dados dos seus dispositivos de forma sistemática e num esquema padronizado.

Uma vez que os dados estão disponíveis na instância Azure FarmBeats dos seus clientes, os seus clientes podem construir análises e ferramentas mais ricas em cima dos seus dados.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a integração de dados de sensores, consulte a [integração de dados de sensores](sensor-partner-integration-in-azure-farmbeats.md) e para integração de parceiros de imagem, consulte a integração de [parceiros de imagem.](imagery-partner-integration-in-azure-farmbeats.md)
