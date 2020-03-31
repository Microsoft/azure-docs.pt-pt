---
title: Como migrar a Gestão da API Azure através de regiões
description: Aprenda a migrar uma instância de Gestão API de uma região para outra.
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
ms.openlocfilehash: 3294a7b2112e9527041ef343f4452aedb7a2a272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073469"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Como migrar a Gestão da API Azure através de regiões
Para migrar instâncias de Gestão API de uma região de Azure para outra, você pode usar a [função de backup e restaurar.](api-management-howto-disaster-recovery-backup-restore.md) Deve escolher o mesmo nível de preços de Gestão API nas regiões-alvo e fonte. 

> [!NOTE]
> A cópia de segurança e restauro não funcionarão enquanto migram entre diferentes tipos de nuvens. Para isso, terá de exportar o recurso [como modelo.](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates) Em seguida, adapte o modelo exportado para a região-alvo de Azure e recrie o recurso. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Opção 1: Utilize um nome de instância de gestão API diferente

1. Na região alvo, crie uma nova instância de Gestão API com o mesmo nível de preços que a fonte da Instância de Gestão aPI. O novo caso deve ter um nome diferente. 
1. Backup existente caso de Gestão aPI para uma conta de armazenamento.
1. Restaurar o backup criado no Passo 2 para a nova instância de Gestão API criada na nova região no Passo 1.
1. Se tiver um domínio personalizado que indique a instância de Gestão API da região de origem, atualize o domínio personalizado CNAME para apontar para a nova instância de Gestão API. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Opção 2: Utilize o mesmo nome de instância de gestão DaPI

> [!NOTE]
> Esta opção resultará em tempo de inatividade durante a migração.

1. Recue a instância de Gestão da API na região fonte para uma conta de armazenamento.
1. Eliminar a Gestão da API na região fonte. 
1. Criar uma nova instância de Gestão de API na região alvo com o mesmo nome que na região de origem.
1. Restaurar o backup criado no Passo 1 para a nova instância de Gestão API na região alvo.  


## <a name="next-steps"></a><a name="next-steps"> </a>Passos seguintes
* Para mais informações sobre a funcionalidade de backup e restauro, consulte [como implementar a recuperação de desastres.](api-management-howto-disaster-recovery-backup-restore.md)
* Para obter informações sobre os recursos azure migratórios, consulte a orientação migratória [transfronteiriça de Azure.](https://github.com/Azure/Azure-Migration-Guidance)