---
title: Suporte de software de código aberto no Azure HDInsight
description: O Microsoft Azure fornece um nível geral de suporte para tecnologias de código aberto.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: dd32d176b3e8821236f39dd1677d6dbe78b40adb
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836078"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Suporte de software de código aberto no Azure HDInsight

O serviço Microsoft Azure HDInsight utiliza um ambiente de tecnologias de código aberto formadas em torno do Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de código aberto. Para mais informações, consulte a secção de Suporte das [FAQs](https://azure.microsoft.com/support/faq/)de **Suporte** Azure . O serviço HDInsight fornece um nível adicional de suporte para componentes incorporados.

## <a name="components"></a>Componentes

Dois tipos de componentes de código aberto estão disponíveis no serviço HDInsight:

### <a name="built-in-components"></a>Componentes incorporados

Estes componentes são pré-instalados em clusters HDInsight e fornecem a funcionalidade central do cluster. Os seguintes componentes pertencem a esta categoria:

* [YARN Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Gestor de Recursos.
* A linguagem de consulta da Colmeia [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
* [Apache Mahout.](https://mahout.apache.org/)

Uma lista completa de componentes de cluster está disponível em [Quais são os componentes e versões Apache Hadoop disponíveis com hDInsight?](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Componentes personalizados

Como utilizador do cluster, pode instalar ou utilizar na sua carga de trabalho qualquer componente disponível na comunidade ou criado por si.

> [!WARNING]  
> Os componentes fornecidos com o cluster HDInsight são totalmente suportados. O Microsoft Support ajuda a isolar e resolver problemas relacionados com estes componentes.
>
> Os componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a resolver o problema. O Microsoft Support poderá ser capaz de resolver o problema. Ou podem pedir-lhe para contratar canais disponíveis para as tecnologias de código aberto onde se encontra uma profunda experiência para essa tecnologia. Muitos locais comunitários podem ser usados. Exemplos são [Microsoft Q&Uma página de perguntas para HDInsight](https://docs.microsoft.com/answers/topics/azure-hdinsight.html) e Stack [Overflow](https://stackoverflow.com).
>
> Os projetos Apache também têm sites de projetos no [site da Apache.](https://apache.org) Um exemplo é [Hadoop.](https://hadoop.apache.org/)

## <a name="component-usage"></a>Utilização do componente

O serviço HDInsight fornece várias formas de utilizar componentes personalizados. O mesmo nível de suporte aplica-se, independentemente da forma como um componente é utilizado ou instalado no cluster. A tabela seguinte descreve as formas mais comuns de os componentes personalizados serem usados em clusters HDInsight:

|Utilização |Descrição |
|---|---|
|Submissão de emprego|Hadoop ou outros tipos de trabalhos que executam ou utilizam componentes personalizados podem ser submetidos ao cluster.|
|Personalização do cluster|Durante a criação do cluster, pode especificar configurações adicionais e componentes personalizados que estão instalados nos nós do cluster.|
|Amostras|Para componentes personalizados populares, a Microsoft e outros podem fornecer amostras de como estes componentes podem ser usados em clusters HDInsight. Estas amostras são fornecidas sem apoio.|

## <a name="next-steps"></a>Passos seguintes

* [Personalize os clusters Azure HDInsight utilizando ações de script](./hdinsight-hadoop-customize-cluster-linux.md)
* [Desenvolver scripts de ação de script para HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Gerir com segurança o ambiente do Python no Azure HDInsight com a Ação de Script](./spark/apache-spark-python-package-installation.md)
