---
title: Utilize as funcionalidades estendidas no Apache Spark History Server para depurar aplicações - Azure HDInsight
description: Utilize as funcionalidades estendidas no Apache Spark History Server para depurar e diagnosticar aplicações Spark - Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: c6645bc605dbd60d331ac0de002c36384b2bbbc4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864759"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Utilize as funcionalidades estendidas do Servidor histórico de faíscas Apache para depurar e diagnosticar aplicações Spark

Este artigo mostra-lhe como utilizar as funcionalidades estendidas do Apache Spark History Server para depurar e diagnosticar aplicações Spark completas ou executando. A extensão inclui um separador **de Dados,** um **separador Gráfico** e um separador **de Diagnóstico.** No separador **Dados,** pode verificar os dados de entrada e saída do trabalho Spark. No **separador Gráfico,** pode verificar o fluxo de dados e reproduzir o gráfico de trabalho. No separador **Diagnóstico,** pode consultar as funções **data Skew,** **Time Skew** e **Executor Usage Analysis.**

## <a name="get-access-to-the-spark-history-server"></a>Obtenha acesso ao Servidor histórico de faíscas

O Spark History Server é o UI web para aplicações Spark concluídas e executando. Pode abri-lo a partir do portal Azure ou a partir de um URL.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Abra a web UI do Spark History Server a partir do portal Azure

1. A partir do [portal Azure,](https://portal.azure.com/)abra o aglomerado de faíscas. Para obter mais informações, consulte [List e mostrar clusters.](../hdinsight-administer-use-portal-linux.md#showClusters)
2. A partir **de dashboards cluster**, selecione  **Spark history server**. Quando solicitado, insira as credenciais de administração para o cluster Spark.

    :::image type="content" source="./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png " alt-text="Lance o Spark History Server a partir do portal Azure." border="true"::: o portal Azure. border="true":::

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Abra o Spark History Server web UI por URL

Abra o Spark History Server navegando para `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` , onde **CLUSTERNAME** é o nome do seu cluster Spark.

A UI web do Spark History Server pode parecer semelhante a esta imagem:

:::image type="content" source="./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png" alt-text="A página do Servidor histórico de faíscas." border="true":::

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Utilize o separador Dados no Servidor histórico de faíscas

Selecione o ID do trabalho e, em seguida, selecione **Dados** no menu da ferramenta para ver a vista de dados.

+ Rever **Entradas,** **Saídas** e **Operações de Tabela** selecionando os separadores individuais.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-tabs.png" alt-text="Separadores de dados na página Data for Spark Application." border="true":::

+ Copie todas as linhas selecionando o botão **Copiar.**

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-copy.png" alt-text="Copie os dados na página de aplicação Spark." border="true":::

+ Guarde todos os dados como. Ficheiro CSV selecionando o botão **csv.**

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-save.png" alt-text="Guardar dados como um . Ficheiro CSV da página Data for Spark Application." border="true":::

+ Pesse os dados introduzindo palavras-chave no campo **Procurar.** Os resultados da pesquisa serão apresentados imediatamente.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-search.png" alt-text="Pesquisar dados na página Data for Spark Application." border="true":::

+ Selecione o cabeçalho da coluna para classificar a tabela. Selecione o sinal de mais para expandir uma linha para mostrar mais detalhes. Selecione o sinal de menos para colapsar uma linha.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-table.png" alt-text="A tabela de dados na página Data for Spark Application." border="true":::

+ Faça o download de um único ficheiro selecionando o botão **De transferência parcial** à direita. O ficheiro selecionado será descarregado localmente. Se o ficheiro já não existir, isto abrirá um novo separador para mostrar as mensagens de erro.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-data-download-row.png" alt-text="A linha de descarregamento de dados na página Data for Spark Application." border="true":::

+ Copie um caminho completo ou um caminho relativo selecionando a opção **Copy Full Path** ou Copy Relative **Path,** que se expandem a partir do menu de descarregamento. Para os ficheiros de armazenamento do Lago de Dados Azure, selecione **Open in Azure Storage Explorer** para lançar o Azure Storage Explorer e localizar a pasta após o sôm-in.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-data-copy-path.png" alt-text="Copie as opções de Percurso Completo e Copy Relative Path na página Data for Spark Application." border="true":::

+ Se houver demasiadas linhas para visualizar numa única página, selecione os números de página na parte inferior da tabela para navegar.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-page.png" alt-text="Números de página na página Data for Spark Application." border="true":::

+ Para obter mais informações, sobressaie ou selecione o ponto de interrogação ao lado **de Data for Spark Application** para mostrar a ponta da ferramenta.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-data-more-info.png" alt-text="Obtenha mais informações na página Data for Spark Application." border="true":::

+  Para enviar feedback sobre questões, **selecione Forneça-nos o feedback**.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-feedback.png" alt-text="Fornecer feedback da página Data for Spark Application." border="true":::

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Utilize o separador Gráfico no Servidor histórico de faíscas

+ Selecione o ID do trabalho e, em seguida, selecione **Gráfico** no menu de ferramentas para ver o gráfico de trabalho. Por defeito, o gráfico mostrará todos os trabalhos. Filtrar os resultados utilizando o menu de entrega do **Job ID.**

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png" alt-text="O menu de drop-down job na página Spark Application & Job Graph." border="true":::

+ **O progresso** é selecionado por padrão. Verifique o fluxo de dados selecionando **Ler** ou **Escrito** no menu drop-down **do Display.**

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-display.png" alt-text="Verifique o fluxo de dados na página de & Gráfico de Trabalho da Aplicação Spark." border="true":::

+ A cor de fundo de cada tarefa corresponde a um mapa de calor.

   :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png" alt-text="O mapa de calor na página spark Application & Job Graph." border="true":::


    |Cor |Descrição |
    |---|---|
    |Green|O trabalho terminou com sucesso.|
    |Laranja|A tarefa falhou, mas isso não afeta o resultado final do trabalho. Estas tarefas duplicaram ou relembrou casos que podem ter sucesso mais tarde.|
    |Blue|A tarefa está a decorrer.|
    |Branco|A tarefa está à espera de ser executada, ou o palco saltou.|
    |Red|A tarefa falhou.|

     :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-color-running.png" alt-text="Executar uma tarefa na página De & Gráfico de Trabalho da Aplicação Spark." border="true":::

     Os estágios saltados exibem em branco.
    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png" alt-text="Uma tarefa ignorada na página De & Gráfico de Trabalho da Aplicação Spark." border="true":::

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png" alt-text="Uma tarefa falhada na página De & Gráfico de Trabalho da Aplicação Spark." border="true":::

     > [!NOTE]  
     > A reprodução está disponível para trabalhos concluídos. Selecione o botão **Reprodução** para reproduzir o trabalho. Pare o trabalho a qualquer momento selecionando o botão de paragem. Quando um trabalho é reproduzido, cada tarefa mostrará o seu estado por cor. A reprodução não é apoiada por empregos incompletos.

+ Percorra para fazer zoom dentro ou fora no gráfico de trabalho, ou selecione **Zoom para se ajustar** ao ecrã.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png" alt-text="Selecione Zoom para caber na página de Gráfico de trabalho & Desempoleto de Faísca." border="true":::

+ Quando as tarefas falharem, paire sobre o nó gráfico para ver a ponta da ferramenta e, em seguida, selecione o palco para abri-la numa nova página.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png" alt-text="Consulte a ponta da ferramenta na página &quot;Aplicação de faíscas & gráfico de trabalho&quot;." border="true":::

+ Na página Spark Application & Job Graph, os estágios apresentarão as pontas das ferramentas e pequenos ícones se as tarefas satisfaçam estas condições:
  + Distorção de dados: O tamanho da leitura de dados > tamanho médio de leitura de dados de todas as tarefas dentro desta fase * 2 *e* tamanho de leitura de dados > 10 MB.
  + Distorção do tempo: Tempo de execução > tempo médio de execução de todas as tarefas dentro desta fase * 2 *e* tempo de execução > 2 minutos.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png" alt-text="O ícone de tarefa enviesado na página De aplicação de faíscas & gráfico de trabalho." border="true":::

+ O nó gráfico de trabalho apresentará as seguintes informações sobre cada fase:
  + ID
  + Nome ou descrição
  + Número total de tarefas
  + Dados leia:a soma do tamanho de entrada e baralhar o tamanho da leitura
  + Os dados escrevem: a soma do tamanho da saída e baralhar o tamanho da escrita
  + Tempo de execução: o tempo entre a hora de início da primeira tentativa e o tempo de conclusão da última tentativa
  + Contagem de linha: a soma dos registos de entradas, registos de saída, baralhar registos de leitura e baralhar discos de escrita
  + Progresso

    > [!NOTE]  
    > Por predefinição, o nó gráfico de trabalho apresentará informações da última tentativa de cada etapa (exceto o tempo de execução do estágio). Mas durante a reprodução, o nó gráfico de trabalho mostrará informações sobre cada tentativa.

    > [!NOTE]  
    > Para a leitura de dados e tamanhos de escrita de dados, usamos 1MB = 1000 KB = 1000 * 1000 bytes.

+ Envie feedback sobre questões selecionando **Fornecer-nos feedback**.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-feedback.png" alt-text="A opção de feedback na página De & Gráfico de Trabalho." border="true":::

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Utilize o separador diagnóstico no Servidor histórico de faíscas

Selecione o ID do trabalho e, em seguida, selecione **Diagnóstico** no menu da ferramenta para ver a vista do diagnóstico de trabalho. O **separador de Diagnóstico** inclui data **skew,** **time skew** e **análise de utilização do executor**.

+ Reveja **o distorce de dados,** **o distorcer do tempo** e a análise de **utilização do executor** selecionando os separadores respectivamente.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png" alt-text="O separador Data Skew dentro do separador Diagnóstico." border="true":::

### <a name="data-skew"></a>Distorcer dados

Selecione o **separador Data Skew.** As tarefas distorcidas correspondentes são apresentadas com base nos parâmetros especificados.

#### <a name="specify-parameters"></a>Especificar parâmetros

A secção **de Parâmetros Especifique** os parâmetros, que são utilizados para detetar o distorcer de dados. A regra padrão é: Os dados de tarefa lidos são superiores a três vezes da leitura média dos dados de tarefa, e os dados de tarefa lidos são superiores a 10 MB. Se quiser definir a sua própria regra para tarefas distorcidas, pode escolher os seus parâmetros. As secções **Skewed Stage** e **Skew Chart** serão atualizadas em conformidade.

#### <a name="skewed-stage"></a>Palco enviesado

A **secção Skewed Stage** exibe fases que têm tarefas distorcidas que satisfazem os critérios especificados. Se houver mais do que uma tarefa distorcida numa fase, a secção **Skewed Stage** apresenta apenas a tarefa mais distorcida (isto é, os maiores dados para distorcer dados).

:::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png" alt-text="Vista maior do separador Data Skew dentro do separador Diagnóstico." border="true":::

##### <a name="skew-chart"></a>Gráfico skew

Quando seleciona uma linha na tabela **Skew Stage,** o **Skew Chart** apresenta mais detalhes de distribuição de tarefas com base no tempo de leitura e execução de dados. As tarefas distorcidas são marcadas a vermelho, e as tarefas normais são marcadas em azul. Para consideração de desempenho, o gráfico apresenta até 100 tarefas de amostra. Os detalhes da tarefa são apresentados no painel inferior direito.

:::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png" alt-text="O Skew Chart para a fase 10 na UI spark." border="true":::

### <a name="time-skew"></a>Distorcer do tempo

O **separador Time Skew** apresenta tarefas distorcidas com base no tempo de execução da tarefa.

#### <a name="specify-parameters"></a>Especificar parâmetros

A secção **de Parâmetros Especifique** os parâmetros, que são utilizados para detetar distorções de tempo. A regra padrão é: O tempo de execução da tarefa é superior a três vezes o tempo médio de execução, e o tempo de execução da tarefa é superior a 30 segundos. Pode alterar os parâmetros com base nas suas necessidades. O **Skewed Stage** and **Skew Chart** exibe as informações correspondentes de estágios e tarefas, tal como no separador Data **Skew.**

Quando selecionar **o Distorcer de Tempo,** o resultado filtrado aparece na secção **Fase Distorcida,** de acordo com os parâmetros definidos na secção **'Especificar Parâmetros'.** Quando seleciona um item na secção **Fase Skewed,** o gráfico correspondente é redigido na terceira secção e os dados de tarefa são apresentados no painel inferior direito.

:::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png" alt-text="O separador Time desvirtua-se no separador Diagnóstico." border="true":::

### <a name="executor-usage-analysis-graphs"></a>Gráficos de análise de utilização do executor

O **Gráfico de Utilização do Executor** apresenta a atribuição e o estado de execução do executor real do trabalho.  

Quando seleciona **a Análise de Utilização do Executor,** são redigidas quatro curvas diferentes sobre a utilização do executor: **Executores Alocados,** **Executores em Execução,** **Executores inativos** e **Max Executor Instances**. Cada **executor adicionado** ou **executor removido** evento aumentará ou diminuirá os executores atribuídos. Pode consultar a **Timeline do Evento** no separador **Jobs** para obter mais comparações.

:::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png" alt-text="O separador de Análise de Utilização do Executor dentro do separador Diagnóstico." border="true":::

Selecione o ícone de cor para selecionar ou desmarcar o conteúdo correspondente em todos os projetos.

 :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png" alt-text="Selecione o gráfico no separador Análise de Utilização do Executor." border="true":::

## <a name="faq"></a>FAQ

### <a name="how-do-i-revert-to-the-community-version"></a>Como volto à versão comunitária?

Para voltar à versão comunitária, faça os seguintes passos.

1. Abra o aglomerado em Ambari.
1. Navegue até **Spark2**  >  **Configs.**
1. Selecione **padrão de faíscas personalizadas**.
1. Selecione **Adicionar Propriedade ...**. .
1. Adicione **spark.ui.enhancement.enabled=falso**, e, em seguida, guarde-o.
1. A propriedade **define-se como falsa** agora.
1. **Selecione Guardar** para guardar a configuração.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-turn-off.png" alt-text="Desligue um recurso em Apache Ambari." border="true":::

1. Selecione **Spark2** no painel esquerdo. Em seguida, no **separador Resumo,** selecione **Spark2 History Server**.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-restart1.png" alt-text="A vista sumária em Apache Ambari." border="true":::

1. Para reiniciar o Servidor histórico de faíscas, selecione o botão **Iniciar** para a direita do **Spark2 History Server** e, em seguida, selecione **Restart** a partir do menu suspenso.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-restart2.png" alt-text="Reinicie o Servidor histórico de faíscas em Apache Ambari." border="true":::  

1. Refresque a uI web do Spark History Server. Vai voltar para a versão comunitária.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Como faço o upload de um evento do Spark History Server para o denunciar como um problema?

Se encontrar um erro no Spark History Server, faça os seguintes passos para reportar o evento.

1. Descarregue o evento selecionando **Download** na web UI do Spark History Server.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-download-event.png" alt-text="Descarregue o evento no Spark History Server UI." border="true":::

2. **Selecione Fornecer-nos feedback** da **página De aplicação spark & gráfico de trabalho.**

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-feedback.png" alt-text="Fornecer feedback na página de & Gráfico de Trabalho da Aplicação Spark" border="true":::

3. Forneça o título e uma descrição do erro. Em seguida, arraste o ficheiro .zip para o campo de edição e **selecione Enviar nova edição**.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-file-issue.png" alt-text="Faça upload e envie um novo problema." border="true":::

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Como faço o upgrade de um ficheiro .jar num cenário de hotfix?

Se quiser atualizar com um hotfix, utilize o seguinte script, que irá atualizar `spark-enhancement.jar*` .

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

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Use o ficheiro bash do portal Azure

1. Lance o [portal Azure](https://ms.portal.azure.com)e, em seguida, selecione o seu cluster.
2. Complete uma [ação de script](../hdinsight-hadoop-customize-cluster-linux.md) com os seguintes parâmetros.

    |Propriedade |Valor |
    |---|---|
    |Tipo de script|- Personalizado|
    |Name|UpgradeJar|
    |URI de guião de bash|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Tipo de nó(s)|Cabeça, Trabalhador|
    |Parâmetros|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-upload1.png" alt-text="Portal Azure submete ação de script" border="true":::

## <a name="known-issues"></a>Problemas conhecidos

+ Atualmente, o Spark History Server funciona apenas para Spark 2.3 e 2.4.

+ Os dados de entrada e saída que utilizem RDD não serão apresentados no separador **Dados.**

## <a name="next-steps"></a>Passos seguintes

+ [Gerir recursos para um cluster Apache Spark em HDInsight](apache-spark-resource-manager.md)
+ [Configurar as definições do Apache Spark](apache-spark-settings.md)

## <a name="suggestions"></a>Sugestões

Se tiver algum feedback ou se deparar com qualquer problema ao utilizar esta ferramenta, envie um e-mail para ( [hdivstool@microsoft.com](mailto:hdivstool@microsoft.com) ).
