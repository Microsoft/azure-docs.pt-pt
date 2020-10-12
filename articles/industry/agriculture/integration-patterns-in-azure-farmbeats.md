---
title: Arquitetura Azure FarmBeats
description: Descreve a arquitetura de Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d64c2175072d9979cfda2ea5f75beb34d3ad0d6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75482377"
---
# <a name="integration-patterns"></a>Padrões de integração

A azure FarmBeats é uma oferta de negócios para empresas, disponível no Azure Marketplace. O FarmBeats permite a agregação de conjuntos de dados agrícolas entre fornecedores, e geração de insights accuíveis através da construção de modelos de Inteligência Artificial (IA) ou Machine Learning (ML), fundindo os conjuntos de dados.

![Project Farm Beats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

As secções seguintes descrevem o padrão de integração para a Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Porquê integrar-se com a Azure FarmBeats?

Esta secção está focada em parceiros que querem integrar os seus sistemas de dados (como sensores, drones, estações meteorológicas) no Azure FarmBeats.

O Azure FarmBeats é uma oferta extensível, que permite às empresas agrícolas adicionar os seus diferentes conjuntos de dados agrícolas históricos e em tempo real numa única plataforma. O Azure FarmBeats ajuda uma empresa agrícola a normalizar, contextualizar e agregar os seus dados no contexto de uma exploração agrícola.

Ao tornar-se um parceiro de dados com a Azure FarmBeats, pode abrir os seus sistemas para uma adoção mais ampla e contactar mais clientes com as suas ofertas de dados. O Azure FarmBeats fornece uma camada API extensível chamada Datahub, que ajuda a ingerir dados dos seus dispositivos sistematicamente e num esquema padronizado.

Uma vez que os dados estão disponíveis dentro da instância Azure FarmBeats dos seus clientes, os seus clientes podem construir análises e ferramentas mais ricas em cima dos seus dados.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a integração de dados de sensores, consulte [a integração de dados de sensores](sensor-partner-integration-in-azure-farmbeats.md) e para a integração de parceiros de imagem, consulte [a integração de parceiros de imagem](imagery-partner-integration-in-azure-farmbeats.md).
