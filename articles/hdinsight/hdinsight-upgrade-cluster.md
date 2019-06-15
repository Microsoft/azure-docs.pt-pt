---
title: Cluster de HDInsight atualização para uma versão mais recente-Azure
description: Saiba como cluster de HDInsight atualizar para uma versão mais recente.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 4d391c095495a3d0e34a2111d7b4564e6f4f2b8d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071997"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Atualizar o cluster do HDInsight para uma versão mais recente
Para tirar partido das funcionalidades mais recentes do HDInsight, é recomendável que os clusters do HDInsight ser atualizados para a versão mais recente. Siga as diretrizes para atualizar seu HDInsight abaixo as versões de cluster.

> [!NOTE]  
> Para obter informações sobre versões suportadas do HDInsight, consulte [versões de componente de HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="upgrade-tasks"></a>Tarefas de atualização
Segue-se o fluxo de trabalho para atualizar o Cluster do HDInsight.

![Diagrama de fluxo de trabalho de atualização](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Leia cada secção deste documento para compreender as alterações que possam ser necessárias ao atualizar o seu cluster do HDInsight.
2. Crie um cluster como um ambiente de garantia de qualidade/teste. Para obter mais informações sobre como criar um cluster, consulte [Saiba como criar clusters do HDInsight baseado em Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Copie as tarefas existentes e Coletores de origens de dados para o novo ambiente.
4. Execute os testes de validação para se certificar de que as tarefas funcionam conforme esperado no novo cluster.

Assim que tiver verificado que tudo funciona conforme esperado, agende o período de indisponibilidade para a migração. Durante este período de indisponibilidade, efetue as seguintes ações:

1.  Fazer backup de todos os dados transitórios armazenados localmente em nós do cluster. Por exemplo, se tiver dados armazenados diretamente num nó principal.
2.  Elimine o cluster existente.
3.  Crie um cluster na mesma sub-rede VNET com a versão HDI mais recente (ou suportada), utilizar o mesmo arquivo de dados predefinido utilizado no cluster anterior. Isso permite que o novo cluster continuar a trabalhar em relação a seus dados de produção existente.
4.  Importe qualquer cópia de segurança de dados transitórios.
5.  Iniciar tarefas/continuam a utilizar o novo cluster de processamento.

## <a name="next-steps"></a>Próximos Passos
* [Saiba como criar clusters do HDInsight baseado em Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Ligar ao HDInsight através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gerir um cluster baseado em Linux com o Apache Ambari](hdinsight-hadoop-manage-ambari.md)

