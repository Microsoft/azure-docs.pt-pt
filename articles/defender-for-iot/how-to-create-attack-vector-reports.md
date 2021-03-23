---
title: Criar relatórios de vetores de ataque
description: Os relatórios de vetores de ataque fornecem uma representação gráfica de uma cadeia de vulnerabilidade de dispositivos exploráveis.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 6c178f379561ca4a572a7ae8f556ae6753d6e2be
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784378"
---
# <a name="attack-vector-reporting"></a>Relatório de vetor de ataque

## <a name="about-attack-vector-reports"></a>Sobre relatórios de vetores de ataque

Os relatórios de vetores de ataque fornecem uma representação gráfica de uma cadeia de vulnerabilidade de dispositivos exploráveis. Estas vulnerabilidades podem dar a um intruso acesso a dispositivos de rede chave. O Simulador de Vetores de Ataque calcula vetores de ataque em tempo real e analisa todos os vetores de ataque para um alvo específico.

Trabalhar com o vetor de ataque permite-lhe avaliar o efeito das atividades de mitigação na sequência de ataque. Pode então determinar, por exemplo, se uma atualização do sistema perturba o caminho do atacante, quebrando a cadeia de ataque, ou se permanece um caminho de ataque alternativo. Esta informação ajuda-o a priorizar atividades de reparação e mitigação.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Veja os seus alertas no centro de controlo.":::

> [!NOTE]
> Administradores e analistas de segurança podem executar os procedimentos descritos nesta secção.

## <a name="create-an-attack-vector-report"></a>Criar um relatório vetorial de ataque

Para criar uma simulação de vetor de ataque:

1. Selecione :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="mais sinal":::no menu lateral para adicionar uma Simulação.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="A simulação do vetor de ataque.":::

2. Introduza propriedades de simulação:

   - **Nome**: Nome da simulação.

   - **Vetores máximos**: O número máximo de vetores numa única simulação.

   - **Mostrar no mapa do dispositivo**: Mostre o vetor de ataque como um filtro no mapa do dispositivo.

   - **Todos os dispositivos Source**: O vetor de ataque considerará todos os dispositivos como uma fonte de ataque.

   - **Fonte de ataque**: O vetor de ataque considerará apenas os dispositivos especificados como uma fonte de ataque.

   - **Todos os dispositivos target**: O vetor de ataque considerará todos os dispositivos como alvo de ataque.

   - **Alvo de ataque**: O vetor de ataque considerará apenas os dispositivos especificados como alvo de ataque.

   - **Excluir dispositivos**: Os dispositivos especificados serão excluídos da simulação do vetor de ataque.

   - **Excluir sub-redes**: As sub-redes especificadas serão excluídas da simulação do vetor de ataque.

3. Selecione **Adicionar Simulação**. A simulação será adicionada à lista de simulações.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Adicione uma nova simulação.":::

4. Selecione :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: se pretender editar a simulação.

   Selecione :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: se pretender eliminar a simulação.

   Selecione :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: se quiser marcar a simulação como uma das favoritas.

5. Uma lista de vetores de ataque aparece e inclui pontuação vetorial (em 100), dispositivo de origem de ataque e dispositivo alvo de ataque. Selecione um ataque específico para representação gráfica de vetores de ataque.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Vetores de ataque.":::

## <a name="see-also"></a>Ver também

[Relatório de vetor de ataque](how-to-create-attack-vector-reports.md)


