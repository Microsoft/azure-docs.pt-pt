---
title: Utilizar o Terraform com o Azure
description: Introdução à utilização da Terraform para versão e implantação de infraestruturas Azure.
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 05b92fdf8c0a0f84d2f29b4aa7479850b2721441
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472167"
---
# <a name="terraform-with-azure"></a>Terraform com o Azure

O [Hashicorp Terraform](https://www.terraform.io/) é uma ferramenta de código-fonte aberto para aprovisionar e gerir infraestrutura de cloud. Codifica a infraestrutura em ficheiros de configuração que descrevem a topologia dos recursos em nuvem. Estes recursos incluem máquinas virtuais, contas de armazenamento e interfaces de networking. O CLI Terraform fornece um mecanismo simples para implementar e verver os ficheiros de configuração para o Azure.

Este artigo descreve os benefícios de utilizar o Terraform para gerir infraestrutura do Azure.

## <a name="automate-infrastructure-management"></a>Automatize a gestão da sua infraestrutura.

Os ficheiros de configuração baseados em modelos do Terraform permitem-lhe definir, aprovisionar e configurar recursos do Azure de forma previsível e repetível. Automatizar a infraestrutura oferece vários benefícios:

- Reduz o potencial para erros humanos durante a implementação e gestão de infraestrutura.
- Implementa o mesmo modelo várias vezes para criar ambientes de desenvolvimento, teste e produção idênticos.
- Reduz o custo de ambientes de desenvolvimento e teste ao criá-los a pedido.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Compreender as mudanças de infraestrutura antes de ser aplicado

À medida que a topologia de recurso se torna complexa, compreender o significado e o impacto das alterações de infraestrutura pode ser difícil.

O CLI Terraform permite aos utilizadores validar e pré-visualizar alterações de infraestrutura antes da aplicação. A visualização das alterações de infraestrutura de forma segura tem vários benefícios:
- Os membros da equipa podem colaborar com mais eficiência, ao entender rapidamente as alterações propostas e o seu impacto.
- As alterações indesejadas podem ser capturadas no início do processo de desenvolvimento

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Implementar infraestrutura em várias clouds

A Terraform é adepta a implantar uma infraestrutura em vários fornecedores de nuvem. Permite que os desenvolvedores utilizem ferramentas consistentes para gerir cada definição de infraestrutura.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma descrição geral do Terraform e dos seus benefícios, eis os passos sugeridos:

- Comece a utilizar ao [instalar o Terraform e configurá-lo para utilizar o Azure](terraform-install-configure.md).
- [Criar uma máquina virtual do Azure com o Terraform](terraform-create-complete-vm.md)
- Explorar o [módulo do Azure Resource Manager para Terraform](https://www.terraform.io/docs/providers/azurerm/) 