---
title: Evitar a supressão acidental - Ações de ficheiro sinuosa
description: Saiba mais sobre a eliminação suave para as partilhas de ficheiros Azure e como pode usá-lo para a recuperação de dados e evitar a eliminação acidental.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 96e3d5001d11455337ae092776a1a4c5c3738012
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83883073"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Evitar a supressão acidental de ações de ficheiros do Azure

O Azure Storage oferece agora uma eliminação suave para ações de ficheiros. A eliminação suave permite-lhe recuperar os seus dados quando estes são erroneamente eliminados por uma aplicação ou outro utilizador de conta de armazenamento.

## <a name="how-soft-delete-works"></a>Como a eliminação suave funciona

Quando ativado, a eliminação suave permite-lhe guardar e recuperar as suas ações de ficheiro quando são eliminadas. Quando os dados são eliminados, transita para um estado suave apagado em vez de ser permanentemente apagado. Pode configurar a quantidade de tempo que os dados apagados suaves são recuperáveis antes de ser eliminado permanentemente.

A eliminação suave pode ser ativada em ações de ficheiros novas ou existentes. O soft delete também é compatível com o retrocesso, por isso não é preciso fazer alterações nas suas aplicações para tirar partido das proteções de eliminação suave. 

No caso das ações de ficheiros premium eliminados, a quota de partilha de ficheiros (o tamanho provisionado de uma parte de ficheiro) é utilizada no cálculo total da quota de depósito até à data de validade da parte eliminada, quando a ação for totalmente eliminada.

### <a name="availability"></a>Disponibilidade

A eliminação suave para as ações de ficheiros Azure está disponível em todos os níveis de armazenamento, em todos os tipos de contas de armazenamento e em todas as regiões onde o Azure Files está disponível.

## <a name="configuration-settings"></a>Definições de configuração

A eliminação suave para as partilhas de ficheiros está ativada ao nível da conta de armazenamento, as definições de eliminação suave aplicam-se a todas as ações de ficheiro dentro de uma conta de armazenamento. Quando cria uma nova conta de armazenamento, a eliminação suave é desligada por defeito. A eliminação suave também está desligada por padrão para as contas de armazenamento existentes. Pode alternar suavemente apagar a qualquer momento.

Se ativar a eliminação suave das ações de ficheiros, elimine algumas ações de ficheiros e, em seguida, desative a eliminação suave, se as ações foram guardadas nesse período, ainda pode aceder e recuperar essas ações de ficheiro. Quando ativa a eliminação suave, também precisa de configurar o período de retenção.

O período de retenção indica o tempo em que as ações de ficheiros eliminados suaves são armazenadas e disponíveis para recuperação. Para ações de ficheiros explicitamente eliminadas, o relógio do período de retenção começa quando os dados são eliminados. Atualmente pode manter as ações apagadas suaves entre 1 e 365 dias.

Pode alterar o período de retenção de eliminação suave a qualquer momento. Um período de retenção atualizado só se aplicará às ações eliminadas após a atualização do período de retenção. As ações eliminadas antes da atualização do período de retenção expirarão com base no período de retenção que foi configurado quando esses dados foram eliminados.

Para eliminar permanentemente uma parte de ficheiro num estado de eliminação suave antes do seu tempo de validade, deve desapagar a parte, desativar suavemente a eliminação e, em seguida, apagar novamente a parte. Em seguida, deve reativar a eliminação suave, uma vez que quaisquer outras ações de ficheiro nessa conta de armazenamento ficarão vulneráveis a uma eliminação acidental enquanto a eliminação suave está desligada.

## <a name="pricing-and-billing"></a>Preços e faturação

Tanto as ações de ficheiros standard como premium são faturadas na capacidade utilizada quando eliminadas suaves, em vez da capacidade provisionada. Adicionalmente, as ações de ficheiropremium são faturadas à taxa de instantâneo enquanto no estado de eliminação suave. As ações de ficheiro padrão são faturadas à taxa normal enquanto no estado de eliminação suave. Não será cobrado por dados que sejam permanentemente eliminados após o período de retenção configurado.

Para obter mais informações sobre os preços do Armazenamento de Ficheiros Azure em geral, consulte a página de preços de armazenamento de [ficheiros Azure](https://azure.microsoft.com/pricing/details/storage/files/).

Quando inicialmente ativa a eliminação suave, recomendamos que utilize um pequeno período de retenção para entender melhor como a funcionalidade afeta a sua conta.

## <a name="next-steps"></a>Passos seguintes

Para aprender a ativar e utilizar o soft delete, continue a [permitir eliminar suavemente](storage-files-enable-soft-delete.md)