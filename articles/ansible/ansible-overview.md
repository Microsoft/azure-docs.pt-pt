---
title: A utilização do Ansible com o Azure
description: Introdução à utilização do Ansible para automatiza o aprovisionamento na cloud, gestão de configuração e implementações de aplicações.
ms.service: azure
keywords: ansible, azure, devops, visão geral, aprovisionar na cloud, gestão de configuração, implementação de aplicações, módulos do ansible, playbooks do ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/02/2018
ms.topic: overview
ms.openlocfilehash: d7fc86f9968ede37623ea9c89a666a35cba7eee8
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791601"
---
# <a name="ansible-with-azure"></a>Ansible com o Azure

[Ansible](https://www.ansible.com) é um produto de código-fonte aberto que automatiza o aprovisionamento na cloud, gestão de configuração e implementações de aplicações. A utilização do Ansible pode aprovisionar máquinas virtuais, contentores e de rede e concluir as infraestruturas de cloud. Além disso, o Ansible permite-lhe automatizar a implementação e configuração dos recursos no seu ambiente.

Este artigo fornece uma descrição geral básica de algumas das vantagens da utilização do Ansible com o Azure.

## <a name="ansible-playbooks"></a>Playbooks do Ansible

[Playbooks do Ansible](https://docs.ansible.com/ansible/latest/playbooks.html) são de idioma de orquestração, configuração e implementação do Ansible. Eles podem descrevem uma política que pretende que os seus sistemas remotos para impor a ou um conjunto de etapas num processo geral de TI. Ao criar um playbook de fazê-lo utilizando YAML, que define um modelo de uma configuração ou de um processo.

## <a name="ansible-modules"></a>Módulos do Ansible

Ansible inclui um conjunto de [módulos do Ansible](https://docs.ansible.com/ansible/latest/modules_by_category.html) que pode ser executado diretamente em hosts remotos ou via [playbooks](https://docs.ansible.com/ansible/latest/playbooks.html). Os utilizadores também podem criar seus próprios módulos. Módulos podem ser utilizados para controlar os recursos de sistema – como serviços, pacotes ou arquivos - ou executar comandos de sistema.

Para interagir com serviços do Azure, o Ansible inclui um conjunto de [módulos de nuvem do Ansible](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) que fornece as ferramentas para facilmente criar e organizar sua infraestrutura no Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrar a carga de trabalho existente para o Azure

Assim que tiver utilizado o Ansible para definir a sua infraestrutura, pode aplicar manual de comunicação social da sua aplicação, permitindo que o Azure dimensione automaticamente seu ambiente, conforme necessário. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizar a aplicação nativa da cloud no Azure

Ansible permite-lhe automatizar aplicações nativas da cloud no Azure através de microsserviços do Azure, como [as funções do Azure](https://azure.microsoft.com//services/functions/) e [Kubernetes no Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Gerenciar Implantações com o inventário dinâmicas
Através do respetivo [inventário dinâmicas](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) funcionalidade, Ansible fornece a capacidade para inventário de solicitação dos recursos do Azure. Pode, em seguida, marque as suas implementações do Azure existentes e gerenciar essas implementações marcadas por meio do Ansible.

## <a name="additional-azure-marketplace-options"></a>Opções adicionais do Azure Marketplace
O [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) imagem do Azure Marketplace da Red Hat ajuda as organizações a dimensionar a automatização de TI e gerir implementações complexas em físicos, virtuais e infraestruturas de cloud. Ansible Tower inclui as funcionalidades que fornecem níveis adicionais de visibilidade, controlo, segurança e eficiência necessária para empresas hoje em dia. Ansible Tower encripta as credenciais, tais como chaves do Azure e o SSH para que pode delegar tarefas aos funcionários experientes menos sem o risco de expor as suas credenciais.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Matriz de módulo e a versão do Ansible para o Azure
O Ansible é fornecido com um número de módulos que podem ser executados diretamente nos hosts remotos ou por meio de playbooks.
O [matriz de módulo e a versão do Ansible](./ansible-matrix.md) apresenta uma lista de módulos o Ansible para o Azure que pode aprovisionar os recursos de cloud do Azure como máquina virtual, redes e serviços de contentores. 

## <a name="next-steps"></a>Passos Seguintes
- [Configurar o Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Criar uma VM de Linux](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
