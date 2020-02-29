---
title: Tutorial - DevOps Integrados para IaaS e PaaS em Azure
description: Neste tutorial, aprende-se a configurar a integração contínua (CI) e a implantação contínua (CD) de uma aplicação para Os VMs Azure utilizando pipelines Azure.
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
ms.openlocfilehash: 5707a99b329915b35131fe793b0dfabd02348677
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912533"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Tutorial: DevOps integrados para IaaS e PaaS em Azure

Com soluções de ponta a ponta no Azure, as equipas podem implementar práticas de DevOps em cada uma das fases do ciclo de vida da aplicação: planear, desenvolver, entregar e operar. 

Abaixo estão alguns dos Serviços Azure que simplificam as cargas de trabalho em nuvem e podem ser combinados para permitir cenários incrivelmente poderosos.
Estas tecnologias, combinadas com pessoas e processos, permitem às equipas fornecer continuamente valor aos clientes. 

- Azure: https://portal.azure.com – Portal para a construção de cargas de trabalho em nuvem. Gerir e monitorizar tudo, desde aplicações web simples até aplicações complexas em nuvem 
- Azure DevOps: https://dev.azure.com – Planeie melhor, colabore melhor e navere mais rápido com um conjunto de serviços modernos de dev 
- Estúdio Azure Machine Learning: https://ml.azure.com - Dados de preparação, treina e implementa modelos de aprendizagem automática 
 

O Azure DevOps é um serviço Azure incorporado que automatiza cada parte do processo DevOps com integração contínua e entrega contínua para qualquer recurso Azure.
Quer a sua aplicação utilize máquinas virtuais, aplicações web, Kubernetes ou qualquer outro recurso, pode implementar, infraestrutura como código, integração contínua, testes contínuos, entrega contínua e monitorização contínua com Azure e Azure DevOps.  
![](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) AzDevOps_portalView 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - Configure CI/CD 
A Azure Pipelines fornece um conjunto completo e completo de ferramentas de automatização CI/CD para implementações em máquinas virtuais. Pode configurar um gasoduto de entrega contínuo para um Azure VM diretamente do portal Azure. Este documento contém os passos associados à criação de um gasoduto CI/CD para implantações multi-máquinas a partir do portal Azure. Configure CI/CD em máquinas virtuais.

As máquinas virtuais podem ser adicionadas como alvos de um grupo de [implantação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) e podem ser direcionadas para atualizações de rolos multi-máquinas. As opiniões do histórico de implantação dentro dos grupos de implantação fornecem rastreabilidade da VM para o oleoduto e, em seguida, para o compromisso. 
 
**Atualizações em rolo**: Uma implementação rolante substitui as instâncias da versão anterior de uma aplicação por instâncias da nova versão da aplicação num conjunto fixo de máquinas (rolling set) em cada iteração. Vamos ver como pode configurar uma atualização rolante para máquinas virtuais.  
Pode configurar atualizações de rolamento para as suas "**máquinas virtuais**" dentro do portal Azure utilizando a opção de entrega contínua. 

Aqui está a passagem passo a passo. 
1. Inscreva-se no seu portal Azure e navegue para uma máquina virtual. 
2. No painel esquerdo VM, navegue para o menu de **entrega contínua** . Em seguida, clique em **Configurar**. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. No painel de configuração, clique em "Azure DevOps Organization" para selecionar uma conta existente ou criar uma. Em seguida, selecione o projeto sob o qual pretende configurar o gasoduto.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. Um grupo de implantação é um conjunto lógico de máquinas-alvo de implantação que representam os ambientes físicos; por exemplo, "Dev", "Test", "UAT" e "Production". Pode criar um novo grupo de implementação ou selecionar um grupo de implementação existente. Pode marcar opcionalmente a máquina com o papel. Por exemplo, 'web', 'db' etc.  
5. Clique **ok** no diálogo para configurar o gasoduto de entrega contínua. 
6. Uma vez feito, terá um gasoduto de entrega contínuo configurado para ser implantado na máquina virtual.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. Verá que a implantação para a máquina virtual está em andamento. Pode clicar no link para navegar até ao oleoduto. Clique no **Lançamento-1** para visualizar a implementação. Ou pode clicar na **Edição** para modificar a definição do pipeline de lançamento. 
8. Se tiver vários VMs para configurar, repita os passos 2-5, para que outros VMs sejam adicionados ao grupo de implementação. 
9. Uma vez feito, clique na definição do pipeline, navegue para a organização Azure DevOps e clique no pipeline de lançamento **edit.** 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Clique no **link 1 trabalho, 1 tarefa** na fase **de dev.** Clique na fase **de implantação.**  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. A partir do painel de configuração à direita, pode ver que, por padrão, o gasoduto está configurado para fazer uma atualização rolante para todos os alvos em paralelo. Pode configurar as implementações para acontecer em termos de percentagem, utilizando o slider.  
  
  
**Canário** reduz o risco ao lançar lentamente a mudança para um pequeno subconjunto de utilizadores. À medida que ganha mais confiança na nova versão, pode começar a lançá-la para mais servidores na sua infraestrutura e encaminhar mais utilizadores para ela. Pode configurar as implementações canárias para as suas "**máquinas virtuais**" com o portal Azure utilizando a opção de entrega contínua. Aqui está a passagem passo a passo. 
1. Inscreva-se no seu portal Azure e navegue para uma máquina virtual 
2. Siga os passos 2-5 na secção anterior para adicionar vários VMs ao grupo de implantação. 
3. Adicione uma etiqueta personalizada aos VMs que devem fazer parte das implementações canárias. Por exemplo, "canário".
4. Assim que o gasoduto estiver configurado para os VMs, clique no oleoduto, lance a organização Azure DevOps, **edite** o pipeline e navegue até ao estágio **de dev.** Adicione a etiqueta ao filtro "canário". 
5. Adicione outra fase de grupo de implementação, configure a fase com as etiquetas para direcionar os VMs restantes no grupo de implementação.  
6. Opcionalmente, configure um passo de validação manual que possa promover/rejeitar as implementações canárias. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**Blue-Green** reduz o tempo de paragem de implantação por ter um ambiente de espera idêntico. A qualquer momento, um dos ambientes está ao vivo. Enquanto se prepara para um novo lançamento, faz a fase final de testes no ambiente verde. Uma vez que o software esteja a funcionar em ambiente verde, mude o tráfego para que todos os pedidos de entrada vão para o ambiente verde - o ambiente azul está agora inativo.
Pode configurar as implementações Blue-Green para as suas "**máquinas virtuais**" a partir do portal Azure utilizando a opção de entrega contínua. 

Aqui está a passagem passo a passo. 

1. Inscreva-se no seu portal Azure e navegue para uma Máquina Virtual 
2. Siga os passos 2-5 na secção **de atualizações Rolling** para adicionar vários VMs ao grupo de implementação. Adicione uma etiqueta personalizada aos VMs que devem fazer parte de implementações verde-azuladas. Por exemplo, "azul" ou "verde" para os VMs que são para o papel de stand-by. 
3. Uma vez configurado o gasoduto para os VMs, clique no oleoduto, lance a organização Azure DevOps, **Edite** o pipeline, navegue para a fase **de dev.** Adicione a etiqueta ao filtro "verde". 
4. Adicione uma fase sem agente, configure a fase com passo de validação manual e um passo de api invocado-REST para trocar as etiquetas. 
   ![](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png) AzDevOps_BlueGreen
 
 
## <a name="azure-devops-project"></a>Projeto Azure DevOps 
Começa com o Azure mais facilmente do que nunca.
 
Com a DevOps Projects, comece a executar a sua aplicação em qualquer serviço Azure em apenas três etapas: selecione um idioma de aplicação, um tempo de execução e um serviço Azure.
 
[Saiba mais](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Recursos adicionais 
- [Implementar para máquinas virtuais Azure usando o projeto DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implemente a implementação contínua da sua app para um conjunto de escala de máquina virtual Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
