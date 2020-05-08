---
title: Tutorial - Configure implementações canárias para máquinas virtuais Azure Linux
description: Neste tutorial, aprende-se a configurar um oleoduto de implantação contínua (CD). Este oleoduto atualiza um grupo de máquinas virtuais Azure Linux utilizando a estratégia de implantação azul-verde.
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
ms.openlocfilehash: a98989ed48e515cafeca27ae492c83efca6002c4
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871608"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial - Configure a estratégia de implantação azul-verde para máquinas virtuais Azure Linux

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infraestrutura como serviço (IaaS) - Configure CI/CD

A Azure Pipelines fornece um conjunto completo de ferramentas de automatização CI/CD para implementações em máquinas virtuais. Pode configurar um gasoduto de entrega contínua para um Azure VM a partir do portal Azure.

Este artigo mostra como configurar um oleoduto CI/CD que utiliza a estratégia azul-verde para implementações multi-máquinas. O portal Azure também apoia outras estratégias como [o rolling](https://aka.ms/AA7jlh8) e [o canário.](https://aka.ms/AA7jdrz)

### <a name="configure-cicd-on-virtual-machines"></a>Configure CI/CD em máquinas virtuais

Pode adicionar máquinas virtuais como alvos a um grupo de [implantação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups). Em seguida, pode direcioná-las para atualizações multi-máquinas. Depois de se implantar em máquinas, veja o Histórico de **Implantação** dentro de um grupo de implantação. Esta vista permite-lhe rastrear desde vm até ao oleoduto e depois ao compromisso.

### <a name="blue-green-deployments"></a>Implantações verde-azuladas

Uma implantação azul-verde reduz o tempo de inatividade por ter um ambiente de espera idêntico. Apenas um ambiente é vivo a qualquer momento.

Enquanto se prepara para um novo lançamento, complete a fase final dos testes em ambiente verde. Depois de o software funcionar em ambiente verde, altere o tráfego para que todos os pedidos de entrada vão para o ambiente verde. O ambiente azul está inativo.

Utilizando a opção de entrega contínua, pode configurar implementações verde-azuladas para as suas máquinas virtuais a partir do portal Azure. Aqui está o passo a passo passo:

1. Inscreva-se no portal Azure e navegue para uma máquina virtual.
1. No painel mais à esquerda das definições vM, selecione **a entrega contínua**. Em seguida, **selecione Configurar**.

   ![O painel de entrega contínua com o botão Configurar](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. No painel de configuração, **selecione Azure DevOps Organization** para escolher uma conta existente ou criar uma nova. Em seguida, selecione o projeto sob o qual pretende configurar o gasoduto.  

   ![O painel de entrega contínua](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Um grupo de implantação é um conjunto lógico de máquinas-alvo de implantação que representam os ambientes físicos. Dev, Test, UAT e Produção são exemplos. Pode criar um novo grupo de implementação ou selecionar um existente.
1. Selecione o pipeline de construção que publica o pacote a ser implantado na máquina virtual. O pacote publicado deve ter um script de implementação denominado deploy.ps1 ou deploy.sh na pasta de scripts de implementação na pasta raiz do pacote. O oleoduto executa este guião de implantação.
1. Na **estratégia de implantação,** selecione **Blue-Green**.
1. Adicione uma etiqueta "azul" ou "verde" aos VMs que devem fazer parte de implantações verde-azuladas. Se um VM é para um papel de espera, marque-o como "verde". Caso contrário, marque-o como "azul".

   ![O painel de entrega contínua, com o valor de estratégia de implantação Blue-Green escolhido](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. Selecione **OK** para configurar o gasoduto de entrega contínua para implantar na máquina virtual.

   ![O oleoduto azul-verde](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. Os detalhes de implantação da máquina virtual são apresentados. Pode selecionar o link para ir ao gasoduto de libertação em Azure DevOps. No gasoduto de lançamento, selecione **Editar** para visualizar a configuração do gasoduto. O oleoduto tem estas três fases:

   1. Esta fase é uma fase de grupo de implantação. As aplicações são implementadas para vMs de espera, que são marcados como "verdes".
   1. Nesta fase, o gasoduto para e aguarda a intervenção manual para retomar a execução. Os utilizadores podem retomar o funcionamento do gasoduto depois de terem assegurado manualmente a estabilidade da implantação para VMs marcados como "verdes".
   1. Esta fase troca as etiquetas "azul" e "verde" nos VMs. Isto garante que os VMs com versões de aplicação mais antigas são agora marcados como "verdes". Durante a próxima execução do gasoduto, serão implementadas aplicações para estes VMs.

      ![O painel do grupo de implantação para a tarefa Deploy Blue-Green](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. A tarefa execute executar o Script de implementação por padrão executa o script de implementação de implementação.ps1 ou deploy.sh. O script está na pasta de scripts de implantação na pasta raiz do pacote publicado. Certifique-se de que o pipeline de construção selecionado publica a implantação na pasta raiz da embalagem.

   ![O painel de artefactos mostrando deploy.sh na pasta de implantscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Outras estratégias de implantação

- [Configure a estratégia de implantação em movimento](https://aka.ms/AA7jlh8)
- [Configure a estratégia de implantação canária](https://aka.ms/AA7jdrz)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Pode começar com azure facilmente. Com a Azure DevOps Projects, comece a executar a sua aplicação em qualquer serviço Azure em apenas três passos selecionando:

- Uma linguagem de aplicação
- Um tempo de execução
- Um serviço Azure

[Saiba mais](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Recursos adicionais

- [Implemente para máquinas virtuais Azure utilizando projetos Azure DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implemente a implementação contínua da sua app para um conjunto de escala de máquina virtual Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
