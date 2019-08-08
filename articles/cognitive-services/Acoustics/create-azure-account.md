---
title: Configuração da conta do lote do Azure acústica do projeto acústicos
titlesuffix: Azure Cognitive Services
description: Este "como" descreve a configuração de uma conta do lote do Azure para uso com o Unity do projeto acústica e integrações do mecanismo inreal.
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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855086"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Configuração da conta do lote do Azure acústica do projeto acústicos
Este "como" descreve a configuração de uma conta do lote do Azure para uso com o Unity do projeto acústica e integrações do mecanismo inreal.

## <a name="get-an-azure-subscription"></a>Obtenha uma subscrição do Azure
Uma [assinatura do Azure](https://azure.microsoft.com/free/) é necessária antes de configurar contas de armazenamento e lote. Se você estiver se inscrevendo pela primeira vez, o Azure fornece alguns recursos gratuitos limitados e o crédito de $200.

## <a name="create-azure-batch-and-storage-accounts"></a>Criar contas de armazenamento e lote do Azure
Em seguida, siga [estas instruções](https://docs.microsoft.com/azure/batch/batch-account-create-portal) para configurar o lote do Azure e as contas de armazenamento do Azure associadas.

Escolha as opções padrão para contas de armazenamento e lote:
  
  ![Captura de tela de opções de novas contas do lote do Azure mostrando as configurações padrão](media/new-batch-account-create.png)

  ![Captura de tela das opções de novas contas do armazenamento do Azure mostrando as configurações padrão](media/batch-storage-account-create.png)

Leva alguns minutos para que o Azure implante as contas. Procure uma notificação de conclusão no canto superior direito no Portal.
  
  ![Captura de tela de notificação implantada de contas do Azure](media/batch-accounts-deploy-notification.png)

Suas contas agora devem estar visíveis no seu painel.
  
  ![Captura de tela do painel de portal do Azure mostrando uma conta de armazenamento e lote](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurar a interface do usuário do disparador acústico com as credenciais do Azure
Clique no link conta do lote no painel e, em seguida, clique no link **chaves** na página conta do lote para acessar suas credenciais.
  
  ![Captura de tela da conta do lote do Azure com a página link para chaves realçada](media/batch-access-keys.png)

  ![Captura de tela da página chaves de conta do lote do Azure com chaves de acesso](media/batch-keys-info.png)

Clique no link da **conta de armazenamento** na página para acessar suas credenciais de conta de armazenamento do Azure.
  
  ![Captura de tela da página chaves de conta de armazenamento do Azure com chaves de acesso](media/storage-keys-info.png)

Insira essas credenciais no [plug-](unity-baking.md) in de torta de Unity ou no [plug-in](unreal-baking.md)de inreal.

## <a name="node-types-and-region-support"></a>Tipos de nó e suporte a região
A acústica do projeto requer nós de VM do Azure otimizados para computação da série Fsv2 e H que podem não ter suporte em todas as regiões do Azure. Verifique [esta tabela](https://azure.microsoft.com/global-infrastructure/services) para garantir que você está selecionando o local certo para sua conta do lote.
![Captura de tela mostrando máquinas virtuais do Azure por região](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Atualizando sua cota
As contas do lote do Azure são provisionadas na criação da conta com um limite de 20 núcleos de computação. Talvez queiramos aumentar esse limite para intervalos mais rápidos, pois você pode paralelizar sua carga de trabalho acústica em vários nós, até o número de pontos de investigação em sua cena. Você pode solicitar um aumento de cota clicando no link de **cota** na página do portal do lote do Azure e clicando no **aumento da cota de solicitação**:

![Captura de tela da página de cota do Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Passos Seguintes
* Integrar o plug-in acústica do projeto ao seu [Unity](unity-integration.md) ou projeto [inreal](unreal-integration.md)

