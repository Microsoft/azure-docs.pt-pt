---
title: Utilize as funcionalidades estendidas no Apache Spark History Server para depurar aplicações - Azure HDInsight
description: Utilize as funcionalidades estendidas no Apache Spark History Server para depurar e diagnosticar aplicações Spark - Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 5cf1986711479f7330b0cd477744d9f4e2ac6459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548939"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Utilize as funcionalidades estendidas do Apache Spark History Server para depurar e diagnosticar aplicações Spark

Este artigo mostra-lhe como usar as funcionalidades estendidas do Apache Spark History Server para depurar e diagnosticar aplicações Spark completas ou executantes. A extensão inclui um separador **Data,** um separador **Graph** e um separador **de diagnóstico.** No separador **Dados,** pode verificar os dados de entrada e saída do trabalho da Spark. No separador **Graph,** pode verificar o fluxo de dados e reproduzir o gráfico de trabalho. No separador **Diagnóstico,** pode consultar as funcionalidades de Análise de Utilização de **Dados,** **Skew time**e **Executor.**

## <a name="get-access-to-the-spark-history-server"></a>Obtenha acesso ao Servidor de História de Faíscas

O Spark History Server é o UI web para aplicações Spark concluídas e executantes. Pode abri-lo a partir do portal Azure ou a partir de um URL.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Abra a Web UI do Spark History Server a partir do portal Azure

1. A partir do [portal Azure,](https://portal.azure.com/)abra o aglomerado de faíscas. Para mais informações, consulte [Lista e mostre clusters.](../hdinsight-administer-use-portal-linux.md#showClusters)
2. A partir de **dashboards Cluster,** selecione **Spark history server**. Quando solicitado, insira as credenciais de administração para o cluster Spark.

    ![Lance o Spark History Server a partir do portal Azure.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Servidor de História de Faíscas")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Abra a Web UI do Spark History Server por URL

Abra o Spark History Server `https://CLUSTERNAME.azurehdinsight.net/sparkhistory`navegando até, onde **clusterNAME** é o nome do seu cluster Spark.

A UI web do Spark History Server pode parecer semelhante a esta imagem:

![A página do Spark History Server.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Utilize o separador Dados no Servidor de Histórico de Faíscas

Selecione o ID de trabalho e, em seguida, selecione **Dados** no menu da ferramenta para ver a visualização de dados.

+ Reveja **as inputs,** **saídas**e **operações** de tabela selecionando os separadores individuais.

    ![Separadores de dados na página Data for Spark Application.](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Copie todas as linhas selecionando o botão **Copiar.**

    ![Copiar dados na página de aplicação Spark.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Guarde todos os dados como . Ficheiro CSV selecionando o botão **CSV.**

    ![Guarde os dados como a . Ficheiro CSV da página Data for Spark Application.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Pesquise os dados inserindo palavras-chave no campo **de pesquisa.** Os resultados da pesquisa serão apresentados imediatamente.

    ![Pesquisar dados na página Data for Spark Application.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Selecione o cabeçalho da coluna para separar a tabela. Selecione o sinal de mais para expandir uma linha para mostrar mais detalhes. Selecione o sinal de menos para colapsar uma linha.

    ![A tabela de dados na página Data for Spark Application.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Faça o download de um único ficheiro selecionando o botão **de descarregamento parcial** à direita. O ficheiro selecionado será descarregado localmente. Se o ficheiro já não existir, isto abrirá um novo separador para mostrar as mensagens de erro.

    ![A linha de descarregamento de dados na página Data for Spark Application.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Copie um caminho completo ou um caminho relativo selecionando a opção **Copy Full Path** ou Copy Relative **Path,** que se expandem a partir do menu de descarregamento. Para ficheiros de armazenamento de lagos de dados Azure, selecione **Open in Azure Storage Explorer** para lançar o Azure Storage Explorer e localizar a pasta após o início de sessão.

    ![Copie as opções de Caminho Completo e Cópia relativa na página Data for Spark Application.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Se houver demasiadas filas para exibir numa única página, selecione os números da página na parte inferior da tabela para navegar.

    ![Números de página na página Data for Spark Application.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Para mais informações, sobrepareça ou selecione o ponto de interrogação ao lado do **Data for Spark Application** para mostrar a dica da ferramenta.

    ![Obtenha mais informações na página data para aplicação de faíscas.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  Para enviar feedback sobre questões, selecione **Fornecer-nos feedback**.

    ![Forneça feedback da página Data for Spark Application.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Use o separador Graph no Servidor de Histórico de Faíscas

+ Selecione o ID de trabalho e, em seguida, selecione **Graph** no menu da ferramenta para ver o gráfico de trabalho. Por padrão, o gráfico mostrará todos os trabalhos. Filtrar os resultados utilizando o menu de entrega do ID de **trabalho.**

    ![O menu de entrega do Id de trabalho na página de Gráfico de Trabalho & Aplicação de Faíscas.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ **O progresso** é selecionado por defeito. Verifique o fluxo de dados selecionando **Ler** ou **Escrito** no menu **'Drop-down' display.**

    ![Verifique o fluxo de dados na página Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ A cor de fundo de cada tarefa corresponde a um mapa de calor.

   ![O mapa de calor na página de Gráfico de & De Trabalho da Aplicação de Faíscas.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Cor |Descrição |
    |---|---|
    |Verde|O trabalho completou com sucesso.|
    |Orange|A tarefa falhou, mas isso não afeta o resultado final do trabalho. Estas tarefas têm casos duplicados ou recorrentes que podem ter sucesso mais tarde.|
    |Azul|A tarefa está a decorrer.|
    |Branco|A tarefa é correr, ou o palco saltou.|
    |Vermelho|A tarefa falhou.|

     ![Executando uma tarefa na página de Gráfico de Trabalho & Aplicação de Faíscas.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     Os estágios ignorados exibem em branco.
    ![Uma tarefa ignorada na página de Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Uma tarefa falhada na página de Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > A reprodução está disponível para trabalhos completos. Selecione o botão **Reprodução** para reproduzir o trabalho. Pare o trabalho a qualquer momento selecionando o botão de paragem. Quando um trabalho é reproduzido, cada tarefa mostrará o seu estado por cor. A reprodução não é apoiada para trabalhos incompletos.

+ Percorra para ampliar para dentro ou para fora no gráfico de trabalho ou **selecione Zoom para se adaptar** ao ecrã.

    ![Selecione Zoom para caber na página de Gráfico de Trabalho & Aplicação de Faíscas.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Quando as tarefas falharem, passe por cima do nó gráfico para ver a ponta da ferramenta e, em seguida, selecione o palco para abri-la numa nova página.

    ![Veja a dica de ferramenta na página de Gráfico de Trabalho & Aplicação de Faíscas.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Na página De função de Vela & Gráfico de Trabalho, os palcos apresentarão dicas de ferramentas e pequenos ícones se as tarefas cumprirem estas condições:
  + Distorção de dados: Dados lêem tamanho > tamanho médio de dados ler tamanho de todas as tarefas dentro desta fase * 2 *e* dados lêem tamanho > 10 MB.
  + Tempo distorcido: O tempo de execução > tempo médio de execução de todas as tarefas dentro desta fase * 2 *e* o tempo de execução > 2 minutos.

    ![O ícone de tarefa distorcido na página spark application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ O nó gráfico de trabalho apresentará as seguintes informações sobre cada fase:
  + ID
  + Nome ou descrição
  + Número total de tarefa
  + Dados lidos: a soma do tamanho da entrada e baralhar o tamanho da leitura
  + Os dados escrevem: a soma do tamanho da saída e baralhar o tamanho da escrita
  + Tempo de execução: o tempo entre o tempo de início da primeira tentativa e o tempo de conclusão da última tentativa
  + Contagem de filas: a soma dos registos de entrada, os registos de saída, a baralhada de registos e a confusão de registos
  + Progresso

    > [!NOTE]  
    > Por padrão, o nó gráfico de trabalho apresentará informações da última tentativa de cada fase (exceto o tempo de execução do estágio). Mas durante a reprodução, o nó gráfico de trabalho mostrará informações sobre cada tentativa.

    > [!NOTE]  
    > Para leitura de dados e tamanhos de escrita de dados, usamos 1MB = 1000 KB = 1000 * 1000 bytes.

+ Envie feedback sobre questões selecionando **Fornecer-nos feedback**.

    ![A opção de feedback na página de Gráfico de Trabalho & Aplicação de Faíscas.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Use o separador Diagnóstico no Servidor de História da Faísca

Selecione o ID do trabalho e, em seguida, selecione **Diagnóstico** no menu da ferramenta para ver a vista de diagnóstico de trabalho. O separador **de diagnóstico** inclui **dados skew,** **Time Skew**e Análise de **Utilização do Executor.**

+ Reveja **o Skew de Dados,** **o Tempo Skew**e a Análise de Utilização do **Executor** selecionando os separadores respectivamente.

    ![O separador Data Skew dentro do separador Diagnóstico.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Distorte de dados

Selecione o separador **Data Skew.** O visor de tarefas enviesadas correspondente supor-se nos parâmetros especificados.

#### <a name="specify-parameters"></a>Especificar Parâmetros

A secção **Desespecificação de parâmetros** apresenta os parâmetros utilizados para detetar o Skew de Dados. A regra padrão é: Os dados de tarefas lidos são superiores a três vezes a média de dados de tarefas lidas, e os dados de tarefas lidos são superiores a 10 MB. Se quiser definir a sua própria regra para tarefas distorcidas, pode escolher os seus parâmetros. As secções **skewed Stage** e **Skew Chart** serão atualizadas em conformidade.

#### <a name="skewed-stage"></a>Palco distorcido

A secção **de palco skewed** apresenta estágios que têm tarefas distorcidas que satisfaçam os critérios especificados. Se houver mais de uma tarefa distorcida numa fase, a secção **de Palco Skewed** apresenta apenas a tarefa mais distorcida (isto é, os maiores dados para a distorção de dados).

![Vista maior do separador Data Skew dentro do separador Diagnóstico.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>Gráfico de skew

Quando seleciona uma linha na tabela **Skew Stage,** o **Gráfico skew** apresenta mais detalhes de distribuição de tarefas com base no tempo de leitura e execução de dados. As tarefas distorcidas são marcadas a vermelho, e as tarefas normais são marcadas em azul. Para consideração de desempenho, o gráfico apresenta até 100 tarefas de amostra. Os detalhes da tarefa são apresentados no painel inferior direito.

![O Skew Chart para a 10ª etapa na UI de Faísca.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tempo skew

O separador **Time Skew** apresenta tarefas distorcidas com base no tempo de execução da tarefa.

#### <a name="specify-parameters"></a>Especificar Parâmetros

A secção **Desespecificação de parâmetros** apresenta os parâmetros utilizados para detetar o desvio de tempo. A regra padrão é: O tempo de execução da tarefa é superior a três vezes o tempo médio de execução, e o tempo de execução da tarefa é superior a 30 segundos. Pode alterar os parâmetros com base nas suas necessidades. O **Skewed Stage** e **o Skew Chart** exibem as fases e as informações correspondentes das tarefas, tal como no separador **Data Skew.**

Quando selecionar time **skew,** o resultado filtrado aparece na secção **Skewed Stage,** de acordo com os parâmetros definidos na secção **Desespecificar Parâmetros.** Quando seleciona um item na secção **Skewed Stage,** o gráfico correspondente é redigido na terceira secção e os detalhes da tarefa mostram no painel inferior direito.

![O separador time skew dentro do separador Diagnóstico.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>Gráficos de análise de utilização de executor

O Gráfico de Utilização do **Executor** exibe a atribuição e o estado de execução do executor real do trabalho.  

Quando selecionar Análise de **Utilização do Executor,** são redigidas quatro curvas diferentes sobre o uso do executor: **Executores atribuídos,** **Executores de Execução,** **Executores ociosos**e Instâncias de **Executor Max**. Cada **executor adicionado** ou **executor removido** evento aumentará ou diminuirá os executores atribuídos. Pode consultar a **Linha do Tempo do Evento** no separador **Jobs** para obter mais comparações.

![O separador de análise de utilização do executor dentro do separador Diagnóstico.](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

Selecione o ícone de cor para selecionar ou desseleccionar o conteúdo correspondente em todos os rascunhos.

 ![Selecione o gráfico no separador Análise de Utilização do Executor.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>FAQ

### <a name="how-do-i-revert-to-the-community-version"></a>Como volto à versão comunitária?

Para voltar à versão comunitária, faça os seguintes passos.

1. Abra o aglomerado em Ambari.
1. Navegue até **Spark2** > **Configs.**
1. Selecione **Padrão de ignição personalizado2**.
1. Selecione **Adicionar Propriedade...**.
1. Adicione **spark.ui.enhancement.enabled=false**, e, em seguida, guarde-o.
1. A propriedade agora se define **falsa.**
1. Selecione **Guardar** para salvar a configuração.

    ![Desligue uma característica em Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Selecione **Spark2** no painel esquerdo. Em seguida, no separador **Resumo,** selecione **Spark2 History Server**.

    ![A vista sumária em Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Para reiniciar o Servidor de História de Faíscas, selecione o botão **Iniciar** à direita do **Spark2 History Server**e, em seguida, selecione **Reiniciar** a partir do menu suspenso.

    ![Reinicie o Servidor de História de Faíscas em Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Refresque a Web UI do Spark History Server. Vai voltar para a versão comunitária.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Como faço o upload de um evento do Spark History Server para o denunciar como um problema?

Se tiver um erro no Spark History Server, faça os seguintes passos para reportar o evento.

1. Descarregue o evento selecionando **download** no Web UI do Spark History Server.

    ![Descarregue o evento na Spark History Server UI.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Selecione **Fornecer-nos feedback** da **página Spark Application & Job Graph.**

    ![Fornecer feedback sobre a página do gráfico de trabalho & aplicação de faíscas](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Forneça o título e uma descrição do erro. Em seguida, arraste o ficheiro .zip para o campo de edição e selecione **Enviar um novo problema**.

    ![Faça upload e envie um novo problema.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Como faço upgrade de um ficheiro .jar num cenário de hotfix?

Se quiser fazer upgrade com um hotfix, utilize `spark-enhancement.jar*`o seguinte script, que irá atualizar .

**upgrade_spark_enhancement.sh:**

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

#### <a name="usage"></a>Utilização

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Exemplo

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Use o arquivo de festa do portal Azure

1. Lance o [portal Azure](https://ms.portal.azure.com)e, em seguida, selecione o seu cluster.
2. Complete uma ação de [guião](../hdinsight-hadoop-customize-cluster-linux.md) com os seguintes parâmetros.

    |Propriedade |Valor |
    |---|---|
    |Tipo de script|- Personalizado|
    |Nome|UpgradeJar|
    |Roteiro de bash URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Tipo de nó(s)|Cabeça, Trabalhador|
    |Parâmetros|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Portal Azure submete ação de script](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Problemas conhecidos

+ Atualmente, o Spark History Server funciona apenas para o Spark 2.3 e 2.4.

+ Os dados de entrada e de saída que utilizam RDD não serão apresentados no separador **Dados.**

## <a name="next-steps"></a>Passos seguintes

+ [Gerir recursos para um cluster Apache Spark no HDInsight](apache-spark-resource-manager.md)
+ [Configurar as definições do Apache Spark](apache-spark-settings.md)

## <a name="feedback"></a>Comentários

Se tiver algum feedback ou se deparar com algum problema[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)ao utilizar esta ferramenta, envie um e-mail para ( ).
