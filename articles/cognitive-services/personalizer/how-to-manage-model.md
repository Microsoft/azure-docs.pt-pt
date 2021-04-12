---
title: Gerir configurações de modelo e aprendizagem - Personalizer
description: O modelo e as definições de aprendizagem aprendidas com máquinas podem ser exportadas para cópias de segurança no seu próprio sistema de controlo de fontes.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 48e954eab9da5dfc638f93a7c4e55c675c4f2ac9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "97797252"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Como gerir configurações de modelo e aprendizagem

O modelo e as definições de aprendizagem aprendidas com máquinas podem ser exportadas para cópias de segurança no seu próprio sistema de controlo de fontes.

## <a name="export-the-personalizer-model"></a>Exportar o modelo Personalizer

A partir da secção de gestão de recursos para **configurações de Modelo e aprendizagem,** revisão da criação de modelos e última data atualizada e exportar o modelo atual. Pode utilizar o portal Azure ou as APIs personalizantes para exportar um ficheiro modelo para fins de arquivo.

![Exportar modelo personalizador atual](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Dados claros para o seu ciclo de aprendizagem

1. No portal Azure, para o seu recurso Personalizer, na página **Modelo e definições de aprendizagem,** selecione **Dados Limpos**.
1. Para limpar todos os dados e redefinir o ciclo de aprendizagem para o estado original, selecione todas as 3 caixas de verificação.

    ![No portal Azure, dados claros do recurso Personalizer.](./media/settings/clear-data-from-personalizer-resource.png)

    |Valor|Objetivo|
    |--|--|
    |Dados de personalização e recompensa registados.|Estes dados de registo são utilizados em avaliações offline. Limpe os dados se estiver a repor o seu recurso.|
    |Reinicie o modelo Personalizer.|Este modelo muda em cada reciclagem. Esta frequência de treino é especificada na frequência do **modelo de upload** na página de **Configuração.** |
    |Desafine a política de aprendizagem para o padrão.|Se mudou a política de aprendizagem como parte de uma avaliação offline, isto reeprova a política de aprendizagem original.|

1. Selecione **Limpar os dados selecionados** para iniciar o processo de compensação. O estado é relatado nas notificações do Azure, na navegação de topo-direita.

## <a name="import-a-new-learning-policy"></a>Importar uma nova política de aprendizagem

As definições [de política de aprendizagem](concept-active-learning.md#understand-learning-policy-settings) determinam os _hiperparmetros_ do treino do modelo. Faça uma [avaliação offline](how-to-offline-evaluation.md) para encontrar uma nova política de aprendizagem.

1. Abra o [portal Azure](https://portal.azure.com)e selecione o seu recurso Personalizer.
1. Selecione **definições de modelo e aprendizagem** na secção **de Gestão de Recursos.**
1. Para as **definições de aprendizagem de Importação** selecione o ficheiro que criou com o formato JSON especificado acima e, em seguida, selecione o botão **Upload.**

    Aguarde a notificação de que a política de aprendizagem foi carregada com sucesso.

## <a name="export-a-learning-policy"></a>Exportar uma política de aprendizagem

1. Abra o [portal Azure](https://portal.azure.com)e selecione o seu recurso Personalizer.
1. Selecione **definições de modelo e aprendizagem** na secção **de Gestão de Recursos.**
1. Para as **definições de aprendizagem de importação** selecione o botão **de aprendizagem de exportação.** Isto guarda o `json` ficheiro para o computador local.

## <a name="next-steps"></a>Passos seguintes

[Analise o seu ciclo de aprendizagem com uma avaliação offline](how-to-offline-evaluation.md)
