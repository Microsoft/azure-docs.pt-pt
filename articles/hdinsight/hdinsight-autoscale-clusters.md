---
title: Dimensionar automaticamente os clusters do HDInsight do Azure (pré-visualização)
description: Utilize a funcionalidade de dimensionamento automático do HDInsight para dimensionar automaticamente os clusters
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: hrasheed
ms.openlocfilehash: bd1ffcfd915fe9ece683ec88d27f54b3a9214621
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475687"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Dimensionar automaticamente os clusters do HDInsight do Azure (pré-visualização)

Cluster do Azure HDInsight recurso dimensiona automaticamente o número de nós de trabalho num cluster e reduza verticalmente de dimensionamento automático com base na carga dentro de um intervalo predefinido. Durante a criação de um novo cluster do HDInsight, pode ser definido um número mínimo e máximo de nós de trabalho. Dimensionamento automático, em seguida, monitores, os requisitos de recursos de análise de carregam e aumenta o número de nós de trabalho ou diminuir devidamente. Não é sem custos adicionais para esta funcionalidade.

## <a name="getting-started"></a>Introdução

### <a name="create-a-cluster-with-the-azure-portal"></a>Criar um cluster com o portal do Azure

> [!Note]
> A escala automática é atualmente suportado apenas para a versão de clusters do Hive do Azure HDInsight, o MapReduce e o Spark 3.6.

Para ativar a funcionalidade de dimensionamento automático, efetue o seguinte como parte do processo de criação do normal cluster:

1. Selecione **personalizado (tamanho, definições, aplicações)** vez **criação rápida**.
2. No **personalizada** passo 5 (**tamanho do Cluster**) verificar o **dimensionamento automático do nó de trabalho** caixa de verificação.
3. Introduza os valores pretendidos para:  

    * Inicial **nós de número de trabalho**.  
    * **Mínimo** número de nós de trabalho.  
    * **Máximo** número de nós de trabalho.  

![Ativar a opção de dimensionamento automático do nó de trabalho](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

O número inicial de nós de trabalho deve estar entre o mínimo e máximo, inclusive. Este valor define o tamanho inicial do cluster quando for criado. O número mínimo de nós de trabalho tem de ser maior que zero.

Depois de escolher o tipo VM para cada tipo de nó, será capaz de ver o intervalo de custo estimado para o cluster inteiro. Em seguida, pode ajustar estas definições para se ajustar ao seu orçamento.

A sua subscrição tem uma quota de capacidade para cada região. O número total de núcleos de seus nós principais, combinados com o número máximo de nós de trabalho não pode exceder a quota da capacidade. No entanto, esta quota é um limite não restritivo; Pode sempre criar um pedido de suporte para facilmente aumentá-la.

> [!Note]  
> Se ultrapassar o limite de quota de núcleos total, receberá uma mensagem de erro dizendo "máxima de nós excedeu os núcleos disponíveis nesta região, escolha outra região ou contacte o suporte para aumentar a quota."

Para obter mais informações sobre a criação de clusters do HDInsight com o portal do Azure, consulte [baseado em Linux criar clusters no HDInsight com o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Criar um cluster com um modelo do Resource Manager

Para criar um cluster do HDInsight com um modelo Azure Resource Manager, adicione uma `autoscale` nó para o `computeProfile`  >  `workernode` seção com as propriedades `minInstanceCount` e `maxInstanceCount` conforme mostrado no fragmento json abaixo.

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "minInstanceCount": 2,
        "maxInstanceCount": 10
    },
    "hardwareProfile": {
        "vmSize": "Standard_D13_V2"
    },
    "osProfile": {
        "linuxOperatingSystemProfile": {
            "username": "[parameters('sshUserName')]",
            "password": "[parameters('sshPassword')]"
        }
    },
    "virtualNetworkProfile": null,
    "scriptActions": []
}
```

Para obter mais informações sobre a criação de clusters com modelos do Resource Manager, consulte [Apache Hadoop criar clusters no HDInsight utilizando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Ativar e desativar o dimensionamento automático para um cluster em execução

Ativar dimensionamento automático para um cluster em execução não é suportada durante a pré-visualização privada. Tem de estar ativada durante a criação do cluster.

Desativar o dimensionamento automático ou modificar as definições de dimensionamento automático para um cluster em execução, não é suportada na pré-visualização privada. Tem de eliminar o cluster e criar um novo cluster que elimine ou modifique as definições.

## <a name="monitoring"></a>Monitorização

Pode ver o cluster de aumentar verticalmente e reduzir verticalmente histórico como parte das métricas de cluster. Também pode listar todas as ações de dimensionamento nos últimos por dia, semana ou período de tempo maior.

## <a name="how-it-works"></a>Como funciona

### <a name="metrics-monitoring"></a>Métricas de monitorização

Dimensionamento automático continuamente monitoriza o cluster e recolhe as métricas seguintes:

1. **Total pendente da CPU**: O número total de núcleos necessários para iniciar a execução de todos os contentores pendentes.
2. **Total pendente memória**: A memória total (em MB), necessária para iniciar a execução de todos os pendentes contentores.
3. **Total de CPU livre**: A soma de todos os núcleos não utilizados em nós de trabalho do Active Directory.
4. **Total de memória livre**: A soma da memória não utilizada (em MB) em nós de trabalho do Active Directory.
5. **Utilização de memória por nó**: A carga num nó de trabalho. Um nó de trabalho em que é utilizada a 10 GB de memória, é considerado sob carga mais do que uma função de trabalho com 2 GB de memória utilizada.
6. **Número de modelos de estrutura mestres de aplicação por nó**: O número de contentores de aplicação Master (AM) em execução num nó de trabalho. Um nó de trabalho que aloja os contentores de 2AM é considerado mais importante do que um nó de trabalho 0 contentores de AM de alojamento.

As métricas acima são verificadas a cada 60 segundos. Dimensionamento automático irá compõem o dimensionamento e reduzir verticalmente decisões com base nessas métricas.

### <a name="cluster-scale-up"></a>Cluster de aumentar verticalmente

Quando forem detetadas as seguintes condições, o dimensionamento automático irá emitir um pedido de dimensionamento:

* Total pendente da CPU é superior ao total de CPU gratuita por mais de 1 minuto.
* Total pendente memória é superior ao total de memória livre para mais de 1 minuto.

Será Calculamos que um determinado número de nós de trabalho novos é necessárias para cumprir os requisitos de CPU e memória atuais e, em seguida, emitir um pedido que adiciona esse número de novos nós de trabalho de dimensionamento.

### <a name="cluster-scale-down"></a>Dimensionamento do cluster pendente

Quando forem detetadas as seguintes condições, o dimensionamento automático irá emitir um reduzir verticalmente a pedido:

* Total pendente da CPU é inferior a total de CPU gratuita durante mais de 10 minutos.
* Total pendente memória é inferior a total de memória livre para mais de 10 minutos.

Com base no número de contentores de AM por nó, bem como os requisitos de CPU e memória atuais, dimensionamento automático irá emitir um pedido para remover um determinado número de nós, especificando os nós nos quais são possíveis candidatos para remoção. Por predefinição, serão removidos um ciclo de dois nós.

## <a name="next-steps"></a>Passos Seguintes

* Leia sobre as melhores práticas para dimensionar clusters manualmente em [melhores práticas de dimensionamento](hdinsight-scaling-best-practices.md)
