---
title: Elevada disponibilidade e recuperação após desastre – Azure Batch | Documentos da Microsoft
description: Saiba como conceber a sua aplicação do Batch para uma falha regional
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: lahugh
ms.openlocfilehash: b863785575263fedd144b3d599962a8e1559e0a3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60549757"
---
# <a name="design-your-application-for-high-availability"></a>Conceber a sua aplicação para elevada disponibilidade

O Azure Batch é um serviço regional. Batch está disponível em todas as regiões do Azure, mas quando é criada uma conta do Batch tem de ser associado a uma região. Em seguida, aplicam todas as operações para a conta do Batch para essa região. Por exemplo, os pools e máquinas de virtuais (VMs) associadas são criadas na mesma região que a conta do Batch.

Ao conceber uma aplicação que utiliza o Batch, tem de considerar a possibilidade de lote não estarem disponíveis numa região. É possível encontrar uma rara situação em que existe um problema com a região como um todo, o lote inteiro de serviço na região ou um problema com a sua conta de Batch específico.

Se a aplicação ou solução através do Batch sempre tem de estar disponível, em seguida, ele deve ser criado para a ativação pós-falha para outra região ou tenha sempre a carga de trabalho divididos entre dois ou mais regiões. Ambas as abordagens exigem pelo menos duas contas do Batch, com cada conta localizada numa região diferente.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Várias contas do Batch em várias regiões

Utilizar várias contas do Batch em várias regiões fornece a capacidade para a sua aplicação continuar em execução, se uma conta do Batch noutra região ficar indisponível. Utilizar várias contas é especialmente importante se a sua aplicação tem de ser de elevada disponibilidade.

Em alguns casos, um aplicativo pode ser criado para utilizar sempre a duas ou mais regiões. Por exemplo, quando precisar de uma quantidade considerável de capacidade, através de várias regiões podem ser necessário para processar uma aplicação de larga escala, ou se adaptar para o crescimento futuro.

## <a name="design-considerations-for-providing-failover"></a>Considerações de design para o fornecimento de ativação pós-falha

Um ponto importante a considerar ao fornecer a capacidade de ativação pós-falha para uma região alternativa é que todos os componentes numa solução precisam ser consideradas; Não é suficiente simplesmente ter uma segunda conta de Batch. Por exemplo, na maioria dos aplicativos de lote, uma conta de armazenamento do Azure é necessária, com a conta de armazenamento e a conta do Batch que precise de ser na mesma região para um desempenho aceitável.

Ao conceber uma solução que pode ativação pós-falha, considere os seguintes pontos:

- Pré-crie todas as contas necessárias em cada região, por exemplo, a conta do Batch e a conta de armazenamento. Muitas vezes, não existe algum custo de ter contas criadas, somente quando houver dados armazenados ou a conta é utilizada.
- Certifique-se de quotas estão definidas nas contas antes do tempo, pelo que pode alocar o número necessário de núcleos com a conta do Batch.
- Utilize modelos de e/ou scripts para automatizar a implementação da aplicação numa região.
- Manter os binários de aplicativos e dados de referência atualizados em todas as regiões. Permaneça atualizado garantirá que a região pode ser colocada online rapidamente sem ter de esperar para o upload e a implantação de arquivos. Por exemplo, se um aplicativo personalizado para instalar em nós do conjunto é armazenado e referenciado com pacotes de aplicações do Batch, em seguida, quando uma nova versão do aplicativo é produzida, deve ser carregado para cada conta do Batch e referenciado pela configuração do conjunto (ou tornar a nova versão a versão predefinida).
- No aplicativo chamar Batch, armazenamento e quaisquer outros serviços, os clientes de alternância ou facilmente a carga para a região diferente.
- Certifique-se de que uma ativação pós-falha será concluída com êxito uma prática recomendada é frequentemente a alternância para uma região alternativa como parte da operação normal. Por exemplo, com duas implementações em regiões diferentes, alternância para a região alternativa todos os meses.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre a criação de contas do Batch com o [portal do Azure](batch-account-create-portal.md), o [CLI do Azure](cli-samples.md), [Powershell](batch-powershell-cmdlets-get-started.md), ou o [API de gestão do Batch](batch-management-dotnet.md).
- Quotas predefinidas estão associadas uma conta do Batch; [este artigo](batch-quota-limit.md) detalhes da cota predefinida valores e descreve como é possível aumentar as quotas.
