---
title: Treine um modelo - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Formar um modelo é um passo importante na construção de um modelo de tradução. A formação acontece com base em documentos que seleciona para os treinos.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68595679"
---
# <a name="train-a-model"></a>Preparar um modelo

Treinar um modelo é o passo importante para a construção de um modelo de tradução, porque sem um treino, o modelo não pode ser construído. A formação acontece com base em documentos que seleciona para os treinos.

Para treinar um modelo:

1.  Selecione o projeto onde pretende construir um modelo.

2.  O separador Dados do projeto mostrará todos os documentos relevantes para o par de idiomas do projeto. Selecione manualmente os documentos que pretende utilizar para treinar o seu modelo. Pode selecionar documentos de treino, afinação e teste a partir deste ecrã. Além disso, basta selecionar o conjunto de treino e pedir ao Custom Tradutor que crie os conjuntos de afinação e teste para si.

    -  Nome do documento: Nome do documento.

    -  Emparelhamento: Se este documento for um documento paralelo ou monolingual. Atualmente, os documentos monolingues não são apoiados para a formação.

    -  Tipo de documento: Pode ser treino, afinação, teste ou dicionário.

    -  Par de idiomas: Isto mostra a fonte e a linguagem alvo para o projeto.

    -  Frases de origem: Mostra o número de frases extraídas do ficheiro fonte.

    -  Frases-alvo: Mostra o número de frases extraídas do ficheiro alvo.

    ![Preparar modelo](media/how-to/how-to-train-model.png)

3.  Clique no botão Train.

4.  No diálogo, especifique um nome para o seu modelo.

5.  Clique no modelo do comboio.

    ![Diálogo de modelo de comboio](media/how-to/how-to-train-model-2.png)

6.  O Custom Tradutor submeterá a formação e mostrará o estado da formação no separador de modelos.

    ![Página de modelo de comboio](media/how-to/how-to-train-model-3.png)

>[!Note]
>O Custom Tradutor suporta 10 treinos simultâneos dentro de um espaço de trabalho a qualquer momento.


## <a name="edit-a-model"></a>Editar um modelo

Pode editar um modelo utilizando o link Editar na página Dedetalhes do Modelo.

1.  Clique no ícone lápis.

    ![Editar modelo](media/how-to/how-to-edit-model.png)

2.  Na mudança de diálogo,

    1.  Nome do modelo (obrigatório): Dê ao seu modelo um nome significativo.

        ![Editar mais diálogo](media/how-to/how-to-edit-model-dialog.png)

3.  Clique em Guardar.


## <a name="next-steps"></a>Passos seguintes

- Saiba como ver detalhes do [modelo.](how-to-view-model-details.md)
