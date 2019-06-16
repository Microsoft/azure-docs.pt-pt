---
title: Azure Data Lake Storage Gen2 espaço de nomes hierárquico
description: Descreve o conceito de espaço de nomes hierárquico para geração 2 de armazenamento do Azure Data Lake
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: e0d888db5f8de137783a3f9282ca7f85d8a30fc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939448"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Espaço de nomes hierárquico geração 2 de armazenamento do Data Lake do Azure

Um mecanismo de chave que permite que o Azure Data Lake Storage Gen2 fornecer o desempenho do sistema de ficheiros em escala de armazenamento de objeto e os preços é a adição de um **espaço de nomes hierárquico**. Isso permite que a coleção de objetos de/arquivos dentro de uma conta para ser organizados numa hierarquia de diretórios e subdiretórios aninhados da mesma forma que o sistema de ficheiros no seu computador é organizado. Com o espaço de nomes hierárquico ativado, uma conta de armazenamento torna-se capazes de fornecer a escalabilidade e a relação custo-eficácia de armazenamento de objetos, com semântica de sistema de ficheiros que estão familiarizada motores de análise e estruturas.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Os benefícios do espaço de nomes hierárquico

Os seguintes benefícios estão associados a sistemas de ficheiros que implementam um espaço de nomes hierárquico sobre os dados de BLOBs:

- **Manipulação de diretório atômicas:** Os arquivos de objetos aproximam uma hierarquia de diretório ao adotar uma convenção de incorporação de barras (/) no nome do objeto para denotar segmentos de caminho. Embora essa convenção funcione para organizar a objetos, a Convenção não fornece nenhuma assistência para ações como mover, mudar o nome ou eliminar diretórios. Sem diretórios real, aplicativos têm de processar potencialmente milhões de blobs individuais para atingir as tarefas de nível de diretório. Por outro lado, o espaço de nomes hierárquico processa estas tarefas mediante a atualização de uma única entrada (o diretório principal).

    É especialmente importante para muitas estruturas de análise de macrodados, essa otimização surpreendentes. Ferramentas como o Hive, Spark, etc., muitas vezes, escrever a saída para localizações temporárias em, em seguida, mude o nome a localização na conclusão da tarefa. Sem espaço de nomes hierárquico, esta mudança de nome, muitas vezes, pode demorar mais tempo do que a análise de processar em si. Latência mais baixa da tarefa é igual a menor custo total de propriedade (TCO) para cargas de trabalho de análise.

- **Estilo de familiar Interface:** Sistemas de ficheiros são bem compreendidos por desenvolvedores e usuários. Não é necessário para obter um novo paradigma de armazenamento ao mover para a cloud como a interface de sistema de ficheiros exposta pela geração 2 de armazenamento do Data Lake é o mesmo paradigma utilizado por computadores, grandes e pequenos.

Uma das razões que os arquivos de objetos historicamente não davam suporte a um espaço de nomes hierárquico é que um espaço de nomes hierárquico limites de dimensionamento. No entanto, o espaço de nomes hierárquico de geração 2 de armazenamento do Data Lake é dimensionado linearmente e não diminui a capacidade de dados ou o desempenho.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Quando habilitar o espaço de nomes hierárquico

Recomendamos que ative o espaço de nomes hierárquico para cargas de trabalho de armazenamento que foram concebidos para sistemas de ficheiros que manipulam diretórios. Isto inclui todas as cargas de trabalho que são principalmente para processamento de análise. Conjuntos de dados que exigem um alto nível da organização também se beneficiarão, permitindo que o espaço de nomes hierárquico.

Os motivos para ativar o espaço de nomes hierárquico são determinados por uma análise de custo total de propriedade. Em termos gerais, melhorias na latência de carga de trabalho devido a aceleração de armazenamento serão necessários recursos de computação para menos tempo. Latência para muitas cargas de trabalho pode ser melhorada devido a manipulação de diretório atômicas que está ativada por espaço de nomes hierárquico. Em muitas cargas de trabalho, o recurso de computação representa > 85% do total de custos e portanto, mesmo uma redução de latência de carga de trabalho modesta equivale a uma quantidade significativa de economia de custo total de propriedade. Mesmo nos casos em que a ativar o espaço de nomes hierárquico aumenta os custos de armazenamento, o TCO ainda é reduzido devido a custos de computação reduzida.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Quando desabilitar o espaço de nomes hierárquico

Algumas cargas de trabalho do arquivo de objeto não poderão obter algum benefício, permitindo que o espaço de nomes hierárquico. Os exemplos incluem as cópias de segurança, armazenamento de imagens e outros aplicativos em que a organização de objeto é armazenada em separado de objetos (por exemplo: numa base de dados separado).

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma conta de armazenamento](./data-lake-storage-quickstart-create-account.md)
