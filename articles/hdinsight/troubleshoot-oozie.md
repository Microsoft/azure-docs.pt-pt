---
title: Resolução de problemas Apache Oozie em Azure HDInsight
description: Resolução de problemas certos erros de Apache Oozie em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: f30c66839228e43f9a6fbdce9914f1ca1572c0ec
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538940"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Resolução de problemas Apache Oozie em Azure HDInsight

Com o Apache Oozie UI, você pode ver os registos de Oozie. O Oozie UI também contém links para os registos JobTracker para as tarefas MapReduce que foram iniciadas pelo fluxo de trabalho. O padrão para a resolução de problemas deve ser:

1. Veja o trabalho na UI web Oozie.

2. Se houver um erro ou falha para uma ação específica, selecione a ação para ver se o campo **Mensagem de Erro** fornece mais informações sobre a falha.

3. Se disponível, utilize o URL da ação para ver mais detalhes, como os registos JobTracker, para a ação.

Seguem-se erros específicos que poderá encontrar e como resolvê-los.

## <a name="ja009-cant-initialize-cluster"></a>JA009: Não se pode inicializar o cluster

### <a name="issue"></a>Problema

O estatuto de trabalho muda para **SUSPENDED** . Os detalhes para o trabalho mostram o `RunHiveScript` estado de **START_MANUAL** . A seleção da ação apresenta a seguinte mensagem de erro:

```output
JA009: Cannot initialize Cluster. Please check your configuration for map
```

### <a name="cause"></a>Causa

Os endereços de armazenamento Azure Blob utilizados no **ficheirojob.xml** não contêm o contentor de armazenamento ou o nome da conta de armazenamento. O formato de endereço de armazenamento Blob deve ser `wasbs://containername@storageaccountname.blob.core.windows.net` .

### <a name="resolution"></a>Resolução

Altere os endereços de armazenamento Blob que o trabalho utiliza.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: Oozie não está autorizado a personificar &lt; UTILIZADOR&gt;

### <a name="issue"></a>Problema

O estatuto de trabalho muda para **SUSPENDED** . Os detalhes para o trabalho mostram o `RunHiveScript` estado de **START_MANUAL** . Se selecionar a ação, mostra a seguinte mensagem de erro:

```output
JA002: User: oozie is not allowed to impersonate <USER>
```

### <a name="cause"></a>Causa

As definições de permissão atuais não permitem que o Oozie personifique a conta de utilizador especificada.

### <a name="resolution"></a>Resolução

Oozie pode personificar utilizadores do **`users`** grupo. Utilize o `groups USERNAME` para ver os grupos de que a conta de utilizador é membro. Se o utilizador não for membro do **`users`** grupo, utilize o seguinte comando para adicionar o utilizador ao grupo:

```bash
sudo adduser USERNAME users
```

> [!NOTE]  
> Pode demorar alguns minutos até que o HDInsight reconheça que o utilizador foi adicionado ao grupo.

---

## <a name="launcher-error-sqoop"></a>ERROR do lançador (Sqoop)

### <a name="issue"></a>Problema

O estatuto de trabalho muda para **KILLED** . Os detalhes do trabalho mostram o `RunSqoopExport` estado de **ERRO** . Se selecionar a ação, mostra a seguinte mensagem de erro:

```output
Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]
```

### <a name="cause"></a>Causa

O Sqoop não consegue carregar o controlador de base de dados necessário para aceder à base de dados.

### <a name="resolution"></a>Resolução

Quando você usa Sqoop de um trabalho Oozie, você deve incluir o motorista de base de dados com outros recursos, como o workflow.xml, o trabalho usa. Além disso, consulte o arquivo que contém o controlador da base de dados a partir `<sqoop>...</sqoop>` da secção do workflow.xml.

Por exemplo, para o exemplo de trabalho da [Use Hadoop Oozie workflows](hdinsight-use-oozie-linux-mac.md), você usaria os seguintes passos:

1. Copie o `mssql-jdbc-7.0.0.jre8.jar` ficheiro para o **diretório /tutoriais/useoozie:**

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Modificar o `workflow.xml` para adicionar o seguinte XML numa nova linha `</sqoop>` acima:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).