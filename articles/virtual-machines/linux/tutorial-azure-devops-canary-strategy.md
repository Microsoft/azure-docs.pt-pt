---
title: Tutorial - Configurar implantações canárias para máquinas virtuais Azure Linux
description: Neste tutorial, aprende-se a configurar um pipeline de implementação contínua (CD). Este oleoduto atualiza um grupo de máquinas virtuais Azure Linux utilizando a estratégia de implantação canária.
author: moala
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: bbfe6571cf075b2ce4930eea91bfd1e239470c5a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552512"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial - Configurar a estratégia de implantação canária para máquinas virtuais Azure Linux

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infraestrutura como serviço (IaaS) - Configure CI/CD

A Azure Pipelines fornece um conjunto totalmente em destaque de ferramentas de automação CI/CD para implementações em máquinas virtuais. Pode configurar um gasoduto de entrega contínua para um Azure VM a partir do portal Azure.

Este artigo mostra como criar um pipeline CI/CD que utiliza a estratégia canária para implementações multi-tecnologia. O portal Azure também apoia outras estratégias como [rolar](./tutorial-devops-azure-pipelines-classic.md) e [azul-verde.](./tutorial-azure-devops-blue-green-strategy.md)

### <a name="configure-cicd-on-virtual-machines"></a>Configure CI/CD em máquinas virtuais

Pode adicionar máquinas virtuais como alvos a um [grupo de implantação](/azure/devops/pipelines/release/deployment-groups). Em seguida, pode direcioná-los para atualizações multi-metralhadoras. Depois de se deslocar para máquinas, veja **o Histórico de Implementação** dentro de um grupo de implantação. Esta vista permite-lhe rastrear desde VM até ao oleoduto e, em seguida, ao compromisso.

### <a name="canary-deployments"></a>Implantações canárias

Uma implementação canária reduz o risco ao lançar lentamente alterações num pequeno subconjunto de utilizadores. À medida que ganhas confiança na nova versão, podes lançá-la para mais servidores na tua infraestrutura e encaminhar mais utilizadores para a própria versão.

Utilizando a opção de entrega contínua, pode configurar as implementações canárias para as suas máquinas virtuais a partir do portal Azure. Aqui está o passo-a-passo passo- através:

1. Inscreva-se no portal Azure e navegue para uma máquina virtual.
1. No painel mais à esquerda das definições de VM, selecione **Distribuição Contínua**. Em seguida, **selecione Configure**.

   ![O painel de entrega contínua com o botão Configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. No painel de configuração, selecione **Azure DevOps Organization** para escolher uma conta existente ou criar uma nova. Em seguida, selecione o projeto ao abrigo do qual pretende configurar o gasoduto.  

   ![O painel de entrega contínua](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Um grupo de implantação é um conjunto lógico de máquinas-alvo de implantação que representam os ambientes físicos. Dev, Teste, UAT e Produção são exemplos. Pode criar um novo grupo de implementação ou selecionar um existente.
1. Selecione o pipeline de construção que publica o pacote a ser implantado na máquina virtual. O pacote publicado deve ter um script de implementação nomeado deploy.ps1 ou deploy.sh na pasta de implementação na pasta raiz do pacote. O oleoduto executa este script de implantação.
1. Na **estratégia de implantação**, selecione **Canário**.
1. Adicione uma etiqueta "canário" aos VMs que devem fazer parte das implementações canárias. Adicione uma etiqueta "prod" aos VMs que fazem parte das implementações efetuadas após a implantação canária ter sucesso. As tags ajudam-no a direcionar apenas os VMs que têm um papel específico.

   ![O painel de entrega contínua, com o valor da estratégia de implantação Canário escolhido](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. Selecione **OK** para configurar o gasoduto de entrega contínua para implantar na máquina virtual.

   ![O oleoduto canário](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. Os detalhes de implantação da máquina virtual são apresentados. Pode selecionar o link para ir ao pipeline de desbloqueio em Azure DevOps. No pipeline de desbloqueio, **selecione Editar** para visualizar a configuração do pipeline. O gasoduto tem estas três fases:

   1. Esta fase é uma fase de grupo de implantação. As aplicações são implantadas em VMs que são marcados como "canários".
   1. Nesta fase, o gasoduto para e aguarda a intervenção manual para retomar o funcionado.
   1. Esta é novamente uma fase de grupo de implantação. A atualização é agora implantada em VMs marcados como "prod".

      ![O painel de grupo de implantação para a tarefa de implantação canária](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. Antes de retomar a tubagem, certifique-se de que pelo menos um VM é marcado como "prod". Na terceira fase do gasoduto, as aplicações são implementadas apenas para os VMs que têm a etiqueta "prod".

1. A tarefa 'Executar executar script' por predefinição executa o script de implementação deploy.ps1 ou deploy.sh. O script está na pasta de implementação na pasta raiz do pacote publicado. Certifique-se de que o gasoduto de construção selecionado publica a implantação na pasta raiz da embalagem.

   ![O painel de artefactos mostrando deploy.sh na pasta de implementação](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Outras estratégias de implantação
- [Configure a estratégia de implantação em rolo](./tutorial-devops-azure-pipelines-classic.md)
- [Configurar a estratégia de implantação azul-verde](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Pode começar com o Azure facilmente. Com a Azure DevOps Projects, comece a executar a sua aplicação em qualquer serviço Azure em apenas três etapas, selecionando:

- Uma linguagem de aplicação
- Um tempo de execução
- Um serviço Azure

[Saiba mais](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Recursos adicionais

- [Implementar para a Azure máquinas virtuais utilizando projetos Azure DevOps](../../devops-project/azure-devops-project-vms.md)
- [Implemente a implementação contínua da sua app para um conjunto de escala de máquina virtual Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)