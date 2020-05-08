---
title: Tutorial - Configure implementações canárias para Máquinas Virtuais Azure Linux
description: Neste tutorial, aprende-se a configurar um oleoduto de implantação contínua (CD). Este oleoduto atualiza um grupo de máquinas virtuais Azure Linux utilizando a estratégia de implantação canária.
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
ms.openlocfilehash: e0fb26896b79fb23bb0f784c0f23aa3af0593c22
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871865"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial - Configure a estratégia de implantação de canários para máquinas virtuais Azure Linux

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infraestrutura como serviço (IaaS) - Configure CI/CD

A Azure Pipelines fornece um conjunto completo de ferramentas de automatização CI/CD para implementações em máquinas virtuais. Pode configurar um gasoduto de entrega contínua para um Azure VM a partir do portal Azure.

Este artigo mostra como configurar um oleoduto CI/CD que utiliza a estratégia canária para implementações multi-máquinas. O portal Azure também apoia outras estratégias como [o rolling](https://aka.ms/AA7jlh8) e [o azul-verde.](https://aka.ms/AA83fwu)

### <a name="configure-cicd-on-virtual-machines"></a>Configure CI/CD em máquinas virtuais

Pode adicionar máquinas virtuais como alvos a um grupo de [implantação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups). Em seguida, pode direcioná-las para atualizações multi-máquinas. Depois de se implantar em máquinas, veja o Histórico de **Implantação** dentro de um grupo de implantação. Esta vista permite-lhe rastrear desde vm até ao oleoduto e depois ao compromisso.

### <a name="canary-deployments"></a>Implantações canárias

Uma implementação canária reduz o risco ao lançar lentamente alterações num pequeno subconjunto de utilizadores. À medida que ganha confiança na nova versão, pode lançá-la para mais servidores na sua infraestrutura e encaminhar mais utilizadores para a sua versão.

Utilizando a opção de entrega contínua, pode configurar as implementações de canários para as suas máquinas virtuais a partir do portal Azure. Aqui está o passo a passo passo:

1. Inscreva-se no portal Azure e navegue para uma máquina virtual.
1. No painel mais à esquerda das definições vM, selecione **a entrega contínua**. Em seguida, **selecione Configurar**.

   ![O painel de entrega contínua com o botão Configurar](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. No painel de configuração, **selecione Azure DevOps Organization** para escolher uma conta existente ou criar uma nova. Em seguida, selecione o projeto sob o qual pretende configurar o gasoduto.  

   ![O painel de entrega contínua](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Um grupo de implantação é um conjunto lógico de máquinas-alvo de implantação que representam os ambientes físicos. Dev, Test, UAT e Produção são exemplos. Pode criar um novo grupo de implementação ou selecionar um existente.
1. Selecione o pipeline de construção que publica o pacote a ser implantado na máquina virtual. O pacote publicado deve ter um script de implementação denominado deploy.ps1 ou deploy.sh na pasta de scripts de implementação na pasta raiz do pacote. O oleoduto executa este guião de implantação.
1. Na **estratégia de implantação,** selecione **Canary**.
1. Adicione uma etiqueta "canária" aos VMs que devem fazer parte das implementações canárias. Adicione uma etiqueta "prod" aos VMs que fazem parte das implementações feitas após o sucesso da implementação canária. As etiquetas ajudam-no a direcionar apenas VMs que têm um papel específico.

   ![O painel de entrega contínua, com o valor de estratégia de implantação Canary escolhido](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. Selecione **OK** para configurar o gasoduto de entrega contínua para implantar na máquina virtual.

   ![O oleoduto canário](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. Os detalhes de implantação da máquina virtual são apresentados. Pode selecionar o link para ir ao gasoduto de libertação em Azure DevOps. No gasoduto de lançamento, selecione **Editar** para visualizar a configuração do gasoduto. O oleoduto tem estas três fases:

   1. Esta fase é uma fase de grupo de implantação. As aplicações são implementadas em VMs que são marcados como "canários".
   1. Nesta fase, o gasoduto para e aguarda a intervenção manual para retomar a execução.
   1. Esta é mais uma vez uma fase de grupo de implantação. A atualização está agora implantada para VMs marcados como "prod".

      ![O painel do grupo de implantação para a tarefa Deploy Canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. Antes de retomar a execução do gasoduto, certifique-se de que pelo menos um VM é marcado como "prod". Na terceira fase do gasoduto, as aplicações são implantadas apenas para os VMs que têm a etiqueta "prod".

1. A tarefa execute executar o Script de implementação por padrão executa o script de implementação de implementação.ps1 ou deploy.sh. O script está na pasta de scripts de implantação na pasta raiz do pacote publicado. Certifique-se de que o pipeline de construção selecionado publica a implantação na pasta raiz da embalagem.

   ![O painel de artefactos mostrando deploy.sh na pasta de implantscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Outras estratégias de implantação
- [Configure a estratégia de implantação em movimento](https://aka.ms/AA7jlh8)
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
