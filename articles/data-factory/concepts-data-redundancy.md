---
title: Redundância de dados na Azure Data Factory Microsoft Docs
description: Conheça os mecanismos de redundância de meta-dados na Azure Data Factory
services: data-factory
documentationcenter: ''
author: nabhishek
manager: weehyongtok
ms.reviewer: abnarain
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: f71fdf66624d67939f915f91c2cc1dbe7553cad7
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332117"
---
# <a name="azure-data-factory-data-redundancy"></a>**Redundância de dados da Azure Data Factory**

Os dados da Azure Data Factory incluem metadados (pipeline, conjuntos de dados, serviços ligados, tempo de execução e gatilhos de integração) e dados de monitorização (pipeline, trigger e operações). 

Em todas as regiões (exceto Brasil Sul e Sudeste Asiático), os dados da Azure Data Factory são armazenados e replicados na [região emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#azure-regional-pairs) para proteger contra a perda de metadados. Durante as falhas regionais do datacenter, a Microsoft pode iniciar uma falha regional da sua instância Azure Data Factory. Na maioria dos casos, não é necessária nenhuma ação da sua parte. Quando o failover gerido pela Microsoft estiver concluído, poderá aceder à sua Azure Data Factory na região de failover. 

Devido aos requisitos de residência de dados no Brasil Sul e Sudeste Asiático, os dados da Azure Data Factory são armazenados apenas na [região local.](https://docs.microsoft.com/azure/storage/common/storage-redundancy#locally-redundant-storage) Para o Sudeste Asiático, todos os dados são armazenados em Singapura. Para o Brasil Sul, todos os dados são armazenados no Brasil. Quando a região se perder devido a um desastre significativo, a Microsoft não será capaz de recuperar os seus dados da Azure Data Factory.  

> [!NOTE]
> O failover gerido pela Microsoft não se aplica ao tempo de execução de integração auto-hospedado (SHIR), uma vez que esta infraestrutura é normalmente gerida pelo cliente. Se o SHIR for criado em Azure VM, então a recomendação é alavancar a recuperação do [local de Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) para lidar com o fracasso do [VM Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture) para outra região.



## <a name="using-source-control-in-azure-data-factory"></a>**Utilização do controlo de fontes na Azure Data Factory**

Para garantir que é capaz de rastrear e auditar as alterações es feitas aos metadados da sua fábrica de dados Azure, deverá considerar a criação de um controlo de origem para a sua Fábrica de Dados Azure. Também lhe permitirá aceder aos seus ficheiros JSON de metadados para pipelines, conjuntos de dados, serviços ligados e desencadear. A Azure Data Factory permite-lhe trabalhar com diferentes repositórios de Git (Azure DevOps e GitHub). 

 Saiba como configurar [o controlo de origem na Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control). 

> [!NOTE]
> Em caso de catástrofe (perda de região), a nova fábrica de dados pode ser a provisionada manualmente ou de forma automatizada. Uma vez criada a nova fábrica de dados, poderá restaurar os seus oleodutos, conjuntos de dados e serviços ligados JSON do repositório git existente. 



## <a name="data-stores"></a>**Arquivos de dados**

A Azure Data Factory permite-lhe mover dados entre lojas de dados localizadas no local e na nuvem. Para garantir a continuidade do negócio com as suas lojas de dados, deverá consultar as recomendações de continuidade do negócio para cada uma destas lojas de dados. 

 

## <a name="see-also"></a>Ver também

- [Pares Regionais Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Residência de dados em Azure](https://azure.microsoft.com/global-infrastructure/data-residency/) 
