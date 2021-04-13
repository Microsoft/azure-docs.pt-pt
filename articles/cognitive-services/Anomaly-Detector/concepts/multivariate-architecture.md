---
title: Arquitetura de manutenção pré-preditiva para a utilização do Detetor de Anomalias API
titleSuffix: Azure Cognitive Services
description: Arquitetura de referência para a utilização das APIs multivariadas do Detetor de Anomalias para aplicar a deteção de anomalias nos dados da série de tempo para manutenção preditiva.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: deteção de anomalias, aprendizagem automática, algoritmos
ms.openlocfilehash: 4d379cfe01dcbd88531b98a32b45e0b30f6adeef
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315691"
---
# <a name="predictive-maintenance-solution-with-anomaly-detector-multivariate"></a>Solução de manutenção preditiva com detetor de anomalia multivariado

Muitas indústrias diferentes precisam de soluções de manutenção preditivas para reduzir os riscos e obter informações acccídicas através do processamento de dados dos seus equipamentos. A manutenção preditiva avalia o estado do equipamento através da monitorização online. O objetivo é efetuar a manutenção antes que o equipamento se degrade ou se decompa.

Monitorizar o estado de saúde dos equipamentos pode ser um desafio, uma vez que cada componente dentro do equipamento pode gerar dezenas de sinais, por exemplo, vibração, orientação e rotação.  Isto pode ser ainda mais complexo quando esses sinais têm uma relação implícita, e precisam de ser monitorizados e analisados em conjunto. Definir regras diferentes para esses sinais e correlacioná-las manualmente pode ser dispendiosa. A funcionalidade multivariada do Detetor de Anomalias permite:

* Vários sinais correlacionados a serem monitorizados em conjunto, e as inter-correlações entre eles são contabilizados no modelo.
* Em cada anomalia capturada, o grau de contribuição de diferentes sinais pode ajudar na explicação de anomalias, e na análise da causa raiz do incidente.
* O modelo de deteção de anomalias multivariadas é construído de forma não supervisionada. Os modelos podem ser treinados especificamente para diferentes tipos de equipamentos.

Aqui, fornecemos uma arquitetura de referência para uma solução de manutenção preditiva baseada no Multivariato de Detetor de Anomalias.

## <a name="reference-architecture"></a>Arquitetura de referência

[![Diagrama arquitetónico que começa com os dados do sensor a serem recolhidos na borda com uma peça de equipamento industrial e rastreia o gasoduto de processamento/análise até à saída final de um alerta de incidente que está a ser gerado após o detetor de anomalias. ](../media/multivariate-architecture/multivariate-architecture.png)](../media/multivariate-architecture/multivariate-architecture.png#lightbox)

Na arquitetura acima, os eventos de streaming provenientes de dados de sensores serão armazenados no Lago de Dados Azure e depois processados por um módulo de transformação de dados para serem convertidos num formato de série temporal. Entretanto, o evento de streaming irá desencadear a deteção em tempo real com o modelo treinado. Em geral, haverá um módulo para gerir o ciclo de vida modelo multivariado, como *o Bridge Service* nesta arquitetura.

**Treino do modelo**: Antes de utilizar o detetor de anomalias multivariado para detetar anomalias para um componente ou equipamento. Precisamos de formar um modelo em sinais específicos (séries temporais) gerados por esta entidade. O *Serviço de Ponte* irá recolher dados históricos e submeter um trabalho de formação ao Detetor de Anomalias e, em seguida, manter o Model ID no armazenamento do Modelo *Meta.*

**Validação do modelo**: O tempo de formação de um determinado modelo pode ser variado com base no volume de dados de formação. O *Serviço de Ponte* poderia consultar o estado do modelo e informações de diagnóstico regularmente. A validação da qualidade do modelo pode ser necessária antes de a colocar online. Se houver etiquetas no cenário, estas etiquetas podem ser usadas para verificar a qualidade do modelo. Caso contrário, a informação de diagnóstico pode ser usada para avaliar a qualidade do modelo, podendo também efetuar a deteção de dados históricos com o modelo treinado e avaliar o resultado para retrossatar a validade do modelo.

**Inferência do modelo**: A deteção online será realizada com o modelo válido, e o ID de resultado pode ser armazenado na *tabela de Inferências*. Tanto o processo de treino como o processo de inferência são feitos de forma assíncronea. Em geral, uma tarefa de deteção pode ser concluída em segundos. Os sinais utilizados para a deteção devem ser os mesmos que foram utilizados para o treino. Por exemplo, se usarmos vibração, orientação e rotação para o treino, na deteção os três sinais devem ser incluídos como uma entrada.

**Alerta de incidente** Os resultados de deteção podem ser consultados com iDs de resultados. Cada resultado contém gravidade de cada anomalia e classificação de contribuição. O posto de contribuição pode ser usado para entender por que esta anomalia aconteceu, e que sinal causou este incidente. Podem ser estabelecidos limiares diferentes sobre a gravidade para gerar alertas e notificações a enviar aos engenheiros de campo para realizar trabalhos de manutenção.

## <a name="next-steps"></a>Passos seguintes

- [Quickstarts](../quickstarts/client-libraries-multivariate.md).
- [Boas Práticas](../concepts/best-practices-multivariate.md): Este artigo é sobre padrões recomendados para usar com as APIs multivariadas.