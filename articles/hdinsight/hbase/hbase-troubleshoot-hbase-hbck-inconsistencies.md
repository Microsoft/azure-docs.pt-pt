---
title: hbase hbck devolve inconsistências no Azure HDInsight
description: hbase hbck devolve inconsistências no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75887330"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Cenário: `hbase hbck` comando devolve inconsistências no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue-region-is-not-in-hbasemeta"></a>Questão: Região não está em`hbase:meta`

Região xxx em HDFS, `hbase:meta` mas não listado ou implantado em qualquer servidor da região.

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Resolução

1. Fixar a meta tabela funcionando:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Atribuir regiões aos RegionServers executando:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Edição: Região está offline

Região xxx não implantada em nenhum RegionServer. Isto significa que `hbase:meta`a região está em, mas offline.

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Resolução

Trazer regiões on-line executando:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Emissão: As regiões têm as mesmas teclas de início/fim

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Resolução

Fundir manualmente as regiões sobrepostas. Vá à tabela HBase HMaster Web UI, selecione o link da tabela, que tem o problema. Você verá chave de início/chave final de cada região pertencente a essa tabela. Em seguida, fundir as regiões sobrepostas. Na concha HBase, faça. `merge_region 'xxxxxxxx','yyyyyyy', true` Por exemplo:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

Neste cenário, é necessário fundir a Região A e a RegiãoC e obter a Região Dcom a mesma gama-chave que a Região B, depois fundir a RegiãoB e a Região D. xxxxxxx e yyyyy yyyy yyyy são a corda de hash no final de cada nome da região. Tenha cuidado aqui para não fundir duas regiões descontínuas. Após cada fusão, como a fusão A e C, a HBase iniciará uma compactação na Região D. Aguarde que a compactação termine antes de fazer outra fusão com a RegionD. Pode encontrar o estado de compaction na página do servidor da região na HBase HMaster UI.

---

## <a name="issue-cant-load-regioninfo"></a>Edição: Não pode carregar`.regioninfo`

Não se `.regioninfo` pode `/hbase/data/default/tablex/regiony`carregar para a região.

### <a name="cause"></a>Causa

Isto deve-se, muito provavelmente, à eliminação parcial da região quando o RegionServer falha ou o VM reinicia. Atualmente, o Armazenamento Azure é um sistema de ficheiros blob plano e algumas operações de ficheiros não são atómicos.

### <a name="resolution"></a>Resolução

Limpe manualmente estes ficheiros e pastas restantes:

1. Execute `hdfs dfs -ls /hbase/data/default/tablex/regiony` para verificar que pastas/ficheiros ainda estão por baixo.

1. Execute `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` para eliminar todos os ficheiros/pastas de crianças

1. Execute `hdfs dfs -rmr /hbase/data/default/tablex/regiony` para eliminar a pasta da região.

---

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
