---
title: Preparar um modelo - Translator personalizado
titleSuffix: Azure Cognitive Services
description: Preparar um modelo é um passo importante ao criar um modelo de tradução. Treinamento ocorre com base em documentos que selecionar para essa treinamentos.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 8fee19b4d7a1ae05ca9a38c5a9ba374c0674a867
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776262"
---
# <a name="train-a-model"></a>Preparar um modelo

Um modelo de preparação é a etapa importante para a criação de um modelo de tradução, porque sem um treinamento, o modelo não pode ser criado. Treinamento ocorre com base em documentos que selecionou para os treinamentos.

Para preparar um modelo:

1.  Selecione o projeto onde pretende criar um modelo.

2.  O separador de dados para o projeto irá mostrar todos os documentos do par de idioma do projeto. Selecione manualmente os documentos que pretende utilizar para preparar o seu modelo. Pode selecionar de treinamento, Otimização e teste documentos neste ecrã. Também lhe apenas selecionar o conjunto de treinamento e ter tradutor de personalizada a otimização de criar e testar conjuntos para.

    -  Nome do documento: Nome do documento.

    -  Emparelhamento: Se este documento é um documento ou monolíngüe em paralelo. Documentos monolíngües não são atualmente suportados para treinamento.

    -  Tipo de documento: Pode ser treinamento, otimização, testes ou dicionário.

    -  Par de idioma: Isto Mostrar a origem e o idioma de destino para o projeto.

    -  Frases de origem: Mostra o número de frases extraídas do arquivo de origem.

    -  Frases de destino: Mostra o número de frases extraídos a partir do ficheiro de destino.

    ![Preparar modelo](media/how-to/how-to-train-model.png)

3.  Clique botão Train.

4.  Na caixa de diálogo, especifique um nome para o seu modelo.

5.  Clique em preparar modelo.

    ![Caixa de diálogo de modelo de formação](media/how-to/how-to-train-model-2.png)

6.  Tradutor personalizado irá submeter o treinamento e mostrar o estado do treinamento no separador de modelos.

    ![Página do modelo de formação](media/how-to/how-to-train-model-3.png)


## <a name="edit-a-model"></a>Editar um modelo

Pode editar um modelo através da ligação de edição na página de detalhes do modelo.

1.  Clique no ícone de lápis.

    ![Editar modelo](media/how-to/how-to-edit-model.png)

2.  Na alteração de caixa de diálogo,

    1.  Nome de modelo (obrigatório): Dê um nome significativo ao seu modelo.

        ![Mais de caixa de diálogo Editar](media/how-to/how-to-edit-model-dialog.png)

3.  Clique em Guardar.


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais [como ver os detalhes do modelo](how-to-view-model-details.md).
