---
title: O que é a API de Conhecimento Académico?
titlesuffix: Azure Cognitive Services
description: Utilize a API de Conhecimento Académico para interpretar consultas de utilizador e obter informações detalhadas do Academic Graph.
services: cognitive-services
author: darrine
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: overview
ms.date: 10/30/2018
ms.author: darrine
ms.openlocfilehash: b15ed5e2b31ed817d3f6558858e2b7285f98a70f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551726"
---
# <a name="academic-knowledge-api"></a>API de Conhecimento Académico

Bem-vindo à API de Conhecimento Académico. Com este serviço, poderá interpretar as consultas de utilizador para fins académicos e obter informações detalhadas do Microsoft Academic Graph (MAG). A base de dados de conhecimento MAG é um gráfico de entidade heterogénea de escala da Web composto de entidades que modelam as atividades académicas: campo de estudo, autor, instituição, documento, local e evento. 

Os dados MAG são extraídos do índice Web do Bing, bem como uma base de dados de conhecimento interna do Bing. Como resultado da indexação em curso do Bing, esta API irá conter informações novas da Web após deteção e indexação pelo Bing. Com base neste conjunto de dados, as API de Conhecimento Académico permitem um diálogo condicionado por conhecimentos e interativo que combina na perfeição a pesquisa reativa com experiências de sugestão proativa, resultados de pesquisa de gráfico de documento de pesquisa detalhada e distribuições de histograma dos valores de atributo para um conjunto de documentos e entidades relacionadas.

Para obter mais informações sobre o Microsoft Academic Graph, consulte [ https://aka.ms/academicgraph ](https://aka.ms/academicgraph).

A API de Conhecimento Académico foi movida da Pré-visualização de Serviços Cognitivos para os Laboratórios dos Serviços Cognitivos. A nova Home Page do projeto é: [ https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge ](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). A sua chave de API existente irá continuar a funcionar até 24 de maio de 2018. Após esta data, gere uma nova chave de API. Tenha em atenção que a pré-visualização paga deixará de estar disponível quando a chave existente expirar. Entre em contato com a nossa equipa, se o escalão gratuito da API não for suficiente para os fins pretendidos. 

## <a name="features"></a>Funcionalidades
A API de Conhecimento Académico consiste em quatro pontos finais de REST relacionados:  
  1. **interpret** – Interpreta uma cadeia de consulta de utilizador de linguagem natural. Devolve interpretações anotadas para permitir experiências de conclusão automática de caixa de pesquisa detalhada que antecipam o que o utilizador está a escrever.  
  2. **evaluate** – Avalia uma expressão de consulta e devolve resultados da entidade de Conhecimento Académico.  
  3. **calchistogram** – Calcula um histograma da distribuição de valores de atributo para entidades académicas devolvidas por uma expressão de consulta, como a distribuição de citações por ano de um determinado autor.  
  
Utilizados em conjunto, estes métodos de API permitem que crie uma experiência de pesquisa de semântica detalhada. Dada uma cadeia de consulta de utilizador, o método **interpret** (Interpretar) fornece uma versão anotada da consulta e uma expressão de consulta estruturada, ao mesmo tempo que conclui, opcionalmente, a consulta de utilizador com base na semântica dos dados académicos subjacentes. Por exemplo, se um utilizador escrever a cadeia *s latente*, o método **interpret** (Interpretar) pode fornecer um conjunto de interpretações classificadas, sugerindo que o utilizador pode estar a procurar o campo de estudo  *análise semântica latente*, o documento *análise de estrutura latente* ou outras expressões de entidade que contenham *s latente*. Estas informações podem ser utilizadas para orientar rapidamente o utilizador para os resultados de pesquisa pretendidos.

O método **evaluate** (Avaliar) pode ser utilizado para recuperar um conjunto de entidades de documento correspondentes da base de dados de conhecimento académica e o método **calchistogram** pode ser utilizado para calcular a distribuição dos valores de atributo para um conjunto de entidades de documento que pode ser utilizado para filtrar ainda mais os resultados da pesquisa.        

## <a name="getting-started"></a>Introdução 
Consulte os subtópicos do lado esquerdo para obter documentação detalhada.  Tenha em atenção que, para melhorar a legibilidade dos exemplos, as chamadas de REST API contêm carateres (por exemplo, espaços), que não possuem codificação URL.  O seu código terá de aplicar as codificações URL apropriadas.
