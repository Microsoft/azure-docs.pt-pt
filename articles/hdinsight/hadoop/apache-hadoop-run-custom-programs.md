---
title: Executar programas MapReduce personalizados – Azure HDInsight
description: Quando e como executar programas do Apache MapReduce personalizados em clusters do Azure HDInsight.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: 305eefbaa674e414ab8134986e6cd526abe8208e
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810753"
---
# <a name="run-custom-mapreduce-programs"></a>Executar programas MapReduce personalizados

Os sistemas de Big Data baseados em Apache Hadoop, como o HDInsight, permitem o processamento de dados usando uma ampla variedade de ferramentas e tecnologias. A tabela a seguir descreve as principais vantagens e considerações para cada uma delas.

| Mecanismo de consulta | Vantagens | Considerações |
| --- | --- | --- |
| **Apache Hive usando HiveQL** | <ul><li>Uma excelente solução para processamento em lote e análise de grandes quantidades de dados imutáveis, para Resumo de dados e para consulta sob demanda. Ele usa uma sintaxe familiar semelhante ao SQL.</li><li>Ele pode ser usado para produzir tabelas persistentes de dados que podem ser facilmente particionados e indexados.</li><li>Várias tabelas externas e exibições podem ser criadas nos mesmos dados.</li><li>Ele dá suporte a uma implementação simples de data warehouse que fornece recursos maciços de expansão e tolerância a falhas para armazenamento e processamento de dados.</li></ul> | <ul><li>Ele requer que os dados de origem tenham pelo menos uma estrutura de identificação.</li><li>Ele não é adequado para consultas em tempo real e atualizações em nível de linha. Ele é melhor usado para trabalhos em lotes em grandes conjuntos de dados.</li><li>Talvez não seja possível executar alguns tipos de tarefas de processamento complexas.</li></ul> |
| **Apache Pig usando Pig Latin** | <ul><li>Uma solução excelente para a manipulação de dados como conjuntos, mesclagem e filtragem de DataSets, aplicação de funções a registros ou grupos de registros e para reestruturação de dados definindo colunas, agrupando valores ou convertendo colunas em linhas.</li><li>Ele pode usar uma abordagem baseada em fluxo de trabalho como uma sequência de operações em dados.</li></ul> | <ul><li>Os usuários do SQL podem achar que o Pig Latin é menos familiar e mais difícil de usar do que o HiveQL.</li><li>A saída padrão é geralmente um arquivo de texto e, portanto, pode ser mais difícil de usar com ferramentas de visualização, como o Excel. Normalmente, você fará a camada de uma tabela do hive na saída.</li></ul> |
| **Mapeamento/redução personalizado** | <ul><li>Ele fornece controle total sobre o mapa e reduz as fases e a execução.</li><li>Ele permite que as consultas sejam otimizadas para alcançar o desempenho máximo do cluster ou para minimizar a carga nos servidores e na rede.</li><li>Os componentes podem ser escritos em uma variedade de linguagens conhecidas.</li></ul> | <ul><li>É mais difícil do que usar o Pig ou o hive porque você deve criar seu próprio mapa e reduzir os componentes.</li><li>Processos que exigem a junção de conjuntos de dados são mais difíceis de implementar.</li><li>Embora existam estruturas de teste disponíveis, o código de depuração é mais complexo do que um aplicativo normal, pois o código é executado como um trabalho em lotes sob o controle do Agendador de trabalhos do Hadoop.</li></ul> |
| **Apache HCatalog** | <ul><li>Ele abstrai os detalhes do caminho do armazenamento, facilitando a administração e removendo a necessidade de que os usuários saibam onde os dados são armazenados.</li><li>Ele habilita a notificação de eventos, como disponibilidade de dados, permitindo que outras ferramentas, como o Oozie, detectem quando as operações ocorreram.</li><li>Ele expõe uma exibição relacional dos dados, incluindo o particionamento por chave e torna os dados fáceis de acessar.</li></ul> | <ul><li>Ele dá suporte a formatos de arquivo RCFile, CSV, texto JSON, Sequencefile e ORC por padrão, mas talvez seja necessário gravar um SerDe personalizado para outros formatos.</li><li>HCatalog não é thread-safe.</li><li>Há algumas restrições sobre os tipos de dados para colunas ao usar o carregador HCatalog em scripts Pig. Para obter mais informações, consulte [tipos de dados HCatLoader](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) na documentação do Apache HCatalog.</li></ul> |

Normalmente, você usa a mais simples dessas abordagens que podem fornecer os resultados necessários. Por exemplo, você pode conseguir obter esses resultados usando apenas o Hive, mas, para cenários mais complexos, talvez seja necessário usar o Pig ou até mesmo escrever seu próprio mapa e reduzir os componentes. Você também pode decidir, depois de experimentar o hive ou o Pig, que o mapa personalizado e reduzir os componentes podem fornecer melhor desempenho, permitindo ajustar e otimizar o processamento.

## <a name="custom-mapreduce-components"></a>Mapear/reduzir componentes personalizados

O código de mapeamento/redução consiste em duas funções separadas implementadas como **mapear** e **reduzir** componentes. O componente de **mapa** é executado em paralelo em vários nós de cluster, cada nó aplicando o mapeamento ao próprio subconjunto dos dados do nó. O componente de **redução** agrupa e resume os resultados de todas as funções de mapa. Para obter mais informações sobre esses dois componentes, consulte [usar o MapReduce no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

Na maioria dos cenários de processamento do HDInsight, é mais simples e mais eficiente usar uma abstração de nível superior, como Pig ou Hive. Você também pode criar um mapa personalizado e reduzir os componentes para uso em scripts do hive para executar um processamento mais sofisticado.

Componentes de mapeamento/redução personalizados normalmente são escritos em Java. O Hadoop fornece uma interface de streaming que também permite que os componentes sejam usados em outras linguagens C#, como F#,, Visual Basic, Python e JavaScript.

* Para obter instruções sobre como desenvolver programas personalizados do MapReduce em Java, consulte [desenvolver programas Java MapReduce para Hadoop no HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Considere criar seu próprio mapa e reduzir os componentes para as seguintes condições:

* Você precisa processar dados que são completamente não estruturados analisando os dados e usando a lógica personalizada para obter informações estruturadas a partir dele.
* Você deseja executar tarefas complexas que são difíceis (ou impossíveis) de expressar em Pig ou Hive sem recorrer à criação de um UDF. Por exemplo, talvez seja necessário usar um serviço de geocodificação externo para converter coordenadas de latitude e longitude ou endereços IP nos dados de origem em nomes de localização geográfica.
* Você deseja reutilizar seu código .NET, Python ou JavaScript existente em mapear/reduzir componentes usando a interface de streaming do Hadoop.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Carregar e executar o programa MapReduce personalizado

Os programas MapReduce mais comuns são escritos em Java e compilados em um arquivo jar.

1. Depois de desenvolver, compilar e testar seu programa MapReduce, use o `scp` comando para carregar o arquivo JAR para o cabeçalho.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Substitua **username** pela conta de usuário SSH para o cluster. Substitua **ClusterName** pelo nome do cluster. Se você usou uma senha para proteger a conta SSH, será solicitado a inserir a senha. Se você usou um certificado, talvez seja necessário usar o `-i` parâmetro para especificar o arquivo de chave privada.

2. Conecte-se ao cluster usando [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Na sessão SSH, execute o programa MapReduce por meio do YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Esse comando envia o trabalho MapReduce para YARN. O arquivo de entrada `/example/data/sample.log`é, e o diretório de `/example/data/logoutput`saída é. O arquivo de entrada e todos os arquivos de saída são armazenados no armazenamento padrão do cluster.

## <a name="next-steps"></a>Passos Seguintes

* [Usar C# com streaming MapReduce no Apache Hadoop no HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Desenvolver programas Java MapReduce para Apache Hadoop no HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Usar Azure Toolkit for Eclipse para criar aplicativos Apache Spark para um cluster HDInsight](../spark/apache-spark-eclipse-tool-plugin.md)
* [Usar funções definidas pelo usuário do Python (UDF) com Apache Hive e Apache Pig no HDInsight](python-udf-hdinsight.md)
