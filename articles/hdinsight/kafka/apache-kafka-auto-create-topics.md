---
title: Habilitar a criação automática de tópicos no Apache Kafka-Azure HDInsight
description: Saiba como configurar o Apache Kafka no HDInsight para criar tópicos automaticamente. Você pode configurar o Kafka definindo auto. Create. topics. Enable para true por meio de Ambari ou durante a criação do cluster por meio de modelos do PowerShell ou do Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 7ec7d15806927306b12624962facbafddf2ce08b
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242357"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Como configurar o Apache Kafka no HDInsight para criar tópicos automaticamente

Por padrão, [Apache Kafka](https://kafka.apache.org/) no HDInsight não habilita a criação automática de tópicos. Você pode habilitar a criação automática de tópicos para clusters existentes usando o [Apache Ambari](https://ambari.apache.org/). Você também pode habilitar a criação automática de tópicos ao criar um novo cluster Kafka usando um modelo de Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Interface do usuário da Web do Apache Ambari

Para habilitar a criação automática de tópicos em um cluster existente por meio da interface do usuário da Web do amAmbari, use as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), selecione o cluster Kafka.

1. Em **painéis de cluster**, selecione **Ambari página inicial**.

    ![Imagem do portal com o painel do cluster selecionado](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Quando solicitado, autentique usando as credenciais de logon (admin) para o cluster. Como alternativa, você pode se conectar ao Amabri diretamente de `https://CLUSTERNAME.azurehdinsight.net/` em que `CLUSTERNAME` é o nome do seu cluster Kafka.

1. Selecione o serviço Kafka na lista à esquerda da página.

    ![Guia lista de serviços do Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Selecione Configurações no meio da página.

    ![Guia Configurações do serviço Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. No campo filtro, insira um valor de `auto.create`.

    ![Campo de filtro de pesquisa do Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Isso filtra a lista de propriedades e exibe a configuração `auto.create.topics.enable`.

1. Altere o valor de `auto.create.topics.enable` para `true`e, em seguida, selecione **salvar**. Adicione uma observação e, em seguida, selecione **salvar** novamente.

    ![Imagem da entrada auto. Create. topics. Enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Selecione o serviço Kafka, selecione __reiniciar__e, em seguida, selecione __reiniciar todos os afetados__. Quando solicitado, selecione __confirmar reiniciar tudo__.

    ![O Apache Ambari reinicia todos os afetados](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Você também pode definir valores de Ambari por meio da API REST do Ambari. Isso geralmente é mais difícil, pois você precisa fazer várias chamadas REST para recuperar a configuração atual, modificá-la etc. Para obter mais informações, consulte o documento [gerenciar clusters HDInsight usando a API REST do Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md) .

## <a name="resource-manager-templates"></a>Modelos do Gestor de Recursos

Ao criar um cluster Kafka usando um modelo de Azure Resource Manager, você pode definir diretamente `auto.create.topics.enable` adicionando-o a um `kafka-broker`. O trecho de código JSON a seguir demonstra como definir esse valor como `true`:

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

Neste documento, você aprendeu a habilitar a criação automática de tópicos para Apache Kafka no HDInsight. Para saber mais sobre como trabalhar com o Kafka, confira os links a seguir:

* [Analisar logs de Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicar dados entre clusters Apache Kafka](apache-kafka-mirroring.md)
