---
title: hbase hbck devolve inconsistências em Azure HDInsight
description: hbase hbck devolve inconsistências em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75887330"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Cenário: `hbase hbck` o comando devolve inconsistências no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue-region-is-not-in-hbasemeta"></a>Questão: A região não está em `hbase:meta`

Região xxx em HDFS, mas não listado ou implantado em `hbase:meta` qualquer servidor da região.

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Resolução

1. Fixar a meta-tabela correndo:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Atribuir regiões aos RegionServers executando:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Problema: Região está offline

Região xxx não implantada em nenhuma RegiãoServer. Isto significa que a região está `hbase:meta` em , mas offline.

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Resolução

Trazer as regiões on-line correndo:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Questão: As regiões têm as mesmas teclas de início/fim

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Resolução

Fundem manualmente as regiões sobrepostas. Vá à secção de tabelaS HBase HMaster Web UI, selecione o link de tabela, que tem o problema. Verá a chave de arranque/chave final de cada região pertencente a essa tabela. Em seguida, fundir as regiões sobrepostas. Na concha HBase, faça `merge_region 'xxxxxxxx','yyyyyyy', true` . Por exemplo:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

Neste cenário, é necessário fundir a RegiãoA e a RegionC e obter a RegiãoD com a mesma gama-chave que a RegiãoB, depois fundir a RegiãoB e a RegiãoD. xxxxxxx e yyyyyyy são a corda de haxixe no final de cada nome da região. Tenha cuidado para não fundir duas regiões descontínuas. Após cada fusão, como a fusão A e C, a HBase iniciará uma compactação na RegiãoD. Aguarde que a compactação termine antes de fazer outra fusão com a RegiãoD. Pode encontrar o estado de compactação na página do servidor da região na HBase HMaster UI.

---

## <a name="issue-cant-load-regioninfo"></a>Questão: Não é possível carregar `.regioninfo`

Não se pode carregar `.regioninfo` para a `/hbase/data/default/tablex/regiony` região.

### <a name="cause"></a>Causa

Isto deve-se, muito provavelmente, à supressão parcial da região quando o RegionServer se despenha ou o VM reinicia. Atualmente, o Azure Storage é um sistema de ficheiros de bolha plana e algumas operações de ficheiro não são atómicas.

### <a name="resolution"></a>Resolução

Limpe manualmente estes ficheiros e pastas restantes:

1. Execute `hdfs dfs -ls /hbase/data/default/tablex/regiony` para verificar quais as pastas/ficheiros que ainda estão por baixo.

1. Execute `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` para eliminar todos os ficheiros/pastas infantis

1. Execute `hdfs dfs -rmr /hbase/data/default/tablex/regiony` para eliminar a pasta da região.

---

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
