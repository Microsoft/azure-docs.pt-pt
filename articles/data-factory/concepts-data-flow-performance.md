---
title: Guia de mapeamento de fluxo de dados de desempenho e otimização no Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre os principais fatores que afetam o desempenho dos fluxos de dados no Azure Data Factory, ao utilizar fluxos de dados de mapeamento.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 7fca586083f70e0b0f7e593d5203392260cd2136
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172344"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>E guia de ajuste do desempenho de fluxos de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory mapeamento de fluxos de dados fornecem uma interface de navegador sem código para conceber, implementar e orquestrar as transformações de dados em escala.

> [!NOTE]
> Se não estiver familiarizado com fluxos de dados de mapeamento ADF em geral, veja [descrição geral de fluxos de dados](concepts-data-flow-overview.md) antes de ler este artigo.
>

> [!NOTE]
> Quando estiver a conceber e a testes de fluxos de dados a partir da interface do Usuário do ADF, certifique-se ativar o comutador de depuração para que pode executar os fluxos de dados em tempo real sem ter de esperar para um cluster entender.
>

![Botão de depuração](media/data-flow/debugb1.png "depurar")

## <a name="optimizing-for-azure-sql-database"></a>Otimização da base de dados SQL do Azure

![Parte de origem](media/data-flow/sourcepart2.png "parte de origem")

### <a name="you-can-match-spark-data-partitioning-to-your-source-database-partitioning-based-on-a-database-table-column-key-in-the-source-transformation"></a>Pode corresponder a criação de partições de dados para o particionamento de banco de dados de origem com base numa chave de coluna de tabela de base de dados na transformação de origem do Spark

* Vá para "Otimizar" e selecione "Origem". Defina uma coluna de tabela específica ou um tipo numa consulta.
* Se escolheu "coluna", em seguida, escolha a coluna de partição.
* Além disso, defina o número máximo de ligações ao seu Azure SQL DB. Pode experimentar uma configuração superior para obter ligações paralelas a sua base de dados. No entanto, alguns casos poderão resultar num desempenho mais rápido com um número limitado de ligações.

### <a name="set-batch-size-and-query-on-source"></a>Definir o tamanho de lote e a consulta na origem

![Origem](media/data-flow/source4.png "origem")

* Definir o tamanho do lote instruirá o ADF para armazenar dados em conjuntos na memória em vez de linha por linha. É uma definição opcional e pode ficar sem recursos em nós de computação se eles não têm a capacidade adequadamente.
* Definir uma consulta pode permitir-lhe filtrar o direito de linhas na origem antes de chegarem até mesmo para o fluxo de dados para processamento, o que pode fazer com que a aquisição de dados inicial mais rápida.
* Se usar uma consulta, pode adicionar as sugestões de consulta opcionais para a BD SQL do Azure, ou seja, READ UNCOMMITTED

### <a name="set-sink-batch-size"></a>Defina o tamanho de lote de sink

![Sink](media/data-flow/sink4.png "Sink")

* Para evitar o processamento de linha por linha de sua floes de dados, defina o "tamanho do lote" nas definições de sink para a BD SQL do Azure. Isso instruirá o que ADF para a base de dados do processo grava em lotes com base no tamanho fornecido.

### <a name="set-partitioning-options-on-your-sink"></a>Definir opções de sink de criação de partições

* Mesmo que não tenha os dados particionados de suas tabelas de BD SQL do Azure de destino, vá até a guia de Otimização e defina a criação de partições.
* Com muita frequência, simplesmente dizer ADF para usar Round Robin, criação de partições em clusters de execução do Spark resultados no carregamento em vez de forçar a todas as ligações de uma única partição/nó de dados muito mais rápidos.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Aumentar o tamanho do seu motor de computação no Runtime de integração do Azure

![Novo Runtime de integração](media/data-flow/ir-new.png "novo Runtime de integração")

* Aumente o número de núcleos, o que irá aumentar o número de nós e fornecer-lhe maior potência de processamento para consultar e escrever para a BD SQL do Azure.
* Experimente as opções "Com otimização de computação" e "Com otimização de memória" para aplicar mais recursos para os nós de computação.

### <a name="disable-indexes-on-write"></a>Desativar os índices na escrita
* Utilize uma atividade de procedimento armazenado de pipeline ADF antes de sua atividade de fluxo de dados de mensagens em fila que desativa a índices em suas tabelas de destino que estão sendo gravadas do coletor.
* Após a sua atividade de fluxo de dados, adicione outra atividade de procedimento armazenado que ativado desses índices.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Aumentar o tamanho do seu Azure SQL DB
* Agendar um redimensionamento de sua origem e sink BD SQL do Azure antes de sua execução limita o seu pipeline para aumentar o débito e minimizar a limitação do Azure, assim que tiver de DTU.
* Depois de concluída a execução de pipeline, que pode redimensionar as bases de dados novamente para a sua velocidade de execução normal.

## <a name="next-steps"></a>Passos Seguintes
Veja os artigos fluxo de dados:

- [Descrição geral do fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)

