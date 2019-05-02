---
title: Executar programas MapReduce personalizados - Azure HDInsight
description: Quando e como executar programas MapReduce personalizados no HDInsight.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: 5ed82fc21aedc9af394922059859f81cfba1867e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713100"
---
# <a name="run-custom-mapreduce-programs"></a>Executar programas MapReduce personalizados

Apache sistemas de macrodados baseada no Hadoop, como o HDInsight permitem o processamento de dados utilizando uma ampla gama de ferramentas e tecnologias. A tabela seguinte descreve as principais vantagens e considerações para cada um deles.

| Mecanismo de consulta | Vantagens | Considerações |
| --- | --- | --- |
| **Apache Hive com o HiveQL** | <ul><li>Uma excelente solução para processamento em lotes e análise de grandes quantidades de dados imutáveis, para resumo de dados e para sobre como consultar a pedido. Ele usa uma sintaxe semelhante a SQL familiar.</li><li>Pode ser utilizado para produzir persistentes tabelas de dados que podem ser facilmente particionados e indexados.</li><li>Várias tabelas externas e as vistas podem ser criadas sobre os mesmos dados.</li><li>Ele oferece suporte uma implementação do armazém de dados simples que fornece capacidades de escalamento horizontal e a tolerância a falhas em massa para o armazenamento de dados e processamento.</li></ul> | <ul><li>Ele requer que os dados de origem ter, pelo menos, algumas estruturas de identificação.</li><li>Não é adequado para consultas em tempo real e atualizações de nível de linha. Melhor é utilizado para tarefas do batch ao longo de grandes conjuntos de dados.</li><li>Não pode ser capaz de realizar alguns tipos de tarefas de processamento complexo.</li></ul> |
| **Utilizar o Pig Latin do Apache Pig** | <ul><li>Uma excelente solução para manipulação de dados, como conjuntos, mesclagem e filtragem de conjuntos de dados, aplicar as funções nos registos ou grupos de registos e para reestruturação dados através da definição de colunas, pelos valores de agrupamento ou convertendo colunas em linhas.</li><li>Ele pode usar uma abordagem baseada em fluxo de trabalho como uma seqüência de operações nos dados.</li></ul> | <ul><li>Utilizadores SQL talvez ache que PIg Latin é menos familiares e mais difícil de usar do que o HiveQL.</li><li>A saída padrão é, normalmente, um arquivo de texto e por isso, pode ser mais difícil de usar com ferramentas de visualização de como o Excel. Normalmente, irá camada uma tabela do Hive através de saída.</li></ul> |
| **Mapeamento/redução personalizado** | <ul><li>Ele fornece controle total sobre o mapa e reduzir fases e execução.</li><li>Ele permite consultas para ser otimizada para obter o máximo desempenho do cluster ou para minimizar a carga nos servidores e a rede.</li><li>Os componentes podem ser escritos em diversas linguagens conhecidas.</li></ul> | <ul><li>É mais difícil do que a utilizar o Pig ou Hive porque tem de criar seu próprio mapa e reduzir os componentes.</li><li>Os processos que exigem a associar a conjuntos de dados são mais difíceis de implementar.</li><li>Apesar de estruturas de teste estão disponíveis, depuração de código é mais complexo do que uma aplicação normal porque o código é executado como uma tarefa do batch sob o controle do que o Programador de tarefas do Hadoop.</li></ul> |
| **O Apache HCatalog** | <ul><li>Ele abstrai os detalhes do caminho de armazenamento, facilitando a administração e eliminando a necessidade de que os usuários saibam onde os dados são armazenados.</li><li>Ele permite que a notificação de eventos, como disponibilidade de dados, permitindo que outras ferramentas, como o Oozie para detectar quando ocorreram operações.</li><li>Ele expõe uma vista de dados, incluindo a criação de partições por chave, relacional e faz com que os dados mais fácil acesso.</li></ul> | <ul><li>Ele oferece suporte a RCFile, texto CSV, texto JSON, SequenceFile e ORC formatos de arquivo por padrão, mas poderá ter de escrever um SerDe personalizado para outros formatos.</li><li>HCatalog não é thread-safe.</li><li>Existem algumas restrições sobre os tipos de dados para colunas ao usar o carregador de HCatalog em Pig scripts. Para obter mais informações, consulte [tipos de dados de HCatLoader](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) na documentação do Apache HCatalog.</li></ul> |

Normalmente, utiliza a essas abordagens que podem fornecer resultados que necessitar de mais simples. Por exemplo, poderá obter esses resultados com o uso apenas do Hive, mas para cenários mais complexos poderá ter de utilizar o Pig, ou até mesmo escrever seu próprio mapa e reduzir os componentes. Também pode decidir, depois de testar do Hive ou Pig, esse mapa personalizado e reduzir componentes podem fornecer um melhor desempenho, que lhe permite ajustar e otimizar o processamento.

## <a name="custom-mapreduce-components"></a>Componentes de mapeamento/redução personalizado

Código de mapeamento/redução consiste em duas funções separadas, implementadas como **mapa** e **reduzir** componentes. O **mapa** componente é executado em paralelo em vários nós de cluster, cada nó de aplicar o mapeamento para o subconjunto do nó dos dados. O **reduzir** componente collates e resume os resultados de todas as funções de mapa. Para obter mais informações sobre esses dois componentes, consulte [utilizar o MapReduce no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

Na maioria dos cenários de processamento do HDInsight é mais simples e eficiente para utilizar uma abstração de nível mais alto, como o Pig ou do Hive. Também pode criar o mapa personalizado e reduzir os componentes para utilização nos scripts de Hive para executar o processamento mais sofisticado.

Componentes personalizados mapeamento/redução geralmente são escritos em Java. Hadoop fornece uma interface de transmissão em fluxo que também permite que os componentes a ser utilizado, que são desenvolvidas em outras linguagens, tais como C#, F#, Visual Basic, Python e JavaScript.

* Para obter instruções sobre como desenvolver programas Java MapReduce personalizados, consulte [programas Java MapReduce desenvolver para o Hadoop no HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Considere a criação de seu próprio mapa e reduzir os componentes para as seguintes condições:

* Precisa processar dados que é completamente não estruturados através da análise dos dados e usando uma lógica personalizada para obter informações estruturadas do mesmo.
* Deseja realizar tarefas complexas que são de difícil (ou impossível) para expressar em Pig ou Hive, sem recorrer a criação de uma UDF. Por exemplo, poderá ter de utilizar um serviço de geocodificação externo para converter a latitude e longitude coordenadas ou endereços IP nos dados de origem para nomes de localização geográfica.
* Quer reutilizar seu código .NET, Python ou JavaScript existente em componentes de mapeamento/redução usando o interface de transmissão em fluxo Hadoop.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Carregar e executar o programa MapReduce personalizado

Os programas MapReduce mais comuns são escritos em Java e compilados para um ficheiro jar.

1. Depois de ter desenvolvido, compilado e testado o seu programa MapReduce, utilize o `scp` comando para carregar o ficheiro jar ao nó principal.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Substitua **nome de utilizador** com a conta de utilizador SSH para o seu cluster. Substitua **CLUSTERNAME** com o nome do cluster. Se utilizou uma palavra-passe para proteger a conta SSH, lhe for pedido para introduzir a palavra-passe. Se utilizou um certificado, poderá ter de utilizar o `-i` parâmetro para especificar o ficheiro de chave privada.

2. Ligar ao cluster através de [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Na sessão SSH, execute o seu programa MapReduce no YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Este comando submete a tarefa de MapReduce para YARN. O ficheiro de entrada é `/example/data/sample.log`, e o diretório de saída é `/example/data/logoutput`. O ficheiro de entrada e de quaisquer ficheiros de saída são armazenados no armazenamento padrão para o cluster.

## <a name="next-steps"></a>Passos Seguintes

* [Utilize C# com o MapReduce de transmissão em fluxo no Apache Hadoop no HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Desenvolver programas Java MapReduce para o Apache Hadoop no HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Utilizar o Azure Toolkit para Eclipse para criar aplicativos de Apache Spark para um cluster do HDInsight](../spark/apache-spark-eclipse-tool-plugin.md)
* [Utilizar Python funções definidas pelo utilizador (UDF) com o Apache Hive e Apache Pig no HDInsight](python-udf-hdinsight.md)
