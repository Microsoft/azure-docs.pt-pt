---
title: Notas de versão do Azure HDInsight
description: Notas de versão mais recentes do Azure HDInsight. Obtenha dicas e detalhes de desenvolvimento para Hadoop, Spark, R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 091ca4d632d89405d85c66e264aff9867979fcd4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905224"
---
# <a name="release-notes"></a>Notas de versão

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte [arquivo de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre clientes empresariais para análise de software livre no Azure.

## <a name="release-date-01092020"></a>Data de lançamento: 01/09/2020

Esta versão se aplica tanto ao HDInsight 3,6 quanto ao 4,0. A versão do HDInsight é disponibilizada para todas as regiões durante vários dias. A data de lançamento aqui indica a data de lançamento da primeira região. Se você não vir as alterações abaixo, aguarde até que a liberação esteja ativa em sua região em vários dias.

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte o [artigo controle de versão do HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Novas funcionalidades
### <a name="tls-12-enforcement"></a>Imposição de TLS 1.2
O protocolo TLS e protocolo SSL (SSL) são protocolos criptográficos que fornecem segurança de comunicações em uma rede de computadores. Saiba mais sobre o [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). O HDInsight usa o TLS 1,2 em pontos de extremidade HTTPs públicos, mas o TLS 1,1 ainda tem suporte para compatibilidade com versões anteriores. 

Com esta versão, os clientes podem optar pelo TLS 1,2 apenas para todas as conexões por meio do ponto de extremidade do cluster público. Para dar suporte a isso, a nova propriedade **minSupportedTlsVersion** é introduzida e pode ser especificada durante a criação do cluster. Se a propriedade não estiver definida, o cluster ainda dará suporte a TLS 1,0, 1,1 e 1,2, que é o mesmo comportamento de hoje. Os clientes podem definir o valor dessa propriedade como "1,2", o que significa que o cluster só oferece suporte a TLS 1,2 e superior. Para obter mais informações, consulte [planejar uma rede virtual – segurança da camada de transporte](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security).

### <a name="bring-your-own-key-for-disk-encryption"></a>Traga sua própria chave para a criptografia de disco
Todos os discos gerenciados no HDInsight são protegidos com o Azure Criptografia do Serviço de Armazenamento (SSE). Por padrão, os dados nesses discos são criptografados por chaves gerenciadas pela Microsoft. A partir desta versão, você pode Bring Your Own Key (BYOK) para a criptografia de disco e gerenciá-lo usando Azure Key Vault. A criptografia BYOK é uma configuração de uma etapa durante a criação do cluster sem custo adicional. Basta registrar o HDInsight como uma identidade gerenciada com Azure Key Vault e adicionar a chave de criptografia ao criar o cluster. Para obter mais informações, consulte [criptografia de disco de chave gerenciada pelo cliente](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Preterição
Nenhuma substituição para esta versão. Para se preparar para [as futuras substituições](#upcoming-changes), confira alterações futuras.

## <a name="behavior-changes"></a>Mudanças de comportamento
Nenhuma alteração de comportamento para esta versão. Para se preparar para as alterações futuras, confira [alterações futuras](#upcoming-changes).

## <a name="upcoming-changes"></a>Alterações futuras
As seguintes alterações ocorrerão em versões futuras. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Uma VM de 4 núcleos mínima é necessária para o nó de cabeçalho 
Uma VM de 4 núcleos mínima é necessária para o nó de cabeçalho para garantir a alta disponibilidade e a confiabilidade dos clusters HDInsight. A partir de 6 de abril de 2020, os clientes podem escolher apenas 4 núcleos ou acima da VM como nó principal para os novos clusters HDInsight. Os clusters existentes continuarão a ser executados conforme esperado. 

### <a name="esp-spark-cluster-node-size-change"></a>Alteração do tamanho do nó do cluster do Spark do ESP 
Na próxima versão, o tamanho mínimo permitido para o cluster ESP Spark será alterado para Standard_D13_V2. As VMs da série a podem causar problemas de cluster ESP devido à capacidade relativamente baixa de CPU e memória. As VMs da série A serão preteridas para a criação de novos clusters ESP.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Migrando para conjuntos de dimensionamento de máquinas virtuais do Azure
O HDInsight agora usa máquinas virtuais do Azure para provisionar o cluster. Na próxima versão, o HDInsight usará os conjuntos de dimensionamento de máquinas virtuais do Azure em vez disso. Veja mais sobre os conjuntos de dimensionamento de máquinas virtuais do Azure.

### <a name="hbase-20-to-21"></a>HBase 2,0 a 2,1
Na próxima versão do HDInsight 4,0, a versão do HBase será atualizada da versão 2,0 para a 2,1.

## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a tornar as melhorias de desempenho e confiabilidade do cluster. 

## <a name="component-version-change"></a>Alteração de versão do componente
Nenhuma alteração de versão de componente para esta versão. Você pode encontrar as versões de componente atuais para o HDInsight 4,0 Ad HDInsight 3,6 aqui.

## <a name="known-issues"></a>Problemas conhecidos

A partir de 29 de janeiro de 2020, existe uma questão ativa em que poderá receber um erro ao tentar usar um caderno Jupyter. Use os passos abaixo para corrigir o problema. Também pode consultar este [post da MSDN](https://social.msdn.microsoft.com/Forums/en-us/8c763fb4-79a9-496f-a75c-44a125e934ac/hdinshight-create-not-create-jupyter-notebook?forum=hdinsight) ou este [post StackOverflow](https://stackoverflow.com/questions/59687614/azure-hdinsight-jupyter-notebook-not-working/59831103) para obter informações atualizadas ou para fazer perguntas adicionais. Esta página será atualizada quando o problema for corrigido.

**Erros**

* ValueError: Não pode converter o caderno em v5 porque essa versão não existe
* Erro de carregamento de caderno Um erro desconhecido ocorreu ao carregar este caderno. Esta versão pode carregar formatos de caderno v4 ou mais cedo

**Motivo** 

O ficheiro _version.py no cluster foi atualizado para 5.x.x em vez de 4.4.x.## ou Ambari precisa de ser reiniciado.

**Solução**

Se criar um novo caderno Jupyter e receber um dos erros acima mencionados, execute os seguintes passos para corrigir o problema.

1. Abra Ambari num navegador web indo para `https://CLUSTERNAME.azurehdinsight.net`, onde CLUSTERNAME é o nome do seu cluster.
1. Em Ambari, no menu esquerdo, clique em **Jupyter,** em seguida, em **Ações de Serviço,** clique em **Parar**.
1. ssh no cabeçada do cluster onde o serviço Jupyter está funcionando.
1. Abra o seguinte ficheiro /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py no modo sudo.
1. Verifique o valor da version_info.
1. Se o valor da version_info for definido para: 

    version_info = (5, 0, 3)

    Em seguida, modificar a entrada para: 
    
    version_info = (4, 4, 0)

    E guarde o ficheiro. 

    Se version_info já está definido para (4, 4, 0), então continue para o próximo passo, pois apenas Ambari precisa ser reiniciado, não são necessárias alterações adicionais.
1. Volte para Ambari, e em Ações de **Serviço,** clique em **Reiniciar Tudo**.
