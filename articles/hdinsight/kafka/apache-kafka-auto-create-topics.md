---
title: Ativar a criação automática de tópicos em Apache Kafka - Azure HDInsight
description: Aprenda a configurar Apache Kafka no HDInsight para criar automaticamente tópicos. Pode configurar Kafka `auto.create.topics.enable` definindo-se como verdadeiro através de Ambari. Ou durante a criação de cluster através de modelos PowerShell ou Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 88dc3e4d761f9b4df63dfa07a24298398f7b0187
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231276"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Como configurar Apache Kafka no HDInsight para criar automaticamente tópicos

Por padrão, Apache Kafka no HDInsight não permite a criação automática de tópicos. Pode permitir a criação de tópicos automáticos para clusters existentes usando Apache Ambari. Também pode ativar a criação de tópicos automáticos ao criar um novo cluster Kafka usando um modelo de Gestor de Recursos Azure.

## <a name="apache-ambari-web-ui"></a>Apache Ambari Web UI

Para permitir a criação automática de tópicos num cluster existente através da Ambari Web UI, utilize os seguintes passos:

1. A partir do [portal Azure,](https://portal.azure.com)selecione o seu cluster Kafka.

1. A partir de **dashboards cluster,** selecione **Ambari home**.

    ![Imagem do portal com painel de instrumentos de cluster selecionado](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Quando solicitado, autenticar utilizando as credenciais de login (administrador) para o cluster. Em vez disso, pode ligar-se `https://CLUSTERNAME.azurehdinsight.net/` `CLUSTERNAME` a Amabri diretamente de onde está o nome do seu aglomerado de Kafka.

1. Selecione o serviço Kafka da lista à esquerda da página.

    ![Separador de lista de serviço salágono Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Selecione Configs no meio da página.

    ![Separador de configs de serviço Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. No campo Filtro, introduza `auto.create`um valor de .

    ![Campo de filtro de pesquisa Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Esta definição filtra a lista `auto.create.topics.enable` de propriedades e exibe a definição.

1. Altere o `auto.create.topics.enable` `true`valor de , e, em seguida, selecione **Guardar**. Adicione uma nota e, em seguida, selecione **Guardar** novamente.

    ![Imagem do auto.criar.tópicos.ativar a entrada](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Selecione o serviço Kafka, selecione __Reiniciar__, e, em seguida, selecione __Reiniciar todos os afetados__. Quando solicitado, selecione __Confirmar reiniciar tudo__.

    !['Apache Ambari reinicia todos os afetados'](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

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
