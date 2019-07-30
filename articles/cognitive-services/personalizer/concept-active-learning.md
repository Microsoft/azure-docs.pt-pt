---
title: Aprendizado ativo-personalizador
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
ms.openlocfilehash: 8c1579be3d11ae14ca45ee861de2d4f705e5d62c
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663718"
---
# <a name="active-learning-and-learning-policies"></a>Políticas de aprendizagem e aprendizado ativas 

Quando o aplicativo chama a API de classificação, você recebe uma classificação do conteúdo. A lógica de negócios pode usar essa classificação para determinar se o conteúdo deve ser exibido para o usuário. Quando você exibe o conteúdo classificado, esse é um evento de classificação _ativa_ . Quando seu aplicativo não exibe esse conteúdo classificado, isso é um evento de classificação inativo. 

As informações de evento de classificação ativas são retornadas ao personalizador. Essas informações são usadas para continuar a treinar o modelo por meio da política de aprendizado atual.

## <a name="active-events"></a>Eventos ativos

Os eventos ativos devem ser sempre mostrados ao usuário e a chamada de recompensa deve ser retornada para fechar o loop de aprendizagem. 

### <a name="inactive-events"></a>Eventos inativos 

Eventos inativos não devem alterar o modelo subjacente porque o usuário não tinha a oportunidade de escolher o conteúdo classificado.

## <a name="dont-train-with-inactive-rank-events"></a>Não treinar com eventos de classificação inativos 

Para alguns aplicativos, talvez seja necessário chamar a API de classificação sem saber ainda se o aplicativo exibirá os resultados para o usuário. 

Isso acontece quando:

* Você pode estar renderizando previamente alguma interface de usuário que o usuário pode ou não conseguir ver. 
* Seu aplicativo pode estar fazendo uma personalização preditiva na qual as chamadas de classificação são feitas com um contexto menos em tempo real e sua saída pode ou não ser usada pelo aplicativo. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Desabilitar o aprendizado ativo para eventos de classificação inativos durante a chamada de classificação

Para desabilitar o aprendizado automático, chame a classificação `learningEnabled = False`com.

O aprendizado de um evento inativo será ativado implicitamente se você enviar uma recompensa para a classificação.

## <a name="learning-policies"></a>Políticas de aprendizado

A política de aprendizado determina os hiperparâmetros específicos do treinamento do modelo. Dois modelos dos mesmos dados, treinados em diferentes políticas de aprendizagem, irão se comportar de maneira diferente.

### <a name="importing-and-exporting-learning-policies"></a>Importando e exportando políticas de aprendizado

Você pode importar e exportar arquivos de política de aprendizagem do portal do Azure. Isso permite que você salve as políticas existentes, teste-as, substitua-as e arquive-as no controle do código-fonte como artefatos para referência e auditoria futuras.

### <a name="learning-policy-settings"></a>Configurações de política de aprendizagem

As configurações na **política de aprendizado** não devem ser alteradas. Só altere as configurações quando entender como elas afetam o personalizador. Alterar as configurações sem esse conhecimento causará efeitos colaterais, incluindo a invalidação de modelos de personalização.

### <a name="comparing-effectiveness-of-learning-policies"></a>Comparando a eficácia das políticas de aprendizado

Você pode comparar como as diferentes políticas de aprendizado teriam realizado em relação aos dados anteriores nos logs do personalizado, fazendo [avaliações offline](concepts-offline-evaluation.md).

[Carregue suas próprias políticas de aprendizado](how-to-offline-evaluation.md) para comparar com a política de aprendizado atual.

### <a name="discovery-of-optimized-learning-policies"></a>Descoberta de políticas de aprendizagem otimizadas

O personalizador pode criar uma política de aprendizado mais otimizada ao fazer uma [avaliação offline](how-to-offline-evaluation.md). Uma política de aprendizado mais otimizada, que é mostrada para obter melhores recompensas em uma avaliação offline, produzirá resultados melhores quando usados online no personalizador.

Depois que uma política de aprendizado otimizado tiver sido criada, você poderá aplicá-la diretamente ao personalizador para que ele substitua a política atual imediatamente ou você possa salvá-la para uma avaliação adicional e decidir no futuro se deseja descartá-la, salvá-la ou aplicá-la posteriormente.