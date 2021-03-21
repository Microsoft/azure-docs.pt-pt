---
title: Azure Data Lake Storage Gen1 migração inter-região | Microsoft Docs
description: Saiba o que considerar como planeia e complete uma migração para a Azure Data Lake Storage Gen1 à medida que se torna disponível em novas regiões.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: twooley
ms.openlocfilehash: c6520036f3ddb8799025129391330268b0604886
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97723811"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Migrar Azure Data Lake Storage Gen1 em regiões

À medida que a Azure Data Lake Storage Gen1 se torna disponível em novas regiões, você pode optar por fazer uma migração única, para aproveitar a nova região. Saiba o que considerar como planeia e complete a migração.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura Azure**. Para mais informações, consulte [hoje a sua conta Azure gratuita.](https://azure.microsoft.com/pricing/free-trial/)
* **Uma conta gen1 de armazenamento de data lake em duas regiões diferentes.** Para mais informações, consulte [Começar com a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Fábrica de Dados Azure.** Para obter mais informações, veja [Introdução ao Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Migration considerations (Considerações sobre a migração)

Em primeiro lugar, identifique a estratégia de migração que funciona melhor para a sua aplicação que escreve, lê ou processa dados na Data Lake Storage Gen1. Ao escolher uma estratégia, considere os requisitos de disponibilidade da sua aplicação e o tempo de inatividade que ocorre durante uma migração. Por exemplo, a sua abordagem mais simples pode ser usar o modelo de migração em nuvem "levantar e mudar". Nesta abordagem, você interrompe a aplicação na sua região existente enquanto todos os seus dados são copiados para a nova região. Quando o processo de cópia estiver concluído, retoma a sua aplicação na nova região e, em seguida, apaga a antiga conta Desema de Armazenamento de Data Lake Gen1. É necessário tempo de inatividade durante a migração.

Para reduzir o tempo de inatividade, pode começar imediatamente a ingerir novos dados na nova região. Quando tiver os dados mínimos necessários, execute a sua aplicação na nova região. Em segundo plano, continue a copiar dados mais antigos da conta de Armazenamento de Data Lake Gen1 para a nova conta da Data Lake Storage Gen1 na nova região. Ao utilizar esta abordagem, pode fazer a mudança para a nova região com pouco tempo de inatividade. Quando todos os dados mais antigos tão antigos tão antigos tão antigos como a Gen1 de armazenamento de dados.

Outros detalhes importantes a ter em conta no planeamento da sua migração são:

* **Volume de dados**. O volume de dados (em gigabytes, o número de ficheiros e pastas, etc.) afeta o tempo e os recursos necessários para a migração.

* **Data Lake Storage Gen1 nome da conta**. O novo nome da conta na nova região deve ser globalmente único. Por exemplo, o nome da sua antiga conta de Data Lake Storage Gen1 no Leste dos EUA 2 pode ser contosoeastus2.azuredatalakestore.net. Pode nomear a sua nova conta de Data Lake Storage Gen1 no Norte da UE contosonortheu.azuredatalakestore.net.

* **Ferramentas**. Recomendamos que utilize a Atividade de Cópia da [Fábrica de Dados Azure](../data-factory/connector-azure-data-lake-store.md) para copiar ficheiros Gen1 de armazenamento de dados. A Data Factory suporta o movimento de dados com elevado desempenho e fiabilidade. Tenha em mente que a Data Factory copia apenas a hierarquia e o conteúdo das pastas. Tem de aplicar manualmente quaisquer listas de controlo de acesso (ACLs) que utilize na conta antiga para a nova conta. Para obter mais informações, incluindo metas de desempenho para os melhores cenários, consulte o [desempenho da Atividade de Cópia e o guia de afinação](../data-factory/copy-activity-performance.md). Se quiser que os dados seja copiados mais rapidamente, poderá ter de utilizar unidades adicionais de movimento de dados de nuvem. Algumas outras ferramentas, como a AdlCopy, não suportam a cópia de dados entre regiões.  

* **Cargas de largura de banda.** [As taxas de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/) aplicam-se porque os dados são transferidos para fora de uma região de Azure.

* **ACLs nos seus dados.** Proteja os seus dados na nova região aplicando ACLs em ficheiros e pastas. Para obter mais informações, consulte [os dados de segurança armazenados na Azure Data Lake Storage Gen1](data-lake-store-secure-data.md). Recomendamos que utilize a migração para atualizar e ajustar os seus ACLs. Pode querer utilizar configurações semelhantes às das definições atuais. Pode ver os ACLs que são aplicados a qualquer ficheiro utilizando o portal Azure, [os cmdlets PowerShell](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)ou os SDKs.  

* **Localização dos serviços de análise.** Para melhor desempenho, os seus serviços de análise, como o Azure Data Lake Analytics ou o Azure HDInsight, devem estar na mesma região que os seus dados.  

## <a name="next-steps"></a>Passos seguintes
* [Visão geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
