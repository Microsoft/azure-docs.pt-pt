---
title: o HBase hbck retorna inconsistências no Azure HDInsight
description: o HBase hbck retorna inconsistências no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
ms.openlocfilehash: 6bf136d943f230eb02df62cbaa732e28d6b359a4
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968465"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Cenário: `hbase hbck` o comando retorna inconsistências no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue-region-is-not-in-hbasemeta"></a>Problema: A região não está em`hbase:meta`

Região xxx no HDFS, mas não listada `hbase:meta` em ou implantada em qualquer servidor de região.

### <a name="cause"></a>Causa

Consoante.

### <a name="resolution"></a>Resolução

1. Corrija a tabela meta executando:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Atribua regiões ao RegionServers executando:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Problema: A região está offline

Região xxx não implantada em nenhum RegionServer. Isso significa que a região está `hbase:meta`em, mas offline.

### <a name="cause"></a>Causa

Consoante.

### <a name="resolution"></a>Resolução

Coloque as regiões online executando:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Problema: As regiões têm as mesmas chaves de início/término

### <a name="cause"></a>Causa

Consoante.

### <a name="resolution"></a>Resolução

Mescle manualmente essas regiões sobrepostas. Vá para a seção da interface do usuário da Web do HBase HMaster, selecione o link da tabela, que tem o problema. Você verá chave inicial/chave final de cada região que pertence a essa tabela. Em seguida, mescle essas regiões sobrepostas. No Shell do HBase, `merge_region 'xxxxxxxx','yyyyyyy', true`faça. Por exemplo:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

Nesse cenário, você precisa mesclar Regiona e RegionC e ser região com o mesmo intervalo de chaves que RegionB, em seguida, Merge RegionB e Regioned. xxxxxxx e Yyyyyy são a cadeia de caracteres de hash no final de cada nome de região. Tenha cuidado para não mesclar duas regiões descontínuas. Após cada mesclagem, como Merge A e C, o HBase iniciará uma compactação em região. Aguarde até que a compactação seja concluída antes de fazer outra mesclagem com região. Você pode encontrar o status de compactação nessa página do servidor de região na interface do usuário do HBase HMaster.

---

## <a name="issue-cant-load-regioninfo"></a>Problema: Não é possível carregar`.regioninfo`

Não é `.regioninfo` possível carregar `/hbase/data/default/tablex/regiony`para a região.

### <a name="cause"></a>Causa

Isso é provavelmente devido à exclusão parcial da região quando RegionServer falha ou reinicializações de VM. Atualmente, o armazenamento do Azure é um sistema de arquivos de blob simples e algumas operações de arquivo não são atômicas.

### <a name="resolution"></a>Resolução

Limpe manualmente estes arquivos e pastas restantes:

1. Execute `hdfs dfs -ls /hbase/data/default/tablex/regiony` para verificar quais pastas/arquivos ainda estão sob ele.

1. Executar `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` para excluir todos os arquivos/pastas filho

1. Execute `hdfs dfs -rmr /hbase/data/default/tablex/regiony` para excluir a pasta da região.

---

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
