---
title: Atualização .NET para Apache Spark para a versão v1.0 em HDI
description: Saiba como atualizar a versão .NET para Apache Spark para 1.0 em HDI e como isso afeta o seu código e clusters existentes.
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: a1602f29a6d0066ec3c99e990532411621652c47
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788134"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>Atualização .NET para Apache Spark para a versão v1.0 em HDInsight

Este documento fala sobre a primeira grande versão de [.NET para Apache Spark](https://github.com/dotnet/spark), e como pode afetar os seus atuais oleodutos de produção em clusters HDInsight.

## <a name="about-net-for-apache-spark-version-100"></a>Cerca de .NET para Apache Spark versão 1.0.0

Este é o primeiro [grande lançamento oficial](https://github.com/dotnet/spark/releases/tag/v1.0.0) de .NET para Apache Spark e fornece a completude da API dataFrame para Spark 2.4.x, bem como Spark 3.0.x juntamente com outras funcionalidades. Para obter uma lista completa de todas as funcionalidades, melhorias e correções de erros, consulte as notas de lançamento oficiais [v1.0.0](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md).
Outra coisa importante a notar é que esta versão **não** é compatível com versões anteriores de `Microsoft.Spark` e `Microsoft.Spark.Worker` . Consulte o [guia de migração](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) se está a planear atualizar a sua aplicação .NET para Apache Spark para ser compatível com v1.0.0.

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>Utilização .NET para Apache Spark v1.0 em HDInsight

Embora os atuais clusters HDI não sejam afetados (ou seja, continuarão a ter a mesma versão de antes), os clusters HDI recém-criados transportarão esta versão mais recente de .1.0.0 de .NET para Apache Spark. O que isto significa se:

- **Tem um cluster HDI mais antigo:** Se pretender atualizar a sua aplicação Spark .NET para v1.0.0 (recomendado), terá de atualizar a `Microsoft.Spark.Worker` versão no seu cluster HDI. Para obter mais informações, consulte as [versões de mudança de .NET para Apache Spark na secção de cluster HDI](#changing-net-for-apache-spark-version-on-hdinsight).
Se não pretender atualizar a versão atual de .NET para Apache Spark na sua aplicação, não são necessários mais passos.  

- **Tem um novo cluster HDI**: Se pretender atualizar a sua aplicação Spark .NET para v1.0.0 (recomendado), não são necessários passos para alterar o trabalhador em HDI, no entanto terá de consultar o [guia de migração](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) para entender os passos necessários para atualizar o seu código e os seus oleodutos.
Se não quiser alterar a versão atual de .NET para Apache Spark na sua aplicação, terá de alterar a versão do seu cluster HDI de v1.0 (padrão em novos clusters) para qualquer versão que esteja a utilizar. Para obter mais informações, consulte as [versões de mudança de .NET para Apache Spark na secção de cluster HDI](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight).  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>Alterar a versão .NET para Apache Spark em HDInsight

### <a name="deploy-microsoftsparkworker"></a>Implementar Microsoft.Spark.Worker

`Microsoft.Spark.Worker` é um componente de backend que vive nos nós individuais dos trabalhadores do seu cluster Spark. Quando pretende executar um UDF C# (função definida pelo utilizador), a Spark precisa de compreender como lançar o .NET CLR para executar este UDF. `Microsoft.Spark.Worker` fornece uma coleção de classes para Spark que permitem esta funcionalidade. Selecione a versão do trabalhador dependendo da versão de .NET para Apache Spark que pretende implantar no cluster HDI.

1. Descarregue o lançamento do Microsoft.Spark.Worker Linux da sua versão em particular. Por exemplo, se `.NET for Apache Spark v1.0.0` quiser, baixará [Microsoft.Spark.Worker.netcoreapp3.1.linux-x64-1.0.0.tar.gz](https://github.com/dotnet/spark/releases/tag/v1.0.0).  

2. Baixe [install-worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) script para instalar os binários de trabalhador descarregados no Passo 1 para todos os nós operários do seu cluster HDI.  

3. Faça o upload dos ficheiros acima mencionados para a conta de Armazenamento Azure a que o seu cluster tem acesso. Pode consultar [o artigo de implementação .NET para Apache Spark HDI](/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure) para obter mais detalhes.

4. Execute o `install-worker.sh` script em todos os nós dos trabalhadores do seu cluster, usando as ações do Script. Consulte [o artigo de implementação .NET para Apache Spark HDI](/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action) para obter mais informações.

### <a name="update-your-application-to-use-specific-version"></a>Atualize a sua aplicação para utilizar versão específica

Pode atualizar a sua aplicação .NET para Apache Spark para utilizar uma versão específica, escolhendo a versão necessária do [pacote Microsoft.Spark NuGet](https://www.nuget.org/packages/Microsoft.Spark/) no seu projeto. Certifique-se de verificar as notas de lançamento da versão em particular e do [guia de migração,](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) conforme mencionado acima, se optar por atualizar a sua aplicação para v1.0.0.

## <a name="faqs"></a>FAQs

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>Será que o meu cluster HDI existente com a versão < 1.0.0 vai começar a falhar com o novo lançamento?

Os clusters HDI existentes continuarão a ter a mesma versão anterior para .NET para Apache Spark e a sua aplicação existente (tendo versão anterior do Spark .NET) não será afetada.

## <a name="next-steps"></a>Próximos passos

[Implemente a sua aplicação .NET para Apache Spark em HDInsight](/dotnet/spark/tutorials/hdinsight-deployment)