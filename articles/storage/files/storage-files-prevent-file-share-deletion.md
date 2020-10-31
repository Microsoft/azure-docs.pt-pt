---
title: Prevenir a eliminação acidental - Azure file shares
description: Saiba mais sobre a eliminação suave das ações de ficheiros Azure e como pode usá-lo para a recuperação de dados e prevenir a eliminação acidental.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 0fecc9fc954a1ac648e8f60badf69ad1d2e8f1cc
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126945"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Evitar a supressão acidental das ações de ficheiros Azure

O Azure Storage oferece agora uma exclusão suave para ações de ficheiros. A eliminação suave permite-lhe recuperar a sua parte do ficheiro quando é erroneamente eliminada por uma aplicação ou outro utilizador de conta de armazenamento.

## <a name="how-soft-delete-works"></a>Como a eliminação suave funciona

Quando a eliminação suave das ações de ficheiros Azure é ativada, se uma partilha de ficheiros for eliminada, ela transite para um estado de eliminação suave em vez de ser permanentemente apagada. Pode configurar a quantidade de tempo que os dados suaves eliminados são recuperáveis antes de serem permanentemente eliminados, e desembolsar a parte a qualquer momento durante este período de retenção. Depois de não ter sido desestado, a parte e todo o conteúdo, incluindo instantâneos, serão restaurados ao estado em que estava antes da eliminação. A eliminação suave funciona apenas num nível de partilha de ficheiros - os ficheiros individuais que são eliminados continuarão a ser permanentemente apagados.

A eliminação suave pode ser ativada em ações de ficheiros novas ou existentes. O soft delete também é compatível com o contrário, pelo que não tem de fazer alterações nas suas aplicações para tirar partido das proteções de eliminação suave. 

Para eliminar permanentemente uma partilha de ficheiros num estado de exclusão suave antes do seu termo, tem de desativar a parte, desativar a exclusão suave e, em seguida, apagar novamente a partilha. Em seguida, deve voltar a permitir a eliminação suave, uma vez que quaisquer outras ações de ficheiros nessa conta de armazenamento ficarão vulneráveis à eliminação acidental enquanto a eliminação suave estiver desligada.

Para as ações de ficheiros premium eliminados suavemente, a quota de ações de ficheiro (a dimensão prevista de uma ação de ficheiro) é utilizada no cálculo total da quota de conta de armazenamento até à data de validade da ação de ações eliminadas suavemente, quando a ação for totalmente eliminada.

## <a name="configuration-settings"></a>Definições de configuração

### <a name="enabling-or-disabling-soft-delete"></a>Ativar ou desativar a eliminação suave

A eliminação suave para ações de ficheiros é ativada ao nível da conta de armazenamento, por isso, as definições de eliminação suave aplicam-se a todas as ações de ficheiros dentro de uma conta de armazenamento. Pode ativar ou desativar a qualquer momento a eliminação suave. Quando criar uma nova conta de armazenamento, a exclusão suave para ações de ficheiros é desativada por padrão, pode activar-a durante a implementação ou a qualquer momento posterior. A eliminação suave permanecerá desativada por padrão para as contas de armazenamento existentes. Se tiver configurado [a cópia de segurança da partilha de ficheiros Azure](../../backup/azure-file-share-backup-overview.md) para uma partilha de ficheiros Azure, então a eliminação suave para ações de ficheiros Azure será automaticamente ativada na conta de armazenamento dessa ação.

Se ativar a eliminação suave para ações de ficheiros, elimine algumas ações de ficheiros e, em seguida, desative a eliminação suave, se as ações forem guardadas nesse período, ainda pode aceder e recuperar essas ações de ficheiros. Quando ativar a eliminação suave, também precisa de configurar o período de retenção.

### <a name="retention-period"></a>Período de retenção

O período de retenção é o período de tempo em que as ações de ficheiros apagadas suaves são armazenadas e disponíveis para recuperação. Para as ações de ficheiros que são explicitamente eliminadas, o relógio do período de retenção começa quando os dados são eliminados. Atualmente pode especificar um período de retenção entre 1 e 365 dias. Pode alterar o período de retenção de eliminação suave a qualquer momento. Um período de retenção atualizado só será aplicável às ações eliminadas após a atualização do período de retenção. As ações eliminadas antes da atualização do período de retenção expirarão com base no período de retenção configurado quando esses dados foram eliminados.

## <a name="pricing-and-billing"></a>Preços e faturação

Tanto as ações de ficheiros standard como premium são faturadas na capacidade utilizada quando eliminadas suavemente, em vez de capacidade suplêntida. Além disso, as ações de ficheiro premium são faturadas à taxa instantânea enquanto estão no estado de exclusão suave. As ações de ficheiros standard são faturadas à taxa normal enquanto estão no estado de exclusão suave. Não será cobrado por dados que sejam permanentemente eliminados após o período de retenção configurado.

Para obter mais informações sobre os preços do Armazenamento de Ficheiros Azure em geral, consulte a [página de preços de armazenamento de ficheiros Azure](https://azure.microsoft.com/pricing/details/storage/files/).

Quando inicialmente ativar a eliminação suave, recomendamos a utilização de um pequeno período de retenção para entender melhor como a funcionalidade afeta a sua conta.

## <a name="next-steps"></a>Passos seguintes

Para aprender a ativar e utilizar a eliminação suave, continue a ativar a [eliminação suave](storage-files-enable-soft-delete.md).
