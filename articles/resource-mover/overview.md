---
title: O que é Azure Resource Mover?
description: Saiba mais sobre a Azure Resource Mover
author: rayne-wiselman
manager: evansma
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 621c5e7b0061ccd76fd0109552107915b943511f
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653622"
---
# <a name="what-is-azure-resource-mover"></a>O que é Azure Resource Mover?

Este artigo fornece uma visão geral do serviço Azure Resource Mover. A Resource Mover ajuda-o a mover recursos Azure entre as regiões de Azure.

Você pode mover recursos para diferentes regiões de Azure para:

- **Alinhar para um lançamento da região**: Mover recursos para uma região de Azure recém-introduzida que não estava disponível anteriormente.
- **Alinhar para serviços/funcionalidades**: Mover recursos para tirar partido de serviços ou funcionalidades que estão disponíveis numa região específica.
- **Responder à evolução do negócio**: Mover recursos para uma região em resposta a mudanças de negócio, tais como fusões ou aquisições.
- **Alinhe-se para a proximidade**: Mova recursos para uma região local para o seu negócio.
- **Satisfazer os requisitos de dados**: Mover recursos para alinhar com os requisitos de residência de dados, ou necessidades de classificação de dados.
- **Responder aos requisitos de implantação**: Mover recursos que foram implantados por engano, ou mover-se em resposta às necessidades de capacidade.
- **Responda ao desmantelamento:** Mova recursos porque uma região é desativada.

> [!IMPORTANT]
> A Azure Resource Mover está atualmente em pré-visualização pública.

## <a name="why-use-resource-mover"></a>Porquê usar o Resource Mover?

O Resource Mover fornece:

- Um único centro para movimentar recursos através das regiões.
- Tempo de movimento reduzido e complexidade. Tudo o que precisa está num único local.
- Uma experiência simples e consistente para mover diferentes tipos de recursos Azure.
- Uma maneira fácil de identificar dependências entre recursos que quer mover. Isto ajuda-o a mover recursos relacionados em conjunto, para que tudo funcione como esperado na região alvo, após a mudança.
- Limpeza automática dos recursos na região de origem, se quiser eliminá-los após a mudança.
- A testar. Podes experimentar um movimento, e depois deitá-lo fora se não quiseres fazer um movimento completo.

## <a name="move-across-regions"></a>Mover entre regiões

Para mover recursos através das regiões, seleciona os recursos que pretende mover. A Resource Mover valida esses recursos e resolve quaisquer dependências que tenham sobre outros recursos. Se houver dependências, tem algumas opções:
- Mover os recursos dependentes para a região alvo.
- Não mova os recursos dependentes, mas use recursos equivalentes na região alvo.

Depois de todas as dependências serem resolvidas, a Resource Mover acompanha-o através de um processo de movimento simples.

1. Dás o pontapé inicial.
2. Após o movimento inicial, pode decidir se compromete e completa o movimento, ou se descarta o movimento.
3. Depois de a mudança ser feita, pode decidir se pretende eliminar os recursos no local de origem.

Você pode mover recursos através de regiões no centro de Recursos Mover, ou de dentro de um grupo de recursos. [Saiba mais](select-move-tool.md)

## <a name="what-resources-can-i-move-across-regions"></a>Que recursos posso deslocar-me através das regiões?

Utilizando o Resource Mover, pode atualmente mover os seguintes recursos através das regiões:

- VMs Azure e discos associados
- NICs
- Conjuntos de disponibilidade 
- Redes virtuais do Azure 
- Endereços IP públicos
- Grupos de segurança de rede (NSGs)
- Equilibradores de carga interna e pública 
- Bases de dados Azure SQL e piscinas elásticas


## <a name="next-steps"></a>Próximos passos

[Saiba mais](about-move-process.md) sobre os componentes do Resource Mover e o processo de mudança.
