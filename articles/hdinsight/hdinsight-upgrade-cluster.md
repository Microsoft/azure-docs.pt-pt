---
title: Atualizar o cluster HDInsight para uma versão mais recente – Azure
description: Aprenda as diretrizes para atualizar seu cluster do Azure HDInsight para uma versão mais recente.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: f4458b33edb9d55c30de8c5168668743abb33161
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811637"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Atualizar o cluster HDInsight para uma versão mais recente
Para aproveitar os recursos mais recentes do HDInsight, recomendamos que os clusters HDInsight sejam atualizados para a versão mais recente. Siga as diretrizes abaixo para atualizar as versões do cluster HDInsight.

> [!NOTE]  
> Para obter informações sobre as versões com suporte do HDInsight, consulte [versões de componente do hdinsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="upgrade-tasks"></a>Tarefas de atualização
O fluxo de trabalho para atualizar o cluster HDInsight é o seguinte.

![Diagrama de fluxo de trabalho de atualização](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Leia cada seção deste documento para entender as alterações que podem ser necessárias ao atualizar o cluster HDInsight.
2. Crie um cluster como um ambiente de garantia de teste/qualidade. Para obter mais informações sobre como criar um cluster, consulte [saiba como criar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Copie os trabalhos, as fontes de dados e os coletores existentes para o novo ambiente.
4. Execute testes de validação para garantir que seus trabalhos funcionem conforme o esperado no novo cluster.

Depois de verificar se tudo está funcionando conforme o esperado, agende o tempo de inatividade para a migração. Durante esse tempo de inatividade, execute as seguintes ações:

1.  Faça backup de todos os dados transitórios armazenados localmente nos nós do cluster. Por exemplo, se você tiver dados armazenados diretamente em um nó de cabeçalho.
2.  Exclua o cluster existente.
3.  Crie um cluster na mesma sub-rede de rede virtual com a versão de HDI mais recente (ou com suporte) usando o mesmo armazenamento de dados padrão usado pelo cluster anterior. Isso permite que o novo cluster continue a trabalhar com seus dados de produção existentes.
4.  Importe os dados transitórios dos quais você fez backup.
5.  Iniciar trabalhos/continuar o processamento usando o novo cluster.

## <a name="next-steps"></a>Próximos Passos
* [Saiba como criar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Ligar ao HDInsight através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gerenciar um cluster baseado em Linux usando o Apache Ambari](hdinsight-hadoop-manage-ambari.md)

