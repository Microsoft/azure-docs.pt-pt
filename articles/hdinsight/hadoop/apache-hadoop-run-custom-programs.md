---
title: Executar programas mapReduce personalizados - Azure HDInsight
description: Quando e como executar programas personalizados apache MapReduce em clusters Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 78623f738285e781cb561a3844db8fbf37226929
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645026"
---
# <a name="run-custom-mapreduce-programs"></a>Executar programas MapReduce personalizados

Os sistemas de big data baseados em Apache Hadoop, como o HDInsight, permitem o processamento de dados utilizando uma vasta gama de ferramentas e tecnologias. A tabela seguinte descreve as principais vantagens e considerações para cada um.

| Mecanismo de consulta | Vantagens | Considerações |
| --- | --- | --- |
| **Colmeia Apache usando HiveQL** | <ul><li>Uma excelente solução para o processamento e análise de grandes quantidades de dados imutáveis, para resumição de dados e para consulta a pedido. Usa uma sintaxe familiar semelhante à SQL.</li><li>Pode ser usado para produzir tabelas persistentes de dados que podem ser facilmente divididos e indexados.</li><li>Várias tabelas e vistas externas podem ser criadas sobre os mesmos dados.</li><li>Suporta uma simples implementação de armazéns de dados que fornece capacidades de escala massiva e tolerância a falhas para armazenamento e processamento de dados.</li></ul> | <ul><li>Requer que os dados de origem tenham pelo menos alguma estrutura identificável.</li><li>Não é adequado para consultas em tempo real e atualizações de nível de linha. É melhor usado para trabalhos de lote sobre grandes conjuntos de dados.</li><li>Pode não ser capaz de realizar alguns tipos de tarefas complexas de processamento.</li></ul> |
| **Porco Apache usando o porco latino** | <ul><li>Uma excelente solução para manipular dados como conjuntos, fundir e filtrar conjuntos de dados, aplicar funções a registos ou grupos de registos, e para reestruturar dados através da definição de colunas, por valores de agrupamento ou pela conversão de colunas em linhas.</li><li>Pode usar uma abordagem baseada no fluxo de trabalho como uma sequência de operações em dados.</li></ul> | <ul><li>Os utilizadores de SQL podem achar que o Pig Latin é menos familiar e mais difícil de usar do que o HiveQL.</li><li>A saída predefinida é geralmente um ficheiro de texto e por isso pode ser mais difícil de usar com ferramentas de visualização como o Excel. Normalmente, você colocará uma mesa de Colmeia sobre a saída.</li></ul> |
| **Mapa/redução personalizado** | <ul><li>Fornece controlo total sobre o mapa e reduz as fases e a execução.</li><li>Permite otimizar consultas para obter o máximo desempenho do cluster, ou para minimizar a carga nos servidores e na rede.</li><li>Os componentes podem ser escritos numa gama de línguas bem conhecidas.</li></ul> | <ul><li>É mais difícil do que usar porco ou colmeia porque você deve criar o seu próprio mapa e reduzir os componentes.</li><li>Os processos que exigem a junção de conjuntos de dados são mais difíceis de implementar.</li><li>Embora existam quadros de teste disponíveis, o código de depuração é mais complexo do que uma aplicação normal porque o código funciona como um trabalho de lote sob o controlo do programador de emprego hadoop.</li></ul> |
| **Apache HCatalog** | <ul><li>Resumia os detalhes do caminho do armazenamento, facilitando a administração e eliminando a necessidade de os utilizadores saberem onde os dados são armazenados.</li><li>Permite a notificação de eventos como a disponibilidade de dados, permitindo que outras ferramentas como a Oozie detetem quando as operações ocorreram.</li><li>Expõe uma visão relacional dos dados, incluindo a divisão por chave, e facilita o acesso dos dados.</li></ul> | <ul><li>Suporta os formatos de ficheiros RCFile, CSV, Texto JSON, SequenceFile e ORC por padrão, mas poderá ser necessário escrever um SerDe personalizado para outros formatos.</li><li>HCatalog não é seguro para fios.</li><li>Existem algumas restrições nos tipos de dados para colunas ao utilizar o carregador HCatalog em scripts Pig. Para mais informações, consulte os Tipos de [Dados HCatLoader](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) na documentação apache HCatalog.</li></ul> |

Normalmente, você usa a mais simples destas abordagens que podem fornecer os resultados que você precisa. Por exemplo, você pode ser capaz de obter tais resultados usando apenas hive, mas para cenários mais complexos você pode precisar usar Porco, ou até mesmo escrever seu próprio mapa e reduzir componentes. Você também pode decidir, depois de experimentar com Hive ou Porco, que o mapa personalizado e reduzir os componentes pode fornecer um melhor desempenho, permitindo-lhe afinar e otimizar o processamento.

## <a name="custom-mapreduce-components"></a>Mapa personalizado/reduzir componentes

O código mapa/redução consiste em duas funções separadas implementadas como **mapa** e **reduzir** componentes. O componente do **mapa** é executado em paralelo em vários nós de cluster, cada nó aplicando o mapeamento ao próprio subconjunto dos dados do nó. O componente **de redução** colide e resume os resultados de todas as funções do mapa. Para obter mais informações sobre estes dois componentes, consulte [Use MapReduce in Hadoop no HDInsight](hdinsight-use-mapreduce.md).

Na maioria dos cenários de processamento do HDInsight, é mais simples e eficiente usar uma abstração de nível superior, como porco ou colmeia. Também pode criar um mapa personalizado e reduzir componentes para uso dentro dos scripts da Hive para realizar um processamento mais sofisticado.

Os componentes personalizados do mapa/redução são normalmente escritos em Java. Hadoop fornece uma interface de streaming que também permite que componentes sejam usados que são desenvolvidos em outros idiomas como C#, F#, Visual Basic, Python e JavaScript.

* Para um passe no desenvolvimento de programas personalizados java mapReduce, consulte [Desenvolver programas Java MapReduce para Hadoop no HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Considere criar o seu próprio mapa e reduzir componentes para as seguintes condições:

* É necessário processar dados completamente desestruturados analisando os dados e utilizando lógica personalizada para obter informações estruturadas a partir dos mesmos.
* Pretende realizar tarefas complexas que são difíceis (ou impossíveis) de expressar em Porco ou Colmeia sem recorrer à criação de uma UDF. Por exemplo, pode ser necessário utilizar um serviço de geocodificação externo para converter coordenadas de latitude e longitude ou endereços IP nos dados de origem para nomes de localização geográfica.
* Pretende reutilizar o código .NET, Python ou JavaScript existente no mapa/reduzir componentes utilizando a interface de streaming Hadoop.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Faça upload e execute o seu programa personalizado MapReduce

Os programas MapReduce mais comuns são escritos em Java e compilados para um ficheiro de frascos.

1. Depois de desenvolver, compilar e testar o seu programa `scp` MapReduce, use o comando para carregar o seu ficheiro de jarro para o nódoo.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Substitua o CLUSTERNAME pelo nome do cluster. Se usou uma palavra-passe para proteger a conta SSH, é-lhe pedido que introduza a palavra-passe. Se usou um certificado, poderá ter `-i` de utilizar o parâmetro para especificar o ficheiro de chave privada.

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sessão SSH, execute o seu programa MapReduce através de ARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Este comando submete o trabalho MapReduce ao ARN. O ficheiro de `/example/data/sample.log`entrada é , `/example/data/logoutput`e o diretório de saída é . O ficheiro de entrada e quaisquer ficheiros de saída são armazenados no armazenamento predefinido do cluster.

## <a name="next-steps"></a>Passos seguintes

* [Use C# com MapReduce streaming no Apache Hadoop em HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Desenvolver java mapReduce programas para Apache Hadoop no HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Utilize o Kit de Ferramentas Azure para eclipse para criar aplicações Apache Spark para um cluster HDInsight](../spark/apache-spark-eclipse-tool-plugin.md)
* [Utilize funções definidas pelo utilizador Python (UDF) com hiv apache e porco Apache em HDInsight](python-udf-hdinsight.md)
