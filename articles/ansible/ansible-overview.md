---
title: Usando Ansible com Azure
description: Introdução à utilização de Ansible para automatizar o fornecimento de nuvem, a gestão de configuração e as implementações de aplicações.
keywords: ansível, azul, devops, visão geral, fornecimento de nuvem, gestão de configuração, implementação de aplicações, módulos ansíveis, playbooks ansíveis
ms.topic: overview
ms.date: 04/30/2019
ms.openlocfilehash: e9d5b8858f052083b157c7d4809fe21018518bcd
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193570"
---
# <a name="using-ansible-with-azure"></a>Usando Ansible com Azure

[Ansible](https://www.ansible.com) é um produto de código aberto que automatiza o fornecimento de nuvem, a gestão de configuração e as implementações de aplicações. Utilizando o Ansible, pode fornecer máquinas virtuais, contentores e rede e infraestruturas completas em nuvem. Além disso, a Ansible permite automatizar a implementação e configuração de recursos no seu ambiente.

Este artigo dá uma visão geral básica de alguns dos benefícios de usar Ansible com Azure.

## <a name="ansible-playbooks"></a>Playbooks ansíveis

[Os playbooks ansible](https://docs.ansible.com/ansible/latest/playbooks.html) permitem-lhe direcionar o Ansible para configurar o seu ambiente. Os livros são codificados usando o YAML de modo a serem legíveis pelo homem. A secção Tutorials dá muitos exemplos de utilização de playbooks para instalar e configurar recursos Azure. 

## <a name="ansible-modules"></a>Módulos ansíveis

Ansible inclui um conjunto de [módulos Ansible](https://docs.ansible.com/ansible/latest/modules_by_category.html) que são executados diretamente em anfitriões remotos ou através de [playbooks](https://docs.ansible.com/ansible/latest/playbooks.html). Os utilizadores podem criar os seus próprios módulos. Os módulos são utilizados para controlar os recursos do sistema - como serviços, pacotes ou ficheiros - ou executar comandos do sistema.

Para interagir com os serviços Azure, o Ansible inclui um conjunto de módulos de [nuvem Ansible.](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) Estes módulos permitem criar e orquestrar a sua infraestrutura no Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrar carga de trabalho existente para Azure

Uma vez que utilize o Ansible para definir a sua infraestrutura, pode aplicar o livro de jogadas da sua aplicação, permitindo que o Azure dimensione automaticamente o seu ambiente conforme necessário. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizar aplicação nativa da nuvem em Azure

Ansible permite automatizar aplicações nativas de nuvem em Azure usando microserviços Azure tais como [Funções Azure](https://azure.microsoft.com//services/functions/) e [Kubernetes em Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Gerir implementações com inventário dinâmico

Através da sua funcionalidade dinâmica de [inventário,](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) a Ansible fornece a capacidade de retirar o inventário dos recursos azure. Em seguida, pode marcar as suas implementações Azure existentes e gerir as implementações marcadas através do Ansible.

## <a name="additional-azure-marketplace-options"></a>Opções adicionais do Azure Marketplace

A [Torre Ansible](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) é uma imagem do Azure Marketplace da Red Hat. 

Ansible Tower é um UI baseado na web e painel de instrumentos para Ansible que tem as seguintes funcionalidades:

* Permite-lhe definir o controlo de acesso baseado em papéis, agendamento de emprego e gestão de inventário gráfico. 
* Inclui um REST API e CLI para que possa inserir a Torre em ferramentas e processos existentes. 
* Suporta a saída em tempo real de jogadas de livro. 
* Encripta credenciais - como chaves Azure e SSH - para que possa delegar tarefas sem expor credenciais.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Módulo ansível e matriz de versão para Azure

O Ansible inclui um conjunto de módulos para utilização na prestação e configuração de recursos Azure. Estes recursos incluem máquinas virtuais, conjuntos de escala, serviços de networking e serviços de contentores. A [matriz Ansible](./ansible-matrix.md) lista os módulos Ansible para as versões Azure e Ansible em que enviam.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Implemente o modelo de solução Ansible para Azure para o CentOS](./ansible-deploy-solution-template.md)
- [Quickstart: Configure máquinas virtuais Linux em Azure usando Ansible](./ansible-install-configure.md)