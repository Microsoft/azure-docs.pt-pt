---
title: Usando o Ansible com o Azure
description: Introdução ao uso do Ansible para automatizar o provisionamento de nuvem, o gerenciamento de configuração e as implantações de aplicativo.
keywords: Ansible, Azure, DevOps, visão geral, provisionamento de nuvem, gerenciamento de configuração, implantação de aplicativos, módulos de Ansible, guias estratégicos de Ansible
ms.topic: overview
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: b6b9656edc43f38a4cb005be53c33bb98781679c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241502"
---
# <a name="using-ansible-with-azure"></a>Usando o Ansible com o Azure

O [Ansible](https://www.ansible.com) é um produto de software livre que automatiza o provisionamento de nuvem, o gerenciamento de configuração e as implantações de aplicativo. Usando o Ansible, você pode provisionar máquinas virtuais, contêineres e rede e infraestruturas de nuvem completas. Além disso, o Ansible permite automatizar a implantação e a configuração de recursos em seu ambiente.

Este artigo fornece uma visão geral básica de alguns dos benefícios de usar o Ansible com o Azure.

## <a name="ansible-playbooks"></a>Guias estratégicos do Ansible

Os [Guias estratégicos do Ansible](https://docs.ansible.com/ansible/latest/playbooks.html) permitem que você direcione o Ansible para configurar seu ambiente. Os guias estratégicos são codificados usando YAML para que possam ser lidos por pessoas. A seção de tutoriais fornece muitos exemplos de uso de guias estratégicos para instalar e configurar recursos do Azure. 

## <a name="ansible-modules"></a>Módulos Ansible

O Ansible inclui um conjunto de [módulos do Ansible](https://docs.ansible.com/ansible/latest/modules_by_category.html) que são executados diretamente em hosts remotos ou por meio de [Guias estratégicos](https://docs.ansible.com/ansible/latest/playbooks.html). Os usuários podem criar seus próprios módulos. Os módulos são usados para controlar recursos do sistema, como serviços, pacotes ou arquivos, ou executar comandos do sistema.

Para interagir com os serviços do Azure, o Ansible inclui um conjunto de [módulos de nuvem Ansible](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Esses módulos permitem criar e orquestrar sua infraestrutura no Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrar a carga de trabalho existente para o Azure

Depois de usar o Ansible para definir sua infraestrutura, você pode aplicar o guia estratégico do seu aplicativo, permitindo que o Azure dimensione automaticamente seu ambiente conforme necessário. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizar o aplicativo nativo de nuvem no Azure

O Ansible permite automatizar aplicativos nativos de nuvem no Azure usando os microserviços do Azure, como [Azure Functions](https://azure.microsoft.com//services/functions/) e [kubernetes no Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Gerenciar implantações com o inventário dinâmico

Por meio de seu recurso de [inventário dinâmico](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) , o Ansible fornece a capacidade de efetuar pull do inventário de recursos do Azure. Em seguida, você pode marcar as implantações existentes do Azure e gerenciar essas implantações marcadas por meio do Ansible.

## <a name="additional-azure-marketplace-options"></a>Opções adicionais do Azure Marketplace

O [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) é uma imagem do Azure Marketplace por Red Hat. 

O Ansible Tower é uma interface do usuário baseada na Web e um painel para Ansible que tem os seguintes recursos:

* Permite que você defina o controle de acesso baseado em função, o agendamento de trabalhos e o gerenciamento de estoque gráfico. 
* Inclui uma API REST e uma CLI para que você possa inserir a torre em ferramentas e processos existentes. 
* Dá suporte à saída em tempo real de execuções de guias estratégicos. 
* Criptografa as credenciais, como as chaves do Azure e SSH, para que você possa delegar tarefas sem expor credenciais.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Módulo Ansible e matriz de versão para o Azure

O Ansible inclui um conjunto de módulos para uso no provisionamento e configuração de recursos do Azure. Esses recursos incluem máquinas virtuais, conjuntos de dimensionamento, serviços de rede e serviços de contêiner. A [matriz Ansible](./ansible-matrix.md) lista os módulos Ansible do Azure e as versões Ansible nas quais eles são fornecidos.

## <a name="next-steps"></a>Passos seguintes

- [Início rápido: implantar o modelo de solução Ansible para o Azure para CentOS](./ansible-deploy-solution-template.md)
- [Início rápido: configurar máquinas virtuais do Linux no Azure usando o Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fazure%2Fansible%2Ftoc.json&bc=%2Fazure%2Fbread%2Ftoc.json)