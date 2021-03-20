---
title: Política de aprendizagem - Personalizar
description: As definições de aprendizagem determinam os *hiperparímetros* do treino do modelo. Dois modelos dos mesmos dados que são treinados em diferentes configurações de aprendizagem acabarão por ser diferentes.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1d2038e3796df843736eb80f7e5645f4141c82f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91253637"
---
# <a name="learning-policy-and-settings"></a>Política de aprendizagem e configurações

As definições de aprendizagem determinam os *hiperparímetros* do treino do modelo. Dois modelos dos mesmos dados que são treinados em diferentes configurações de aprendizagem acabarão por ser diferentes.

[A política de aprendizagem e as configurações](how-to-settings.md#configure-rewards-for-the-feedback-loop) estão definidas no seu recurso Personalizer no portal Azure.

## <a name="import-and-export-learning-policies"></a>Políticas de importação e de aprendizagem das exportações

Pode importar e exportar ficheiros de política de aprendizagem a partir do portal Azure. Utilize este método para salvar as políticas existentes, testá-las, substituí-las e arquivá-las no seu controlo de código fonte como artefactos para futuras referências e auditorias.

Saiba como importar e [exportar](how-to-manage-model.md#import-a-new-learning-policy) uma política de aprendizagem no portal Azure para o seu recurso Personalizer.

## <a name="understand-learning-policy-settings"></a>Compreender as definições da política de aprendizagem

As definições na política de aprendizagem não se destinam a ser alteradas. Altere as definições apenas se entender como afetam o Personalizar. Sem este conhecimento, poderá causar problemas, incluindo invalidar modelos Personalizer.

O personalista usa [vowpalwabbit](https://github.com/VowpalWabbit) para treinar e marcar os eventos. Consulte a [documentação do vowpalwabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) sobre como editar as definições de aprendizagem usando o vowpalwabbit. Assim que tiver os argumentos corretos da linha de comando, guarde o comando para um ficheiro com o seguinte formato (substitua o valor da propriedade dos argumentos pelo comando pretendido) e faça o upload do ficheiro para importar definições de aprendizagem no painel **de Definições de Modelo e Aprendizagem** no painel Azure para o seu recurso Personalizer.

`.json`Segue-se um exemplo de uma política de aprendizagem.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Comparar políticas de aprendizagem

Pode comparar como diferentes políticas de aprendizagem funcionam com dados anteriores em registos personalizados fazendo [avaliações offline.](concepts-offline-evaluation.md)

[Faça upload das suas próprias políticas de aprendizagem](how-to-manage-model.md) para compará-las com a política de aprendizagem atual.

## <a name="optimize-learning-policies"></a>Otimizar políticas de aprendizagem

O personalizador pode criar uma política de aprendizagem otimizada numa [avaliação offline.](how-to-offline-evaluation.md) Uma política de aprendizagem otimizada que tenha melhores recompensas numa avaliação offline produzirá melhores resultados quando for usada online no Personalizer.

Depois de otimizar uma política de aprendizagem, pode aplicá-la diretamente ao Personalizer para que substitua imediatamente a política atual. Ou pode guardar a política otimizada para uma avaliação posterior e, mais tarde, decidir se descarta, salva ou aplica.

## <a name="next-steps"></a>Passos seguintes

* Aprenda [eventos ativos e inativos.](concept-active-inactive-events.md)
