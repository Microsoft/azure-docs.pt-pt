---
title: Tutorial - Configure rolling implementações para máquinas virtuais Azure Linux
description: Neste tutorial você aprenderá como configurar o pipeline de implantação contínua (CD) que atualiza incrementalmente um grupo de Máquinas Virtuais Azure Linux usando a estratégia de implementação rolling
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: 75888b1ebbda33891296fe0b54c5d204955e32a3
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113493"
---
# <a name="tutorial---configure-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial - Configure estratégia de implantação de rolling para máquinas virtuais Azure Linux

O Azure DevOps é um serviço Azure incorporado que automatiza cada parte do processo DevOps com integração contínua e entrega contínua para qualquer recurso Azure.
Quer a sua aplicação utilize máquinas virtuais, aplicações web, Kubernetes ou qualquer outro recurso, pode implementar infraestruturas como código, integração contínua, testes contínuos, entrega contínua e monitorização contínua com Azure e Azure DevOps.  

![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 


## <a name="iaas---configure-cicd"></a>IaaS - Configure CI/CD 
A Azure Pipelines fornece um conjunto completo e completo de ferramentas de automatização CI/CD para implementações em máquinas virtuais. Pode configurar um gasoduto de entrega contínuo para um Azure VM diretamente do portal Azure. Este documento contém os passos associados à criação de um gasoduto CI/CD para a implantação de várias máquinas a partir do portal Azure. Você também pode olhar para outras estratégias como [canário](https://aka.ms/AA7jdrz) e [azul-verde](https://aka.ms/AA83fwu), que são suportadas fora da caixa do portal Azure. 


**Configure CI/CD em máquinas virtuais**

As máquinas virtuais podem ser adicionadas como alvos de um grupo de [implementação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) e podem ser direcionadas para a atualização multi-máquina. Uma vez implantado, o Histórico de **Implantação** dentro de um grupo de implantação fornece rastreabilidade da VM para o oleoduto e, em seguida, para o compromisso. 
 

**Rolling Deployments**: Uma implantação rolante substitui as instâncias da versão anterior de uma aplicação por instâncias da nova versão da aplicação num conjunto fixo de máquinas (rolling set) em cada iteração. Vamos ver como pode configurar uma atualização rolante para máquinas virtuais.  
Pode configurar atualizações de rolamento para as suas "**máquinas virtuais**" dentro do portal Azure utilizando a opção de entrega contínua. 

Aqui está a passagem passo a passo. 
1. Inscreva-se no seu portal Azure e navegue para uma máquina virtual. 
2. No painel esquerdo VM, navegue para o menu de **entrega** contínua. Em seguida, clique em **Configurar**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. No painel de configuração, clique em "Azure DevOps Organization" para selecionar uma conta existente ou criar uma. Em seguida, selecione o projeto sob o qual pretende configurar o gasoduto.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Um grupo de implantação é um conjunto lógico de máquinas-alvo de implantação que representam os ambientes físicos; por exemplo, "Dev", "Test", "UAT" e "Production". Pode criar um novo grupo de implementação ou selecionar um grupo de implementação existente. 
5. Selecione o pipeline de construção que publica o pacote a ser implantado na máquina virtual. Note que o pacote publicado deve ter um _deploy.sh_ script `deployscripts` de implementação _deploy.ps1_ ou deploy.sh na pasta na raiz do pacote. Este script de implantação será executado pelo oleoduto Azure DevOps no tempo de execução.
6. Selecione a estratégia de implementação à sua escolha. Neste caso, permite selecionar 'Rolling'.
7. Opcionalmente, pode marcar a máquina com o papel. Por exemplo, 'web', 'db' etc. Isto ajuda-o a direcionar Os VMs que têm um papel específico apenas.
8. Clique **ok** no diálogo para configurar o gasoduto de entrega contínua. 
9. Uma vez feito, terá um gasoduto de entrega contínuo configurado para ser implantado na máquina virtual.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. Verá que a implantação para a máquina virtual está em andamento. Pode clicar no link para navegar até ao oleoduto. Clique no **Lançamento-1** para visualizar a implementação. Ou pode clicar na **Edição** para modificar a definição do pipeline de lançamento. 
11. Se tiver vários VMs para configurar, repita os passos 2-4 para que outros VMs sejam adicionados ao grupo de implementação. Note que se selecionar um Grupo de Implantação para o qual já existe um pipeline, o VM será adicionado ao grupo de implantação sem criar novos oleodutos. 
12. Uma vez feito, clique na definição do pipeline, navegue para a organização Azure DevOps e clique no pipeline de lançamento **edit.** 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Clique no **link 1 trabalho, 1 tarefa** na fase **de dev.** Clique na fase **de implantação.**
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. A partir do painel de configuração à direita, pode especificar o número de máquinas que pretende implantar paralelamente em cada iteração. Caso pretenda ser implantado em várias máquinas de cada vez, pode especificá-lo em termos de percentagem utilizando o slider.  

15. A tarefa execute script de execução executará por padrão o script de _implementação de implementação.ps1_ ou _deploy.sh_ na pasta 'deployscripts' no diretório raiz do pacote publicado.  
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Outras estratégias de implantação

- [Configure estratégia de implantação canária](https://aka.ms/AA7jdrz)
- [Configure estratégia de implantação azul-verde](https://aka.ms/AA83fwu)

 
## <a name="azure-devops-project"></a>Projeto Azure DevOps 
Começa com o Azure mais facilmente do que nunca.
 
Com a DevOps Projects, comece a executar a sua aplicação em qualquer serviço Azure em apenas três etapas: selecione um idioma de aplicação, um tempo de execução e um serviço Azure.
 
[Saiba mais](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Recursos adicionais 
- [Implementar para máquinas virtuais Azure usando o projeto DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implemente a implementação contínua da sua app para um conjunto de escala de máquina virtual Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
