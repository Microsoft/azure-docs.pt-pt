---
title: Azure Data Lake Storage Gen2 Hierarchical Namespace
description: Descreve o conceito de um espaço hierárquico para Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 26062d73ae4c61af77b15dd2cac0541f2a988d11
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912999"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Espaço hierárquico de nome hierárquico Azure Data Lake De armazenamento Gen2

Um mecanismo chave que permite ao Azure Data Lake Storage Gen2 fornecer o desempenho do sistema de ficheiros à escala de armazenamento de objetos e preços é a adição de um **espaço hierárquico de nomes.** Isto permite que a recolha de objetos/ficheiros dentro de uma conta seja organizada numa hierarquia de diretórios e subdiretórios aninhados da mesma forma que o sistema de ficheiros no seu computador está organizado. Com um espaço hierárquico habilitado, uma conta de armazenamento torna-se capaz de fornecer a escalabilidade e rentabilidade do armazenamento de objetos, com semântica do sistema de ficheiros que são familiares aos motores e estruturas de análise.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>Os benefícios de um espaço hierárquico

Os seguintes benefícios estão associados a sistemas de ficheiros que implementam um espaço hierárquico de nomes sobre dados blob:

- **Manipulação do diretório atómico:** O objeto armazena aproximadamente uma hierarquia de diretório adotando uma convenção de incorporação de cortes (/) no nome do objeto para denotar segmentos de caminhos. Embora esta convenção trabalhe para a organização de objetos, a convenção não fornece assistência para ações como mover, renomear ou eliminar diretórios. Sem diretórios reais, as aplicações devem processar potencialmente milhões de bolhas individuais para realizar tarefas ao nível do diretório. Em contraste, um espaço hierárquico processa estas tarefas atualizando uma única entrada (o diretório principal).

    Esta otimização dramática é especialmente significativa para muitos quadros de análise de dados grandes. Ferramentas como Hive, Spark, etc. muitas vezes escrevem a saída para locais temporários e, em seguida, renomeam a localização no final do trabalho. Sem um espaço hierárquico, este renome pode muitas vezes demorar mais tempo do que o próprio processo de análise. A menor latência do emprego equivale a um menor custo total de propriedade (TCO) para cargas de trabalho analíticas.

- **Estilo de interface familiar:** Os sistemas de ficheiros são bem compreendidos tanto pelos desenvolvedores como pelos utilizadores. Não há necessidade de aprender um novo paradigma de armazenamento quando se muda para a nuvem, uma vez que a interface do sistema de ficheiros exposta pela Data Lake Storage Gen2 é o mesmo paradigma usado pelos computadores, grandes e pequenos.

Uma das razões pelas quais as lojas de objetos não têm historicamente suportado um espaço hierárquico de nomes é que uma escala hierárquica de espaço de nome. No entanto, o espaço hierárquico de armazenamento de dados Gen2 escala linearmente e não degrada nem a capacidade de dados nem o desempenho.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Decidir se deve permitir um espaço hierárquico

Depois de ter ativado um espaço hierárquico na sua conta, não pode revertê-lo de volta para um espaço de nome plano. Por isso, considere se faz sentido ativar um espaço hierárquico baseado na natureza das cargas de trabalho da loja de objetos.

Algumas cargas de trabalho podem não obter qualquer benefício ao permitir um espaço hierárquico de nomes. Exemplos incluem backups, armazenamento de imagem e outras aplicações onde a organização de objetos é armazenada separadamente dos próprios objetos (por exemplo: numa base de dados separada). 

Além disso, embora o suporte para funcionalidades de armazenamento Blob e o ecossistema de serviços Azure continue a crescer, ainda existem algumas funcionalidades e serviços Azure que ainda não são suportados em contas que têm um espaço hierárquico de nomes. Ver [Questões Conhecidas.](data-lake-storage-known-issues.md) 

Em geral, recomendamos que ligue um espaço hierárquico para cargas de trabalho de armazenamento que são projetados para sistemas de ficheiros que manipulam diretórios. Isto inclui todas as cargas de trabalho que são principalmente para o processamento de analíticos. Conjuntos de dados que requerem um alto grau de organização também beneficiarão permitindo um espaço hierárquico de nomes.

As razões para permitir um espaço hierárquico são determinadas por uma análise de TCO. De um modo geral, as melhorias na latência da carga de trabalho devido à aceleração do armazenamento exigirão recursos computacional por menos tempo. A latência para muitas cargas de trabalho pode ser melhorada devido à manipulação do diretório atómico que é possibilitada por um espaço hierárquico de nomes. Em muitas cargas de trabalho, o recurso computacional representa > 85% do custo total e, portanto, mesmo uma redução modesta da latência da carga de trabalho equivale a uma quantidade significativa de poupanças de TCO. Mesmo nos casos em que permitir um espaço hierárquico aumenta os custos de armazenamento, o TCO continua a ser reduzido devido à redução dos custos de computação.

Para analisar as diferenças nos preços de armazenamento de dados, preços de transação e preços de reserva de capacidade de armazenamento entre contas que têm um espaço hierárquico plano e um espaço hierárquico de nomes, consulte [o preço da Azure Data Lake Storage Gen2.](https://azure.microsoft.com/pricing/details/storage/data-lake/)

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta de Armazenamento](../common/storage-account-create.md)