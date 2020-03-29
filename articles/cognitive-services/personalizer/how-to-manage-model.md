---
title: Gerir modelos e definições de aprendizagem - Personalizer
description: O modelo aprendido e as definições de aprendizagem podem ser exportados para backup no seu próprio sistema de controlo de fontes.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624299"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Como gerir modelos e definições de aprendizagem

O modelo aprendido e as definições de aprendizagem podem ser exportados para backup no seu próprio sistema de controlo de fontes.

## <a name="export-the-personalizer-model"></a>Exportar o modelo Personalizer

Da secção de gestão de Recursos para **Model and learning settings,** reveja a criação de modelos e última data atualizada e exporte o modelo atual. Pode utilizar o portal Azure ou as APIs personalizadas para exportar um ficheiro modelo para fins de arquivo.

![Modelo personalizado réscontado de exportação](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Dados claros para o seu ciclo de aprendizagem

1. No portal Azure, para o seu recurso Personalizer, na página de definições de **Modelo e aprendizagem,** selecione **Dados Claros**.
1. Para limpar todos os dados e redefinir o ciclo de aprendizagem para o estado original, selecione todas as 3 caixas de verificação.

    ![No portal Azure, limpe os dados do recurso Personalizer.](./media/settings/clear-data-from-personalizer-resource.png)

    |Valor|Objetivo|
    |--|--|
    |Dados de personalização e recompensa registados.|Estes dados de registo são utilizados em avaliações offline. Limpe os dados se estiver a reajustar o seu recurso.|
    |Redefinir o modelo Personalizer.|Este modelo muda em todas as retreinações. Esta frequência de treino é especificada na frequência do **modelo de upload** na página de **Configuração.** |
    |Desloque a política de aprendizagem ao incumprimento.|Se mudou a política de aprendizagem como parte de uma avaliação offline, isto repõe-se na política de aprendizagem original.|

1. Selecione **Dados selecionados clear** para iniciar o processo de compensação. O estado é relatado nas notificações do Azure, na navegação de extrema-direita.

## <a name="import-a-new-learning-policy"></a>Importar uma nova política de aprendizagem

As definições de política de [aprendizagem](concept-active-learning.md#understand-learning-policy-settings) determinam os _hiperparâmetros_ do treino do modelo. Faça uma [avaliação offline](how-to-offline-evaluation.md) para encontrar uma nova política de aprendizagem.

1. Abra o [portal Azure](https://portal.azure.com)e selecione o seu recurso Personalizer.
1. Selecione **Modelo e definições** de aprendizagem na secção Gestão de **Recursos.**
1. Para as definições de **aprendizagem importadas,** selecione o ficheiro que criou com o formato JSON acima especificado e, em seguida, selecione o botão **Upload.**

    Aguarde a notificação de que a política de aprendizagem foi carregada com sucesso.

## <a name="export-a-learning-policy"></a>Exportar uma política de aprendizagem

1. Abra o [portal Azure](https://portal.azure.com)e selecione o seu recurso Personalizer.
1. Selecione **Modelo e definições** de aprendizagem na secção Gestão de **Recursos.**
1. Para as definições de **aprendizagem de importação,** selecione o botão de definições de **aprendizagem de exportação.** Isto guarda `json` o ficheiro para o seu computador local.

## <a name="next-steps"></a>Passos seguintes

[Aprenda a gerir uma política de aprendizagem](how-to-manage-model.md)
