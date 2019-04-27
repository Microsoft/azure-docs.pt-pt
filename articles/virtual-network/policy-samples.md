---
title: Exemplos de modelo de política | Microsoft Docs
description: Exemplos de modelo de política do Azure para a Rede Virtual.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: b0b3b65ee2360a5cec32235f2ee5bf8d4839cc8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679617"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Exemplos de modelo de política do Azure para a rede virtual

A tabela seguinte inclui ligações para [do Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) exemplos. Os exemplos encontram-se no [repositório de exemplos do Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Rede**||
| [NSG X em cada NIC](../governance/policy/samples/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que um grupo de segurança de rede específico seja utilizado em cada interface de rede virtual. Especifica o ID do grupo de segurança de rede a utilizar. |
| [NSG X em cada sub-rede](../governance/policy/samples/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que um grupo de segurança de rede específico seja utilizado em cada sub-rede virtual. Especifica o ID do grupo de segurança de rede a utilizar. |
| [Sem tabela de rotas](../governance/policy/samples/no-user-defined-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Proíbe que as redes virtuais sejam implementadas com uma tabela de rotas. |
| [Utilizar sub-rede aprovada para interfaces de rede de VM](../governance/policy/samples/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que as interfaces de rede utilizem uma sub-rede aprovada. Especifica o ID da sub-rede aprovada. |
| [Utilizar vNet aprovada para interfaces de rede de VM](../governance/policy/samples/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que as interfaces de rede utilizem uma rede virtual aprovada. Especifica o ID da rede virtual aprovada. |
|**Monitorização**||
| [Definição de diagnóstico de auditoria](../governance/policy/samples/audit-diagnostic-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Audita se as definições de diagnóstico não estão ativadas para tipos de recursos especificados. Especifica uma matriz de tipos de recursos para verificar se as definições de diagnóstico estão ativadas. |
|**Convenções de nome e texto**||
| [Permitir múltiplos padrões de nome](../governance/policy/samples/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Permita que um dos muitos padrões de nomes seja utilizado para recursos. |
| [Exigir padrão like](../governance/policy/samples/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Certifique-se de que os nomes de recursos cumprem a condição *like* de um padrão. |
| [Exigir padrão de correspondência](../governance/policy/samples/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Certifique-se de que os nomes de recursos correspondem a um padrão de nomenclatura especificado. |
| [Exigir padrão de correspondência de etiqueta](../governance/policy/samples/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Certifique-se de que um valor de etiqueta corresponde a um padrão de texto. |
|**Etiquetas**||
| [Iniciativa de política de etiquetas de faturação](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige valores de etiqueta especificados para o nome do produto e o centro de custos. Utiliza políticas incorporadas para aplicar e impor etiquetas necessárias. O utilizador especifica os valores necessários para as etiquetas.  |
| [Impor a etiqueta e o respetivo valor em grupos de recursos](../governance/policy/samples/enforce-tag-on-resource-groups.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige uma etiqueta e um valor num grupo de recursos. Especifica o nome de etiqueta e o valor necessário.  |
| [Impor etiqueta e o respetivo valor](../governance/policy/samples/enforce-tag-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige um nome de etiqueta e um valor especificado. Especifica o nome de etiqueta e o valor a impor.  |
| [Aplicar etiqueta e o respetivo valor predefinido](../governance/policy/samples/apply-tag-default-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Acrescenta um nome de etiqueta e um valor especificado, se essa etiqueta não for fornecida. Especifica o nome de etiqueta e o valor a aplicar.  |
|**Geral**||
| [Localizações permitidas](../governance/policy/samples/allowed-locations.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que todos os recursos sejam implementados nas localizações aprovadas. Especifica uma matriz de localizações aprovadas.  |
| [Tipos de recursos permitidos](../governance/policy/samples/allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Garante que apenas os tipos de recursos aprovados são implementados. Especifica uma matriz dos tipos de recursos que são permitidos.  |
| [Tipos de recursos não permitidos](../governance/policy/samples/not-allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Proíbe a implementação de tipos de recursos especificados. Especifica uma matriz dos tipos de recursos a bloquear.  |