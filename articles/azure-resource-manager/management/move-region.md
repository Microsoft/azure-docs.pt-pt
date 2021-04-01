---
title: Mover recursos do Azure para outra região
description: Fornece uma visão geral da movimentação de recursos Azure em todas as regiões de Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7a71502ec361004079e0962d8bc6433316a4ba81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90007643"
---
# <a name="moving-azure-resources-across-regions"></a>Movimentação de recursos Azure em regiões

Este artigo fornece informações sobre a movimentação de recursos Azure em todas as regiões de Azure.

Geografias, regiões e zonas de disponibilidade azure formam a base da infraestrutura global do Azure. As [geografias azul normalmente](https://azure.microsoft.com/global-infrastructure/geographies/) contêm duas ou mais [regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/) Uma região é uma área dentro de uma geografia, contendo Zonas de Disponibilidade, e vários centros de dados. 

Depois de mobilizar recursos na região específica de Azure, há uma série de razões pelas quais talvez queira mover recursos para uma região diferente.

- **Alinhe-se com um lançamento da região**: Mova os seus recursos para uma região de Azure recém-introduzida que não estava disponível anteriormente.
- **Alinhar para serviços/funcionalidades**: Mover recursos para tirar partido de serviços ou funcionalidades que estão disponíveis numa região específica.
- **Responder à evolução do negócio**: Mover recursos para uma região em resposta a mudanças de negócio, tais como fusões ou aquisições.
- **Alinhe-se para a proximidade**: Mova recursos para uma região local para o seu negócio.
- **Satisfazer os requisitos de dados**: Mover recursos para alinhar com os requisitos de residência de dados, ou necessidades de classificação de dados. [Saiba mais](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Responder aos requisitos de implantação**: Mover recursos que foram implantados por engano, ou mover-se em resposta às necessidades de capacidade. 
- **Responder ao desmantelamento**: Mover recursos devido ao desmantelamento das regiões.

## <a name="move-resources-with-resource-mover"></a>Mover recursos com o Mover de Recursos

Você pode mover recursos para uma região diferente com [Azure Resource Mover](../../resource-mover/overview.md). O Resource Mover fornece:

- Um único centro para movimentar recursos através das regiões.
- Tempo de movimento reduzido e complexidade. Tudo o que precisa está num único local.
- Uma experiência simples e consistente para mover diferentes tipos de recursos Azure.
- Uma maneira fácil de identificar dependências entre recursos que quer mover. Isto ajuda-o a mover recursos relacionados em conjunto, para que tudo funcione como esperado na região alvo, após a mudança.
- Limpeza automática dos recursos na região de origem, se quiser eliminá-los após a mudança.
- A testar. Podes experimentar um movimento, e depois deitá-lo fora se não quiseres fazer um movimento completo.

Você pode mover recursos para outra região usando um par de métodos diferentes:

- **Comece a mover recursos de um grupo de recursos**: Com este método, inicia-se a mudança da região de dentro de um grupo de recursos. Depois de selecionar os recursos que pretende mover, o processo continua no hub de Resource Mover, para verificar as dependências de recursos e orquestrar o processo de movimento. [Saiba mais](../../resource-mover/move-region-within-resource-group.md).
- **Comece a mover recursos diretamente do centro de Recursos Mover**: Com este método, inicia o processo de movimentação da região diretamente no centro. [Saiba mais](../../resource-mover/tutorial-move-region-virtual-machines.md).


## <a name="support-for-region-move"></a>Apoio ao movimento da região

Atualmente pode utilizar o Resource Mover para mover estes recursos para outra região:

- VMs Azure e discos associados
- NICs
- Conjuntos de disponibilidade
- Redes virtuais do Azure
- Endereços IP públicos
- Grupos de segurança de rede (NSGs)
- Equilibradores de carga interna e pública
- Bases de dados Azure SQL e piscinas elásticas

## <a name="region-move-process"></a>Processo de mudança de região

O processo real de movimentação de recursos através das regiões depende dos recursos que está a mover. No entanto, existem alguns passos-chave comuns:

1. **Verifique os pré-requisitos**: Os pré-requisitos incluem garantir que os recursos de que necessita estão disponíveis na região alvo, verificar se tem quota suficiente e verificar se a sua subscrição pode aceder à região alvo.
2. **Analisar dependências**: Os seus recursos podem ter dependências de outros recursos. Antes de se mover, descubra as dependências para que os recursos movidos continuem a funcionar como esperado após a mudança.
3. **Prepare-se para o movimento**: Estes são os passos que toma na sua região primária antes da mudança. Por exemplo, pode precisar de exportar um modelo de Gestor de Recursos Azure ou começar a replicar recursos de origem para alvo.
4. **Mover os recursos**: A forma como movimenta os recursos depende do que são. Você pode precisar de implementar um modelo na região alvo, ou falhar recursos para o alvo.
5. **Descartar recursos-alvo**: Depois de mover recursos, talvez queira olhar para os recursos agora na região alvo, e decidir se há algo que não precisa.
6. **Cometer o movimento**: Após verificar os recursos na região alvo, alguns recursos podem exigir uma ação de compromisso final. Por exemplo, numa região alvo que é agora a região principal, talvez precise de preparar a recuperação de desastres para uma nova região secundária. 
7. **Limpe a fonte**: Finalmente, depois de tudo estar a funcionar na nova região, pode limpar e desativar os recursos que criou para a mudança, e recursos na sua região primária.



## <a name="next-steps"></a>Passos seguintes

[Saiba mais](../../resource-mover/about-move-process.md) sobre o processo de mudança em Resource Mover.
