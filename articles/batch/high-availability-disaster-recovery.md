---
title: Elevada disponibilidade e recuperação após desastre
description: Saiba como desenhar a sua aplicação batch para uma paragem regional.
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 1e22cb19aba1dcedc4ece7ddc2d1de0ab3233238
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83725742"
---
# <a name="design-your-application-for-high-availability"></a>Conceber a sua aplicação para elevada disponibilidade

Azure Batch é um serviço regional. O lote está disponível em todas as regiões do Azure, mas quando uma conta Batch é criada deve ser associada a uma região. Todas as operações para a conta Batch aplicam-se então a essa região. Por exemplo, piscinas e máquinas virtuais associadas (VMs) são criadas na mesma região que a conta Batch.

Ao conceber uma aplicação que utilize o Batch, deve considerar a possibilidade de o Batch não estar disponível numa região. É possível encontrar uma situação rara em que há um problema com a região como um todo, todo o serviço Batch na região, ou um problema com a sua conta específica do Batch.

Se a aplicação ou solução que utiliza o Lote tiver sempre de estar disponível, então deve ser concebida para falhar para outra região ou ter sempre a carga de trabalho dividida entre duas ou mais regiões. Ambas as abordagens requerem pelo menos duas contas de Lote, com cada conta localizada numa região diferente.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Várias contas de Lote em várias regiões

A utilização de várias contas batch em várias regiões fornece a capacidade para a sua aplicação continuar a funcionar se uma conta Batch noutra região ficar indisponível. A utilização de várias contas é especialmente importante se a sua aplicação necessitar de estar altamente disponível.

Em alguns casos, uma aplicação pode ser concebida para utilizar sempre duas ou mais regiões. Por exemplo, quando se precisa de uma quantidade considerável de capacidade, pode ser necessário utilizar várias regiões para lidar com uma aplicação em larga escala ou atender ao crescimento futuro.

## <a name="design-considerations-for-providing-failover"></a>Considerações de design para a prestação de failover

Um ponto-chave a ter em conta quando se proporciona a capacidade de falhar numa região alternativa é que todos os componentes de uma solução devem ser considerados; não é suficiente ter simplesmente uma segunda conta batch. Por exemplo, na maioria das aplicações do Batch, é necessária uma conta de armazenamento Azure, com a conta de armazenamento e a conta Batch a precisarem de estar na mesma região para um desempenho aceitável.

Considere os seguintes pontos ao desenhar uma solução que possa falhar:

- Pré-criar todas as contas necessárias em cada região, como a conta Batch e a conta de armazenamento. Muitas vezes não há qualquer encargo por ter contas criadas, apenas quando há dados armazenados ou a conta é utilizada.
- Certifique-se de que as quotas são definidas nas contas com antecedência, para que possa alocar o número necessário de núcleos utilizando a conta Batch.
- Utilize modelos e/ou scripts para automatizar a implementação da aplicação numa região.
- Mantenha binários de aplicações e dados de referência atualizados em todas as regiões. Manter-se atualizado garantirá que a região pode ser trazida on-line rapidamente sem ter que esperar pelo upload e implementação de ficheiros. Por exemplo, se uma aplicação personalizada para instalar nos nós de piscina for armazenada e referenciada utilizando pacotes de aplicações Batch, então quando uma nova versão da aplicação é produzida, deve ser carregada em cada conta batch e referenciada pela configuração do pool (ou fazer da nova versão a versão padrão).
- Na aplicação chamando Batch, armazenamento e quaisquer outros serviços, facilmente trocam clientes ou a carga para a região diferente.
- Uma boa prática para garantir que um fracasso seja bem sucedido é a transição frequente para uma região alternativa como parte do funcionamento normal. Por exemplo, com duas implantações em regiões distintas, a transição para a região alternativa todos os meses.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a criação de contas batch com o [portal Azure,](batch-account-create-portal.md)o [Azure CLI,](cli-samples.md) [PowerShell](batch-powershell-cmdlets-get-started.md)ou a [API de gestão de lotes.](batch-management-dotnet.md)
- As quotas predefinidas estão associadas a uma conta Batch; [este artigo](batch-quota-limit.md) detalha os valores de quota padrão e descreve como as quotas podem ser aumentadas.
