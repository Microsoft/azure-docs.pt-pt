---
title: Espaço de nome hierárquico de armazenamento de lagos azure Data Gen2
description: Descreve o conceito de um espaço hierárquico para Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6ce94590a1d0de6941c27d972bdd1c4194080e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153082"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Espaço hierárquico de armazenamento de lagos Azure Data Gen2

Um mecanismo-chave que permite ao Azure Data Lake Storage Gen2 fornecer o desempenho do sistema de ficheiros à escala de armazenamento de objetos e os preços é a adição de um espaço de **nome hierárquico.** Isto permite que a recolha de objetos/ficheiros dentro de uma conta seja organizada numa hierarquia de diretórios e subdiretórios aninhados da mesma forma que o sistema de ficheiros no seu computador está organizado. Com um espaço de nome hierárquico habilitado, uma conta de armazenamento torna-se capaz de fornecer a escalabilidade e a custo-eficácia do armazenamento de objetos, com semântica do sistema de ficheiros que são familiares aos motores e estruturas de análise.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>Os benefícios de um espaço de nome hierárquico

Os seguintes benefícios estão associados a sistemas de ficheiros que implementam um espaço hierárquico sobre dados blob:

- **Manipulação de diretório atómico:** As lojas de objetos aproximam-se de uma hierarquia de diretório, adotando uma convenção de incorporação de cortes (/) no nome do objeto para denotar segmentos de caminho. Enquanto esta convenção funciona para a organização de objetos, a convenção não presta assistência para ações como mover, renomear ou apagar diretórios. Sem diretórios reais, as aplicações devem processar potencialmente milhões de bolhas individuais para alcançar tarefas ao nível do diretório. Em contraste, um espaço de nome hierárquico processa estas tarefas atualizando uma única entrada (o directório-mãe).

    Esta otimização dramática é especialmente significativa para muitos quadros de análise de big data. Ferramentas como Hive, Spark, etc. muitas vezes escrevem saída para locais temporários e, em seguida, renomeam o local no final do trabalho. Sem um espaço de nome hierárquico, este renome pode muitas vezes demorar mais do que o próprio processo de análise. A menor latência do trabalho equivale a um menor custo total de propriedade (TCO) para cargas de trabalho analíticas.

- **Estilo interface familiar:** Os sistemas de ficheiros são bem compreendidos tanto pelos desenvolvedores como pelos utilizadores. Não há necessidade de aprender um novo paradigma de armazenamento quando se muda para a nuvem, uma vez que a interface do sistema de ficheiros exposta pelo Data Lake Storage Gen2 é o mesmo paradigma utilizado por computadores, grandes e pequenos.

Uma das razões pelas quais as lojas de objetos não têm historicamente suportado um espaço de nome hierárquico é que um espaço hierárquico limites de espaço. No entanto, o espaço hierárquico de armazenamento de data lake Gen2 escala linearmente e não degrada nem a capacidade de dados nem o desempenho.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Decidir se permite um espaço hierárquico

Depois de ter ativado um espaço hierárquico na sua conta, não pode revertê-lo de volta para um espaço de nome plano. Por isso, considere se faz sentido permitir um espaço de nome hierárquico baseado na natureza das cargas de trabalho da sua loja de objetos.

Algumas cargas de trabalho podem não obter qualquer benefício permitindo um espaço de nome hierárquico. Exemplos incluem cópias de segurança, armazenamento de imagem e outras aplicações onde a organização de objetos é armazenada separadamente dos próprios objetos (por exemplo: numa base de dados separada). 

Além disso, embora o apoio às funcionalidades de armazenamento blob e ao ecossistema de serviços Azure continue a crescer, existem ainda algumas funcionalidades e serviços Azure que ainda não são suportados em contas que têm um espaço de nome hierárquico. Ver [Questões Conhecidas](data-lake-storage-known-issues.md). 

Em geral, recomendamos que ligue um espaço hierárquico para cargas de trabalho de armazenamento que sejam projetados para sistemas de ficheiros que manipulam diretórios. Isto inclui todas as cargas de trabalho que são principalmente para processamento de análise. Os conjuntos de dados que requerem um elevado grau de organização também beneficiarão permitindo um espaço de nome hierárquico.

As razões para permitir um espaço hierárquico são determinadas por uma análise de TCO. De um modo geral, as melhorias na latência da carga de trabalho devido à aceleração do armazenamento exigirão recursos computacionais por menos tempo. A latência para muitas cargas de trabalho pode ser melhorada devido à manipulação do diretório atómico que é ativada por um espaço de nome hierárquico. Em muitas cargas de trabalho, o recurso computacional representa > 85% do custo total e, por isso, mesmo uma redução modesta da latência da carga de trabalho equivale a uma quantidade significativa de poupanças de TCO. Mesmo nos casos em que permitir um espaço hierárquico aumenta os custos de armazenamento, o TCO continua a ser reduzido devido à redução dos custos do cálculo.

Para analisar diferenças nos preços de armazenamento de dados, preços de transação e preços de reserva de capacidade de armazenamento entre contas que têm um espaço de nome hierárquico plano versus um espaço hierárquico, consulte o preço do Armazenamento [do Lago Dedados Do Azure.](https://azure.microsoft.com/pricing/details/storage/data-lake/)

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta de Armazenamento](./data-lake-storage-quickstart-create-account.md)
