---
title: A utilização do Ansible com o Azure | Documentos da Microsoft
description: Introdução à utilização do Ansible para automatiza o aprovisionamento na cloud, gestão de configuração e implementações de aplicações.
keywords: ansible, azure, devops, visão geral, aprovisionar na cloud, gestão de configuração, implementação de aplicações, módulos do ansible, playbooks do ansible
ms.topic: overview
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 54d66a0ac425a9a0a63c91317ead0e02ecf9452c
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764146"
---
# <a name="using-ansible-with-azure"></a>A utilização do Ansible com o Azure

[Ansible](https://www.ansible.com) é um produto de código-fonte aberto que automatiza o aprovisionamento na cloud, gestão de configuração e implementações de aplicações. A utilização do Ansible pode aprovisionar máquinas virtuais, contentores e de rede e concluir as infraestruturas de cloud. Além disso, o Ansible permite-lhe automatizar a implementação e configuração de recursos no seu ambiente.

Este artigo fornece uma descrição geral básica de algumas das vantagens da utilização do Ansible com o Azure.

## <a name="ansible-playbooks"></a>Playbooks do Ansible

[Playbooks do Ansible](https://docs.ansible.com/ansible/latest/playbooks.html) permitem-lhe direcionar o Ansible para configurar o ambiente. Playbooks são codificadas com YAML para podermos legível por humanos. A secção de tutoriais fornece muitos exemplos de utilização playbooks para instalar e configurar recursos do Azure. 

## <a name="ansible-modules"></a>Módulos do Ansible

Ansible inclui um conjunto de [módulos do Ansible](https://docs.ansible.com/ansible/latest/modules_by_category.html) que são executadas diretamente nos hosts remotos ou via [playbooks](https://docs.ansible.com/ansible/latest/playbooks.html). Os utilizadores podem criar seus próprios módulos. Módulos são utilizados para controlar os recursos de sistema – como serviços, pacotes ou arquivos - ou executar comandos de sistema.

Para interagir com serviços do Azure, o Ansible inclui um conjunto de [módulos de nuvem do Ansible](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Estes módulos permitem-lhe criar e organizar sua infraestrutura no Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrar a carga de trabalho existente para o Azure

Depois de utilizar o Ansible para definir a sua infraestrutura, pode aplicar manual de comunicação social da sua aplicação, permitindo que o Azure dimensione automaticamente seu ambiente, conforme necessário. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizar a aplicação nativa da cloud no Azure

Ansible permite-lhe automatizar aplicações nativas da cloud no Azure através de microsserviços do Azure, como [as funções do Azure](https://azure.microsoft.com//services/functions/) e [Kubernetes no Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Gerenciar Implantações com o inventário dinâmicas

Através do respetivo [inventário dinâmicas](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) funcionalidade, Ansible fornece a capacidade para inventário de solicitação dos recursos do Azure. Pode, em seguida, marque as suas implementações do Azure existentes e gerenciar essas implementações marcadas por meio do Ansible.

## <a name="additional-azure-marketplace-options"></a>Opções adicionais do Azure Marketplace

O [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) é uma imagem do Azure Marketplace da Red Hat. 

Ansible Tower é uma interface do Usuário e o dashboard para o Ansible que tem as seguintes funcionalidades baseadas na web:

* Permite-lhe definir o controlo de acesso baseado em funções, agendamento de tarefas e gestão de inventário de gráfico. 
* Inclui uma REST API e a CLI, para que pode inserir Torre de em ferramentas e processos existentes. 
* Oferece suporte a saída em tempo real das execuções de playbook. 
* Encripta credenciais - tal como chaves do Azure e o SSH - para que pode delegar tarefas sem expor as credenciais.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Matriz de módulo e a versão do Ansible para o Azure

Ansible inclui um conjunto de módulos para utilização no provisionamento e configuração de recursos do Azure. Estes recursos incluem máquinas virtuais, conjuntos de dimensionamento, funcionamento em rede de serviços e os serviços de contentor. O [Ansible matriz](./ansible-matrix.md) apresenta uma lista de módulos o Ansible para o Azure e as versões do Ansible no qual eles são enviados.

## <a name="next-steps"></a>Passos Seguintes

- [Quickstart: Implementar o modelo de solução do Ansible para o Azure para CentOS](./ansible-deploy-solution-template.md)
- [Quickstart: Configurar máquinas virtuais do Linux no Azure com o Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fazure%2Fansible%2Ftoc.json&bc=%2Fazure%2Fbread%2Ftoc.json)