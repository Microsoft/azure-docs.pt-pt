---
title: Tutorial - Configure implementações canárias para Máquinas Virtuais Azure Linux
description: Neste tutorial você aprenderá como configurar o oleoduto de implantação contínua (CD) que atualiza um grupo de Máquinas Virtuais Azure Linux usando a estratégia de implementação canária
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
ms.openlocfilehash: b51b4aed85f737e436565ce8ba1ab4a295714734
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120479"
---
# <a name="tutorial---configure-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial - Configure Estratégia de implantação canária para máquinas virtuais Azure Linux


## <a name="iaas---configure-cicd"></a>IaaS - Configure CI/CD 
A Azure Pipelines fornece um conjunto completo e completo de ferramentas de automatização CI/CD para implementações em máquinas virtuais. Pode configurar um gasoduto de entrega contínuo para um Azure VM diretamente do portal Azure. Este documento contém os passos associados à criação de um oleoduto CI/CD que utiliza a estratégia Canária para implantações multi-máquinas. Você também pode olhar para outras estratégias como [rolar](https://aka.ms/AA7jlh8) e [azul-verde](https://aka.ms/AA83fwu), que são suportados fora da caixa do portal Azure. 


**Configure CI/CD em máquinas virtuais**

As máquinas virtuais podem ser adicionadas como alvos de um grupo de [implementação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) e podem ser direcionadas para atualizações multi-máquinas. Uma vez implantado, o Histórico de **Implantação** dentro do seu grupo de implantação fornece rastreabilidade da VM para o oleoduto e, em seguida, para o compromisso. 
 
  
**Implementações canárias**: Uma implementação canária reduz o risco ao lançar lentamente a mudança para um pequeno subconjunto de utilizadores. À medida que ganha mais confiança na nova versão, pode começar a lançá-la para mais servidores na sua infraestrutura e encaminhar mais utilizadores para ela. Pode configurar as implementações canárias para as suas máquinas virtuais com o portal Azure utilizando a opção de entrega contínua. Aqui está a passagem passo a passo. 
1. Inscreva-se no seu portal Azure e navegue para uma máquina virtual 
2. No painel esquerdo VM, navegue para o menu de **entrega** contínua. Clique em **Configurar**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. No painel de configuração, clique na **Organização Azure DevOps** para selecionar uma conta existente ou criar uma. Em seguida, selecione o projeto sob o qual pretende configurar o gasoduto.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Um grupo de implantação é um conjunto lógico de máquinas-alvo de implantação que representam os ambientes físicos; por exemplo, "Dev", "Test", "UAT" e "Production". Pode criar um novo grupo de implementação ou selecionar um grupo de implementação existente. 
5. Selecione o pipeline de construção que publica o pacote a ser implantado na máquina virtual. Note que o pacote publicado deve ter um _deploy.sh_ script `deployscripts` de implementação _deploy.ps1_ ou deploy.sh na pasta na raiz do pacote. Este script de implantação será executado pelo oleoduto Azure DevOps no tempo de execução.
6. Selecione a estratégia de implementação à sua escolha. Selecione **Canário**.
7. Adicione uma etiqueta 'canária' aos VMs que devem fazer parte das implementações canárias e uma etiqueta 'prod' aos VMs que fazem parte das implantações após a implantação do canário em sucesso. As etiquetas ajudam-no a direcionar Os VMs que têm um papel específico apenas.
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

8. Clique **ok** no diálogo para configurar o gasoduto de entrega contínua. Terá agora um gasoduto de entrega contínuo configurado para ser implantado na máquina virtual.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


9. Clique no oleoduto de lançamento **editar** em Azure DevOps para ver a configuração do pipeline. O gasoduto é composto por três fases. A primeira fase é uma fase de grupo de implantação e implanta-se para VMs que são marcados como _canários_. A segunda fase, interrompe o gasoduto e aguarda a intervenção manual para retomar a execução. Uma vez que um utilizador esteja convencido de que a implantação do canário é estável, pode retomar a execução do gasoduto que irá então executar a terceira fase que se implanta para VMs marcados como _prod_. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

10. Antes de retomar a execução do gasoduto, certifique-se de que pelo menos um VM está marcado como _proa_. Na terceira fase do gasoduto, a aplicação será implantada apenas para os VMs que tenham etiqueta _de proa._

11. A tarefa execute script de execução executará por padrão o script de _implementação de implementação.ps1_ ou _deploy.sh_ na pasta 'deployscripts' no diretório raiz do pacote publicado. Certifique-se de que o pipeline de construção selecionado publica isto na pasta raiz do pacote. 
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Outras estratégias de implantação
- [Configure estratégia de implantação rolante](https://aka.ms/AA7jlh8)
- [Configure estratégia de implantação azul-verde](https://aka.ms/AA83fwu)

## <a name="azure-devops-project"></a>Projeto Azure DevOps 
Começa com o Azure mais facilmente do que nunca.
 
Com a DevOps Projects, comece a executar a sua aplicação em qualquer serviço Azure em apenas três etapas: selecione um idioma de aplicação, um tempo de execução e um serviço Azure.
 
[Saiba mais](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Recursos adicionais 
- [Implementar para máquinas virtuais Azure usando o projeto DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implemente a implementação contínua da sua app para um conjunto de escala de máquina virtual Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
