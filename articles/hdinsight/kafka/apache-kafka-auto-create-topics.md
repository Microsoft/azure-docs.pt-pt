---
title: Permitir a criação automática de tópicos em Apache Kafka - Azure HDInsight
description: Aprenda a configurar Apache Kafka em HDInsight para criar automaticamente tópicos. Você pode configurar Kafka `auto.create.topics.enable` definindo-se como verdadeiro através de Ambari. Ou durante a criação de clusters através de modelos PowerShell ou Resource Manager.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 3766d41959383d802e50aafbf59b9841d1c8d74e
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870692"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Como configurar Apache Kafka em HDInsight para criar automaticamente tópicos

Por padrão, Apache Kafka no HDInsight não permite a criação automática de tópicos. Você pode ativar a criação de tópicos automáticos para clusters existentes usando Apache Ambari. Também pode ativar a criação de tópicos automáticos ao criar um novo cluster Kafka utilizando um modelo de Gestor de Recursos Azure.

## <a name="apache-ambari-web-ui"></a>Apache Ambari Web UI

Para permitir a criação automática de tópicos num cluster existente através da UI Web Ambari, utilize os seguintes passos:

1. A partir do [portal Azure,](https://portal.azure.com)selecione o seu cluster Kafka.

1. A partir **de dashboards cluster,** selecione **Ambari casa.**

    :::image type="content" source="./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png" alt-text="Imagem do portal com painel de cluster selecionado" border="true":::

    Quando solicitado, autentica usando as credenciais de login (administrador) para o cluster. Em vez disso, pode ligar-se à Amabri diretamente de `https://CLUSTERNAME.azurehdinsight.net/` onde está o nome do seu cluster `CLUSTERNAME` Kafka.

1. Selecione o serviço Kafka da lista à esquerda da página.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-list.png" alt-text="Separador da lista de serviços Apache Ambari" border="true":::

1. Selecione Configs no meio da página.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-config.png" alt-text="Separador de configs de serviço Apache Ambari" border="true":::

1. No campo Filtro, insira um valor de `auto.create` .

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png" alt-text="Campo de filtro de pesquisa Apache Ambari" border="true":::

    Esta definição filtra a lista de propriedades e apresenta a `auto.create.topics.enable` definição.

1. Alterar o valor de `auto.create.topics.enable` `true` , e, em seguida, selecionar **Guardar**. Adicione uma nota e, em seguida, **selecione Guardar** novamente.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png" alt-text="Imagem do auto.create.topics.enable entry" border="true":::

1. Selecione o serviço Kafka, __selecione Restart__ e, em seguida, selecione Reiniciar todos __os afetados__. Quando solicitado, __selecione Confirme reiniciar tudo__.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/restart-all-affected.png" alt-text="'Apache Ambari reinicia todos os afetados'" border="true":::

> [!NOTE]  
> Também pode definir valores Ambari através da AMBAri REST API. Isto é geralmente mais difícil, pois você tem que fazer várias chamadas REST para recuperar a configuração atual, modificá-la, etc. Para obter mais informações, consulte os [clusters Manage HDInsight utilizando o documento Apache Ambari REST API.](../hdinsight-hadoop-manage-ambari-rest-api.md)

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

Ao criar um cluster Kafka utilizando um modelo de Gestor de Recursos Azure, pode definir diretamente `auto.create.topics.enable` adicionando-o num `kafka-broker` . O seguinte snippet JSON demonstra como definir este valor `true` para:

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

Neste documento, aprendeu a permitir a criação automática de tópicos para Apache Kafka em HDInsight. Para saber mais sobre trabalhar com Kafka, consulte os seguintes links:

* [Analisar registos do Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicar dados entre aglomerados Apache Kafka](apache-kafka-mirroring.md)
