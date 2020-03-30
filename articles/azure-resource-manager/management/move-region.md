---
title: Mover os recursos do Azure para outra região
description: Fornece uma visão geral da movimentação de recursos Azure através das regiões de Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485211"
---
# <a name="moving-azure-resources-across-regions"></a>Recursos Azure em movimento em regiões

Este artigo fornece informações sobre a transferência de recursos Azure através das regiões de Azure.

Geografias, regiões e Zonas de Disponibilidade azure formam a base da infraestrutura global Azure. As [geografias](https://azure.microsoft.com/global-infrastructure/geographies/) azure normalmente contêm duas ou mais [regiões azure.](https://azure.microsoft.com/global-infrastructure/regions/) Uma região é uma área dentro de uma geografia, contendo Zonas de Disponibilidade, e múltiplos centros de dados. 

Depois de mobilizar recursos em região específica de Azure, existem várias razões para que queira mover recursos para outra região.

- **Alinha-te com um lançamento de região**: Move os teus recursos para uma região azure recém-introduzida que não estava disponível anteriormente.
- **Alinhar para serviços/funcionalidades**: Mover recursos para tirar partido de serviços ou funcionalidades disponíveis numa região específica.
- **Responder à evolução do negócio**: Transferir recursos para uma região em resposta a mudanças de negócio, tais como fusões ou aquisições.
- **Alinhe-se para a proximidade**: Desloque recursos para uma região local para o seu negócio.
- **Satisfazer os requisitos de dados**: Mover recursos para alinhar com os requisitos de residência de dados ou necessidades de classificação de dados. [Saiba mais](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Responder aos requisitos de implantação**: Mover recursos que foram utilizados por engano ou mover-se em resposta às necessidades de capacidade. 
- **Responder ao desmantelamento**: Movimentar recursos devido ao desmantelamento das regiões.

## <a name="move-process"></a>Processo de mudança

O processo de mudança depende dos recursos que estáa mover. No entanto, existem alguns passos-chave comuns:

- **Verificar os pré-requisitos**: Os pré-requisitos incluem certificar-se de que os recursos de que necessita estão disponíveis na região alvo, verificar se tem quota suficiente e verificar se a sua subscrição pode aceder à região alvo.
- **Analise as dependências**: Os seus recursos podem ter dependências de outros recursos. Antes de se mover, descubra as dependências para que os recursos movidos continuem a funcionar como esperado após a mudança.
- **Prepare-se para avançar**: Estes são os passos que toma na sua região primária antes da mudança. Por exemplo, você pode precisar de exportar um modelo de Gestor de Recursos Azure, ou começar a replicar recursos de origem para alvo.
- **Mover os recursos**: Como movimenta recursos depende do que são. Você pode precisar de implementar um modelo na região alvo, ou falhar recursos para o alvo.
- **Descartar recursos-alvo**: Depois de movimentar recursos, é melhor dar uma olhada nos recursos agora na região alvo, e decidir se há algo que não precisa.
- **Cometer o movimento**: Após a verificação dos recursos na região alvo, alguns recursos podem exigir uma ação final de compromisso. Por exemplo, numa região-alvo que é agora a região primária, talvez seja necessário estabelecer uma recuperação de desastres para uma nova região secundária. 
- **Limpe a fonte**: Finalmente, depois de tudo estar a funcionar na nova região, pode limpar e desativar os recursos que criou para a mudança, e recursos na sua região primária.



## <a name="next-steps"></a>Passos seguintes

Para obter uma lista dos recursos que suportam a deslocação através das regiões, consulte o apoio da [operação Move para os recursos.](region-move-support.md)
