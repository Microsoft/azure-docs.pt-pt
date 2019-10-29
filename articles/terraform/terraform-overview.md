---
title: Utilizar o Terraform com o Azure
description: Introdução ao uso do Terraform para a versão e implantação da infraestrutura do Azure.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 1c6ac9b67f556b039b9ffd5ed725ea1f24aeeb3a
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969249"
---
# <a name="terraform-with-azure"></a>Terraform com o Azure

O [Hashicorp Terraform](https://www.terraform.io/) é uma ferramenta de código-fonte aberto para aprovisionar e gerir infraestrutura de cloud. A infraestrutura de ti codifica em arquivos de configuração que descrevem a topologia dos recursos de nuvem. Esses recursos incluem máquinas virtuais, contas de armazenamento e interfaces de rede. A CLI do Terraform fornece um mecanismo simples para implantar e versão dos arquivos de configuração no Azure.

Este artigo descreve os benefícios de utilizar o Terraform para gerir infraestrutura do Azure.

## <a name="automate-infrastructure-management"></a>Automatize a gestão da sua infraestrutura.

Os ficheiros de configuração baseados em modelos do Terraform permitem-lhe definir, aprovisionar e configurar recursos do Azure de forma previsível e repetível. Automatizar a infraestrutura oferece vários benefícios:

- Reduz o potencial para erros humanos durante a implementação e gestão de infraestrutura.
- Implementa o mesmo modelo várias vezes para criar ambientes de desenvolvimento, teste e produção idênticos.
- Reduz o custo de ambientes de desenvolvimento e teste ao criá-los a pedido.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Entender as alterações de infraestrutura antes de serem aplicadas

À medida que a topologia de recurso se torna complexa, compreender o significado e o impacto das alterações de infraestrutura pode ser difícil.

A CLI do Terraform permite que os usuários validem e visualizem as alterações de infraestrutura antes do aplicativo. A visualização de alterações de infraestrutura de maneira segura tem vários benefícios:
- Os membros da equipa podem colaborar com mais eficiência, ao entender rapidamente as alterações propostas e o seu impacto.
- As alterações indesejadas podem ser capturadas no início do processo de desenvolvimento

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Implementar infraestrutura em várias clouds

O Terraform é adepto na implantação de uma infraestrutura em vários provedores de nuvem. Ele permite que os desenvolvedores usem ferramentas consistentes para gerenciar cada definição de infraestrutura.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma descrição geral do Terraform e dos seus benefícios, eis os passos sugeridos:

- Comece a utilizar ao [instalar o Terraform e configurá-lo para utilizar o Azure](/azure/virtual-machines/linux/terraform-install-configure).
- [Criar uma máquina virtual do Azure com o Terraform](/azure/virtual-machines/linux/terraform-create-complete-vm)
- Explorar o [módulo do Azure Resource Manager para Terraform](https://www.terraform.io/docs/providers/azurerm/) 