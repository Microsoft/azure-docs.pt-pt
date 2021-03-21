---
title: Como migrar a Azure API Management através das regiões
description: Saiba como migrar um caso de Gestão de API de uma região para outra.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 0eed2328aca78402c5f4691bb9b3d07d4f36472e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86250231"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Como migrar a Azure API Management através das regiões
Para migrar instâncias de Gestão da API de uma região Azure para outra, pode utilizar a [funcionalidade de backup e restauro.](api-management-howto-disaster-recovery-backup-restore.md) Deve escolher o mesmo nível de preços da API Management nas regiões de origem e alvo. 

> [!NOTE]
> A cópia de segurança e a restauração não funcionarão enquanto migram entre diferentes tipos de nuvens. Para isso, terá de exportar o recurso [como modelo.](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates) Em seguida, adapte o modelo exportado para a região de Azure alvo e re-crie o recurso. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Opção 1: Utilize um nome diferente de instância de gestão de API

1. Na região-alvo, crie um novo caso de Gestão de API com o mesmo nível de preços que a fonte API Management. O novo caso deve ter um nome diferente. 
1. Cópia de segurança existente da API Management para uma conta de armazenamento.
1. Restaurar o backup criado no Passo 2 para o novo exemplo de Gestão da API criado na nova região no Passo 1.
1. Se tiver um domínio personalizado que indique a instância de gestão da API da região de origem, atualize o domínio personalizado CNAME para apontar para a nova instância de Gestão da API. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Opção 2: Use o mesmo nome de instância de gestão da API

> [!NOTE]
> Esta opção resultará em tempo de inatividade durante a migração.

1. Apoiar a instância de Gestão da API na região de origem para uma conta de armazenamento.
1. Eliminar a Gestão da API na região origem. 
1. Criar um novo caso de Gestão da API na região alvo com o mesmo nome que o da região nascente.
1. Restaurar o backup criado no Passo 1 para o novo caso de Gestão da API na região alvo.  


## <a name="next-steps"></a><a name="next-steps"> </a>Passos seguintes
* Para obter mais informações sobre a funcionalidade de backup e restauro, consulte [como implementar a recuperação de desastres.](api-management-howto-disaster-recovery-backup-restore.md)
* Para obter informações sobre os recursos migratórios Azure, consulte [a orientação para a migração transnações de Azure.](https://github.com/Azure/Azure-Migration-Guidance)
* [Otimize e poupe nos gastos na nuvem.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
