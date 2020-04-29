---
title: Como fazer upload de um documento - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: A funcionalidade de upload do documento envia documentos paralelos (dois documentos em que um é a origem e o outro é a tradução) para o serviço.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: b2a249a40d8c782d54a12df43d33655f3409753c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73647389"
---
# <a name="upload-a-document"></a>Carregar um documento

Em [Custom Tradutor,](https://portal.customtranslator.azure.ai)pode fazer upload de documentos paralelos para treinar os seus modelos de tradução. [Documentos paralelos](what-are-parallel-documents.md) são pares de documentos onde um é uma tradução do outro. Um documento no par contém frases na língua de origem e o outro documento contém estas frases traduzidas na linguagem-alvo.

Antes de fazer o upload dos seus documentos, reveja os [formatos de documentos e nomeie a orientação](document-formats-naming-convention.md) da convenção para se certificar de que o seu formato de ficheiro é suportado em Tradutor Personalizado.

## <a name="how-to-upload-document"></a>Como carregar o documento?

No portal [Custom Tradutor,](https://portal.customtranslator.azure.ai) clique no separador "Documentos" para ir à página de documentos.

![Link de upload de documento](media/how-to/how-to-upload-1.png)


1.  Clique no botão de ficheiros upload na página de documentos.

    ![Página de documento de upload](media/how-to/how-to-upload-2.png)

2.  No diálogo preencha as seguintes informações:

    a.  Tipo de documento:

    -  Formação: Estes documentos serão utilizados para o conjunto de treino.
    -  Afinação: Estes documentos serão utilizados para afinar.
    -  Ensaio: Estes documentos serão utilizados para o conjunto de testes.
    -  Frase Dicionário: Estes documentos serão utilizados para o dicionário de frases.
    -  Dicionário da Sentença: Estes documentos serão utilizados para o dicionário de frases

    b.  Par de línguas

    c.  Sobrepor documento se existir: Selecione esta caixa de verificação se pretender substituir quaisquer documentos existentes com o mesmo nome.

    d.  Preencha a secção relevante para obter dados paralelos ou combo.

    -  Dados paralelos:
        -  Ficheiro fonte: Selecione ficheiro de idioma fonte a partir do seu computador local.
        -  Ficheiro de destino: Selecione ficheiro de idioma alvo a partir do seu computador local.
        -  Nome do documento: Utilizado apenas se estiver a carregar ficheiros paralelos.

    - Dados da combo:
        -  Ficheiro Combo: Selecione o ficheiro combo do seu computador local. O seu ficheiro combo tem as suas frases de origem e linguagem alvo. [A convenção de nomeação](document-formats-naming-convention.md) é importante para ficheiros combo.

    e.  Clique em Carregar

    ![Carregar dialog de documento](media/how-to/how-to-upload-dialog.png)

3.  Neste momento, estamos a processar os seus documentos e a tentar extrair frases. Pode clicar em "Ver carregar Progresso" para verificar o estado dos seus documentos à medida que estes processam.

    ![Carregar dialog de processamento de documentos](media/how-to/how-to-upload-processing-dialog.png)

4.  Esta página mostrará o estado e quaisquer erros para cada ficheiro dentro do seu upload. Pode visualizar o estado de upload passado a qualquer momento clicando no separador "Upload history".

    ![Carregar o diálogo do histórico do documento](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Ver o histórico de upload

Na página de histórico de upload pode ver o histórico de todos os dados de upload de documentos como tipo de documento, par de idiomas, estado de upload etc.

1. A partir do portal [Tradutor Personalizado,](https://portal.customtranslator.azure.ai) clique no separador 'Upload History' para ver o histórico.

    ![Carregar guia de histórico](media/how-to/how-to-upload-history-1.png)

2. Esta página mostra o estado de todos os seus uploads passados. Apresenta uploads das mais recentes ao menos recentes. Para cada upload, mostra o nome do documento, o estado de upload, a data de upload, o número de ficheiros carregados, o tipo de ficheiro carregado e o par de idiomas do ficheiro.

    ![Página de histórico de upload](media/how-to/how-to-document-history-2.png)

3. Clique em qualquer registo de histórico de upload. Na página de dados do histórico de upload, pode visualizar os ficheiros enviados como parte do upload, estado carregado do ficheiro, idioma do ficheiro e mensagem de erro (se houver algum erro no upload).

## <a name="next-steps"></a>Passos seguintes

- Utilize a [página de detalhes](how-to-view-document-details.md) do documento para rever a lista de frases extraídas.
- [Como treinar um modelo.](how-to-train-model.md)
