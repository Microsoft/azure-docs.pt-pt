---
title: Criar um espaço de trabalho Log Analytics no portal do Azure | Microsoft Docs
description: Saiba como criar um espaço de trabalho Log Analytics para habilitar soluções de gerenciamento e coleta de dados de seus ambientes de nuvem e locais no portal do Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.openlocfilehash: 6dd4486d52cc5c2fb568241ebaefad454e50cb2c
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894901"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Criar um espaço de trabalho Log Analytics no portal do Azure
Use o menu **log Analytics espaços de trabalho** para criar um espaço de trabalho do log Analytics usando o portal do Azure. Um espaço de trabalho Log Analytics é um ambiente exclusivo para dados de Azure Monitor log. Cada espaço de trabalho tem seu próprio repositório de dados e configuração, e as fontes de dados e soluções são configuradas para armazenar seus dados em um espaço de trabalho específico. Você precisará de um espaço de trabalho Log Analytics se pretender coletar dados das seguintes fontes:

* Recursos do Azure em sua assinatura
* Computadores locais monitorados pelo System Center Operations Manager
* Coleções de dispositivos de System Center Configuration Manager 
* Diagnóstico ou dados de log do armazenamento do Azure

Para outras fontes, como VMs do Azure e VMs do Windows ou Linux em seu ambiente, consulte os seguintes tópicos:

*  [Coletar dados de máquinas virtuais do Azure](../learn/quick-collect-azurevm.md) 
*  [Coletar dados de um computador Linux híbrido](../learn/quick-collect-linux-computer.md)
*  [Coletar dados de um computador Windows híbrido](quick-collect-windows-computer.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar áreas de trabalho
1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log Analytics espaços de trabalho**.

    ![Portal do Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Clique em **Adicionar**e selecione opções para os seguintes itens:

   * Indique um nome para a nova **Área de trabalho do Log Analytics**, como *DefaultLAWorkspace*. Esse nome deve ser globalmente exclusivo em todas as assinaturas de Azure Monitor.
   * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   * Para **grupo de recursos**, escolha usar um grupo de recursos existente já configurado ou criar um novo.  
   * Selecione um **local**disponível.  Para obter mais informações, consulte quais [regiões log Analytics está disponível no](https://azure.microsoft.com/regions/services/) e procure por Azure monitor no campo **Pesquisar um produto** .  
   * Se estiver a criar uma área de trabalho numa subscrição nova criada depois de 2 de abril de 2018, a mesma utilizará automaticamente o plano de preços *Por GB* e a opção para selecionar um escalão de preços não estará disponível.  Se você estiver criando um espaço de trabalho para uma assinatura existente criada antes de 2 de abril ou para a assinatura que estava vinculada a um registro de Enterprise Agreement (EA) existente, selecione seu tipo de preço preferido.  Para obter mais informações sobre as camadas específicas, consulte [log Analytics detalhes de preços](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Criar Log Analytics folha de recursos](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Depois de dar as informações necessárias no painel **Área de Trabalho do Log Analytics**, clique em **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="next-steps"></a>Passos seguintes
Agora que você tem um espaço de trabalho disponível, é possível configurar a coleta de telemetria de monitoramento, executar pesquisas de log para analisar esses dados e adicionar uma solução de gerenciamento para fornecer dados adicionais e informações analíticas. 

* Para habilitar a coleta de dados de recursos do Azure com o Diagnóstico do Azure ou o armazenamento do Azure, consulte [coletar logs e métricas de serviço do Azure para uso no log Analytics](../platform/collect-azure-metrics-logs.md).  
* [Adicione System Center Operations Manager como uma fonte de dados](../platform/om-agents.md) para coletar dados de agentes que relatam seu grupo de gerenciamento de Operations Manager e armazená-los em seu espaço de trabalho do log Analytics. 
* Conecte [Configuration Manager](../platform/collect-sccm.md) para importar computadores que são membros de coleções na hierarquia.  
* Examine as [soluções de monitoramento](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução do seu espaço de trabalho.
