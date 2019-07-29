---
title: Como carregar um documento-Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Usando o recurso de upload de documento, você pode carregar um documento paralelo para seus treinamentos. Os documentos paralelos são pares de documentos em que um é a tradução do outro. Um documento no par contém sentenças no idioma de origem e o outro documento contém essas frases traduzidas para o idioma de destino.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f2bd6103c27d455265ee967554fb27513f78a472
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595642"
---
# <a name="upload-a-document"></a>Carregar um documento

No [Tradutor personalizado](https://portal.customtranslator.azure.ai), você pode carregar documentos paralelos para treinar seus modelos de tradução. Os [documentos paralelos](what-are-parallel-documents.md) são pares de documentos em que um é uma tradução do outro. Um documento no par contém sentenças no idioma de origem e o outro documento contém essas frases traduzidas para o idioma de destino.

Antes de carregar seus documentos, examine as [diretrizes de formatos de documento e Convenção de nomenclatura](document-formats-naming-convention.md) para verificar se o formato de arquivo tem suporte no Tradutor personalizado.

## <a name="how-to-upload-document"></a>Como carregar o documento?

No portal do [Tradutor personalizado](https://portal.customtranslator.azure.ai) , clique na guia "documentos" para ir para a página de documentos.

![Link de carregamento de documento](media/how-to/how-to-upload-1.png)


1.  Clique no botão carregar arquivos na página documentos.

    ![Página carregar documento](media/how-to/how-to-upload-2.png)

2.  Na caixa de diálogo, preencha as seguintes informações:

    a.  Tipo de documento:

    -  Preparação: Estes documentos serão usados para o conjunto de treinamento.
    -  Orienta Estes documentos serão usados para conjunto de ajuste.
    -  Testado Estes documentos serão usados para o conjunto de testes.
    -  Dicionário de frases: Esses documentos serão usados para o dicionário de frases.
    -  Dicionário de sentenças: Estes documentos serão usados para o dicionário de sentenças

    b.  Par de idiomas

    c.  Substituir documento se existir: Marque essa caixa de seleção se desejar substituir quaisquer documentos existentes com o mesmo nome.

    d.  Preencha a seção relevante para dados paralelos ou dados de combinação.

    -  Dados paralelos:
        -  Arquivo de origem: Selecione o arquivo de idioma de origem em seu computador local.
        -  Arquivo de destino: Selecione o arquivo de idioma de destino em seu computador local.
        -  Nome do documento: Usado somente se você estiver carregando arquivos paralelos.

    - Dados de combinação:
        -  Arquivo de combinação: Selecione o arquivo de combinação do seu computador local. O arquivo de combinação tem as duas frases de linguagem de origem e de destino. A [Convenção de nomenclatura](document-formats-naming-convention.md) é importante para arquivos de combinação.

    e.  Clique em carregar

    ![Caixa de diálogo Carregar documento](media/how-to/how-to-upload-dialog.png)

3.  Neste ponto, estamos processando seus documentos e tentando extrair frases. Você pode clicar em "Exibir progresso do upload" para verificar o status dos documentos conforme eles são processados.

    ![Caixa de diálogo Carregar processamento de documentos](media/how-to/how-to-upload-processing-dialog.png)

4.  Esta página exibirá o status e quaisquer erros para cada arquivo em seu carregamento. Você pode exibir o status de carregamento anterior a qualquer momento clicando na guia "carregar histórico".

    ![Caixa de diálogo Carregar histórico do documento](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Exibir histórico de carregamento

Na página carregar histórico, você pode exibir o histórico de todos os carregamentos de documentos, como tipo de documento, par de idiomas, status de carregamento, etc.

1. No portal do [Tradutor personalizado](https://portal.customtranslator.azure.ai) , clique na guia carregar histórico para exibir o histórico.

    ![Guia carregar histórico](media/how-to/how-to-upload-history-1.png)

2. Esta página mostra o status de todos os seus carregamentos passados. Ele exibe os carregamentos do mais recente para o menos recente. Para cada carregamento, ele mostra o nome do documento, o status de carregamento, a data de carregamento, o número de arquivos carregados, o tipo de arquivo carregado e o par de idiomas do arquivo.

    ![Página carregar histórico](media/how-to/how-to-document-history-2.png)

3. Clique em qualquer registro de histórico de carregamento. Na página de detalhes carregar histórico, você pode exibir os arquivos carregados como parte do upload, o status carregado do arquivo, o idioma do arquivo e a mensagem de erro (se houver algum erro no carregamento).

## <a name="next-steps"></a>Passos seguintes

- Use a [página detalhes do documento](how-to-view-document-details.md) para revisar a lista de sentenças extraídas.
- [Como treinar um modelo](how-to-train-model.md).
