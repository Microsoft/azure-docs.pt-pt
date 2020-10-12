---
title: Tutorial - Configurar implantações rolantes para máquinas virtuais Azure Linux
description: Neste tutorial, aprende-se a configurar um pipeline de implementação contínua (CD). Este oleoduto atualiza gradualmente um grupo de máquinas virtuais Azure Linux utilizando a estratégia de implementação de rolos.
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
ms.openlocfilehash: 6cc4fdb82e98c30ca33eec58675f2175dfcc2607
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89612783"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial - Configurar a estratégia de implantação rolante para máquinas virtuais Azure Linux

Azure DevOps é um serviço Azure incorporado que automatiza cada parte do processo DevOps para qualquer recurso Azure. Quer a sua aplicação utilize máquinas virtuais, aplicações web, Kubernetes ou qualquer outro recurso, pode implementar infraestruturas como código (IaC), integração contínua, testes contínuos, entrega contínua e monitorização contínua com Azure e Azure DevOps.

![O portal Azure com Azure DevOps selecionados em Serviços](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infraestrutura como serviço (IaaS) - Configure CI/CD

A Azure Pipelines fornece um conjunto totalmente em destaque de ferramentas de automação CI/CD para implementações em máquinas virtuais. Pode configurar um gasoduto de entrega contínua para um Azure VM a partir do portal Azure.

Este artigo mostra como configurar um pipeline CI/CD para implantações multi-metralhadoras rolantes a partir do portal Azure. O portal Azure também apoia outras estratégias como [canário](https://aka.ms/AA7jdrz) e [azul-verde.](https://aka.ms/AA83fwu)

### <a name="configure-cicd-on-virtual-machines"></a>Configure CI/CD em máquinas virtuais

Pode adicionar máquinas virtuais como alvos a um [grupo de implantação](/azure/devops/pipelines/release/deployment-groups). Em seguida, pode direcioná-los para atualizações multi-metralhadoras. Depois de se deslocar para máquinas, veja **o Histórico de Implementação** dentro de um grupo de implantação. Esta vista permite-lhe rastrear desde VM até ao oleoduto e, em seguida, ao compromisso.

### <a name="rolling-deployments"></a>Implantações rolantes

Em cada iteração, uma implantação rolante substitui instâncias da versão anterior de uma aplicação. Substitui-as por instâncias da nova versão num conjunto fixo de máquinas (conjunto de rolos). O seguinte walk-through mostra como configurar uma atualização rolante para máquinas virtuais.

Utilizando a opção de entrega contínua, pode configurar atualizações rolantes para as suas máquinas virtuais dentro do portal Azure. Aqui está o passo-a-passo passo- através:

1. Inscreva-se no portal Azure e navegue para uma máquina virtual.
1. No painel mais à esquerda das definições de VM, selecione **Distribuição Contínua**. Em seguida, **selecione Configure**.

   ![O painel de entrega contínua com o botão Configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. No painel de configuração, selecione **Azure DevOps Organization** para escolher uma conta existente ou criar uma nova. Em seguida, selecione o projeto ao abrigo do qual pretende configurar o gasoduto.  

   ![O painel de entrega contínua](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Um grupo de implantação é um conjunto lógico de máquinas-alvo de implantação que representam os ambientes físicos. Dev, Teste, UAT e Produção são exemplos. Pode criar um novo grupo de implementação ou selecionar um existente.
1. Selecione o pipeline de construção que publica o pacote a ser implantado na máquina virtual. O pacote publicado deve ter um script de implementação nomeado deploy.ps1 ou deploy.sh na pasta de implementação na pasta raiz do pacote. O oleoduto executa este script de implantação.
1. Na **estratégia de implementação**, selecione **Rolling**.
1. Opcionalmente, pode marcar cada máquina com o seu papel. As tags "web" e "db" são exemplos. Estas tags ajudam-no a direcionar apenas os VMs que têm um papel específico.
1. Selecione **OK** para configurar o gasoduto de entrega contínua.
1. Após o acabamento da configuração, tem um gasoduto de entrega contínua configurado para ser implantado na máquina virtual.  

   ![O painel de entrega contínuo mostrando o histórico de implementação](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. Os detalhes de implantação da máquina virtual são apresentados. Pode selecionar o link para ir ao pipeline, **Release-1** para visualizar a implementação ou **editar** para modificar a definição de pipeline de libertação.

1. Se estiver a configurar vários VMs, repita os passos 2 a 4 para que outros VMs adicionem ao grupo de implantação. Se selecionar um grupo de implantação que já tenha uma corrida ao gasoduto, os VMs são apenas adicionados ao grupo de implantação. Não são criados novos oleodutos.
1. Depois de a configuração ser feita, selecione a definição do pipeline, navegue para a organização Azure DevOps e **selecione Editar** para o pipeline de lançamento.

   ![Edição do gasoduto de rolamento](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. Selecione **1 trabalho, 1 tarefa** na fase **dev.** Selecione a fase **de implantação.**

   ![Tarefas de pipeline rolante com a tarefa de implantação selecionada](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. A partir do painel de configuração mais à direita, pode especificar o número de máquinas que pretende implantar paralelamente em cada iteração. Se pretender implantar em várias máquinas de cada vez, pode especificar o número de máquinas em percentagem utilizando o deslizador.  

1. A tarefa 'Executar executar script' por predefinição executa o deploy.ps1 de script de implementação ou deploy.sh. O script está na pasta de implementação na pasta raiz do pacote publicado.

   ![O painel de artefactos mostrando deploy.sh na pasta de implementação](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Outras estratégias de implantação

- [Configurar a estratégia de implantação canária](https://aka.ms/AA7jdrz)
- [Configurar a estratégia de implantação azul-verde](https://aka.ms/AA83fwu)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Pode começar com o Azure facilmente. Com a Azure DevOps Projects, comece a executar a sua aplicação em qualquer serviço Azure em apenas três etapas, selecionando:

- Uma linguagem de aplicação
- Um tempo de execução
- Um serviço Azure
 
[Saiba mais](https://azure.microsoft.com/features/devops-projects/).
 
## <a name="additional-resources"></a>Recursos adicionais

- [Implementar para a Azure máquinas virtuais utilizando projetos Azure DevOps](../../devops-project/azure-devops-project-vms.md)
- [Implemente a implementação contínua da sua app para um conjunto de escala de máquina virtual Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
