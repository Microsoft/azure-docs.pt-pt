---
title: Política de aprendizagem - Personalizer
description: As definições de aprendizagem determinam os *hiperparâmetros* do treino do modelo. Dois modelos dos mesmos dados que são treinados em diferentes configurações de aprendizagem acabarão por ser diferentes.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381287"
---
# <a name="learning-policy-and-settings"></a>Política de aprendizagem e configurações

As definições de aprendizagem determinam os *hiperparâmetros* do treino do modelo. Dois modelos dos mesmos dados que são treinados em diferentes configurações de aprendizagem acabarão por ser diferentes.

[A política e definições de aprendizagem estão](how-to-settings.md#configure-rewards-for-the-feedback-loop) definidas no seu recurso Personalizer no portal Azure.

## <a name="import-and-export-learning-policies"></a>Políticas de importação e de aprendizagem das exportações

Pode importar e exportar ficheiros de política de aprendizagem do portal Azure. Utilize este método para salvar as políticas existentes, testá-las, substituí-las e arquivá-las no seu controlo de código fonte como artefactos para futuras referências e auditorias.

Aprenda [a](how-to-manage-model.md#import-a-new-learning-policy) importar e exportar uma política de aprendizagem no portal Azure para o seu recurso Personalizer.

## <a name="understand-learning-policy-settings"></a>Compreender as definições de políticas de aprendizagem

As configurações da política de aprendizagem não se destinam a ser alteradas. Alterar as definições apenas se entender como afetam o Personalizer. Sem este conhecimento, pode causar problemas, incluindo a invalidação de modelos Personalizer.

Personalizer usa [vowpalwabbit](https://github.com/VowpalWabbit) para treinar e marcar os eventos. Consulte a [documentação da vowpalwabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) sobre como editar as definições de aprendizagem usando o vowpalwabbit. Assim que tiver os argumentos corretos da linha de comando, guarde o comando para um ficheiro com o seguinte formato (substitua o valor da propriedade dos argumentos pelo comando pretendido) e faça o upload do ficheiro para importar definições de aprendizagem no painel de Definições de **Modelo e Aprendizagem** no portal Azure para o seu recurso Personalizer.

O seguinte `.json` é um exemplo de uma política de aprendizagem.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Comparar políticas de aprendizagem

Pode comparar como diferentes políticas de aprendizagem funcionam com dados passados em registos personalizer, fazendo [avaliações offline](concepts-offline-evaluation.md).

[Faça upload das suas próprias políticas de aprendizagem](how-to-manage-model.md) para compará-las com a política de aprendizagem atual.

## <a name="optimize-learning-policies"></a>Otimizar as políticas de aprendizagem

O personalizer pode criar uma política de aprendizagem otimizada numa [avaliação offline.](how-to-offline-evaluation.md) Uma política de aprendizagem otimizada que tenha melhores recompensas numa avaliação offline produzirá melhores resultados quando é usada online no Personalizer.

Depois de otimizar uma política de aprendizagem, pode aplicá-la diretamente ao Personalizer para que substitua imediatamente a política atual. Ou pode guardar a política otimizada para posterior avaliação e, mais tarde, decidir se descarta, poupa ou aplica.

## <a name="next-steps"></a>Passos seguintes

* Aprenda [eventos ativos e inativos.](concept-active-inactive-events.md)
