---
title: Limitações do Arco Azure ativadas em Hiperescala Pós-SQL
description: Limitações do Arco Azure ativadas em Hiperescala Pós-SQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b1a56c8acf1789690c01f1c16b7c37a237720e39
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100418240"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Limitações do Arco Azure ativadas em Hiperescala Pós-SQL

Este artigo descreve limitações de Azure Arc ativadas em Hiperescala PósgreSQL. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

- Ponto no tempo a restauração (como restaurar a data e hora específicas) para o mesmo grupo de servidores não é suportado. Ao fazer um ponto no tempo restaurar, deve restaurar um grupo de servidor diferente que implementou antes de restaurar. Depois de restaurar o novo grupo de servidores, pode eliminar o grupo de origem do servidor.
- Restaurar todo o conteúdo de uma cópia de segurança (em vez de restaurar até um ponto específico no tempo) para o mesmo grupo de servidores é suportado para a versão 12 do PostgreSQL. Não é suportado para a versão 11 do PostgreSQL devido a uma limitação do motor PostgreSQL com linhas de tempo. Para restaurar todo o conteúdo de uma cópia de segurança para um grupo de servidores PostgreSQL da versão 11, deve restaurá-lo para um grupo de servidor diferente.


## <a name="databases"></a>Bases de Dados

Não é suportada a hospedagem de mais de uma base de dados num grupo de servidores.


## <a name="security"></a>Segurança

A gestão de utilizadores e funções não é suportada. Por enquanto, continue a utilizar o utilizador padrão de postgres.

## <a name="roles-and-responsibilities"></a>Funções e responsabilidades

As funções e responsabilidades entre a Microsoft e os seus clientes diferem entre os serviços Azure PaaS (Platform As A Service) e o Azure hybrid (como o Azure Arc habilitado a Escala Pós-SQL). 

### <a name="frequently-asked-questions"></a>Perguntas mais frequentes

O quadro abaixo resume as respostas a perguntas frequentes sobre funções de apoio e responsabilidades.

| Pergunta                      | Plataforma Azure Como Serviço (PaaS) | Serviços híbridos Azure Arc |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Quem fornece a infraestrutura?  | Microsoft                          | Cliente                  |
| Quem fornece o software?*       | Microsoft                          | Microsoft                 |
| Quem faz as operações? | Microsoft                          | Cliente                  |
| A Microsoft fornece SLAs?      | Yes                                | No                        |
| Quem está no comando das AEA? | Microsoft                          | Cliente                  |

\* Serviços Azure

__Porque é que a Microsoft não fornece SLAs em serviços híbridos Azure Arc?__ Porque a Microsoft não é proprietária da infraestrutura e não a opera. Os clientes têm.

## <a name="next-steps"></a>Passos seguintes

- **Experimente.** Inicie-se rapidamente com [o Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) no Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou num Azure VM. 

- **Crie o seu próprio.** Siga estes passos para criar no seu próprio cluster Kubernetes: 
   1. [Instale as ferramentas do cliente](install-client-tools.md)
   2. [Criar o controlador de dados Azure Arc](create-data-controller.md)
   3. [Criar uma base de dados de Azure para o grupo de servidores de hiperescala PostgreSQL em Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Ler mais sobre serviços de dados habilitados a Azure Arc](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Leia sobre Azure Arc](https://aka.ms/azurearc)
