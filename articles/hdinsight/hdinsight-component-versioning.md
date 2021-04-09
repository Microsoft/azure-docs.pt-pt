---
title: Componentes e versões Apache Hadoop - Azure HDInsight
description: Saiba mais sobre os componentes e versões Apache Hadoop em Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: dbd5b507fd4a7b2434158dbdc80584a7fd348732
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726588"
---
# <a name="azure-hdinsight-versions"></a>Versões Azure HDInsight

HDInsight agrega componentes ambientais Apache Hadoop e plataforma HDInsight num pacote que é implantado num cluster. Para mais detalhes, consulte [como funciona a versão HDInsight](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>Versões HDInsight suportadas

Esta tabela lista as versões de HDInsight que estão disponíveis no portal Azure e outros métodos de implementação como PowerShell, CLI e .NET SDK.

| Versão HDInsight | SO da VM | Data da versão| Tipo de suporte | Prazo de validade do suporte | Data da reforma | Elevada disponibilidade |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |Setembro de 24, 2018 | [Standard](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |Yes |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |4 de abril de 2017      | [Básica](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | Expiração padrão do suporte - 30 de junho de 2021 <br> Expiração do suporte básico - 3 de abril de 2022 |4 de abril de 2022 |Yes |

*A partir de 1 de julho de 2021 a Microsoft oferecerá suporte básico para certos tipos de cluster HDI 3.6. Consulte [as versões de componentes HDInsight 3.6](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Notas de versão

Para obter notas de lançamento adicionais sobre as versões mais recentes do HDInsight, consulte as notas de [lançamento do HDInsight](hdinsight-release-notes.md).

## <a name="support-options-for-hdinsight-versions"></a>Opções de suporte para versões HDInsight

O suporte é definido como um período de tempo em que uma versão HDInsight é suportada pelo Microsoft Customer Service and Support. O HDInsight oferece dois tipos de suporte: 
- **O suporte padrão** é um período de tempo em que a Microsoft fornece atualizações e suporte em clusters HDInsight.  
    Recomendamos a construção de soluções utilizando a versão mais recente totalmente suportada. 
- **O suporte básico** é um período de tempo em que a Microsoft fornecerá assistência limitada ao fornecedor de recursos HDInsight. As imagens HDInsight e os componentes de software de código aberto (OSS) não serão reparados.   Apenas as correções críticas de segurança serão corrigidas em clusters HDInsight.  
  A Microsoft não incentiva a criação de novos clusters ou a construção de novas soluções quando uma versão está no suporte Basic. Recomendamos a migração dos clusters existentes para a versão mais recente totalmente suportada. 

**A expiração do suporte** significa que a Microsoft já não fornece suporte para a versão HDInsight específica. E pode já não estar disponível através do portal Azure para a criação de clusters.

**A reforma** significa que os aglomerados existentes de uma versão HDInsight continuam a funcionar como está. Novos clusters desta versão não podem ser criados através de qualquer meio, que inclui os CLI e SDKs. Outras características do plano de controlo, tais como a escala manual e a autoscalagem, não estão garantidas para trabalhar após a data de aposentadoria. O suporte não está disponível para versões aposentadas.

## <a name="versioning-considerations"></a>Considerações de versão
- Uma vez que um cluster é implantado com uma imagem, esse cluster não é automaticamente atualizado para a versão de imagem mais recente. Ao criar novos clusters, a versão de imagem mais recente será implementada.
- Os clientes devem testar e validar que as aplicações funcionam corretamente quando utilizam a nova versão HDInsight.
- O HDInsight reserva-se o direito de alterar a versão padrão sem aviso prévio. Se tiver uma dependência de versão, especifique a versão HDInsight quando criar os seus clusters.
- O HDInsight pode retirar uma versão componente OSS antes de retirar a versão HDInsight.

## <a name="next-steps"></a>Passos seguintes

- [Configuração de cluster para Apache Hadoop, Spark, e muito mais em HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Pacote de Segurança Enterprise](./enterprise-security-package.md)
- [Trabalhar em Apache Hadoop em HDInsight a partir de um PC windows](hdinsight-hadoop-windows-tools.md)
