---
title: Alta disponibilidade e recuperação de desastres - Lote Azure
description: Saiba como conceber a sua aplicação Batch para uma paragem regional. As cargas de trabalho devem falhar noutra região ou ser divididas entre duas ou mais regiões.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2019
ms.author: labrenne
ms.openlocfilehash: 84b0cce9557b4ae05586579f175cd0f5db14fdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77026086"
---
# <a name="design-your-application-for-high-availability"></a>Conceber a sua aplicação para elevada disponibilidade

Azure Batch é um serviço regional. O lote está disponível em todas as regiões do Azure, mas quando uma conta de Lote é criada deve ser associada a uma região. Todas as operações para a conta Batch aplicam-se então a essa região. Por exemplo, piscinas e máquinas virtuais associadas (VMs) são criadas na mesma região que a conta Batch.

Ao conceber uma aplicação que utilize o Batch, deve considerar a possibilidade de o Lote não estar disponível numa região. É possível encontrar uma situação rara em que há um problema com a região como um todo, todo o serviço de Lote na região, ou um problema com a sua conta de Lote específica.

Se a aplicação ou solução que utiliza o Batch tiver sempre de estar disponível, então deve ser concebida para falhar noutra região ou ter sempre a carga de trabalho dividida entre duas ou mais regiões. Ambas as abordagens requerem pelo menos duas contas de Lote, com cada conta localizada numa região diferente.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Contas de Lote Múltiplas em várias regiões

A utilização de várias contas do Batch em várias regiões fornece a capacidade de a sua aplicação continuar a funcionar se uma conta de Lote noutra região ficar indisponível. A utilização de várias contas é especialmente importante se a sua aplicação precisar de estar altamente disponível.

Em alguns casos, uma aplicação pode ser concebida para sempre utilizar duas ou mais regiões. Por exemplo, quando se precisa de uma quantidade considerável de capacidade, a utilização de várias regiões pode ser necessária para lidar com uma aplicação em larga escala ou para atender ao crescimento futuro.

## <a name="design-considerations-for-providing-failover"></a>Considerações de conceção para fornecer failover

Um ponto-chave a ter em conta ao proporcionar a capacidade de falhar a uma região alternativa é que todos os componentes de uma solução devem ser considerados; não basta simplesmente ter uma segunda conta batch. Por exemplo, na maioria das aplicações do Batch, é necessária uma conta de armazenamento Azure, com a conta de armazenamento e a conta batch a necessitarem de estar na mesma região para um desempenho aceitável.

Considere os seguintes pontos ao conceber uma solução que possa falhar:

- Pré-criar todas as contas necessárias em cada região, como a conta Batch e a conta de armazenamento. Muitas vezes não há qualquer encargo por ter contas criadas, apenas quando há dados armazenados ou a conta é usada.
- Certifique-se de que as quotas são definidas nas contas com antecedência, para que possa alocar o número necessário de núcleos utilizando a conta Batch.
- Utilize modelos e/ou scripts para automatizar a implementação da aplicação numa região.
- Mantenha os binários de aplicação e os dados de referência atualizados em todas as regiões. Manter-se atualizado garantirá que a região pode ser trazida online rapidamente sem ter que esperar pelo upload e implementação de ficheiros. Por exemplo, se uma aplicação personalizada para instalar em nós de piscina for armazenada e referenciada utilizando pacotes de aplicação Batch, então quando uma nova versão da aplicação for produzida, deve ser enviada para cada conta do Lote e referenciada pela configuração do pool (ou fazer da nova versão a versão padrão).
- Na aplicação que chama Lote, armazenamento e quaisquer outros serviços, facilmente troca clientes ou a carga para a região diferente.
- As melhores práticas para garantir que uma falha será bem sucedida é mudar frequentemente para uma região alternativa como parte do funcionamento normal. Por exemplo, com duas implantações em regiões distintas, a transição para a região alternativa todos os meses.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a criação de contas de Lote com o [portal Azure,](batch-account-create-portal.md)o [Azure CLI,](cli-samples.md) [PowerShell](batch-powershell-cmdlets-get-started.md)ou a API de gestão de [Lotes.](batch-management-dotnet.md)
- As quotas por defeito estão associadas a uma conta Batch; [este artigo](batch-quota-limit.md) detalha os valores das quotas por defeito e descreve como as quotas podem ser aumentadas.
