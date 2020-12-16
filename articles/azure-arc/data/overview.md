---
title: O que são os serviços de Dados preparados para Azure Arc
description: Introduz serviços de dados habilitados a Azure Arc
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: overview
ms.openlocfilehash: ef16a4c0b6ea40fb4934307916f1fe79ccea72f9
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609144"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>O que são serviços de dados compatíveis com o Azure Arc (pré-visualização)?

O Azure Arc permite executar os serviços de dados da Azure no local, no limite, e em nuvens públicas usando Kubernetes e a infraestrutura à sua escolha.

Atualmente, os seguintes serviços de dados habilitados Azure Arc estão disponíveis na pré-visualização:

- Instância Gerida do SQL
- Hiperescala pós-alta

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>Sempre atual

O Azure Arc permitiu que serviços de dados como o Azure Arc permitissem que o SQL permitisse a ocorrência gerida e o Azure Arc via o PostgreSQL Hyperscale receber atualizações de forma frequente, incluindo patches de manutenção e novas funcionalidades semelhantes à experiência em Azure. As atualizações do Registo de Contentores da Microsoft são fornecidas a si e as cadências de implantação são definidas por si de acordo com as suas políticas. Desta forma, as bases de dados no local podem manter-se atualizadas, garantindo ao mesmo tempo o controlo. Uma vez que os serviços de dados habilitados a Azure Arc são um serviço de subscrição, deixará de enfrentar situações de fim de suporte para as suas bases de dados.

## <a name="elastic-scale"></a>Dimensionamento elástico

A elasticidade em nuvem no local permite-lhe escalar as bases de dados para cima ou para baixo de forma dinâmica da mesma forma que em Azure, com base na capacidade disponível da sua infraestrutura. Esta capacidade pode satisfazer cenários de explosão que têm necessidades voláteis, incluindo cenários que requerem ingerir e consultar dados em tempo real, em qualquer escala, com tempo de resposta sub-segundo. Além disso, também pode escalar instâncias de base de dados usando a opção única de implementação de hiperescala da Base de Dados Azure para a Hiperescala PostgreSQL. Esta capacidade dá aos dados um aumento adicional na otimização da capacidade, utilizando *leituras* e escritos únicos em escala.

## <a name="self-service-provisioning"></a>Prestação de self-service

O Azure Arc também fornece outros benefícios em nuvem, como a rápida implantação e automação em escala. Graças à orquestração baseada em Kubernetes, pode implementar uma base de dados em segundos utilizando ferramentas GUI ou CLI.

## <a name="unified-management"></a>Gestão unificada

Utilizando ferramentas familiares como o portal Azure, Azure Data Studio, e o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] , pode agora obter uma visão unificada de todos os seus ativos de dados implantados com Azure Arc. Você é capaz não só de ver e gerir uma variedade de bases de dados relacionais em todo o seu ambiente e Azure, mas também obter registos e telemetria de Kubernetes APIs para analisar a capacidade e saúde subjacentes à infraestrutura. Além de ter a análise de registos localizada e a monitorização do desempenho, pode agora aproveitar o Azure Monitor para obter informações operacionais abrangentes em toda a sua propriedade.

## <a name="disconnected-scenario-support"></a>Suporte de cenário desligado

Muitos dos serviços, tais como fornecimento de self-service, backups automatizados/restauro, e monitorização podem ser executados localmente na sua infraestrutura com ou sem ligação direta ao Azure. A ligação direta ao Azure abre opções adicionais de integração com outros serviços Azure, como o Azure Monitor e a capacidade de usar o portal Azure e APIs do Azure Resource Manager de qualquer parte do mundo para gerir os seus serviços de dados habilitados a Azure Arc.

## <a name="next-steps"></a>Passos seguintes

> **Só quer experimentar as coisas?**  
> Inicie-se rapidamente com [o Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) no Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou num Azure VM.

[Instale as ferramentas do cliente](install-client-tools.md)

[Criar o controlador de dados Azure Arc](create-data-controller.md) (requer a instalação das ferramentas do cliente primeiro)

[Criar um exemplo gerido pelo Azure SQL em Azure Arc](create-sql-managed-instance.md) (requer a criação de um controlador de dados Azure Arc primeiro)

[Criar uma base de dados Azure para o grupo de servidores de hiperescala PostgreSQL em Azure Arc](create-postgresql-hyperscale-server-group.md) (requer a criação de um controlador de dados Azure Arc primeiro)
