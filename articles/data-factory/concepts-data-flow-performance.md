---
title: Guia de mapeamento de fluxo de dados de desempenho e otimização no Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre os principais fatores que afetam o desempenho dos fluxos de dados no Azure Data Factory, ao utilizar fluxos de dados de mapeamento.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 1ee266d7d9846a357dce613817affdb0cde5bfdc
ms.sourcegitcommit: e6cb7ca206a125c05acfd431b5a64391a8dcc6b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67569032"
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

## <a name="monitor-data-flow-performance"></a>Monitor de desempenho de fluxo de dados

Embora estruturar os seus dados de mapeamento de fluxos no navegador, pode teste de unidade cada transformação individual ao clicar no separador de pré-visualização de dados no painel de definições de na parte inferior para cada transformação. A próxima etapa, que deve levar é testar o seu fluxo de dados ponto a ponto no estruturador do pipeline. Adicionar uma atividade de executar o fluxo de dados e utilize o botão de depuração para testar o desempenho do seu fluxo de dados. No painel inferior da janela do pipeline, verá um ícone de eyeglass em "ações":

![Monitor de fluxo de dados](media/data-flow/mon002.png "2 do Monitor de fluxo de dados")

Clicar nesse ícone, verá o plano de execução e o perfil de desempenho subsequentes do seu fluxo de dados. Pode usar essas informações para calcular o desempenho do seu fluxo de dados relativamente a origens de dados de tamanho diferente. Observe que pode assumir 1 minuto de tempo de configuração de execução de tarefa de cluster em seus cálculos de desempenho geral e se estiver a utilizar a predefinição de Runtime de integração do Azure, poderá ter de adicionar a 5 minutos do tempo de cópia de segurança de rotação de cluster também.

![Monitorização de fluxo de dados](media/data-flow/mon003.png "3 do Monitor de fluxo de dados")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Otimizar para base de dados SQL do Azure e o armazém de dados SQL do Azure

![Parte de origem](media/data-flow/sourcepart3.png "parte de origem")

### <a name="partition-your-source-data"></a>Particionar os dados de origem

* Vá para "Otimizar" e selecione "Origem". Defina uma coluna de tabela específica ou um tipo numa consulta.
* Se escolheu "coluna", em seguida, escolha a coluna de partição.
* Além disso, defina o número máximo de ligações ao seu Azure SQL DB. Pode experimentar uma configuração superior para obter ligações paralelas a sua base de dados. No entanto, alguns casos poderão resultar num desempenho mais rápido com um número limitado de ligações.
* Não é necessário que as suas tabelas de base de dados de origem ser particionados.
* A definição de uma consulta em sua transformação de origem que corresponde ao esquema de particionamento da sua tabela de base de dados, permitirá que o motor de base de dados de origem tirar partido de eliminação de partições.
* Se a sua origem já não for particionada, o ADF continuar a utilizar o ambiente de transformação do Spark com base na chave de que seleciona a transformação de origem de criação de partições de dados.

### <a name="set-batch-size-and-query-on-source"></a>Definir o tamanho de lote e a consulta na origem

![Origem](media/data-flow/source4.png "origem")

* Definir o tamanho do lote instruirá o ADF para armazenar dados em conjuntos na memória em vez de linha por linha. É uma definição opcional e pode ficar sem recursos em nós de computação se eles não têm a capacidade adequadamente.
* Definir uma consulta pode permitir-lhe filtrar o direito de linhas na origem antes de chegarem até mesmo para o fluxo de dados para processamento, o que pode fazer com que a aquisição de dados inicial mais rápida.
* Se usar uma consulta, pode adicionar as sugestões de consulta opcionais para a BD SQL do Azure, ou seja, READ UNCOMMITTED

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>Definir o nível de isolamento em definições da origem de transformação para conjuntos de dados SQL

* Read uncommitted fornecerá resultados de consulta mais rápidos sobre transformação de origem

![Nível de isolamento](media/data-flow/isolationlevel.png "nível de isolamento")

### <a name="set-sink-batch-size"></a>Defina o tamanho de lote de sink

![Sink](media/data-flow/sink4.png "Sink")

* Para evitar o processamento de linha por linha dos seus fluxos de dados, defina o "tamanho do lote" nas definições de sink para a BD SQL do Azure. Isso instruirá o que ADF para a base de dados do processo grava em lotes com base no tamanho fornecido.

### <a name="set-partitioning-options-on-your-sink"></a>Definir opções de sink de criação de partições

* Mesmo que não tenha os dados particionados de suas tabelas de BD SQL do Azure de destino, vá até a guia de Otimização e defina a criação de partições.
* Com muita frequência, simplesmente dizer ADF para usar Round Robin, criação de partições em clusters de execução do Spark resultados no carregamento em vez de forçar a todas as ligações de uma única partição/nó de dados muito mais rápidos.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Aumentar o tamanho do seu motor de computação no Runtime de integração do Azure

![Novo Runtime de integração](media/data-flow/ir-new.png "novo Runtime de integração")

* Aumente o número de núcleos, o que irá aumentar o número de nós e fornecer-lhe maior potência de processamento para consultar e escrever para a BD SQL do Azure.
* Experimente as opções "Com otimização de computação" e "Com otimização de memória" para aplicar mais recursos para os nós de computação.

### <a name="unit-test-and-performance-test-with-debug"></a>Teste de unidade e de teste de desempenho com depuração

* Quando os fluxos de dados testes de unidade, definido no botão "Dados de fluxo Debug" on.
* Dentro do designer fluxo de dados, utilize o separador de pré-visualização de dados em transformações para ver os resultados da sua lógica de transformação.
* Teste de unidade de seus dados, fluxos de estruturador do pipeline, colocando uma atividade de fluxo de dados sobre o design de pipeline baseadas em telas e utilize o botão de "Debug" para testar.
* Teste no modo de depuração funcionam num ambiente de cluster começando em direto sem a necessidade de esperar por uma rotação de cluster de just-in-time-up.

### <a name="disable-indexes-on-write"></a>Desativar os índices na escrita
* Utilize uma atividade de procedimento armazenado de pipeline ADF antes de sua atividade de fluxo de dados de mensagens em fila que desativa a índices em suas tabelas de destino que estão sendo gravadas do coletor.
* Após a sua atividade de fluxo de dados, adicione outra atividade de procedimento armazenado que ativado desses índices.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Aumentar o tamanho do seu Azure SQL DB
* Agendar um redimensionamento de sua origem e sink BD SQL do Azure antes de sua execução limita o seu pipeline para aumentar o débito e minimizar a limitação do Azure, assim que tiver de DTU.
* Depois de concluída a execução de pipeline, que pode redimensionar as bases de dados novamente para a sua velocidade de execução normal.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Otimizando o armazém de dados SQL do Azure

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>Usar a transição para carregar dados em massa através do Polybase

* Para evitar o processamento de linha por linha dos seus fluxos de dados, defina a opção de "Transição" nas definições de Sink para que o ADF pode tirar partido do Polybase para evitar inserções de linha por linha no armazém de dados. Isso instruirá o ADF para utilizar o Polybase para que os dados podem ser carregados em massa.
* Quando executa sua atividade de fluxo de dados de um pipeline, com teste ativada, terá de selecionar a localização de armazenamento de BLOBs dos dados do testes para carregamento em massa.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Aumentar o tamanho do seu Azure SQL DW

* Agendar um redimensionamento de sua origem e sink do Azure SQL DW antes de executar o seu pipeline para aumentar o débito e minimizar a limitação do Azure, assim que atingir os limites DWU.

* Depois de concluída a execução de pipeline, que pode redimensionar as bases de dados novamente para a sua velocidade de execução normal.

## <a name="optimize-for-files"></a>Otimizar para ficheiros

* Pode controlar quantas partições que utilizarão o ADF. Em cada transformação de origem e Sink, bem como em cada transformação individual, pode definir um esquema de particionamento. Para ficheiros mais pequenos, pode achar selecionar "Partição única", por vezes, pode trabalhar melhor e mais rápido do que perguntar Spark para particionar os ficheiros pequenos.
* Se não tiver informações suficientes sobre os seus dados de origem, pode escolher "Round Robin" criação de partições e definir o número de partições.
* Se explorar os seus dados e constatar que tem colunas que podem ser as chaves de hash boa, utilize o opção de criação de partições de Hash.

### <a name="file-naming-options"></a>Opções de nomenclatura de ficheiros

* A natureza do padrão de escrever os dados transformados nos fluxos de dados de mapeamento ADF é escrever um conjunto de dados que tenha um Blob ou um serviço ligado do ADLS. Deve definir esse conjunto de dados para apontar para uma pasta ou contentor, não é um ficheiro com nome.
* A utilização de fluxos de dados do Azure Databricks Spark para execução, o que significa que a saída será dividida ao longo de vários ficheiros com base no padrão ou Spark criação de partições ou a criação de partições de esquema que escolheu explicitamente.
* Uma operação muito comum nos fluxos de dados do ADF é escolher "De saída para o arquivo único", para que todos os ficheiros de parte de saída são mesclados em conjunto para um ficheiro de saída individual.
* No entanto, esta operação requer que a saída reduz a uma única partição num nó de cluster único.
* Tenha isso em mente ao escolher esta opção popular. Pode executar fora dos recursos de nó de cluster se está a combinar muitos arquivos de origem grande numa partição de ficheiro de saída individual.
* Para evitar o esgotamento de recursos de nó de computação, pode manter o padrão ou o esquema de particionamento explícita no ADF, o que otimiza o desempenho, e, em seguida, adicionar uma atividade de cópia subsequente no pipeline que une todos da parte ficheiros a partir da pasta de saída para um único novo ficheiro. Essencialmente, essa técnica separa a ação de transformação de mesclagem de arquivos e alcança o mesmo resultado que a definição "para o único ficheiro de saída".

### <a name="looping-through-file-lists"></a>Looping através de listas de ficheiro

Na maioria dos casos, os dados fluem no ADF executará melhor a partir de um pipeline que permite a transformação de origem de fluxo de dados para fazer a iteração ao longo de vários ficheiros. Em outras palavras, é preferível utilizar carateres universais ou fluxo que para iterar sobre uma grande lista de ficheiros de uso de ForEach no pipeline, chamar um executar fluxo de dados em cada iteração de listas de ficheiro dentro da sua origem de dados. O processo de fluxo de dados serão executadas mais rapidamente, permitindo que o loop ocorrer dentro do fluxo de dados.

Por exemplo, se eu tiver uma lista de ficheiros de dados a partir de Julho de 2019 que gostaria de processar numa pasta no armazenamento de BLOBs, seria um melhor desempenho para chamar uma atividade de fluxo de dados de executar uma vez a partir do seu pipeline e utilizar um caráter universal na sua origem, como este :

```DateFiles/*_201907*.txt```

Isso executará melhor do que o Blob Store num pipeline que, em seguida, itera em todos os ficheiros correspondentes usando um ForEach com uma atividade executar fluxo de dados dentro de uma pesquisa.

## <a name="next-steps"></a>Passos Seguintes

Consulte os outros artigos fluxo de dados relacionados com desempenho:

- [Separador otimizar o fluxo de dados](concepts-data-flow-optimize-tab.md)
- [Atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
- [Monitorizar o desempenho do fluxo de dados](concepts-data-flow-monitoring.md)
