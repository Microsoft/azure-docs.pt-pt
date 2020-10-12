---
title: Executar programas personalizados MapReduce - Azure HDInsight
description: Quando e como executar programas apache mapreduce personalizados em clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 1dcc2a944fc527e4cbc8c7c1072503377ecb5798
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89505272"
---
# <a name="run-custom-mapreduce-programs"></a>Executar programas MapReduce personalizados

Os grandes sistemas de dados baseados em Apache Hadoop, como o HDInsight, permitem o processamento de dados utilizando uma vasta gama de ferramentas e tecnologias. A tabela seguinte descreve as principais vantagens e considerações para cada um.

| Mecanismo de consulta | Vantagens | Considerações |
| --- | --- | --- |
| **Colmeia Apache usando HiveQL** | <ul><li>Uma excelente solução para o processamento e análise de grandes quantidades de dados imutáveis, para a resumo de dados e para consulta a pedido. Usa uma sintaxe familiar semelhante ao SQL.</li><li>Pode ser usado para produzir tabelas persistentes de dados que podem ser facilmente divididos e indexados.</li><li>Várias tabelas e vistas externas podem ser criadas ao longo dos mesmos dados.</li><li>Suporta uma simples implementação de armazém de dados que fornece capacidades massivas de dimensionamento e tolerância a falhas para armazenamento e processamento de dados.</li></ul> | <ul><li>Requer que os dados de origem tenham pelo menos alguma estrutura identificável.</li><li>Não é adequado para consultas em tempo real e atualizações de nível de linha. É melhor usado para trabalhos de lote sobre grandes conjuntos de dados.</li><li>Pode não ser capaz de realizar alguns tipos de tarefas complexas de processamento.</li></ul> |
| **Porco Apache usando o latim do porco** | <ul><li>Uma excelente solução para manipular dados como conjuntos, fundir e filtrar conjuntos de dados, aplicar funções a registos ou grupos de registos, e para reestruturar dados definindo colunas, por valores de agrupamento ou convertendo colunas em linhas.</li><li>Pode usar uma abordagem baseada no fluxo de trabalho como uma sequência de operações em dados.</li></ul> | <ul><li>Os utilizadores de SQL podem achar que o pig latin é menos familiar e mais difícil de usar do que o HiveQL.</li><li>A saída predefinitiva é geralmente um ficheiro de texto e por isso pode ser mais difícil de usar com ferramentas de visualização como o Excel. Normalmente, colocará uma mesa de colmeia sobre a saída.</li></ul> |
| **Mapa personalizado/reduzir** | <ul><li>Fornece controlo total sobre o mapa e reduz fases e execução.</li><li>Permite que as consultas sejam otimizadas para obter o máximo desempenho do cluster, ou para minimizar a carga nos servidores e na rede.</li><li>Os componentes podem ser escritos numa gama de línguas bem conhecidas.</li></ul> | <ul><li>É mais difícil do que usar Porco ou Colmeia porque você deve criar o seu próprio mapa e reduzir componentes.</li><li>Os processos que requerem a junção de conjuntos de dados são mais difíceis de implementar.</li><li>Apesar de existirem quadros de teste disponíveis, o código de depurar é mais complexo do que uma aplicação normal, porque o código funciona como um lote de trabalho sob o controlo do programador de emprego hadoop.</li></ul> |
| **Apache HCatalog** | <ul><li>Abstrai os detalhes do caminho do armazenamento, facilitando a administração e eliminando a necessidade de os utilizadores saberem onde os dados são armazenados.</li><li>Permite a notificação de eventos como a disponibilidade de dados, permitindo que outras ferramentas, como a Oozie, detetem quando as operações ocorreram.</li><li>Expõe uma visão relacional dos dados, incluindo a divisão por chave, e facilita o acesso dos dados.</li></ul> | <ul><li>Suporta formatos de ficheiros RCFile, CSV, texto JSON, SequenceFile e orc por padrão, mas pode precisar de escrever um SerDe personalizado para outros formatos.</li><li>O HCatalog não é seguro.</li><li>Existem algumas restrições sobre os tipos de dados para colunas ao utilizar o carregador HCatalog em scripts de porco. Para obter mais informações, consulte [os tipos de dados HCatLoader](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) na documentação Apache HCatalog.</li></ul> |

Normalmente, você usa a mais simples destas abordagens que podem fornecer os resultados que você precisa. Por exemplo, você pode ser capaz de alcançar tais resultados usando apenas Hive, mas para cenários mais complexos você pode precisar usar o Porco, ou até mesmo escrever o seu próprio mapa e reduzir componentes. Você também pode decidir, após experiência com Hive ou Porco, que o mapa personalizado e os componentes de redução podem fornecer um melhor desempenho, permitindo-lhe afinar e otimizar o processamento.

## <a name="custom-mapreduce-components"></a>Mapa personalizado/componentes de redução

O código de mapa/redução consiste em duas funções separadas implementadas como **mapa** e **componentes de redução.** O componente do **mapa** é executado em paralelo em múltiplos nós de cluster, cada nó aplicando o mapeamento ao subconjunto do próprio nó dos dados. O componente **de redução** colide e resume os resultados de todas as funções do mapa. Para obter mais informações sobre estes dois componentes, consulte [Use MapReduce in Hadoop em HDInsight](hdinsight-use-mapreduce.md).

Na maioria dos cenários de processamento de HDInsight, é mais simples e eficiente usar uma abstração de nível mais alto, como Porco ou Colmeia. Também pode criar mapa personalizado e reduzir componentes para uso dentro dos scripts da Hive para realizar um processamento mais sofisticado.

Os componentes de mapa/redução personalizados são normalmente escritos em Java. Hadoop fornece uma interface de streaming que também permite a utilização de componentes que são desenvolvidos em outros idiomas como C#, F#, Visual Basic, Python e JavaScript.

* Para uma passagem pelo desenvolvimento de programas personalizados java MapReduce, consulte [programas Develop Java MapReduce para Hadoop em HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Considere criar o seu próprio mapa e reduzir os componentes para as seguintes condições:

* É necessário processar dados que não estão estruturados através da análise dos dados e da lógica personalizada para obter informação estruturada a partir dos mesmos.
* Você quer executar tarefas complexas que são difíceis (ou impossíveis) de expressar em Porco ou Colmeia sem recorrer à criação de um UDF. Por exemplo, poderá ser necessário utilizar um serviço de geocodagem externo para converter coordenadas de latitude e longitude ou endereços IP nos dados de origem para nomes de localização geográfica.
* Pretende reutilizar o código .NET, Python ou JavaScript existente em componentes de mapa/redução utilizando a interface de streaming Hadoop.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Faça upload e execute o seu programa personalizado MapReduce

Os programas MapReduce mais comuns são escritos em Java e compilados num ficheiro de frascos.

1. Depois de desenvolver, compilar e testar o seu programa MapReduce, use o comando para enviar o `scp` seu ficheiro de frasco para o headnode.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Substitua o CLUSTERNAME pelo nome do cluster. Se usou uma palavra-passe para proteger a conta SSH, é-lhe pedido que introduza a palavra-passe. Se usou um certificado, poderá ter de utilizar o `-i` parâmetro para especificar o ficheiro de chave privada.

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sessão SSH, execute o seu programa MapReduce através do YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Este comando submete o trabalho de MapReduce ao YARN. O ficheiro de entrada é `/example/data/sample.log` , e o diretório de saída é `/example/data/logoutput` . O ficheiro de entrada e quaisquer ficheiros de saída são armazenados no armazenamento predefinido para o cluster.

## <a name="next-steps"></a>Passos seguintes

* [Use C# com mapReduce streaming em Apache Hadoop em HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Desenvolver programas Java MapReduce para Apache Hadoop em HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Use o kit de ferramentas Azure para eclipse para criar aplicações Apache Spark para um cluster HDInsight](../spark/apache-spark-eclipse-tool-plugin.md)
* [Use funções definidas do utilizador Python (UDF) com a Colmeia Apache e o Porco Apache em HDInsight](python-udf-hdinsight.md)
