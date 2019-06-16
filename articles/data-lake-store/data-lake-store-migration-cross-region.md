---
title: Migração de entre regiões de geração 1 do Data Lake Storage do Azure | Documentos da Microsoft
description: Saiba mais sobre a migração entre regiões de geração 1 de armazenamento do Azure Data Lake.
services: data-lake-store
documentationcenter: ''
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: stewu
ms.openlocfilehash: 0bf0843314f38c0de28820c82e95b7921297bf40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60518477"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Migrar Gen1 de armazenamento do Azure Data Lake em várias regiões

Como a geração 1 de armazenamento do Azure Data Lake torna-se disponíveis em novas regiões, pode optar por fazer uma única migração, para aproveitar a nova região. Saiba o que considerar quando planear e concluir a migração.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Para obter mais informações, consulte [crie hoje a sua conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de geração 1 de armazenamento do Data Lake em duas regiões diferentes**. Para obter mais informações, consulte [introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Para obter mais informações, veja [Introdução ao Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Considerações sobre a migração

Em primeiro lugar, identifique a estratégia de migração que funciona melhor para a sua aplicação que grava, lê ou processa dados no Data Lake Storage Gen1. Ao escolher uma estratégia, considere os requisitos de disponibilidade de seu aplicativo e o período de indisponibilidade ocorre durante uma migração. Por exemplo, a abordagem mais simples pode ser a utilização do modelo de migração "lift-and-shift" de cloud. Nesta abordagem, colocar em pausa a aplicação na sua região existente, enquanto todos os seus dados são copiados para a nova região. Quando o processo de cópia estiver concluído, pode retomar a sua aplicação na nova região e, em seguida, elimina a conta de geração 1 de armazenamento do Data Lake antiga. É necessário o período de indisponibilidade durante a migração.

Para reduzir o período de indisponibilidade, pode começar imediatamente a ingestão de novos dados na nova região. Quando tiver os dados mínimos necessários, execute a sua aplicação na nova região. Em segundo plano, continue a copiar os dados mais antigos da conta do Data Lake Storage Gen1 existente para a nova conta de geração 1 de armazenamento do Data Lake na nova região. Ao utilizar esta abordagem, pode fazer a transição para a nova região com pouco tempo de inatividade. Quando todos os dados mais antigos foi copiado, elimina a conta de geração 1 de armazenamento do Data Lake antiga.

Outros detalhes importantes a considerar quando planear a migração são:

* **Volume de dados**. O volume de dados (em gigabytes, o número de ficheiros e pastas e assim por diante) afeta o tempo e os recursos que necessários para a migração.

* **Nome da conta do Data Lake Storage Gen1**. O novo nome de conta na nova região deve ser globalmente exclusivo. Por exemplo, o nome da sua conta do Data Lake Storage Gen1 antiga na região E.U.A. Leste 2 pode ser contosoeastus2.azuredatalakestore.net. Pode nomear sua nova conta de geração 1 de armazenamento do Data Lake na UE Norte contosonortheu.azuredatalakestore.net.

* **Ferramentas**. Recomendamos que utilize o [atividade de cópia de fábrica de dados do Azure](../data-factory/connector-azure-data-lake-store.md) para copiar ficheiros de geração 1 de armazenamento do Data Lake. O Data Factory suporta o movimento de dados com alto desempenho e confiabilidade. Tenha em atenção que o Data Factory copia apenas a hierarquia de pasta e o conteúdo dos ficheiros. Tem de aplicar manualmente a quaisquer listas de controlo de acesso (ACLs) que utilize a conta antiga para a nova conta. Para obter mais informações, incluindo as metas de desempenho para cenários mais favorável, consulte a [guia de sintonização de desempenho de atividade de cópia e](../data-factory/copy-activity-performance.md). Se pretender que os dados copiados mais rapidamente, poderá ter de utilizar unidades de movimento de dados na Cloud adicionais. Algumas outras ferramentas, como o AdlCopy, não suportam a cópia de dados entre regiões.  

* **Custos de largura de banda**. [Custos de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/) aplicam-se de uma vez que dados são transferidos para fora de uma região do Azure.

* **ACLs nos seus dados**. Proteger os seus dados na nova região através da aplicação de ACLs para ficheiros e pastas. Para obter mais informações, consulte [proteger os dados armazenados no Azure Data Lake Storage Gen1](data-lake-store-secure-data.md). Recomendamos que utilize a migração para atualizar e ajustar as ACLs. Pode querer utilizar definições semelhantes às suas definições atuais. Pode ver as ACLs que são aplicadas a qualquer arquivo com o portal do Azure, [cmdlets do PowerShell](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission), ou SDKs.  

* **Localização de serviços de análise**. Para um melhor desempenho, seus serviços de análise, como o Azure Data Lake Analytics ou o Azure HDInsight, devem estar na mesma região que seus dados.  

## <a name="next-steps"></a>Passos Seguintes
* [Descrição geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
