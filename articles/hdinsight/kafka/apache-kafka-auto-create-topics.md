---
title: Ativar a criação automática de tópicos em Apache Kafka - Azure HDInsight
description: Aprenda a configurar Apache Kafka no HDInsight para criar automaticamente tópicos. Pode configurar kafka definindo auto.create.topics.enable to true through Ambari ou during cluster creation through PowerShell ou Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 7ec7d15806927306b12624962facbafddf2ce08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242357"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Como configurar Apache Kafka no HDInsight para criar automaticamente tópicos

Por padrão, [Apache Kafka](https://kafka.apache.org/) no HDInsight não permite a criação automática de tópicos. Pode permitir a criação de tópicos automáticos para clusters existentes utilizando [Apache Ambari](https://ambari.apache.org/). Também pode ativar a criação de tópicos automáticos ao criar um novo cluster Kafka usando um modelo de Gestor de Recursos Azure.

## <a name="apache-ambari-web-ui"></a>Apache Ambari Web UI

Para permitir a criação automática de tópicos num cluster existente através da Ambari Web UI, utilize os seguintes passos:

1. A partir do [portal Azure,](https://portal.azure.com)selecione o seu cluster Kafka.

1. A partir de **dashboards cluster,** selecione **Ambari home**.

    ![Imagem do portal com painel de instrumentos de cluster selecionado](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Quando solicitado, autenticar utilizando as credenciais de login (administrador) para o cluster. Em alternativa, pode ligar-se diretamente `https://CLUSTERNAME.azurehdinsight.net/` `CLUSTERNAME` a Amabri de onde está o nome do seu aglomerado de Kafka.

1. Selecione o serviço Kafka da lista à esquerda da página.

    ![Separador de lista de serviço salágono Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Selecione Configs no meio da página.

    ![Separador de configs de serviço Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. No campo Filtro, introduza `auto.create`um valor de .

    ![Campo de filtro de pesquisa Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Isto filtra a lista de `auto.create.topics.enable` propriedades e exibe a definição.

1. Altere o `auto.create.topics.enable` `true`valor de , e, em seguida, selecione **Guardar**. Adicione uma nota e, em seguida, selecione **Guardar** novamente.

    ![Imagem do auto.criar.tópicos.ativar a entrada](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Selecione o serviço Kafka, selecione __Reiniciar__, e, em seguida, selecione __Reiniciar todos os afetados__. Quando solicitado, selecione __Confirmar reiniciar tudo__.

    ![Apache Ambari reinicia todos os afetados](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Também pode definir os valores de Ambari através da API Ambari REST. Isto é geralmente mais difícil, pois você tem que fazer várias chamadas REST para recuperar a configuração atual, modificá-la, etc. Para mais informações, consulte os [clusters Manage HDInsight utilizando o documento Apache Ambari REST API.](../hdinsight-hadoop-manage-ambari-rest-api.md)

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

Ao criar um cluster Kafka utilizando um modelo de `auto.create.topics.enable` Gestor de Recursos `kafka-broker`Azure, pode configurar diretamente adicionando-o em . O seguinte corte JSON demonstra como definir `true`este valor para:

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

## <a name="next-steps"></a>Passos Seguintes

Neste documento, aprendeu a permitir a criação automática de tópicos para Apache Kafka no HDInsight. Para saber mais sobre trabalhar com Kafka, consulte os seguintes links:

* [Analisar registos do Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicar dados entre os clusters Apache Kafka](apache-kafka-mirroring.md)
