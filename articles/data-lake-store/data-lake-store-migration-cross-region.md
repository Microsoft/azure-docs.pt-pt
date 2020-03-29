---
title: Migração transversal do Lago Azure Data Gen1 Microsoft Docs
description: Saiba mais sobre a migração transversal para azure Data Lake Storage Gen1.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60518477"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Migrate Azure Data Lake Storage Gen1 através de regiões

À medida que o Azure Data Lake Storage Gen1 se torna disponível em novas regiões, poderá optar por fazer uma migração única, para aproveitar a nova região. Aprenda o que considerar como planeia e complete a migração.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Para mais informações, consulte [Criar a sua conta Azure gratuita hoje](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de Data Lake Storage Gen1 em duas regiões diferentes.** Para mais informações, consulte [Start start with Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* Fábrica de **Dados Azure.** Para obter mais informações, veja [Introdução ao Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Considerações sobre a migração

Em primeiro lugar, identifique a estratégia de migração que funciona melhor para a sua aplicação que escreve, lê ou processa dados em Data Lake Storage Gen1. Ao escolher uma estratégia, considere os requisitos de disponibilidade da sua aplicação e o tempo de inatividade que ocorre durante uma migração. Por exemplo, a sua abordagem mais simples pode ser usar o modelo de migração em nuvem "lift-and-shift". Nesta abordagem, você pausa a aplicação na sua região existente enquanto todos os seus dados são copiados para a nova região. Quando o processo de cópia estiver concluído, recomeça a sua aplicação na nova região e, em seguida, elimina a antiga conta Data Lake Storage Gen1. É necessário tempo de inatividade durante a migração.

Para reduzir o tempo de inatividade, poderá começar imediatamente a ingerir novos dados na nova região. Quando tiver os dados mínimos necessários, faça a sua aplicação na nova região. Em segundo plano, continue a copiar dados mais antigos da conta gen1 de armazenamento de data lake para a nova conta Data Lake Storage Gen1 na nova região. Ao utilizar esta abordagem, pode fazer a mudança para a nova região com pouco tempo de inatividade. Quando todos os dados mais antigos forem copiados, elimine a antiga conta Data Lake Storage Gen1.

Outros detalhes importantes a ter em conta ao planear a sua migração são:

* **Volume de dados**. O volume de dados (em gigabytes, o número de ficheiros e pastas, e assim por diante) afeta o tempo e os recursos necessários para a migração.

* **Data Lake Storage Gen1 nome da conta**. O novo nome de conta na nova região deve ser globalmente único. Por exemplo, o nome da sua antiga conta data lake storage Gen1 no Leste dos EUA 2 pode ser contosoeastus2.azuredatalakestore.net. Pode nomear a sua nova conta data lake storage Gen1 no norte da UE contosonortheu.azuredatalakestore.net.

* **Ferramentas.** Recomendamos que utilize a Atividade de Cópia da Fábrica de [Dados Azure](../data-factory/connector-azure-data-lake-store.md) para copiar ficheiros Gen1 de Armazenamento de Data Lake. Data Factory suporta o movimento de dados com alto desempenho e fiabilidade. Tenha em mente que a Data Factory copia apenas a hierarquia da pasta e o conteúdo dos ficheiros. É necessário aplicar manualmente quaisquer listas de controlo de acesso (ACLs) que utilize na conta antiga para a nova conta. Para obter mais informações, incluindo alvos de desempenho para cenários melhores, consulte o desempenho da Atividade de Cópia e o guia de [afinação](../data-factory/copy-activity-performance.md). Se pretender que os dados sejam copiados mais rapidamente, poderá ter de utilizar unidades adicionais de movimento de dados em nuvem. Algumas outras ferramentas, como a AdlCopy, não suportam copiar dados entre regiões.  

* **Cargas de largura de banda**. [Os encargos de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/) aplicam-se porque os dados são transferidos para fora de uma região de Azure.

* **ACLs nos seus dados**. Proteja os seus dados na nova região aplicando ACLs em ficheiros e pastas. Para mais informações, consulte [a Segurança dos Dados armazenados no Azure Data Lake Storage Gen1](data-lake-store-secure-data.md). Recomendamos que utilize a migração para atualizar e ajustar os seus ACLs. É melhor utilizar definições semelhantes às definições atuais. Pode visualizar os ACLs que são aplicados a qualquer ficheiro utilizando o portal Azure, [cmdlets PowerShell](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)ou SDKs.  

* **Localização dos serviços de análise.** Para um melhor desempenho, os seus serviços de análise, como o Azure Data Lake Analytics ou o Azure HDInsight, devem estar na mesma região que os seus dados.  

## <a name="next-steps"></a>Passos seguintes
* [Visão geral do Armazenamento de Lagos De Dados Azure Gen1](data-lake-store-overview.md)
