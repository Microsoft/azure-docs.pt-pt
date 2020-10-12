---
title: Como carregar um documento - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: A funcionalidade de upload do documento envia documentos paralelos (dois documentos em que um é a origem e o outro é a tradução) para o serviço.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 8597b3e4ffab44672e88374010fa829211fbc18b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88507334"
---
# <a name="upload-a-document"></a>Carregar um documento

No [Custom Tradutor,](https://portal.customtranslator.azure.ai)pode carregar documentos paralelos para treinar os seus modelos de tradução. [Documentos paralelos](what-are-parallel-documents.md) são pares de documentos onde um é uma tradução do outro. Um documento do par contém frases na língua de origem e o outro documento contém estas frases traduzidas para a língua-alvo.

Antes de enviar os seus documentos, reveja os formatos de [documentos e nomeie a orientação da convenção](document-formats-naming-convention.md) para se certificar de que o seu formato de ficheiro é suportado no Custom Tradutor.

## <a name="how-to-upload-document"></a>Como carregar o documento?

A partir do portal [Custom Tradutor,](https://portal.customtranslator.azure.ai) clique no separador "Documentos" para ir à página de documentos.

![Link de upload de documentos](media/how-to/how-to-upload-1.png)


1.  Clique no botão 'Carregar ficheiros' na página de documentos.

    ![Enviar página de documento](media/how-to/how-to-upload-2.png)

2.  No dialogar preenchem as seguintes informações:

    a.  Tipo de documento:

    -  Formação: Estes documentos serão utilizados para o conjunto de treino.
    -  Afinação: Estes documentos serão utilizados para afinar.
    -  Testes: Estes documentos serão utilizados para o conjunto de testes.
    -  Formulação da frase: Estes documentos serão utilizados para o dicionário de frases.
    -  Dicionário de Frase: Estes documentos serão usados para dicionário de sentenças

    b.  Par de linguagem

    c.  Documento de substituição se existir: Selecione esta caixa de verificação se pretender substituir quaisquer documentos existentes com o mesmo nome.

    d.  Preencha a secção relevante para dados paralelos ou dados de combinação.

    -  Dados paralelos:
        -  Ficheiro de origem: Selecione o ficheiro de idioma de origem do computador local.
        -  Ficheiro-alvo: Selecione o ficheiro de idioma-alvo do computador local.
        -  Nome do documento: Utilizado apenas se estiver a enviar ficheiros paralelos.

    - Dados da combinação:
        -  Combo File: Selecione o ficheiro de combinação do seu computador local. O seu ficheiro de combinação tem as suas frases linguísticas de origem e alvo. [A convenção de nomeação](document-formats-naming-convention.md) é importante para ficheiros combo.

    e.  Clique em Upload

    ![Carregar o diálogo do documento](media/how-to/how-to-upload-dialog.png)

3.  Neste momento, estamos a processar os seus documentos e a tentar extrair frases. Pode clicar em "Ver carregar o Progresso" para verificar o estado dos seus documentos à medida que processam.

    ![Carregar o diálogo de processamento de documentos](media/how-to/how-to-upload-processing-dialog.png)

4.  Esta página apresentará o estado e quaisquer erros para cada ficheiro dentro do seu upload. Pode visualizar o estado de upload passado a qualquer momento clicando no separador "Carregar histórico".

    ![Faça upload do diálogo do histórico do documento](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Ver histórico de upload

Na página de histórico de upload pode ver o histórico de todos os documentos que envia detalhes como tipo de documento, par de idiomas, estado de upload etc.

1. A partir do portal ['Tradução personalizada',](https://portal.customtranslator.azure.ai) clique em 'Carregar Histórico' para ver o histórico.

    ![Faça upload do separador histórico](media/how-to/how-to-upload-history-1.png)

2. Esta página mostra o estado de todos os seus uploads anteriores. Exibe uploads do mais recente ao menos recente. Para cada upload, mostra o nome do documento, o estado do upload, a data de upload, o número de ficheiros carregados, o tipo de ficheiro carregado e o par de idiomas do ficheiro.

    ![Enviar página de histórico](media/how-to/how-to-document-history-2.png)

3. Clique em qualquer registo histórico de upload. Na página de dados do histórico do upload, pode ver os ficheiros carregados como parte do upload, o estado do upload do ficheiro, o idioma do ficheiro e a mensagem de erro (se houver algum erro no upload).

## <a name="next-steps"></a>Passos seguintes

- Use a [página de detalhes](how-to-view-document-details.md) do documento para rever a lista de frases extraídas.
- [Como treinar um modelo.](how-to-train-model.md)
