---
title: Mover recursos do Azure para outra região
description: Fornece uma visão geral da movimentação de recursos Azure em todas as regiões de Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75485211"
---
# <a name="moving-azure-resources-across-regions"></a>Movimentação de recursos Azure em regiões

Este artigo fornece informações sobre a movimentação de recursos Azure em todas as regiões de Azure.

Geografias, regiões e Zonas de Disponibilidade do Azul formam a base da infraestrutura global do Azure. As [geografias azul normalmente](https://azure.microsoft.com/global-infrastructure/geographies/) contêm duas ou mais [regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/) Uma região é uma área dentro de uma geografia, contendo Zonas de Disponibilidade, e vários centros de dados. 

Depois de mobilizar recursos na região específica de Azure, há uma série de razões pelas quais talvez queira mover recursos para uma região diferente.

- **Alinhe-se com um lançamento da região**: Mova os seus recursos para uma região de Azure recém-introduzida que não estava disponível anteriormente.
- **Alinhar para serviços/funcionalidades**: Mover recursos para tirar partido de serviços ou funcionalidades que estão disponíveis numa região específica.
- **Responder à evolução do negócio**: Mover recursos para uma região em resposta a mudanças de negócio, tais como fusões ou aquisições.
- **Alinhe-se para a proximidade**: Mova recursos para uma região local para o seu negócio.
- **Satisfazer os requisitos de dados**: Mover recursos para alinhar com os requisitos de residência de dados, ou necessidades de classificação de dados. [Saiba mais](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Responder aos requisitos de implantação**: Mover recursos que foram implantados por engano, ou mover-se em resposta às necessidades de capacidade. 
- **Responder ao desmantelamento**: Mover recursos devido ao desmantelamento das regiões.

## <a name="move-process"></a>Processo de mudança

O processo de mudança depende dos recursos que está a mover. No entanto, existem alguns passos-chave comuns:

- **Verifique os pré-requisitos**: Os pré-requisitos incluem garantir que os recursos de que necessita estão disponíveis na região alvo, verificar se tem quota suficiente e verificar se a sua subscrição pode aceder à região alvo.
- **Analisar dependências**: Os seus recursos podem ter dependências de outros recursos. Antes de se mover, descubra as dependências para que os recursos movidos continuem a funcionar como esperado após a mudança.
- **Prepare-se para o movimento**: Estes são os passos que toma na sua região primária antes da mudança. Por exemplo, pode precisar de exportar um modelo de Gestor de Recursos Azure ou começar a replicar recursos de origem para alvo.
- **Mover os recursos**: A forma como movimenta os recursos depende do que são. Você pode precisar de implementar um modelo na região alvo, ou falhar recursos para o alvo.
- **Descartar recursos-alvo**: Depois de mover recursos, talvez queira olhar para os recursos agora na região alvo, e decidir se há algo que não precisa.
- **Cometer o movimento**: Após verificar os recursos na região alvo, alguns recursos podem exigir uma ação de compromisso final. Por exemplo, numa região alvo que é agora a região principal, talvez precise de preparar a recuperação de desastres para uma nova região secundária. 
- **Limpe a fonte**: Finalmente, depois de tudo estar a funcionar na nova região, pode limpar e desativar os recursos que criou para a mudança, e recursos na sua região primária.



## <a name="next-steps"></a>Próximos passos

Para obter uma lista de recursos que apoiam a deslocação de regiões, consulte [o apoio à operação Move para recursos.](region-move-support.md)
