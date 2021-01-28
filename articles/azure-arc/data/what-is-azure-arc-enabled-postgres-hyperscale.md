---
title: O que é a hiperescala pós-SQL ativada por Azure?
description: O que é a hiperescala pós-SQL ativada por Azure?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 10f21067f48155a394ac20337d77e3e82aae64d8
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985942"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>O que é a hiperescala pós-SQL ativada por Azure?

A azure Arc ativado PostgreSQL Hyperscale é um dos serviços de base de dados disponíveis como parte dos serviços de dados habilitados a Azure Arc. O Azure Arc permite executar os serviços de dados da Azure no local, no limite, e em nuvens públicas usando Kubernetes e a infraestrutura à sua escolha. A proposta de valor do Azure Arc permitiu que os serviços de dados se articulassem em torno de:
- Sempre atual
- Dimensionamento elástico
- Prestação de self-service
- Gestão unificada
- Suporte de cenário desligado

Leia mais detalhes em:
- [O que são serviços de dados habilitados a Azure Arc](overview.md)
- [Modos e requisitos de conectividade](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>Comparar soluções

Esta secção descreve como a hiperescala pós-alta ativada Azure Arc difere da Base de Dados Azure para a Hiperescala Pós-SQL (Citus)?

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Hyperscale (Citus) da Base de Dados do Azure para PostgreSQL

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Base de Dados Azure SQL para a hiperescala pós-SQL (Citus)":::

Este é o fator de forma de hiperescala do motor de base de dados Postgres disponível como base de dados como um serviço em Azure (PaaS). É alimentado pela extensão Citus que permite a experiência de hiperescala. Neste fator de formulário, o serviço funciona nos datacenters da Microsoft e é operado pela Microsoft.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc habilitado a hiperescala pósgresql

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure Arc habilitado a hiperescala pósgresql":::

Este é o fator de forma de hiperescala do motor de base de dados Postgres que está disponível com serviços de dados habilitados a Azure Arc. Também é alimentado pela extensão Citus que permite a experiência de hiperescala. Neste fator de forma, os nossos clientes fornecem a infraestrutura que acolhe os sistemas e os opera.

## <a name="next-steps"></a>Passos seguintes
- **Experimente.** Inicie-se rapidamente com [o Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) no Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou num Azure VM. 

- **Crie o seu próprio.** Siga estes passos para criar no seu próprio cluster Kubernetes: 
   1. [Instale as ferramentas do cliente](install-client-tools.md)
   2. [Criar o controlador de dados Azure Arc](create-data-controller.md)
   3. [Criar uma base de dados de Azure para o grupo de servidores de hiperescala PostgreSQL em Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Ler mais sobre serviços de dados habilitados a Azure Arc](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Leia sobre Azure Arc](https://aka.ms/azurearc)
