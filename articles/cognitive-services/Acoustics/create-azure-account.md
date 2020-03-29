---
title: Configuração da conta de lote de lote de projeto acoustics azure
titlesuffix: Azure Cognitive Services
description: Este how-to descreve a criação de uma conta De Lote Azure para uso com a Unidade da Acústica do Projeto e integrações de motores irreais.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68855086"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Configuração da conta de lote de lote de projeto acoustics azure
Este how-to descreve a criação de uma conta De Lote Azure para uso com a Unidade da Acústica do Projeto e integrações de motores irreais.

## <a name="get-an-azure-subscription"></a>Obter uma subscrição do Azure
É necessária uma [subscrição Azure](https://azure.microsoft.com/free/) antes de configurar as contas de Lote e Armazenamento. Se se inscrever pela primeira vez, o Azure fornece alguns recursos gratuitos limitados e crédito de 200 dólares.

## <a name="create-azure-batch-and-storage-accounts"></a>Criar contas de lote azure e armazenamento
Em seguida, siga [estas instruções](https://docs.microsoft.com/azure/batch/batch-account-create-portal) para configurar o seu Lote Azure e contas de Armazenamento Azure associadas.

Escolha opções predefinidas tanto para as contas de Lote como de Armazenamento:
  
  ![Screenshot das novas opções de contas do Lote Azure mostrando definições padrão](media/new-batch-account-create.png)

  ![Screenshot do Azure Storage novas opções de contas que mostram definições padrão](media/batch-storage-account-create.png)

Leva alguns minutos para o Azure implementar as contas. Procure uma notificação de conclusão no canto superior direito no portal.
  
  ![Screenshot das contas Azure implementadanotificada](media/batch-accounts-deploy-notification.png)

As suas contas devem agora estar visíveis no seu painel de instrumentos.
  
  ![Screenshot do painel do portal Azure mostrando uma conta de Lote e Armazenamento](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurar a acústica assar UI com credenciais Azure
Clique no link da conta 'Lote' no painel de instrumentos e, em seguida, clique no link **Keys** na página da conta 'Lote' para aceder às suas credenciais.
  
  ![Screenshot da conta Do Lote Azure com link para a página Keys em destaque](media/batch-access-keys.png)

  ![Screenshot da página de chaves da conta do Lote Azure com chaves de acesso](media/batch-keys-info.png)

Clique no link **da Conta de Armazenamento** na página para aceder às credenciais da sua conta de Armazenamento Azure.
  
  ![Screenshot da página de chaves da conta azure armazenamento com chaves de acesso](media/storage-keys-info.png)

Introduza estas credenciais no [plugin](unity-baking.md) de cozedura Unidade ou [plugin de cozedura unreal](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Tipos de nó e apoio à região
O Project Acoustics requer computação da série Fsv2 e H otimizada em nós Azure VM que podem não ser suportados em todas as regiões do Azure. Por favor, verifique [esta tabela](https://azure.microsoft.com/global-infrastructure/services) para se certificar de que está a escolher o local certo para a sua conta Batch.
![Screenshot mostrando máquinas virtuais azure por região](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Atualizar a sua quota
As contas do Lote Azure são provisionadas na criação de contas com um limite de 20 núcleos de computação. Podemos querer aumentar este limite para tempos de cozedura mais rápidos, porque você pode paralelor a sua carga de trabalho acústica em muitos nós, até o número de pontos de sonda na sua cena. Pode solicitar um aumento de quota clicando no link **quota** na página do portal do Lote Azure e, em seguida, clicando no **Request Quota Increase**:

![Screenshot da página de Quota Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Passos seguintes
* Integre o projeto De sinuoso do Projeto Acústica no seu projeto [Unidade](unity-integration.md) ou [Irreal](unreal-integration.md)

