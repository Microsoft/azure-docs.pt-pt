---
title: Problemas de conectividade Apache Phoenix em Azure HDInsight
description: Problemas de conectividade entre Apache HBase e Apache Phoenix em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 2cc6556f681ece170bdfe02b985f56274c0faa1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936970"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Cenário: Problemas de conectividade Apache Phoenix em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Incapaz de ligar à Apache HBase com Apache Phoenix. As razões podem variar.

## <a name="cause-incorrect-ip"></a>Causa: IP incorreto

IP incorreto do nó do Zookeeper ativo.

### <a name="resolution"></a>Resolução

O IP do nó zookeeper ativo pode ser identificado a partir do UI Ambari seguindo as ligações com **HBase**  >  **Quick Links**  >  **ZK (Ative)**  >  **Zookeeper Info**. Corrija o IP conforme necessário.

---

## <a name="cause-systemcatalog-table-offline"></a>Causa: SISTEMA. Tabela DE CATÁLOGO offline

Ao executar comandos `!tables` como, recebe uma mensagem de erro semelhante a:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Ao executar comandos `count 'SYSTEM.CATALOG'` como, recebe uma mensagem de erro semelhante a:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Resolução

A partir do Apache Ambari UI, complete os seguintes passos para reiniciar o serviço HMaster em todos os nós do ZooKeeper:

1. A partir da secção **Sumária** da HBase, vá ao **HBase**  >  **Ative HBase Master**.

1. A partir da secção **Componentes,** reinicie o serviço HBase Master.

1. Repita estes passos para todos os restantes serviços **de Standby HBase Master.**

Pode levar até cinco minutos para o serviço HBase Master estabilizar e terminar a recuperação. Depois de a `SYSTEM.CATALOG` tabela voltar ao normal, a questão da conectividade com a Apache Phoenix deve ser resolvida automaticamente.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).