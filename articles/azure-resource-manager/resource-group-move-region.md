---
title: Mover recursos do Azure para outra região
description: Fornece uma visão geral da movimentação de recursos do Azure entre regiões do Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308118"
---
# <a name="moving-azure-resources-across-regions"></a>Movendo recursos do Azure entre regiões

Este artigo fornece informações sobre como mover recursos do Azure entre regiões do Azure.

Geografias, regiões e Zonas de Disponibilidade do Azure formam a base da infraestrutura global do Azure. As regiões [geográficas](https://azure.microsoft.com/global-infrastructure/geographies/) do Azure normalmente contêm duas ou mais [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). Uma região é uma área em uma geografia, contendo Zonas de Disponibilidade e vários data centers. 

Depois de implantar recursos em uma região específica do Azure, há vários motivos pelos quais você pode querer mover recursos para uma região diferente.

- **Alinhar a uma região de lançamento**: Mova seus recursos para uma região do Azure introduzida recentemente que não estava disponível anteriormente.
- **Alinhar para serviços/recursos**: Mova os recursos para aproveitar os serviços ou recursos que estão disponíveis em uma região específica.
- **Responder a desenvolvimentos de negócios**: mover recursos para uma região em resposta a alterações comerciais, como fusões ou aquisições.
- **Alinhar**-se à proximidade: Mova os recursos para uma região local para sua empresa.
- **Atender aos requisitos de dados**: Mova os recursos para se alinhar aos requisitos de residência de dados ou às necessidades de classificação de dados. [Saiba mais](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Responder aos requisitos de implantação**: mover os recursos que foram implantados com erro ou mover-se em resposta às necessidades de capacidade. 
- **Responder ao encerramento**: mover recursos devido ao encerramento de regiões.

## <a name="move-process"></a>Mover processo

O processo de movimentação real depende dos recursos que você está movendo. No entanto, há algumas etapas principais comuns:

- **Verificar pré-requisitos**: os pré-requisitos incluem certificar-se de que os recursos necessários estão disponíveis na região de destino, verificando se você tem cota suficiente e verificando se sua assinatura pode acessar a região de destino.
- **Analisar dependências**: seus recursos podem ter dependências de outros recursos. Antes de mover, descubra as dependências para que os recursos movidos continuem a funcionar conforme o esperado após a movimentação.
- **Preparar para mover**: essas são as etapas que você seguirá em sua região primária antes da movimentação. Por exemplo, talvez seja necessário exportar um modelo de Azure Resource Manager ou iniciar a replicação de recursos da origem para o destino.
- **Mover os recursos**: como você move os recursos depende do que eles são. Talvez seja necessário implantar um modelo na região de destino ou reprovar os recursos para o destino.
- **Descartar recursos de destino**: depois de mover os recursos, talvez você queira dar uma olhada nos recursos agora na região de destino e decidir se há algo que você não precisa.
- **Confirme a movimentação**: depois de verificar os recursos na região de destino, alguns recursos podem exigir uma ação de confirmação final. Por exemplo, em uma região de destino que agora é a região primária, talvez seja necessário configurar a recuperação de desastres para uma nova região secundária. 
- **Limpar a origem**: por fim, depois que tudo estiver em funcionamento na nova região, você poderá limpar e encerrar os recursos criados para a movimentação e os recursos em sua região primária.



## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista dos recursos que dão suporte à movimentação entre regiões, consulte [mover suporte de operação para recursos](region-move-support.md).
