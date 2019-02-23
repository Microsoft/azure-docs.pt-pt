---
title: Como carregar um documento - Translator personalizado
titleSuffix: Azure Cognitive Services
description: Pode utilizar a funcionalidade de carregamento do documento para carregar o documento de paralelo para sua treinamentos. Documentos paralelos são pares de documentos em que uma é a tradução do outro. Um documento no par contém frases no idioma de origem e o outro documento contém essas frases traduzidas no idioma de destino.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 83d6791740cd6aebb9da14b86e0d5272ab78f43a
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737591"
---
# <a name="upload-a-document"></a>Carregar um documento

Na [Translator personalizado](https://portal.customtranslator.azure.ai), pode carregar documentos paralelos para treinar seus modelos de tradução. [Documentos em paralelo](what-are-parallel-documents.md) são pares de documentos em que uma é uma tradução das outras. Um documento no par contém frases no idioma de origem e o outro documento contém essas frases traduzidas no idioma de destino.

Antes de carregar os seus documentos, reveja os [formatos e orientações de convenção de nomenclatura de documentos](document-formats-naming-convention.md) para se certificar de que o ficheiro de formato é suportado no Translator personalizado.

## <a name="how-to-upload-document"></a>Como carregar documentos?

Partir [Translator personalizado](https://portal.customtranslator.azure.ai) portal, clique no separador "Documentos" para aceder à página de documentos.

![Ligação de carregamento do documento](media/how-to/how-to-upload-1.png)


1.  Clique no botão de ficheiros de carregamento da página de documentos.

    ![Carregar a página do documento](media/how-to/how-to-upload-2.png)

2.  Na caixa de diálogo, preencha as seguintes informações:

    a.  Tipo de documento:

    -  Preparação: Estes document(s) será utilizado para o conjunto de preparação.
    -  Otimização: Estes document(s) será utilizado para o conjunto de ajuste.
    -  Teste: Estes document(s) será utilizado para o conjunto de teste.
    -  Dicionário da frase: Estes document(s) será utilizado para o dicionário de frase.
    -  Dicionário da frase: Estes document(s) será utilizado para o dicionário de frase

    b.  Par de idioma

    c.  Substituir o documento se existe: Selecione esta caixa de verificação se pretender substituir todos os documentos existentes com o mesmo nome.

    d.  Preencha a secção relevante para paralelas de dados ou dados de caixa de combinação.

    -  Paralelas de dados:
        -  Ficheiro de origem: Selecione o ficheiro de idioma de origem do computador local.
        -  Ficheiro de destino: Selecione o ficheiro de idioma de destino do computador local.
        -  Nome do documento: Utilizado apenas se o que está a carregar ficheiros paralelos.

    - Dados de caixa de combinação:
        -  Ficheiro de combinação: Selecione o ficheiro de combinação do computador local. O ficheiro de combinação tem tanto da sua origem e frases de idioma de destino. [Convenção de nomenclatura](document-formats-naming-convention.md) é importante para os ficheiros de combinação.

    e.  Clique em carregar

    ![Carregar a caixa de diálogo do documento](media/how-to/how-to-upload-dialog.png)

3.  Neste momento, estamos seus documentos de processamento e tentar extrair frases. Pode clicar em "Ver progresso do carregamento" para verificar o estado dos seus documentos, como processam.

    ![Carregar a caixa de diálogo de processamento de documentos](media/how-to/how-to-upload-processing-dialog.png)

4.  Esta página irá apresentar o estado e os erros para cada ficheiro no seu carregamento. Pode ver em estado de carregamento em qualquer altura ao clicar na guia "Carregar histórico".

    ![Caixa de diálogo de histórico de documento carregar](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Ver histórico de carregamento

Na página de histórico de carregamento pode ver o histórico de todos os carregamentos de documento detalhes como o tipo de documento, par de idioma, carregar Estado etc.

1. Do [Translator personalizado](https://portal.customtranslator.azure.ai) portal, clique o separador de carregar histórico para ver o histórico.

    ![Carregar o separador de histórico](media/how-to/how-to-upload-history-1.png)

2. Esta página mostra o estado de todos os seus carregamentos anteriores. Ele exibe carregamentos da mais recente para menos recente. Para cada carregamento, mostra o nome do documento, estado de carregamento, a data de carregamento, o número de ficheiros carregados, tipo de ficheiro carregado e o par de idioma do ficheiro.

    ![Carregar a página de histórico](media/how-to/how-to-document-history-2.png)

3. Clique em qualquer registro de histórico de carregamento. Na página de detalhes do histórico de carregamento, pode ver os ficheiros carregados como parte do carregamento, estado carregado do ficheiro, o idioma da mensagem de ficheiro e o erro (se existir qualquer erro no carregamento).

## <a name="next-steps"></a>Passos Seguintes

- Utilize o [página de detalhes do documento](how-to-view-document-details.md) para rever a lista de frases extraídas.
- [Como preparar um modelo](how-to-train-model.md).
