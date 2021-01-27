---
title: Treine um modelo - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Treinar um modelo é um passo importante na construção de um modelo de tradução. O treino acontece com base em documentos que seleciona para esses treinos.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 5c17f842b93fb99a6a06b94f84ae26126794b776
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896431"
---
# <a name="train-a-model"></a>Preparar um modelo

Treinar um modelo é o primeiro e mais importante passo para a construção de um modelo de tradução, caso contrário, o modelo não pode ser construído. O treino acontece com base em documentos que seleciona para os treinos. Quando selecionar documentos do tipo de documento "Training", esteja atento aos requisitos mínimos de 10.000 frases paralelas. Ao selecionar documentos, apresentamos o número total de frases de treino para guiá-lo. Este requisito não se aplica quando seleciona apenas documentos do tipo de documento do dicionário para treinar um modelo.

Para treinar um modelo:

1. Selecione o projeto onde pretende construir um modelo.

2. O separador Data para o projeto mostrará todos os documentos relevantes para o par de linguagem do projeto. Selecione manualmente os documentos que pretende utilizar para treinar o seu modelo. Pode selecionar documentos de treino, afinação e teste deste ecrã. Também basta selecionar o conjunto de treino e pedir ao Tradutor Personalizado que crie os conjuntos de afinação e teste para si.

    - Nome do documento: Nome do documento.

    - Emparelhamento: Se este documento for um documento paralelo ou monolíngue. Atualmente, os documentos monolingues não são apoiados para a formação.

    - Tipo de documento: Pode ser treino, afinação, teste ou dicionário.

    - Par de idiomas: Isto mostra a origem e a linguagem-alvo para o projeto.

    - Frases de origem: Mostra o número de frases extraídas do ficheiro de origem.

    - Frases-alvo: Mostra o número de frases extraídas do ficheiro-alvo.

    ![Preparar modelo](media/how-to/how-to-train-model.png)

3. Clique no botão "Criar modelo".

4. No diálogo, especifique o nome do seu modelo. Por predefinição, "Treina imediatamente" é selecionado para iniciar o pipeline de treino quando clicar no botão "Criar modelo". Pode selecionar "Guardar como rascunho" para criar os metadados do modelo e colocar o modelo num estado de projeto, mas a formação de modelos não começaria. Mais tarde, tem de selecionar manualmente modelos em estado de projeto para treinar.

5. Clique no botão "Criar modelo".

    ![Diálogo do modelo do trem](media/how-to/how-to-train-model-2.png)

6. O Tradutor Personalizado submeterá a formação e mostrará o estado da formação no separador modelos.

    ![Página de modelo de trem](media/how-to/how-to-train-model-3.png)

>[!Note]
>O Custom Tradutor suporta 10 treinos simultâneos dentro de um espaço de trabalho a qualquer momento.

## <a name="modify-a-model-name"></a>Modificar um nome de modelo

Pode modificar um nome de modelo a partir da página 'Detalhes do Modelo'.

1. Na página do projeto, clique no nome do projeto onde o modelo existe.
2. Clique no separador modelo.
3. Clique no nome do modelo para ver os detalhes do modelo.
4. Clique no ícone Lápis.

    ![Editar modelo](media/how-to/how-to-edit-model.png)

5. No diálogo, mude o nome do modelo e dê ao seu modelo um nome significativo.

    ![Editar mais diálogo](media/how-to/how-to-edit-model-dialog.png)

6. Clique em Guardar.

## <a name="next-steps"></a>Próximos passos

- Saiba [como ver detalhes do modelo.](how-to-view-model-details.md)
