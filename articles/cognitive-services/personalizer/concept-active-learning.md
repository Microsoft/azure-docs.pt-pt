---
title: Eventos ativos e inativos – personalizador
titleSuffix: Azure Cognitive Services
description: Este artigo aborda o uso de eventos ativos e inativos, configurações de aprendizado e políticas de aprendizado no serviço personalizador.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 90658e030c907a9fd99dd8fb9a6e90698d72b1f0
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834467"
---
# <a name="active-and-inactive-events"></a>Eventos ativos e inativos

Quando o aplicativo chama a API de classificação, você recebe a ação que o aplicativo deve mostrar no campo **rewardActionId** .  A partir desse momento, o personalizador espera uma chamada de recompensa com o mesmo eventId. A pontuação de recompensa será usada para treinar o modelo para futuras chamadas de classificação. Se nenhuma chamada de recompensa for recebida para o eventId, um prêmio padrão será aplicado. As recompensas padrão são definidas no portal do Azure.

Em alguns cenários, o aplicativo pode precisar chamar a classificação antes mesmo de saber se o resultado será usado ou exibido para o usuário. Isso pode acontecer em situações em que, por exemplo, a renderização da página de conteúdo promovido é substituída por uma campanha de marketing. Se o resultado da chamada de classificação nunca foi usado e o usuário nunca o viu, não envie uma chamada de recompensa correspondente.

Normalmente, esses cenários acontecem quando:

* Você está processando a IU que o usuário pode ou não conseguir ver.
* Seu aplicativo está fazendo uma personalização preditiva na qual as chamadas de classificação são feitas com pouco contexto em tempo real e o aplicativo pode ou não usar a saída.

Nesses casos, use personalizador para chamar a classificação, solicitando que o evento fique _inativo_. O personalizador não esperará um prêmio para esse evento e não aplicará uma recompensa padrão.
Posteriormente, em sua lógica de negócios, se o aplicativo usar as informações da chamada de classificação, basta _Ativar_ o evento. Assim que o evento estiver ativo, o personalizador espera um recompensa de evento. Se nenhuma chamada explícita for feita na API de recompensa, o personalizador aplicará uma recompensa padrão.

## <a name="inactive-events"></a>Eventos inativos

Para desabilitar o treinamento de um evento, chame Rank usando `learningEnabled = False`. Para um evento inativo, o aprendizado será ativado implicitamente se você enviar uma recompensa para o eventId ou chamar a API de `activate` para esse eventId.

## <a name="learning-settings"></a>Configurações de aprendizado

As configurações de aprendizado determinam os *hiperparâmetros* do treinamento do modelo. Dois modelos dos mesmos dados que são treinados em diferentes configurações de aprendizado acabarão sendo diferentes.

### <a name="import-and-export-learning-policies"></a>Importar e exportar políticas de aprendizado

Você pode importar e exportar arquivos de política de aprendizagem do portal do Azure. Use esse método para salvar as políticas existentes, testá-las, substituí-las e arquivá-las no controle do código-fonte como artefatos para referência e auditoria futuras.

Saiba [como](how-to-learning-policy.md) importar e exportar uma política de aprendizado.

### <a name="understand-learning-policy-settings"></a>Entender as configurações da política de aprendizagem

As configurações na política de aprendizado não devem ser alteradas. Altere as configurações somente se você entender como elas afetam o personalizador. Sem esse conhecimento, você pode causar problemas, incluindo a invalidação de modelos de personalização.

O personalizador usa [vowpalwabbit](https://github.com/VowpalWabbit) para treinar e pontuar os eventos. Consulte a [documentação do vowpalwabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) sobre como editar as configurações de aprendizado usando o vowpalwabbit. Depois de ter os argumentos de linha de comando corretos, salve o comando em um arquivo com o seguinte formato (substitua o valor da propriedade arguments pelo comando desejado) e carregue o arquivo para importar as configurações de aprendizado no painel de **configurações de aprendizado e de modelo** no portal do Azure para o recurso personalizador.

O `.json` a seguir é um exemplo de uma política de aprendizado.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

### <a name="compare-learning-policies"></a>Comparar políticas de aprendizado

Você pode comparar o desempenho das diferentes políticas de aprendizagem em relação aos dados anteriores nos logs do personalizado, fazendo [avaliações offline](concepts-offline-evaluation.md).

[Carregue suas próprias políticas de aprendizado](how-to-learning-policy.md) para compará-las com a política de aprendizado atual.

### <a name="optimize-learning-policies"></a>Otimizar políticas de aprendizado

O personalizador pode criar uma política de aprendizado otimizada em uma [avaliação offline](how-to-offline-evaluation.md). Uma política de aprendizado otimizada que tem recompensas melhores em uma avaliação offline produzirá resultados melhores quando for usada online no personalizador.

Depois de otimizar uma política de aprendizado, você pode aplicá-la diretamente ao personalizador para que ela substitua imediatamente a política atual. Ou você pode salvar a política otimizada para avaliação adicional e, posteriormente, decidir se deseja descartá-la, salvá-la ou aplicá-la.
