---
title: Habilitar a criação automática de tópicos no Apache Kafka-Azure HDInsight
description: Saiba como configurar o Apache Kafka no HDInsight para criar tópicos automaticamente. Você pode configurar o Kafka definindo auto. Create. topics. Enable para true por meio de Ambari ou durante a criação do cluster por meio de modelos do PowerShell ou do Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: 5d990a1c39495090fed1c78f1ddf5e879490e6c4
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960673"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Como configurar o Apache Kafka no HDInsight para criar tópicos automaticamente

Por padrão, [Apache Kafka](https://kafka.apache.org/) no HDInsight não habilita a criação automática de tópicos. Você pode habilitar a criação automática de tópicos para clusters existentes usando o [Apache Ambari](https://ambari.apache.org/). Você também pode habilitar a criação automática de tópicos ao criar um novo cluster Kafka usando um modelo de Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Interface do usuário da Web do Apache Ambari

Para habilitar a criação automática de tópicos em um cluster existente por meio da interface do usuário da Web do amAmbari, use as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), selecione o cluster Kafka.

2. Na __visão geral do cluster__, selecione __painel do cluster__. 

    ![Imagem do portal com o painel do cluster selecionado](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Em seguida, selecione __painel do cluster HDInsight__. Quando solicitado, autentique usando as credenciais de logon (admin) para o cluster.

    ![Imagem da entrada do painel do cluster HDInsight](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Selecione o serviço Kafka na lista à esquerda da página.

    ![Lista de serviços](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

4. Selecione Configurações no meio da página.

    ![Guia Configuração de serviço](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

5. No campo filtro, insira um valor de `auto.create`. 

    ![Imagem do campo de filtro](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Isso filtra a lista de propriedades e exibe a `auto.create.topics.enable` configuração.

6. Altere o valor de `auto.create.topics.enable` para `true`e, em seguida, selecione salvar. Adicione uma observação e, em seguida, selecione salvar novamente.

    ![Imagem da entrada auto. Create. topics. Enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Selecione o serviço Kafka, selecione __reiniciar__e, em seguida, selecione __reiniciar todos os afetados__. Quando solicitado, selecione __confirmar reiniciar tudo__.

    ![Imagem da seleção de reinicialização](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Você também pode definir valores de Ambari por meio da API REST do Ambari. Isso geralmente é mais difícil, pois você precisa fazer várias chamadas REST para recuperar a configuração atual, modificá-la etc. Para obter mais informações, consulte o documento [gerenciar clusters HDInsight usando a API REST do Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md) .

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

Ao criar um cluster Kafka usando um modelo de Azure Resource Manager, você pode definir `auto.create.topics.enable` diretamente adicionando-o a `kafka-broker`um. O trecho de código JSON a seguir demonstra como definir esse `true`valor como:

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
