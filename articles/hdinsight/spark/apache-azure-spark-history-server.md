---
title: Servidor de histórico do Spark estendido para depurar aplicativos Spark – Azure HDInsight
description: Use o servidor de histórico do Spark estendido para depurar e diagnosticar aplicativos Spark – Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: fad2c83138f211e83e9462182d33f6169cbdb833
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968168"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Usar o servidor de histórico de Apache Spark estendido para depurar e diagnosticar Apache Spark aplicativos

Este artigo fornece orientação sobre como usar o servidor de histórico de Apache Spark estendido para depurar e diagnosticar aplicativos Spark concluídos e em execução. A extensão inclui a guia de dados e a guia gráfico e a guia diagnóstico. Na guia **dados** , os usuários podem verificar os dados de entrada e saída do trabalho do Spark. Na guia **gráfico** , os usuários podem verificar o fluxo de dados e reproduzir o grafo de trabalho. Na guia **diagnóstico** , o usuário pode referir-se à **distorção de dados**, à **distorção de tempo** e à **análise de uso de executor**.

## <a name="get-access-to-apache-spark-history-server"></a>Obter acesso ao servidor de histórico de Apache Spark

Apache Spark servidor de histórico é a interface do usuário da Web para aplicativos Spark concluídos e em execução. 

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Abra a interface do usuário da Web do servidor de histórico de Apache Spark do portal do Azure

1. No [portal do Azure](https://portal.azure.com/), abra o cluster Spark. Para obter mais informações, consulte [listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Em **links rápidos**, clique em **painel do cluster**e clique em servidor de **histórico do Spark**. Quando solicitado, insira as credenciais de administrador para o cluster Spark. 

    ![Servidor de histórico do Spark](./media/apache-azure-spark-history-server/launch-history-server.png "Servidor de histórico do Spark")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Abrir a interface do usuário da Web do servidor de histórico do Spark por URL
Abra o servidor de histórico do Spark navegando até a URL a `<ClusterName>` seguir, substitua pelo nome do cluster Spark do cliente.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

A interface do usuário da Web do servidor de histórico do Spark é semelhante a:

![Servidor de histórico do HDInsight Spark](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="data-tab-in-spark-history-server"></a>Guia dados no servidor de histórico do Spark
Selecione ID do trabalho e clique em **dados** no menu ferramenta para obter a exibição de dados.

+ Verifique as **entradas**, as **saídas**e **as operações de tabela** selecionando as guias separadamente.

    ![Guias de dados](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Copie todas as linhas clicando em **copiar**do botão.

    ![Cópia de dados](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Salve todos os dados como arquivo CSV clicando em **CSV**de botão.

    ![Salvar dados](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Pesquisar inserindo palavras-chave na **pesquisa**de campo, o resultado da pesquisa será exibido imediatamente.

    ![Pesquisa de dados](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Clique no cabeçalho da coluna para classificar a tabela, clique no sinal de adição para expandir uma linha para mostrar mais detalhes ou clique no sinal de menos para recolher uma linha.

    ![Tabela de dados](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Baixar um único arquivo clicando no botão **Download parcial** no lado direito, o arquivo selecionado será baixado para local, se o arquivo não existir mais, ele abrirá uma nova guia para mostrar as mensagens de erro.

    ![Linha de download de dados](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Copie o caminho completo ou o caminho relativo selecionando a **cópia caminho completo**, **Copie o caminho relativo** que se expande no menu de download. Para arquivos de armazenamento do Azure data Lake, **abra o Gerenciador de armazenamento do Azure** será iniciado Gerenciador de armazenamento do Azure e localize a pasta ao entrar.

    ![Caminho da cópia de dados](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Clique no número abaixo da tabela para navegar pelas páginas quando houver muitas linhas a serem exibidas em uma página. 

    ![Página de dados](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Focalize o ponto de interrogação ao lado dos dados para mostrar a dica de ferramenta ou clique no ponto de interrogação para obter mais informações.

    ![Dados mais informações](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Envie comentários com problemas clicando em **fornecer comentários**.

    ![Comentários do grafo](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="graph-tab-in-apache-spark-history-server"></a>Guia grafo no servidor de histórico de Apache Spark
Selecione ID do trabalho e, em seguida, clique em **grafo** no menu ferramenta para obter o modo de exibição de gráfico de trabalho.

+ Verifique a visão geral do seu trabalho pelo grafo de trabalho gerado. 

+ Por padrão, ele mostrará todos os trabalhos e poderá ser filtrado pela **ID do trabalho**.

    ![ID do trabalho do grafo](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ Por padrão, o **progresso** é selecionado, o usuário pode verificar o fluxo de dados selecionando **leitura/gravação** na lista suspensa de **exibição**.

    ![exibição do grafo](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    O nó do gráfico é exibido em cores que mostra o calor.

    ![calor do grafo](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Reproduza o trabalho clicando no botão de **reprodução** e pare a qualquer momento clicando no botão parar. A tarefa é exibida em cores para mostrar o status diferente ao reproduzir:

  + Verde para bem-sucedido: O trabalho foi concluído com êxito.
  + Laranja para tentativa: Instâncias de tarefas que falharam, mas não afetam o resultado final do trabalho. Essas tarefas tinham instâncias duplicadas ou repetidas que podem ter sucesso mais tarde.
  + Azul para execução: A tarefa está em execução.
  + Branco para espera ou ignorado: A tarefa está aguardando para ser executada ou o estágio foi ignorado.
  + Vermelho para falha: Falha na tarefa.

    ![exemplo de cor do grafo, em execução](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    O estágio ignorado é exibido em branco.
    ![exemplo de cor do grafo, ignorar](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![exemplo de cor do grafo, com falha](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]  
    > A reprodução de cada trabalho é permitida. Para trabalhos incompletos, não há suporte para reprodução.


+ O mouse rola para ampliar/reduzir o grafo de trabalho ou clica em **aplicar zoom para ajustá** -lo à tela.
 
    ![ajustar zoom do grafo](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Focalize o nó do grafo para ver a dica de ferramenta quando houver tarefas com falha e clique em estágio para abrir a página do estágio.

    ![Dica de ferramenta do grafo](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Na guia grafo de trabalho, os estágios terão a dica de ferramenta e o ícone pequeno exibido se eles tiverem tarefas que atendam às condições abaixo:
  + Distorção de dados: tamanho de leitura de dados > tamanho médio de leitura de dados de todas as tarefas dentro deste estágio * 2 e tamanho de leitura de dados > 10 MB.
  + Distorção de tempo: tempo de execução > tempo médio de execução de todas as tarefas dentro deste estágio * 2 e tempo de execução > 2 minutos.

    ![ícone de distorção do grafo](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ O nó do gráfico de trabalho exibirá as seguintes informações de cada estágio:
  + ID.
  + Nome ou descrição.
  + Número da tarefa total.
  + Leitura de dados: a soma do tamanho de entrada e o tamanho de leitura em ordem aleatória.
  + Gravação de dados: a soma do tamanho de saída e o tamanho de gravação de ordem aleatória.
  + Tempo de execução: o tempo entre a hora de início da primeira tentativa e o tempo de conclusão da última tentativa.
  + Contagem de linhas: a soma dos registros de entrada, os registros de saída, os registros de leitura aleatória e os registros de gravação em ordem aleatória.
  + Andamento.

    > [!NOTE]  
    > Por padrão, o nó do grafo de trabalho exibirá informações da última tentativa de cada estágio (exceto para o tempo de execução do estágio), mas durante a reprodução, o nó do grafo mostrará informações de cada tentativa.

    > [!NOTE]  
    > Para o tamanho dos dados de leitura e gravação, usamos 1MB = 1000 KB = 1000 * 1000 bytes.

+ Envie comentários com problemas clicando em **fornecer comentários**.

    ![Comentários do grafo](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Guia diagnóstico no servidor de histórico de Apache Spark
Selecione ID do trabalho e clique em **diagnóstico** no menu ferramenta para obter a exibição de diagnóstico do trabalho. A guia diagnóstico inclui **distorção de dados**, **distorção de tempo**e **análise de uso de executor**.
    
+ Verifique a **distorção de dados**, a **distorção de tempo**e a **análise de uso do executor** selecionando as guias respectivamente.

    ![Guias de diagnóstico](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Distorção de dados
Clique na guia **distorção de dados** , as tarefas distorcidas correspondentes são exibidas com base nos parâmetros especificados. 

+ **Especificar parâmetros** – a primeira seção exibe os parâmetros que são usados para detectar a distorção de dados. A regra interna é: A leitura dos dados da tarefa é maior que 3 vezes a média de dados da tarefa lida e a leitura dos dados da tarefa é maior do que 10 MB. Se você quiser definir sua própria regra para tarefas distorcidas, você poderá escolher seus parâmetros, a seção do **estágio distorcido**e a **inclinação do caractere de distorção** será atualizada de acordo.

+ **Estágio distorcido** – a segunda seção exibe os estágios que têm tarefas distorcidas que atendem aos critérios especificados acima. Se houver mais de uma tarefa distorcida em um estágio, a tabela de estágio distorcido exibirá apenas a tarefa mais distorcida (por exemplo, os maiores dados para distorção de dados).

    ![Section2 de distorção de dados](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Gráfico de distorção** – quando uma linha na tabela de estágio de distorção é selecionada, o gráfico de distorção exibe mais detalhes de distribuições de tarefas com base no tempo de leitura e de execução de dados. As tarefas distorcidas são marcadas em vermelho e as tarefas normais são marcadas em azul. Para considerar o desempenho, o gráfico exibe apenas até 100 tarefas de exemplo. Os detalhes da tarefa são exibidos no painel inferior direito.

    ![Section3 de distorção de dados](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Distorção de tempo
A guia **distorção de tempo** exibe tarefas distorcidas com base no tempo de execução da tarefa. 

+ **Especificar parâmetros** – a primeira seção exibe os parâmetros que são usados para detectar a distorção de tempo. Os critérios padrão para detectar a distorção de tempo são: o tempo de execução da tarefa é maior que 3 vezes do tempo médio de execução e o tempo de execução da tarefa é maior que 30 segundos. Você pode alterar os parâmetros com base em suas necessidades. O gráfico de **intertorção** e de **distorção** exibe as informações de estágios e tarefas correspondentes, assim como a guia de **distorção de dados** acima.

+ Clique em **distorção de tempo**e o resultado filtrado será exibido na seção **estágio inclinado** de acordo com os parâmetros definidos na seção **especificar parâmetros**. Clique em um item na seção **estágio inclinado** , o gráfico correspondente é exibido em section3 e os detalhes da tarefa são exibidos no painel inferior direito.

    ![Section2 de distorção de tempo](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Análise de uso do executor
O grafo de uso do executor visualiza a alocação real do executor do trabalho do Spark e o status de execução.  

+ Clique em **análise de uso do executor**, quatro tipos de curvas sobre o uso do executor são rascunhos, incluindo **executores alocados**, **execuções em execução**,**executores ociosos**e **instâncias de máximo de executor**. Em relação aos executores alocados, cada evento "executor adicionado" ou "executor removido" aumentará ou diminuirá os executores alocados, você poderá verificar "linha do tempo do evento" na guia "trabalhos" para obter mais comparações.

    ![Guia executores](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Clique no ícone de cor para selecionar ou desmarcar o conteúdo correspondente em todos os rascunhos.

    ![Selecionar gráfico](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)


## <a name="faq"></a>FAQ

### <a name="1-revert-to-community-version"></a>1. Reverter para a versão da Comunidade

Para reverter para a versão da Comunidade, execute as seguintes etapas:

1. Abra o cluster no Ambari. Clique em **Spark2** no painel esquerdo.
2. Clique na guia **configurações** .
3. Expanda o grupo **Spark2 personalizado-padrões**.
4. Clique em **Adicionar Propriedade**, adicionar **Spark. UI. Enhancement. Enabled = False**, salvar.
5. A propriedade é definida como **false** agora.
6. Clique em **salvar** para salvar a configuração.

    ![o recurso se desativa](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

7. Clique em **Spark2** no painel esquerdo, na guia **Resumo** , clique em servidor de **histórico do Spark2**.

    ![reiniciar Server1](./media/apache-azure-spark-history-server/apache-spark-restart1.png) 

8. Reinicie o servidor de histórico clicando em **reiniciar** do **servidor de histórico do Spark2**.

    ![reiniciar o Server2](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

9. Atualize a interface do usuário da Web do servidor de histórico do Spark, ela será revertida para a versão da Comunidade.

### <a name="2-upload-history-server-event"></a>2. Carregar evento do servidor de histórico

Se você encontrar um erro do servidor de histórico, siga as etapas para fornecer o evento:
1. Baixe o evento clicando em **baixar** na interface do usuário da Web do servidor de histórico.

    ![evento de download](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Clique em **fornecer comentários** na guia dados/grafo.

    ![Comentários do grafo](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Forneça o título e a descrição do erro, arraste o arquivo zip para o campo de edição e clique em **Enviar novo problema**.

    ![problema de arquivo](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Atualizar arquivo JAR para o cenário de hotfix

Se você quiser atualizar com o hotfix, use o script abaixo, que atualizará Spark-Enhancement. jar *.

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

**Uso**: 

`upgrade_spark_enhancement.sh https://${jar_path}`

**Exemplo**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar` 

**Para usar o arquivo bash do portal do Azure**

1. Inicie o [portal do Azure](https://ms.portal.azure.com)e selecione o cluster.
2. Clique em **ações de script**e em **Enviar novo**. Conclua o formulário **Enviar ação de script** e, em seguida, clique no botão **criar** .
    
    + **Tipo de script**: selecione **personalizado**.
    + **Nome**: especifique um nome de script.
    + **URI do script bash**: carregue o arquivo bash no cluster privado e copie a URL aqui. Como alternativa, use o URI fornecido.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

   + Verifique no **cabeçalho** e no **trabalho**.
   + **Parâmetros**: defina os parâmetros seguindo o uso do bash.

     ![carregar o log ou hotfix de atualização](./media/apache-azure-spark-history-server/apache-spark-upload1.png)


## <a name="known-issues"></a>Problemas conhecidos

1.  Atualmente, ele funciona apenas para o cluster Spark 2,3 e 2,4.

2.  Os dados de entrada/saída usando RDD não serão mostrados na guia dados.

## <a name="next-steps"></a>Passos Seguintes

* [Gerenciar recursos para um cluster Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Definir configurações de Apache Spark](apache-spark-settings.md)


## <a name="contact-us"></a>Contacte-nos

Se você tiver algum comentário ou se encontrar outros problemas ao usar essa ferramenta, envie um email em ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).
