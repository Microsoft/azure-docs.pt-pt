---
title: Tutorial - Configure implementações canárias para Máquinas Virtuais Azure Linux
description: Neste tutorial você aprenderá como configurar o oleoduto de implantação contínua (CD) que atualiza um grupo de máquinas virtuais Azure usando a estratégia de implementação azul-verde
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
ms.openlocfilehash: b1a57245434bb188ffaab56a8891b4b0ee27f044
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120493"
---
# <a name="tutorial---configure-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial - Configure estratégia de implantação Blue-Green para máquinas virtuais Azure Linux


## <a name="iaas---configure-cicd"></a>IaaS - Configure CI/CD 
A Azure Pipelines fornece um conjunto completo e completo de ferramentas de automatização CI/CD para implementações em máquinas virtuais. Pode configurar um gasoduto de entrega contínuo para um Azure VM diretamente do portal Azure. Este documento contém os passos associados à criação de um oleoduto CI/CD que utiliza a estratégia azul-verde para implantações multi-máquinas. Você também pode olhar para outras estratégias como [rolar](https://aka.ms/AA7jlh8) e [canário](https://aka.ms/AA7jdrz), que são suportados fora da caixa do portal Azure. 

 
 **Configure CI/CD em máquinas virtuais**

As máquinas virtuais podem ser adicionadas como alvos de um grupo de [implementação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) e podem ser direcionadas para atualizações multi-máquinas. Uma vez implantado, o Histórico de Implementação de **visualização** dentro de um grupo de implantação fornece rastreabilidade da VM para o oleoduto e, em seguida, para o compromisso. 
 
  
**Destacamentos Blue-Green**: Uma implantação Blue-Green reduz o tempo de inatividade por ter um ambiente de espera idêntico. A qualquer momento, um dos ambientes está ao vivo. Enquanto se prepara para um novo lançamento, completa a fase final de testes no ambiente verde. Uma vez que o software esteja a funcionar em ambiente verde, mude o tráfego para que todos os pedidos de entrada vão para o ambiente verde - o ambiente azul está agora inativo.
Pode configurar as implementações Blue-Green para as suas "**máquinas virtuais**" a partir do portal Azure utilizando a opção de entrega contínua. 

Aqui está a passagem passo a passo.

1. Inscreva-se no seu portal Azure e navegue para uma máquina virtual 
2. No painel esquerdo VM, vá **entrega contínua.** Em seguida, clique em **Configurar**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. No painel de configuração, clique na **Organização Azure DevOps** para selecionar uma conta existente ou criar uma. Em seguida, selecione o projeto sob o qual pretende configurar o gasoduto.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Um grupo de implantação é um conjunto lógico de máquinas-alvo de implantação que representam os ambientes físicos; por exemplo, "Dev", "Test", "UAT" e "Production". Pode criar um novo grupo de implementação ou selecionar um grupo de implementação existente. 
5. Selecione o pipeline de construção que publica o pacote a ser implantado na máquina virtual. Note que o pacote publicado deve ter um _deploy.sh_ script `deployscripts` de implementação _deploy.ps1_ ou deploy.sh na pasta na raiz do pacote. Este script de implantação será executado pelo oleoduto Azure DevOps no tempo de execução.
6. Selecione a estratégia de implementação à sua escolha. Selecione **Blue-Green**.
7. Adicione uma etiqueta "azul" ou "verde" aos VMs que devem fazer parte das implementações Blue-Green. Se o VM é para um papel de standby, então você deve marcá-lo como "verde", caso contrário, marque-o como "azul".
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

8. Clique **em OK** para configurar o gasoduto de entrega contínua. Terá agora um gasoduto de entrega contínuo configurado para ser implantado na máquina virtual.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)


9. Clique no oleoduto de lançamento **editar** em Azure DevOps para ver a configuração do pipeline. O gasoduto é composto por três fases. A primeira fase é uma fase de grupo de implantação e implanta-se para VMs que são marcados como _verdes_ (VMs de espera). A segunda fase interrompe o gasoduto e aguarda a intervenção manual para retomar a execução. Uma vez que um utilizador esteja convencido de que a implantação é estável, pode agora redirecionar o tráfego para VMs _verdes_ e retomar a execução do gasoduto que, em seguida, trocará etiquetas _azuis_ e _verdes_ nos VMs. Isto garante que os VMs que têm versão de aplicação mais antiga são marcados como _verdes_ e são implantados na próxima execução do gasoduto.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)


10. A tarefa execute script de execução executará por padrão `deployscripts` executar o script de _implementação deploy.ps1_ ou _deploy.sh_ na pasta no diretório raiz do pacote publicado. Certifique-se de que o pipeline de construção selecionado publica isto na pasta raiz do pacote.
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Outras estratégias de implantação
- [Configure estratégia de implantação rolante](https://aka.ms/AA7jlh8)
- [Configure estratégia de implantação canária](https://aka.ms/AA7jdrz)

## <a name="azure-devops-project"></a>Projeto Azure DevOps 
Começa com o Azure mais facilmente do que nunca.
 
Com a DevOps Projects, comece a executar a sua aplicação em qualquer serviço Azure em apenas três etapas: selecione um idioma de aplicação, um tempo de execução e um serviço Azure.
 
[Saiba mais](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Recursos adicionais 
- [Implementar para máquinas virtuais Azure usando o projeto DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implemente a implementação contínua da sua app para um conjunto de escala de máquina virtual Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
