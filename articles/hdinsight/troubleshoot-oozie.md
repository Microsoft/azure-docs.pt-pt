---
title: Resolução de problemas Apache Oozie em Azure HDInsight
description: Problemas de resolução de certos erros de Apache Oozie no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 18831832f82cdbc8cec69e368f006f7acd4836c1
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205264"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Resolução de problemas Apache Oozie em Azure HDInsight

Com a Apache Oozie UI, pode ver os registos da Oozie. O Oozie UI também contém links para os registos JobTracker para as tarefas MapReduce que foram iniciadas pelo fluxo de trabalho. O padrão para resolução de problemas deve ser:

1. Veja o trabalho na Oozie web UI.

2. Se houver um erro ou falha para uma ação específica, selecione a ação para ver se o campo **Error Message** fornece mais informações sobre a falha.

3. Se disponível, utilize o URL da ação para ver mais detalhes, como os registos JobTracker, para a ação.

Seguem-se erros específicos que poderá encontrar e como resolvê-los.

## <a name="ja009-cant-initialize-cluster"></a>JA009: Não pode inicializar o cluster

### <a name="issue"></a>Problema

O estado do trabalho altera-se para **suspenso**. Os detalhes para `RunHiveScript` o trabalho mostram o estado como **START_MANUAL**. A seleção da ação apresenta a seguinte mensagem de erro:

    JA009: Cannot initialize Cluster. Please check your configuration for map

### <a name="cause"></a>Causa

Os endereços de armazenamento Azure Blob utilizados no ficheiro **job.xml** não contêm o recipiente de armazenamento ou o nome da conta de armazenamento. O formato de endereço `wasbs://containername@storageaccountname.blob.core.windows.net`de armazenamento Blob deve ser .

### <a name="resolution"></a>Resolução

Mude os endereços de armazenamento blob que o trabalho utiliza.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: Oozie não está autorizado &lt;a personificar USER&gt;

### <a name="issue"></a>Problema

O estado do trabalho altera-se para **suspenso**. Os detalhes para `RunHiveScript` o trabalho mostram o estado como **START_MANUAL**. Se selecionar a ação, mostra a seguinte mensagem de erro:

    JA002: User: oozie is not allowed to impersonate <USER>

### <a name="cause"></a>Causa

As definições de permissão atuais não permitem que o Oozie se personifique pela conta de utilizador especificada.

### <a name="resolution"></a>Resolução

Oozie pode personificar **`users`** os utilizadores do grupo. Utilize `groups USERNAME` o para ver os grupos de que a conta de utilizador é membro. Se o utilizador não for **`users`** membro do grupo, utilize o seguinte comando para adicionar o utilizador ao grupo:

    sudo adduser USERNAME users

> [!NOTE]  
> Pode levar alguns minutos até que o HDInsight reconheça que o utilizador foi adicionado ao grupo.

---

## <a name="launcher-error-sqoop"></a>ERRO do lançador (Sqoop)

### <a name="issue"></a>Problema

O estado do trabalho muda para **O KILLED**. Os detalhes para `RunSqoopExport` o trabalho mostram o estado como **ERRO**. Se selecionar a ação, mostra a seguinte mensagem de erro:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

### <a name="cause"></a>Causa

O Sqoop não consegue carregar o controlador de base de dados necessário para aceder à base de dados.

### <a name="resolution"></a>Resolução

Quando utiliza o Sqoop de um trabalho da Oozie, deve incluir o condutor da base de dados com os outros recursos, como o workflow.xml, que o trabalho utiliza. Consulte também o arquivo que contém `<sqoop>...</sqoop>` o controlador de base de dados da secção do fluxo de trabalho.xml.

Por exemplo, para o exemplo de trabalho dos fluxos de [trabalho Use Hadoop Oozie,](hdinsight-use-oozie-linux-mac.md)utilizaria os seguintes passos:

1. Copie `mssql-jdbc-7.0.0.jre8.jar` o ficheiro para o diretório **/tutorial/useoozie:**

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Modifique `workflow.xml` o seguinte XML numa nova `</sqoop>`linha acima:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
