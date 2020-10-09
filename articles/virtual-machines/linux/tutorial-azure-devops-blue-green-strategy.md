---
title: Tutorial - Configurar implantações canárias para máquinas virtuais Azure Linux
description: Neste tutorial, aprende-se a configurar um pipeline de implementação contínua (CD). Este oleoduto atualiza um grupo de máquinas virtuais Azure Linux utilizando a estratégia de implantação azul-verde.
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
ms.openlocfilehash: 0d001144f1a2703db118261e5cae5417b1d8c17a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080136"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial - Configurar a estratégia de implantação azul-verde para máquinas virtuais Azure Linux

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infraestrutura como serviço (IaaS) - Configure CI/CD

A Azure Pipelines fornece um conjunto totalmente em destaque de ferramentas de automação CI/CD para implementações em máquinas virtuais. Pode configurar um gasoduto de entrega contínua para um Azure VM a partir do portal Azure.

Este artigo mostra como criar um oleoduto CI/CD que utiliza a estratégia azul-verde para implementações multi-tecnologia. O portal Azure também apoia outras estratégias como [o rolo](https://aka.ms/AA7jlh8) e [o canário.](https://aka.ms/AA7jdrz)

### <a name="configure-cicd-on-virtual-machines"></a>Configure CI/CD em máquinas virtuais

Pode adicionar máquinas virtuais como alvos a um [grupo de implantação](/azure/devops/pipelines/release/deployment-groups). Em seguida, pode direcioná-los para atualizações multi-metralhadoras. Depois de se deslocar para máquinas, veja **o Histórico de Implementação** dentro de um grupo de implantação. Esta vista permite-lhe rastrear desde VM até ao oleoduto e, em seguida, ao compromisso.

### <a name="blue-green-deployments"></a>Implantações verde-azulais

Uma implantação azul-verde reduz o tempo de inatividade por ter um ambiente de espera idêntico. Apenas um ambiente é ao vivo a qualquer momento.

Enquanto se prepara para um novo lançamento, complete a fase final dos testes no ambiente verde. Depois de o software funcionar no ambiente verde, altere o tráfego para que todos os pedidos de entrada vão para o ambiente verde. O ambiente azul é inativo.

Utilizando a opção de entrega contínua, pode configurar implementações verde-azulais para as suas máquinas virtuais a partir do portal Azure. Aqui está o passo-a-passo passo- através:

1. Inscreva-se no portal Azure e navegue para uma máquina virtual.
1. No painel mais à esquerda das definições de VM, selecione **Distribuição Contínua**. Em seguida, **selecione Configure**.

   ![O painel de entrega contínua com o botão Configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. No painel de configuração, selecione **Azure DevOps Organization** para escolher uma conta existente ou criar uma nova. Em seguida, selecione o projeto ao abrigo do qual pretende configurar o gasoduto.  

   ![O painel de entrega contínua](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Um grupo de implantação é um conjunto lógico de máquinas-alvo de implantação que representam os ambientes físicos. Dev, Teste, UAT e Produção são exemplos. Pode criar um novo grupo de implementação ou selecionar um existente.
1. Selecione o pipeline de construção que publica o pacote a ser implantado na máquina virtual. O pacote publicado deve ter um script de implementação nomeado deploy.ps1 ou deploy.sh na pasta de implementação na pasta raiz do pacote. O oleoduto executa este script de implantação.
1. Na **estratégia de implantação**, selecione **Blue-Green**.
1. Adicione uma etiqueta "azul" ou "verde" aos VMs que devem fazer parte de implantações verde-azulais. Se um VM for para um papel de standby, marque-o como "verde". Caso contrário, marque-o como "azul".

   ![O painel de entrega contínua, com o valor de estratégia de implementação Blue-Green escolhido](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. Selecione **OK** para configurar o gasoduto de entrega contínua para implantar na máquina virtual.

   ![O oleoduto azul-verde](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. Os detalhes de implantação da máquina virtual são apresentados. Pode selecionar o link para ir ao pipeline de desbloqueio em Azure DevOps. No pipeline de desbloqueio, **selecione Editar** para visualizar a configuração do pipeline. O gasoduto tem estas três fases:

   1. Esta fase é uma fase de grupo de implantação. As aplicações são implementadas para VMs de espera, que são marcados como "verdes".
   1. Nesta fase, o gasoduto para e aguarda a intervenção manual para retomar o funcionado. Os utilizadores podem retomar a execução do gasoduto uma vez que tenham assegurado manualmente a estabilidade da implantação para VMs marcados como "verdes".
   1. Esta fase troca as etiquetas "azul" e "verde" nos VMs. Isto garante que os VMs com versões de aplicação mais antigas são agora marcados como "verdes". Durante o próximo gasoduto, serão implementadas aplicações nestes VMs.

      ![O painel de grupo de implantação para a tarefa de Blue-Green de implantação](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. A tarefa 'Executar executar script' por predefinição executa o script de implementação deploy.ps1 ou deploy.sh. O script está na pasta de implementação na pasta raiz do pacote publicado. Certifique-se de que o gasoduto de construção selecionado publica a implantação na pasta raiz da embalagem.

   ![O painel de artefactos mostrando deploy.sh na pasta de implementação](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Outras estratégias de implantação

- [Configure a estratégia de implantação em rolo](https://aka.ms/AA7jlh8)
- [Configurar a estratégia de implantação canária](https://aka.ms/AA7jdrz)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Pode começar com o Azure facilmente. Com a Azure DevOps Projects, comece a executar a sua aplicação em qualquer serviço Azure em apenas três etapas, selecionando:

- Uma linguagem de aplicação
- Um tempo de execução
- Um serviço Azure

[Saiba mais](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Recursos adicionais

- [Implementar para a Azure máquinas virtuais utilizando projetos Azure DevOps](../../devops-project/azure-devops-project-vms.md)
- [Implemente a implementação contínua da sua app para um conjunto de escala de máquina virtual Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
