---
title: Treinar um modelo-Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Treinar um modelo é uma etapa importante ao criar um modelo de tradução. O treinamento ocorre com base nos documentos selecionados para esses treinamentos.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595679"
---
# <a name="train-a-model"></a>Preparar um modelo

Treinar um modelo é a etapa importante para criar um modelo de tradução, porque sem um treinamento, o modelo não pode ser criado. O treinamento ocorre com base nos documentos selecionados para os treinamentos.

Para treinar um modelo:

1.  Selecione o projeto onde você deseja criar um modelo.

2.  A guia dados do projeto mostrará todos os documentos relevantes para o par de idiomas do projeto. Selecione manualmente os documentos que você deseja usar para treinar seu modelo. Você pode selecionar treinamento, ajuste e teste de documentos nessa tela. Além disso, basta selecionar o conjunto de treinamento e fazer com que o tradutor personalizado crie os conjuntos de ajuste e teste para você.

    -  Nome do documento: Nome do documento.

    -  Emparelhamento Se este documento for um documento paralelo ou multilíngue. Atualmente, não há suporte para documentos monobilíngües para treinamento.

    -  Tipo de documento: Pode ser treinamento, ajuste, teste ou dicionário.

    -  Par de idiomas: Isso mostra o idioma de origem e de destino do projeto.

    -  Sentenças de origem: Mostra o número de frases extraídas do arquivo de origem.

    -  Sentenças de destino: Mostra o número de frases extraídas do arquivo de destino.

    ![Modelo de treinamento](media/how-to/how-to-train-model.png)

3.  Clique no botão treinar.

4.  Na caixa de diálogo, especifique um nome para seu modelo.

5.  Clique em treinar modelo.

    ![Caixa de diálogo treinar modelo](media/how-to/how-to-train-model-2.png)

6.  O tradutor personalizado enviará o treinamento e mostrará o status do treinamento na guia modelos.

    ![Página treinar modelo](media/how-to/how-to-train-model-3.png)

>[!Note]
>O tradutor personalizado dá suporte a 10 treinamentos simultâneos em um espaço de trabalho em qualquer ponto no tempo.


## <a name="edit-a-model"></a>Editar um modelo

Você pode editar um modelo usando o link editar na página detalhes do modelo.

1.  Clique no ícone de lápis.

    ![Editar modelo](media/how-to/how-to-edit-model.png)

2.  Na caixa de diálogo Alterar,

    1.  Nome do modelo (obrigatório): Dê um nome significativo ao seu modelo.

        ![Editar caixa de diálogo mais](media/how-to/how-to-edit-model-dialog.png)

3.  Clique em Guardar.


## <a name="next-steps"></a>Passos Seguintes

- Saiba [como exibir detalhes do modelo](how-to-view-model-details.md).
