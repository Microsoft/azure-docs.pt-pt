---
title: Ativar a criação de tópico automática no Apache Kafka - Azure HDInsight
description: Saiba como configurar o Apache Kafka no HDInsight para criar automaticamente os tópicos. Pode configurar o Kafka definindo auto.create.topics.enable como true através do Ambari ou durante a criação do cluster através de modelos do PowerShell ou do Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: af26bcee08ded8eb66d640f954113be3e7672e1b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097866"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Como configurar o Apache Kafka no HDInsight para criar automaticamente tópicos

Por predefinição, [Apache Kafka](https://kafka.apache.org/) no HDInsight não permitir a criação automática de tópico. Pode ativar a criação automática de tópicos para clusters existentes usando [Apache Ambari](https://ambari.apache.org/). Também pode ativar a criação automática de tópicos quando criar um novo cluster de Kafka com um modelo Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Apache Ambari Web UI

Para permitir a criação de tópico automática num cluster existente através da IU da Web do Ambari, utilize os seguintes passos:

1. Partir do [portal do Azure](https://portal.azure.com), selecione o cluster do Kafka.

2. Partir do __descrição geral do Cluster__, selecione __dashboard de clusters__. 

    ![Imagem do portal com o dashboard do cluster selecionado](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Em seguida, selecione __dashboard de clusters do HDInsight__. Quando lhe for pedido, autentique com as credenciais de início de sessão (admin) para o cluster.

    ![Imagem da entrada de dashboard do cluster do HDInsight](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Selecione o serviço de Kafka a partir da lista à esquerda da página.

    ![Lista de serviço](./media/apache-kafka-auto-create-topics/service-list.png)

4. Selecione as configurações no meio da página.

    ![Guia de configuração de serviço](./media/apache-kafka-auto-create-topics/service-config.png)

5. No campo de filtro, introduza um valor de `auto.create`. 

    ![Imagem do campo de filtro](./media/apache-kafka-auto-create-topics/filter.png)

    Este procedimento filtra a lista de propriedades e apresenta o `auto.create.topics.enable` definição.

6. Alterar o valor de `auto.create.topics.enable` para `true`e, em seguida, selecione guardar. Adicionar uma nota e, em seguida, selecione guardar novamente.

    ![Imagem da entrada auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Selecione o serviço de Kafka, selecione __reinicie__e, em seguida, selecione __reiniciar as__. Quando lhe for pedido, selecione __confirmar reinicie todos__.

    ![Imagem da seleção de reinício](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Também pode definir valores de Ambari através da API de REST do Ambari. Isso é geralmente mais difícil, pois terá de fazer várias chamadas REST para obter a configuração atual, modifique-la, etc. Para obter mais informações, consulte a [clusters do HDInsight gerir com a API de REST do Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md) documento.

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

Ao criar um cluster do Kafka com um modelo Azure Resource Manager, pode definir diretamente `auto.create.topics.enable` ao adicioná-la num `kafka-broker`. O fragmento JSON seguinte demonstra como definir este valor como `true`:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Próximos Passos

Neste documento, aprendeu como permitir a criação de tópico automática para o Apache Kafka no HDInsight. Para saber mais sobre como trabalhar com o Kafka, consulte as seguintes ligações:

* [Analisar registos do Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicar dados entre clusters do Apache Kafka](apache-kafka-mirroring.md)
