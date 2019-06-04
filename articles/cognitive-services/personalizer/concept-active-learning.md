---
title: Aprendizagem ativa - Personalizer
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/30/2019
ms.author: edjez
ms.openlocfilehash: d758e8fc7952a414003746d39df9368f3274b08b
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481398"
---
# <a name="active-learning-and-learning-policies"></a>Aprendizagem ativa e políticas de aprendizagem 

Quando seu aplicativo chama a API de classificação, receberá uma classificação do conteúdo. Lógica de negócios pode utilizar esta classificação para determinar se o conteúdo deve ser apresentar ao usuário. Quando exibir o conteúdo classificado, ou seja uma _Active Directory_ eventos de classificação. Quando seu aplicativo não apresenta que classificados conteúdo, ou seja uma _Inativos_ eventos de classificação. 

Informações de eventos de classificação de Active Directory são retornadas para Personalizer. Estas informações são utilizadas para continuar a dar formação sobre o modelo através da política de aprendizado atual.

## <a name="active-events"></a>Eventos ativos

Eventos ativos sempre devem ser mostrados ao usuário e a chamada de recompensa deve ser retornada para fechar o loop de aprendizado. 

### <a name="inactive-events"></a>Eventos Inativos 

Eventos Inativos não devem alterar o modelo subjacente porque o utilizador não era a chance de escolher entre o conteúdo classificado.

## <a name="dont-train-with-inactive-rank-events"></a>Não treinar com eventos de classificação Inativos 

Para algumas aplicações, terá de chamar a API de classificação sem saber, ainda, se a aplicação irá apresentar os resultados ao usuário. 

Isto acontece quando:

* Pode ser composição previamente algumas interfaces do Usuário que o utilizador pode ou não pode obter para ver. 
* Seu aplicativo pode estar fazendo a previsão personalização no qual classificação chamadas são feitas com o contexto de menos em tempo real e sua saída pode ou não pode ser utilizada pela aplicação. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Desativar a aprendizagem ativa para eventos de classificação Inativos durante a chamada de classificação

Para desativar a aprendizagem automática, chame a classificação com `learningEnabled = False`.

Aprendizagem de um evento inativo implicitamente é ativada, se tiver de enviar uma recompensa para a classificação.

## <a name="learning-policies"></a>Políticas de aprendizagem

Política de aprendizagem determina as específicas *hiperparâmetros* de preparação de modelos. Dois modelos dos mesmos dados, com base em com políticas de aprendizagem diferente, irão ter um comportamento diferente.

### <a name="importing-and-exporting-learning-policies"></a>Importar e exportar políticas de aprendizagem

Pode importar e exportar learning ficheiros de política do portal do Azure. Isto permite-lhe guardar as políticas existentes, testá-los, substituí-los e arquivá-los em seu controle de código de origem como artefactos para referência futura e auditoria.

### <a name="learning-policy-settings"></a>Definições de política de aprendizagem

As definições no **Learning política** não se destinam a ser alterado. Altere apenas as definições, quando compreender seu impacto Personalizer. Alterar as definições sem esse conhecimento fará com que os efeitos colaterais, incluindo invalidar modelos Personalizer.

### <a name="comparing-effectiveness-of-learning-policies"></a>Comparando a eficácia das políticas de aprendizagem

É possível comparar diferentes políticas de Aprendizado ser realizada em relação a dados nos últimos nos registos de Personalizer ao fazê-lo [avaliações offline](concepts-offline-evaluation.md).

[Carregar as suas políticas de aprendizagem](how-to-offline-evaluation.md) a comparar com a política de aprendizado atual.

### <a name="discovery-of-optimized-learning-policies"></a>Deteção de políticas de learning otimizado

Personalizer pode criar uma política de aprendizado mais otimizada ao efetuar uma [avaliação offline](how-to-offline-evaluation.md). Uma política de aprendizado mais otimizada, o que é mostrada ter remunerações melhor numa edição de avaliação offline, irá produzir resultados melhores quando utilizadas online no Personalizer.

Depois de ter sido criada uma política de learning otimizado, pode aplicá-la diretamente Personalizer para que ele substitui a política atual imediatamente ou pode guardá-lo para avaliação posterior e no futuro decidir se pretende eliminar, guardar ou aplicá-la mais tarde.