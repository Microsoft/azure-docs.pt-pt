---
title: Ações de script de resolução de problemas em Azure HDInsight
description: Etapas gerais de resolução de problemas para ações de script em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 977e3571a24e8be9d9ef6cd79e80e654ca944fa4
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538821"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Ações de script de resolução de problemas em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="viewing-logs"></a>Ver registos

Pode utilizar a UI web Apache Ambari para visualizar informações registadas pelas ações de script. Se o script falhar durante a criação do cluster, os registos estão na conta de armazenamento de cluster padrão. Esta secção fornece informações sobre como recuperar os registos utilizando ambas as opções.

### <a name="apache-ambari-web-ui"></a>Apache Ambari web UI

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net` para, onde `CLUSTERNAME` está o nome do seu cluster.

1. A partir da barra no topo da página, selecione a entrada **de ops.** Uma lista apresenta operações atuais e anteriores feitas no cluster através de Ambari.

    ![Ambari web UI bar com ops selecionados](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. Encontre as entradas que **executaram \_ a subscrição personalizada** na coluna **Operações.** Estas entradas são criadas quando as ações do script são executadas.

    ![Apache Ambari script operações de ação](./media/troubleshoot-script-action/ambari-script-action.png)

    Para visualizar a saída **STDOUT** e **STDERR,** selecione a entrada **de execução por subscrição personalizada** e faça exercício através dos links. Esta saída é gerada quando o script é executado e pode ter informações úteis.

### <a name="default-storage-account"></a>Conta de armazenamento predefinido

Se a criação de cluster falhar devido a um erro de script, os registos são mantidos na conta de armazenamento do cluster.

* Os registos de armazenamento estão disponíveis em `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` .

    ![Troncos de ação do script](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    Sob este diretório, os troncos são organizados separadamente para **headnode,** **nóiro operário,** e **nó do zookeeper** . Veja os exemplos seguintes:

    * **Headnode:**`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Nó do trabalhador:**`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Nó do zookeeper:**`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Toda **a sestram** e **stderr** do anfitrião correspondente são enviados para a conta de armazenamento. Há uma **saída- \* .txt** e **erros- \* .txt** para cada ação do script. O ficheiro **output-*.txt** contém informações sobre o URI do script que foi executado no anfitrião. O seguinte texto é um exemplo destas informações:

    ```output
    'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'
    ```

* É possível que crie repetidamente um cluster de ação de script com o mesmo nome. Nesse caso, pode distinguir os registos relevantes com base no nome da pasta **DATE.** Por exemplo, a estrutura da pasta para um cluster, **mycluster,** criada em datas diferentes parece semelhante às seguintes entradas de registo:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se criar um cluster de ação de script com o mesmo nome no mesmo dia, pode utilizar o prefixo único para identificar os ficheiros de registo relevantes.

* Se criar um aglomerado perto das 00:00, meia-noite, é possível que os ficheiros de registo se estendem por dois dias. Nesse caso, vê-se duas pastas de data diferentes para o mesmo cluster.

* O upload de ficheiros de registo para o recipiente predefinido pode demorar até cinco minutos, especialmente para grandes aglomerados. Por isso, se pretender aceder aos registos, não deverá eliminar imediatamente o cluster se uma ação de script falhar.

## <a name="ambari-watchdog"></a>Cão de guarda Ambari

Não altere a palavra-passe para o cão de guarda Ambari, hdinsightwatchdog, no seu cluster HDInsight baseado em Linux. Uma mudança de palavra-passe quebra a capacidade de executar novas ações de script no cluster HDInsight.

## <a name="cant-import-name-blobservice"></a>Não se pode importar nome BlobService

__Sintomas.__ A ação do guião falha. Texto semelhante aos seguintes erros mostra quando vê a operação em Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Porque.__ Este erro ocorre se atualizar o cliente Python Azure Storage que está incluído no cluster HDInsight. A HDInsight espera o cliente Azure Storage 0.20.0.

__Resolução__ . Para resolver este erro, ligue manualmente a cada nó de cluster utilizando `ssh` . Executar o seguinte comando para reinstalar a versão correta do cliente de armazenamento:

```bash
sudo pip install azure-storage==0.20.0
```

Para obter informações sobre a ligação ao cluster com o SSH, consulte [Connect to HDInsight (Apache Hadoop) utilizando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>A história não mostra os scripts usados durante a criação de clusters

Se o seu cluster foi criado antes de 15 de março de 2016, você pode não ver uma entrada na história da ação do script. Redimensionar o cluster faz com que os scripts apareçam na história da ação do script.

Há duas exceções:

* O seu aglomerado foi criado antes de 1 de setembro de 2015. Esta data é quando as ações do script foram introduzidas. Qualquer cluster criado antes desta data não poderia ter usado ações de script para criação de clusters.

* Usaste várias ações de guião durante a criação de agrupamentos. Ou usaste o mesmo nome para vários scripts ou o mesmo nome, o mesmo URI, mas parâmetros diferentes para vários scripts. Nestes casos, obtém-se o seguinte erro:

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).