---
title: Problemas de conectividade de Apache Phoenix no Azure HDInsight
description: Problemas de conectividade entre o Apache HBase e o Apache Phoenix no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887296"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Cenário: problemas de conectividade de Apache Phoenix no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível conectar-se ao Apache HBase com o Apache Phoenix. Os motivos podem variar.

## <a name="cause-incorrect-ip"></a>Causa: IP incorreto

IP incorreto do nó active Zookeeper.

### <a name="resolution"></a>Resolução

O IP do nó active Zookeeper pode ser identificado na interface do usuário do amAmbari seguindo os links para o **HBase** > **links rápidos** > **ZK (ativo)**  > **informações de Zookeeper**. Corrija o IP conforme necessário.

---

## <a name="cause-systemcatalog-table-offline"></a>Causa: sistema. Tabela de catálogo offline

Ao executar comandos como `!tables`, você receberá uma mensagem de erro semelhante a:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Ao executar comandos como `count 'SYSTEM.CATALOG'`, você receberá uma mensagem de erro semelhante a:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Resolução

Na interface do usuário do Apache Ambari, conclua as seguintes etapas para reiniciar o serviço HMaster em todos os nós do ZooKeeper:

1. Na seção de **Resumo** do HBase, vá para **HBase** > **Active HBase Master**.

1. Na seção **componentes** , reinicie o serviço HBase Master.

1. Repita essas etapas para todos os serviços de **HBase Master em espera** restantes.

Pode levar até cinco minutos para o serviço de HBase Master estabilizar e concluir a recuperação. Depois que a tabela de `SYSTEM.CATALOG` voltar ao normal, o problema de conectividade para Apache Phoenix deve ser resolvido automaticamente.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
