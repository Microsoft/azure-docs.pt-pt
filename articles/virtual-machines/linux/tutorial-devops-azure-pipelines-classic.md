---
title: Tutorial - Configure implementações rolantes para máquinas virtuais Azure Linux
description: Neste tutorial, aprende-se a configurar um oleoduto de implantação contínua (CD). Este gasoduto atualiza gradualmente um grupo de máquinas virtuais Azure Linux utilizando a estratégia de implantação de rolos.
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
ms.openlocfilehash: 28f093bc464a45862d3b253d628b7ae03810f81a
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871228"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial - Configure a estratégia de implantação rolante para máquinas virtuais Azure Linux

Azure DevOps é um serviço Azure incorporado que automatiza cada parte do processo DevOps para qualquer recurso Azure. Quer a sua aplicação utilize máquinas virtuais, aplicações web, Kubernetes ou qualquer outro recurso, pode implementar infraestruturas como código (IaaC), integração contínua, testes contínuos, entrega contínua e monitorização contínua com Azure e Azure DevOps.

![O portal Azure com A Azure DevOps selecionado ao abrigo de Serviços](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infraestrutura como serviço (IaaS) - Configure CI/CD

A Azure Pipelines fornece um conjunto completo de ferramentas de automatização CI/CD para implementações em máquinas virtuais. Pode configurar um gasoduto de entrega contínua para um Azure VM a partir do portal Azure.

Este artigo mostra como configurar um oleoduto CI/CD para a implantação de multimáquinas rolantes a partir do portal Azure. O portal Azure também apoia outras estratégias como [canário](https://aka.ms/AA7jdrz) e [azul-verde.](https://aka.ms/AA83fwu)

### <a name="configure-cicd-on-virtual-machines"></a>Configure CI/CD em máquinas virtuais

Pode adicionar máquinas virtuais como alvos a um grupo de [implantação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups). Em seguida, pode direcioná-las para atualizações multi-máquinas. Depois de se implantar em máquinas, veja o Histórico de **Implantação** dentro de um grupo de implantação. Esta vista permite-lhe rastrear desde vm até ao oleoduto e depois ao compromisso.

### <a name="rolling-deployments"></a>Implantações rolantes

Em cada iteração, uma implementação rolante substitui os casos da versão anterior de uma aplicação. Substitui-os por instâncias da nova versão num conjunto fixo de máquinas (conjunto de rolamentos). O seguinte walk-through mostra como configurar uma atualização rolante para máquinas virtuais.

Utilizando a opção de entrega contínua, pode configurar atualizações de rolamento para as suas máquinas virtuais dentro do portal Azure. Aqui está o passo a passo passo:

1. Inscreva-se no portal Azure e navegue para uma máquina virtual.
1. No painel mais à esquerda das definições vM, selecione **a entrega contínua**. Em seguida, **selecione Configurar**.

   ![O painel de entrega contínua com o botão Configurar](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. No painel de configuração, **selecione Azure DevOps Organization** para escolher uma conta existente ou criar uma nova. Em seguida, selecione o projeto sob o qual pretende configurar o gasoduto.  

   ![O painel de entrega contínua](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Um grupo de implantação é um conjunto lógico de máquinas-alvo de implantação que representam os ambientes físicos. Dev, Test, UAT e Produção são exemplos. Pode criar um novo grupo de implementação ou selecionar um existente.
1. Selecione o pipeline de construção que publica o pacote a ser implantado na máquina virtual. O pacote publicado deve ter um script de implementação denominado deploy.ps1 ou deploy.sh na pasta de scripts de implementação na pasta raiz do pacote. O oleoduto executa este guião de implantação.
1. Na **estratégia de implantação,** selecione **Rolling**.
1. Opcionalmente, pode marcar cada máquina com o seu papel. As tags "web" e "db" são exemplos. Estas etiquetas ajudam-no a direcionar apenas VMs que têm um papel específico.
1. Selecione **OK** para configurar o gasoduto de entrega contínua.
1. Após os acabamentos de configuração, tem um gasoduto de entrega contínua configurado para ser implantado na máquina virtual.  

   ![O painel de entrega contínua que mostra o histórico de implantação](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. Os detalhes de implantação da máquina virtual são apresentados. Pode selecionar o link para ir ao pipeline, **Release-1** para visualizar a implementação ou **Editar** para modificar a definição de pipeline de lançamento.

1. Se estiver a configurar vários VMs, repita os passos 2 a 4 para que outros VMs adicionem ao grupo de implementação. Se selecionar um grupo de implantação que já tem uma execução de gasoduto, os VMs são adicionados ao grupo de implantação. Não são criados novos oleodutos.
1. Depois de feita a configuração, selecione a definição do pipeline, navegue para a organização Azure DevOps e selecione **Editar** para o pipeline de lançamento.

   ![Edição do gasoduto rolante](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. Selecione **1 trabalho, 1 tarefa** na fase de **dev.** Selecione a fase **de implantação.**

   ![Tarefas de pipeline rolante com a tarefa de implantação selecionada](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. A partir do painel de configuração mais à direita, pode especificar o número de máquinas que pretende implantar paralelamente em cada iteração. Se pretender ser implantado em várias máquinas de cada vez, pode especificar o número de máquinas em percentagem utilizando o slider.  

1. A tarefa executar executar script de execução por padrão executa o script de implementação de implementação.ps1 ou deploy.sh. O script está na pasta de scripts de implantação na pasta raiz do pacote publicado.

   ![O painel de artefactos mostrando deploy.sh na pasta de implantscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Outras estratégias de implantação

- [Configure a estratégia de implantação canária](https://aka.ms/AA7jdrz)
- [Configure a estratégia de implantação azul-verde](https://aka.ms/AA83fwu)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Pode começar com azure facilmente. Com a Azure DevOps Projects, comece a executar a sua aplicação em qualquer serviço Azure em apenas três passos selecionando:

- Uma linguagem de aplicação
- Um tempo de execução
- Um serviço Azure
 
[Saiba mais](https://azure.microsoft.com/features/devops-projects/).
 
## <a name="additional-resources"></a>Recursos adicionais

- [Implemente para máquinas virtuais Azure utilizando projetos Azure DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implemente a implementação contínua da sua app para um conjunto de escala de máquina virtual Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
