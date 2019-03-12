---
title: Utilizar o Terraform com o Azure
description: Introdução ao uso do Terraform para a versão e implementar a infraestrutura do Azure.
services: terraform
ms.service: azure
keywords: terraform, devops, descrição geral, planear, aplicar, automatizar
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/31/2018
ms.openlocfilehash: 8b5ad3f060e2c8760014d7fc1990b971976388f7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779203"
---
# <a name="terraform-with-azure"></a>Terraform com o Azure

O [Hashicorp Terraform](https://www.terraform.io/) é uma ferramenta de código-fonte aberto para aprovisionar e gerir infraestrutura de cloud. Ele codifica infraestrutura nos ficheiros de configuração que descrevem a topologia de recursos da cloud, como máquinas virtuais, contas de armazenamento e interfaces de rede. A interface de linha de comandos do Terraform (CLI) oferece um mecanismo simples para implementar e atualizar os ficheiros de configuração para o Azure ou qualquer outra cloud suportada.

Este artigo descreve os benefícios de utilizar o Terraform para gerir infraestrutura do Azure.

## <a name="automate-infrastructure-management"></a>Automatize a gestão da sua infraestrutura.

Os ficheiros de configuração baseados em modelos do Terraform permitem-lhe definir, aprovisionar e configurar recursos do Azure de forma previsível e repetível. Automatizar a infraestrutura oferece vários benefícios:

- Reduz o potencial para erros humanos durante a implementação e gestão de infraestrutura.
- Implementa o mesmo modelo várias vezes para criar ambientes de desenvolvimento, teste e produção idênticos.
- Reduz o custo de ambientes de desenvolvimento e teste ao criá-los a pedido.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Compreender as alterações de infraestrutura antes de serem aplicadas 

À medida que a topologia de recurso se torna complexa, compreender o significado e o impacto das alterações de infraestrutura pode ser difícil.

O Terraform oferece uma interface de linha de comandos (CLI) que permite aos utilizadores validarem e visualizarem as alterações de infraestrutura antes de estas serem implementadas. Pré-visualizar as alterações de infraestrutura de forma segura e produtiva tem vários benefícios:
- Os membros da equipa podem colaborar com mais eficiência, ao entender rapidamente as alterações propostas e o seu impacto.
- As alterações indesejadas podem ser capturadas no início do processo de desenvolvimento


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Implementar infraestrutura em várias clouds

O Terraform é uma opção de ferramenta popular para cenários de várias clouds, em que a infraestrutura semelhante é implementada no Azure e em fornecedores de serviços cloud adicionais ou datacenters no local. Permite que os programadores utilizem as mesmas ferramentas e ficheiros de configuração para gerir a infraestrutura de vários fornecedores de cloud.

## <a name="next-steps"></a>Passos Seguintes

Agora que tem uma descrição geral do Terraform e dos seus benefícios, eis os passos sugeridos:

- Comece a utilizar ao [instalar o Terraform e configurá-lo para utilizar o Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure).
- [Criar uma máquina virtual do Azure com o Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm)
- Explorar o [módulo do Azure Resource Manager para Terraform](https://www.terraform.io/docs/providers/azurerm/) 