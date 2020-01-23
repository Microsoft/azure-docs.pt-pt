---
title: Tutorial – DevOps integradas para IaaS e PaaS no Azure
description: Neste tutorial, você aprende a configurar a integração contínua (CI) e a implantação contínua (CD) de um aplicativo para VMs do Azure usando o Azure Pipelines.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: eba793a761bd9f96b1a4ec5d4730f08187a758ef
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515344"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Tutorial: DevOps integrado para IaaS e PaaS no Azure

Com soluções de ponta a ponta no Azure, as equipes podem implementar práticas de DevOps em cada uma das fases do ciclo de vida do aplicativo: planejar, desenvolver, entregar e operar. 

Abaixo estão alguns dos serviços do Azure que simplificam as cargas de trabalho de nuvem e podem ser combinados para permitir cenários incrivelmente poderosos.
Essas tecnologias, combinadas com pessoas e processos, permitem às equipes fornecer valor continuamente aos clientes. 

- Azure: https://portal.azure.com – portal para a criação de cargas de trabalho de nuvem. Gerencie e monitore tudo, desde aplicativos Web simples a aplicativos de nuvem complexos 
- Azure DevOps: https://dev.azure.com – plano inteligente, colaborar melhor e enviar mais rapidamente com um conjunto de serviços de desenvolvimento modernos 
- Azure Machine Learning Studio: https://ml.azure.com -preparar dados, treinar e implantar modelos de aprendizado de máquina 
 

O Azure DevOps é um serviço interno do Azure que automatiza cada parte do processo DevOps com integração contínua e entrega contínua para qualquer recurso do Azure.
Se seu aplicativo usa máquinas virtuais, aplicativos Web, kubernetes ou qualquer outro recurso, você pode implementar, infraestrutura como código, integração contínua, teste contínuo, entrega contínua e monitoramento contínuo com o Azure e o Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS-configurar CI/CD 
O Azure Pipelines fornece um conjunto completo de ferramentas de automação de CI/CD para implantações em máquinas virtuais. Você pode configurar um pipeline de entrega contínua para uma VM do Azure diretamente do portal do Azure. Este documento contém as etapas associadas à configuração de um pipeline de CI/CD para implantações de vários computadores de portal do Azure. Configure o CI/CD em máquinas virtuais.

As máquinas virtuais podem ser adicionadas como destinos a um [grupo de implantação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) e podem ser direcionadas para atualizações sem interrupção de vários computadores. As exibições do histórico de implantação nos grupos de implantação fornecem a rastreabilidade da VM para o pipeline e, em seguida, para a confirmação. 
 
**Atualizações sem interrupção**: uma implantação sem interrupção substitui as instâncias da versão anterior de um aplicativo por instâncias da nova versão do aplicativo em um conjunto fixo de computadores (conjunto sem interrupção) em cada iteração. Vamos analisar como você pode configurar uma atualização sem interrupção para máquinas virtuais.  
Você pode configurar as atualizações sem interrupção em suas "**máquinas virtuais**" dentro do portal do Azure usando a opção de entrega contínua. 

Veja a seguir o passo a passo. 
1. Entre no seu portal do Azure e navegue até uma máquina virtual. 
2. No painel da VM à esquerda, navegue até o menu de de **entrega contínua** . Em seguida, clique em **Configurar**. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. No painel de configuração, clique em "Azure DevOps Organization" para selecionar uma conta existente ou criar uma. Em seguida, selecione o projeto no qual você deseja configurar o pipeline.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. Um grupo de implantação é um conjunto lógico de computadores de destino de implantação que representam os ambientes físicos; por exemplo, "dev", "Test", "UAT" e "Production". Você pode criar um novo grupo de implantação ou selecionar um grupo de implantação existente. Opcionalmente, você pode marcar a máquina com a função. Por exemplo, ' Web ', ' DB ' etc.  
5. Clique em **OK** na caixa de diálogo para configurar o pipeline de entrega contínua. 
6. Depois de concluído, você terá um pipeline de entrega contínua configurado para implantar na máquina virtual.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. Você verá que a implantação na máquina virtual está em andamento. Você pode clicar no link para navegar até o pipeline. Clique em **Release-1** para exibir a implantação. Ou você pode clicar em **Editar** para modificar a definição do pipeline de liberação. 
8. Se você tiver várias VMs a serem configuradas, repita as etapas 2-5 para que outras VMs sejam adicionadas ao grupo de implantação. 
9. Depois de terminar, clique na definição de pipeline, navegue até a organização DevOps do Azure e clique em **Editar** pipeline de liberação. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Clique no trabalho do link **1, uma tarefa** no estágio de **desenvolvimento** . Clique na fase **implantar** .  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. No painel de configuração à direita, você pode ver que, por padrão, o pipeline está configurado para fazer uma atualização sem interrupção para todos os destinos em paralelo. Você pode configurar as implantações para acontecer uma de cada vez ou em termos de porcentagem usando o controle deslizante.  
  
  
O **canário** reduz o risco ao distribuir lentamente a alteração para um pequeno subconjunto de usuários. Ao obter mais confiança na nova versão, você pode começar a liberá-la para mais servidores em sua infraestrutura e para rotear mais usuários para ela. Você pode configurar implantações do canário para suas "**máquinas virtuais**" com o portal do Azure usando a opção de entrega contínua. Veja a seguir o passo a passo. 
1. Entre no seu portal do Azure e navegue até uma máquina virtual 
2. Siga as etapas 2-5 na seção anterior para adicionar várias VMs ao grupo de implantação. 
3. Adicione uma marca personalizada às VMs que devem fazer parte das implantações do canário. Por exemplo, "canário".
4. Depois que o pipeline estiver configurado para as VMs, clique no pipeline, inicie a organização do Azure DevOps, **edite** o pipeline e navegue até o estágio de **desenvolvimento** . Adicione a marca ao filtro "canário". 
5. Adicionar outra fase do grupo de implantação, configure a fase com as marcas para direcionar as VMs restantes no grupo de implantação.  
6. Opcionalmente, configure uma etapa de validação manual que possa promover/rejeitar as implantações de canário. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

O **verde-azulado** reduz o tempo de inatividade da implantação ao ter um ambiente de espera idêntico. A qualquer momento, um dos ambientes é dinâmico. Ao se preparar para uma nova versão, você faz o estágio final do teste no ambiente verde. Depois que o software estiver funcionando no ambiente verde, mude o tráfego para que todas as solicitações de entrada vá para o ambiente verde – o ambiente azul agora está ocioso.
Você pode configurar implantações em vermelho-verde em suas "**máquinas virtuais**" no portal do Azure usando a opção de entrega contínua. 

Veja a seguir o passo a passo. 

1. Entre no seu portal do Azure e navegue até uma máquina virtual 
2. Siga as etapas 2-5 na seção **atualizações sem interrupção** para adicionar várias VMs ao grupo de implantação. Adicione uma marca personalizada às VMs que devem fazer parte de implantações azuis-verdes. Por exemplo, "azul" ou "verde" para as VMs que são para a função de espera. 
3. Depois que o pipeline estiver configurado para as VMs, clique no pipeline, inicie a organização do Azure DevOps, **edite** o pipeline e navegue até o estágio de **desenvolvimento** . Adicionar marca ao filtro "verde". 
4. Adicione uma fase sem agente, configure a fase com a etapa de validação manual e uma etapa invocar API REST para trocar as marcas. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Projeto DevOps do Azure 
Comece a usar o Azure com mais facilidade do que nunca.
 
Com DevOps Projects, comece a executar seu aplicativo em qualquer serviço do Azure em apenas três etapas: selecione um idioma de aplicativo, um tempo de execução e um serviço do Azure.
 
[Saiba mais](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Recursos adicionais 
- [Implantar em máquinas virtuais do Azure usando o projeto DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementar a implantação contínua de seu aplicativo em um conjunto de dimensionamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
