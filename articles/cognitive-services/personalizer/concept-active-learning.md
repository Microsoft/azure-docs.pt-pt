---
title: Eventos ativos e inativos – personalizador
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 321f12fef44cae43caf53d78b2908e68f9edd0a8
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043892"
---
# <a name="active-and-inactive-events"></a>Eventos ativos e inativos

Quando o aplicativo chama a API de classificação, você recebe a ação que o aplicativo deve mostrar no campo rewardActionId.  A partir desse momento, o personalizador estará esperando uma chamada de recompensa com o mesmo eventId. A pontuação de recompensa será usada para treinar o modelo que será usado para futuras chamadas de classificação. Se nenhuma chamada de recompensa for recebida para o eventId, um prêmio bits será aplicado. As recompensas padrão são estabelecidas no portal do Azure.

Em alguns casos, o aplicativo pode precisar chamar a classificação antes mesmo de saber se o resultado será usado ou exibido para o usuário. Isso pode acontecer em situações em que, por exemplo, a renderização de página de conteúdo promovido é substituída por uma campanha de marketing. Se o resultado da chamada de classificação nunca foi usado e o usuário nunca conseguiu vê-la, seria incorreto treiná-la com qualquer recompensa, zero ou de outra forma.
Normalmente, isso acontece quando:

* Você pode estar renderizando previamente alguma interface de usuário que o usuário pode ou não conseguir ver. 
* Seu aplicativo pode estar fazendo uma personalização preditiva na qual as chamadas de classificação são feitas com um contexto menos em tempo real e sua saída pode ou não ser usada pelo aplicativo. 

Nesses casos, a maneira correta de usar o personalizador é chamar rank solicitando que o evento fique _inativo_. O personalizador não esperará um prêmio para esse evento e também não aplicará uma recompensa padrão. Posteriormente, em sua lógica de negócios, se o aplicativo usar as informações da chamada de classificação, tudo o que você precisará fazer é _Ativar_ o evento. A partir do momento em que o evento está ativo, o personalizador esperará um prêmio para o evento ou aplicará um recompensa padrão se nenhuma chamada explícita for feita na API de recompensa.

## <a name="get-inactive-events"></a>Obter eventos inativos

Para desabilitar o treinamento de um evento, chame Rank com `learningEnabled = False`.

O aprendizado de um evento inativo será ativado implicitamente se você enviar uma recompensa para o eventId ou chamar a API `activate` para esse eventId.

## <a name="learning-settings"></a>Configurações de aprendizado

As configurações de aprendizado determinam os *hiperparâmetros* específicos do treinamento do modelo. Dois modelos dos mesmos dados, treinados em diferentes configurações de aprendizado, acabarão sendo diferentes.

### <a name="import-and-export-learning-policies"></a>Importar e exportar políticas de aprendizado

Você pode importar e exportar arquivos de política de aprendizagem do portal do Azure. Isso permite que você salve as políticas existentes, teste-as, substitua-as e arquive-as no controle do código-fonte como artefatos para referência e auditoria futuras.

### <a name="learning-policy-settings"></a>Configurações de política de aprendizagem

As configurações na **política de aprendizado** não devem ser alteradas. Só altere as configurações quando entender como elas afetam o personalizador. Alterar as configurações sem esse conhecimento causará efeitos colaterais, incluindo a invalidação de modelos de personalização.

### <a name="comparing-effectiveness-of-learning-policies"></a>Comparando a eficácia das políticas de aprendizado

Você pode comparar como as diferentes políticas de aprendizado teriam realizado em relação aos dados anteriores nos logs do personalizado, fazendo [avaliações offline](concepts-offline-evaluation.md).

[Carregue suas próprias políticas de aprendizado](how-to-offline-evaluation.md) para comparar com a política de aprendizado atual.

### <a name="discovery-of-optimized-learning-policies"></a>Descoberta de políticas de aprendizagem otimizadas

O personalizador pode criar uma política de aprendizado mais otimizada ao fazer uma [avaliação offline](how-to-offline-evaluation.md). Uma política de aprendizado mais otimizada, que é mostrada para obter melhores recompensas em uma avaliação offline, produzirá resultados melhores quando usados online no personalizador.

Depois que uma política de aprendizado otimizado tiver sido criada, você poderá aplicá-la diretamente ao personalizador para que ele substitua a política atual imediatamente ou você possa salvá-la para uma avaliação adicional e decidir no futuro se deseja descartá-la, salvá-la ou aplicá-la posteriormente.
