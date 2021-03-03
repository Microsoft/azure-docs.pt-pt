---
title: Introdução da versão - Azure HDInsight
description: Saiba como funciona a versão em Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: c4dddeef5daf167eeef92532b61ed986861896e8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746368"
---
# <a name="how-versioning-works-in-hdinsight"></a>Como a versão funciona em HDInsight

O serviço HDInsight tem dois componentes principais: um fornecedor de recursos e componentes Apache Hadoop que são implantados num cluster. 

## <a name="hdinsight-resource-provider"></a>Fornecedor de recursos HDInsight

Os recursos em Azure são disponibilizados por um fornecedor de recursos. O fornecedor de recursos HDInsight é responsável pela criação, gestão e eliminação de clusters.

## <a name="hdinsight-images"></a>Imagens HDInsight

O HDInsight utiliza imagens para reunir componentes de software de código aberto (OSS) que podem ser implantados num cluster. Estas imagens contêm o sistema operativo Ubuntu base e componentes principais tais como Spark, Hadoop, Kafka, HBase ou Hive.

## <a name="versioning-in-hdinsight"></a>Versão em HDInsight

A Microsoft atualiza periodicamente as imagens e o fornecedor de Recursos HDInsight para incluir novas melhorias e funcionalidades.

A nova versão HDInsight pode ser criada quando uma ou mais das seguintes são verdadeiras:

- Grandes alterações ou atualizações para a funcionalidade do fornecedor de recursos HDInsight
- Principais lançamentos de componentes da OSS
- Grandes alterações no sistema operativo Ubuntu

Uma nova versão de imagem é criada quando uma ou mais das seguintes são verdadeiras:

- Lançamentos e atualizações principais ou menores dos componentes da OSS
- Patches ou correções para um componente na imagem

## <a name="next-steps"></a>Passos seguintes

- [Componentes e versões Apache em HDInsight](./hdinsight-component-versioning.md)
