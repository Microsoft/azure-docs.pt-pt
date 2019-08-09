---
title: Azure Data Lake Storage Gen2 namespace hierárquico
description: Descreve o conceito do namespace hierárquico para Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 0b98892bd31b097e3dc217d54f52f12550599d32
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847140"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 namespace hierárquico

Um mecanismo-chave que permite ao Azure Data Lake Storage Gen2 fornecer desempenho do sistema de arquivos em escala de armazenamento de objetos e preços é a adição de um **namespace hierárquico**. Isso permite que a coleção de objetos/arquivos em uma conta seja organizada em uma hierarquia de diretórios e subdiretórios aninhados da mesma maneira que o sistema de arquivos em seu computador é organizado. Com o namespace hierárquico habilitado, uma conta de armazenamento torna-se capaz de fornecer a escalabilidade e a economia de custo do armazenamento de objetos, com a semântica do sistema de arquivos familiar aos mecanismos e estruturas de análise.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Os benefícios do namespace hierárquico

Os seguintes benefícios estão associados a sistemas de arquivos que implementam um namespace hierárquico em dados de blob:

- **Manipulação de diretório atômico:** O objeto armazena uma hierarquia de diretório aproximada, adotando uma Convenção de barras inseridas (/) no nome do objeto para denotar segmentos de caminho. Embora essa Convenção funcione para organizar objetos, a Convenção não fornece assistência para ações como mover, renomear ou excluir diretórios. Sem os diretórios reais, os aplicativos devem processar potencialmente milhões de BLOBs individuais para obter tarefas em nível de diretório. Por outro lado, o namespace hierárquico processa essas tarefas atualizando uma única entrada (o diretório pai).

    Essa otimização drástica é especialmente significativa para muitas Big Data estruturas de análise. Ferramentas como Hive, Spark, etc. geralmente gravam a saída em locais temporários e, em seguida, renomeam o local na conclusão do trabalho. Sem o namespace hierárquico, essa renomeação geralmente pode levar mais tempo do que o próprio processo de análise. Menor latência de trabalho é igual ao TCO (custo total de propriedade) para cargas de trabalhos de análise.

- **Estilo de interface familiar:** Os sistemas de arquivos são bem compreendidos por desenvolvedores e usuários. Não há necessidade de aprender um novo paradigma de armazenamento quando você muda para a nuvem, já que a interface do sistema de arquivos exposta por Data Lake Storage Gen2 é o mesmo paradigma usado por computadores, grandes ou pequenos.

Um dos motivos pelos quais os repositórios de objetos não têm suporte historicamente em um namespace hierárquico é que um namespace hierárquico limita a escala. No entanto, o namespace hierárquico Data Lake Storage Gen2 é dimensionado linearmente e não degrada a capacidade ou o desempenho dos dados.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Quando habilitar o namespace hierárquico

Recomendamos que você ative o namespace hierárquico para cargas de trabalho de armazenamento projetadas para sistemas de arquivos que manipulam diretórios. Isso inclui todas as cargas de trabalho que são principalmente para processamento de análise. Os conjuntos de dado que exigem um alto grau de organização também se beneficiarão ao habilitar o namespace hierárquico.

Os motivos para habilitar o namespace hierárquico são determinados por uma análise de TCO. Em termos gerais, melhorias na latência da carga de trabalho devido à aceleração do armazenamento exigirão recursos de computação por menos tempo. A latência de várias cargas de trabalho pode ser aprimorada devido à manipulação de diretório atômica habilitada pelo namespace hierárquico. Em muitas cargas de trabalho, o recurso de computação representa > 85% do custo total e, portanto, até mesmo uma redução modesto na latência da carga de trabalho equivale a uma quantidade significativa de economia de TCO. Mesmo nos casos em que a habilitação do namespace hierárquico aumenta os custos de armazenamento, o TCO ainda é reduzido devido a custos de computação reduzidos.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Quando desabilitar o namespace hierárquico

Algumas cargas de trabalho do repositório de objetos podem não obter nenhum benefício ao habilitar o namespace hierárquico. Os exemplos incluem backups, armazenamento de imagem e outros aplicativos em que a organização de objetos é armazenada separadamente dos próprios objetos (por exemplo: em um banco de dados separado).

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma conta de armazenamento](./data-lake-storage-quickstart-create-account.md)
