---
title: Use Grafana em Azure HDInsight
description: Saiba como aceder ao dashboard grafana com clusters Apache Hadoop em Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: cd2a09b6c3196302dcb74fd363481706021a4d4d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940894"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Aceder ao Grafana no Azure HDInsight

[Grafana](https://grafana.com/) é um popular construtor de gráficos e dashboards de código aberto. Grafana é rica em recursos; não só permite que os utilizadores criem dashboards personalizáveis e partilháveis, como também oferece dashboards com modelo/script, integração LDAP, múltiplas fontes de dados, e muito mais.

Atualmente, em Azure HDInsight, Grafana é suportado com os tipos de cluster Spark, HBase, Kafka e Interactive Query. Não é suportado para clusters com Pacote de Segurança Empresarial habilitado.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-apache-hadoop-cluster"></a>Criar um aglomerado Apache Hadoop

Consulte [os clusters Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md). Para **o tipo cluster**, selecione **Spark,** **Kafka,** **HBase** ou **Consulta Interativa**.

## <a name="access-the-grafana-dashboard"></a>Aceda ao painel grafana

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/grafana/` até onde CLUSTERNAME é o nome do seu cluster.

1. Insira as credenciais de utilizador do cluster Hadoop.

1. O tablier Grafana aparece e parece este exemplo:

    ![Painel web HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Painel HDInsight Grafana")

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine o cluster que criou com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Na caixa **de pesquisa** no topo, escreva **HDInsight**.

1. Selecione **clusters HDInsight** em **Serviços**.

1. Na lista de clusters HDInsight que aparecem, selecione o **...** ao lado do cluster que criou.

1. Selecione **Eliminar**. Selecione **Yes** (Sim).

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como analisar os dados com o HDInsight, veja os seguintes artigos:

* [Use a Colmeia Apache com HDInsight](../hadoop/hdinsight-use-hive.md).

* [Use MapReduce com HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* [Começa a usar ferramentas Visual Studio Hadoop para HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
