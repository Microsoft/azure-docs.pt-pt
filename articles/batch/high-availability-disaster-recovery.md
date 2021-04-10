---
title: Elevada disponibilidade e recuperação após desastre
description: Saiba como desenhar a sua aplicação batch para uma paragem regional.
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: 51bcb0cfa35aacd24c0f79082491ef1fc7040889
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831011"
---
# <a name="design-your-batch-application-for-high-availability"></a>Desenhe a sua aplicação batch para alta disponibilidade

O Azure Batch está disponível em todas as regiões do Azure, mas quando uma conta Batch é criada deve ser associada a uma região específica. Todas as operações para a conta Batch aplicam-se então a essa região. Por exemplo, piscinas e máquinas virtuais associadas (VMs) são criadas na mesma região que a conta Batch.

Ao conceber uma aplicação que utilize o Batch, deve considerar a possibilidade de o Batch não estar disponível numa região. É possível encontrar uma situação rara em que há um problema com a região como um todo, todo o serviço Batch na região, ou a sua conta específica do Batch.

Se a aplicação ou solução que utiliza o Lote tiver sempre de estar disponível, então deve ser concebida para falhar para outra região ou ter sempre a carga de trabalho dividida entre duas ou mais regiões. Ambas as abordagens requerem pelo menos duas contas de Lote, com cada conta localizada numa região diferente.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Várias contas de Lote em várias regiões

A utilização de várias contas batch em várias regiões permite que a sua aplicação continue a funcionar se uma conta Batch numa região ficar indisponível. Se a sua aplicação precisa de estar altamente disponível, ter várias contas é especialmente importante.

Em alguns casos, as aplicações podem ser concebidas para utilizar intencionalmente duas ou mais regiões. Por exemplo, quando se precisa de uma quantidade considerável de capacidade, pode ser necessário utilizar várias regiões para lidar com uma aplicação em larga escala ou atender ao crescimento futuro. Estas aplicações também exigirão várias contas de Lote (uma por região utilizada).

## <a name="design-considerations-for-providing-failover"></a>Considerações de design para a prestação de failover

Ao proporcionar a capacidade de falhar numa região alternativa, é necessário considerar todos os componentes numa solução; não é suficiente ter simplesmente uma segunda conta batch. Por exemplo, na maioria das aplicações do Batch, é necessária uma conta de armazenamento Azure, com a conta de armazenamento e a conta Batch a precisarem de estar na mesma região para um desempenho aceitável.

Considere os seguintes pontos ao desenhar uma solução que possa falhar:

- Pré-criar todas as contas necessárias em cada região, como a conta Batch e a conta de armazenamento. Muitas vezes não há qualquer encargo por ter contas criadas, e os encargos acumulam-se apenas quando a conta é utilizada ou quando os dados são armazenados.
- Certifique-se de que as [quotas](batch-quota-limit.md) adequadas são definidas em todas as contas com antecedência, para que possa alocar o número necessário de núcleos utilizando a conta Batch.
- Utilize modelos e/ou scripts para automatizar a implementação da aplicação numa região.
- Mantenha binários de aplicações e dados de referência atualizados em todas as regiões. Manter-se atualizado garantirá que a região pode ser trazida on-line rapidamente sem ter que esperar pelo upload e implementação de ficheiros. Por exemplo, se uma aplicação personalizada para instalar nos nós de piscina for armazenada e referenciada utilizando pacotes de aplicações Batch, então quando uma nova versão da aplicação é produzida, deve ser carregada em cada conta batch e referenciada pela configuração do pool (ou fazer da nova versão a versão padrão).
- Na aplicação chamada Batch, armazenamento e quaisquer outros serviços, facilitam a troca de clientes ou a carga para diferentes regiões.
- Considere frequentemente mudar para uma região alternativa como parte do funcionamento normal. Por exemplo, com duas implantações em regiões distintas, passe para a região alternativa todos os meses.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a criação de contas batch com o [portal Azure,](batch-account-create-portal.md)o [Azure CLI,](./scripts/batch-cli-sample-create-account.md) [PowerShell](batch-powershell-cmdlets-get-started.md)ou a [API de gestão de lotes.](batch-management-dotnet.md)
- Saiba mais sobre as [quotas padrão associadas a uma conta Batch](batch-quota-limit.md) e como as quotas podem ser aumentadas.
