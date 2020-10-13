---
title: Personalizar modelo de linguagem com site do Indexer de Vídeo
titleSuffix: Azure Media Services
description: Saiba como personalizar um modelo de Linguagem com o website do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/10/2020
ms.author: kumud
ms.openlocfilehash: f9eedba6c8aee5c8aab73e2ed598a4511b4c5982
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020461"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Personalize um modelo de linguagem com o site do Indexer de Vídeo

O Video Indexer permite criar modelos de linguagem personalizados para personalizar o reconhecimento da fala através do upload de textos de adaptação, nomeadamente texto do domínio cujo vocabulário gostaria que o motor se adaptasse. Assim que treinar o seu modelo, serão reconhecidas novas palavras no texto de adaptação.

Para obter uma visão geral detalhada e as melhores práticas para modelos de linguagem personalizados, consulte [Personalizar um modelo de linguagem com índice de vídeo.](customize-language-model-overview.md)

Pode utilizar o website Do Indexer de Vídeo para criar e editar modelos de Linguagem personalizados na sua conta, conforme descrito neste tópico. Também pode utilizar a API, conforme descrito no [modelo Personale Language utilizando APIs](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Criar um modelo de linguagem

1. Vá ao site do [Video Indexer](https://www.videoindexer.ai/) e inscreva-se.
2. Para personalizar um modelo na sua conta, selecione o botão de **personalização** do modelo Content no canto superior direito da página.

   ![Personalizar modelo de conteúdo no Video Indexer](./media/content-model-customization/content-model-customization.png)

3. Selecione o **separador Idioma.**

    Vê uma lista de línguas apoiadas.

    ![Lista de modelos de idiomas no Indexador de Vídeo](./media/customize-language-model/customize-language-model.png)

4. Sob o idioma que pretende, **selecione Adicionar modelo**.
5. Digite o nome para o modelo idioma e acerte a entrada.

    Este passo cria o modelo e dá a opção de carregar ficheiros de texto para o modelo.

6. Para adicionar um ficheiro de texto, **selecione Adicionar ficheiro**. O seu explorador de ficheiros abrirá.

7. Navegue para e selecione o ficheiro de texto. Pode adicionar vários ficheiros de texto a um modelo de idioma.

    Também pode adicionar um ficheiro de texto selecionando o botão **...** no lado direito do modelo idioma e selecionando **o ficheiro Adicionar**.

8. Assim que terminar de carregar os ficheiros de texto, selecione a opção **Comboio** verde.

    ![Modelo de linguagem de comboio no Indexador de Vídeo](./media/customize-language-model/train-model.png)

O processo de treino pode demorar alguns minutos. Uma vez feito o treino, **vê-se treinado** ao lado do modelo. Pode pré-visualizar, transferir e eliminar o ficheiro do modelo.

![Modelo de linguagem treinado no Indexador de Vídeo](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Usando um modelo de linguagem em um novo vídeo

Para utilizar o seu modelo Language num novo vídeo, faça uma das seguintes ações:

* Selecione o botão **upload** na parte superior da página.

    ![Botão de upload Índice de Vídeo](./media/customize-language-model/upload.png)

* Deixe cair o seu ficheiro áudio ou de vídeo no círculo ou navegue para o seu ficheiro.

    ![Upload media file Video Indexer](./media/customize-language-model/upload2.png)

É-lhe dada a opção de selecionar o **idioma de origem vídeo**. Selecione o drop-down e selecione um modelo de Linguagem que criou a partir da lista. Deve dizer a linguagem do seu modelo linguístico e o nome que lhe deu em parênteses.

Selecione a opção **Upload** na parte inferior da página e o seu novo vídeo será indexado usando o seu modelo Language.

### <a name="using-a-language-model-to-reindex"></a>Usando um modelo de linguagem para reindexo

Para utilizar o seu modelo Language para reindexar um vídeo na sua coleção, vá aos vídeos da sua **Conta** na página inicial do [Video Indexer](https://www.videoindexer.ai/) e paire sobre o nome do vídeo que pretende reindexar.

Verá opções para editar o seu vídeo, apagar-lhe o vídeo e reindex o seu vídeo. Selecione a opção de reindexar o seu vídeo.

![Reindex com Indexador de Vídeo](./media/customize-language-model/reindex1.png)

É-lhe dada a opção de selecionar o **idioma de origem vídeo** para reindexar o seu vídeo. Selecione o drop-down e selecione um modelo de Linguagem que criou a partir da lista. Deve dizer a linguagem do seu modelo linguístico e o nome que lhe deu em parênteses.

![Escolha o idioma de origem de vídeo — Reindex um vídeo com o Video Indexer](./media/customize-language-model/reindex.png)

Selecione o botão **Re-index** e o seu vídeo será reexexexuado utilizando o seu modelo Language.

## <a name="edit-a-language-model"></a>Editar um modelo de linguagem

Pode editar um modelo de idioma alterando o seu nome, adicionando-lhe ficheiros e eliminando ficheiros do mesmo.

Se adicionar ou eliminar ficheiros do modelo Language, terá de treinar novamente o modelo selecionando a opção **Comboio** verde.

### <a name="rename-the-language-model"></a>Mude o nome do modelo language

Pode alterar o nome do modelo Language selecionando o botão elipse **(...)** no lado direito do modelo idioma e selecionando **o Nome .**

Digite o novo nome e acerte entrar.

### <a name="add-files"></a>Adicionar ficheiros

Para adicionar um ficheiro de texto, **selecione Adicionar ficheiro**. O seu explorador de ficheiros abrirá.

Navegue para e selecione o ficheiro de texto. Pode adicionar vários ficheiros de texto a um modelo de idioma.

Também pode adicionar um ficheiro de texto selecionando o botão elipse **(...**) no lado direito do modelo idioma e selecionando **o ficheiro Adicionar**.

### <a name="delete-files"></a>Eliminar ficheiros

Para eliminar um ficheiro do modelo Language, selecione o botão elipse **(...**) no lado direito do ficheiro de texto e selecione **Delete**. Aparece uma nova janela a dizer-te que a eliminação não pode ser desfeita. Selecione a opção **Eliminar** na nova janela.

Esta ação remove completamente o ficheiro do modelo Language.

## <a name="delete-a-language-model"></a>Excluir um modelo de linguagem

Para eliminar um modelo de idioma da sua conta, selecione o botão elipse **(...)** no lado direito do modelo idioma e selecione **Delete**.

Aparece uma nova janela a dizer-te que a eliminação não pode ser desfeita. Selecione a opção **Eliminar** na nova janela.

Esta ação remove completamente o modelo idioma da sua conta. Qualquer vídeo que estivesse a usar o modelo linguagem apagado manterá o mesmo índice até que reindex o vídeo. Se voltar a usar o vídeo, pode atribuir um novo modelo de Linguagem ao vídeo. Caso contrário, o Video Indexer utilizará o seu modelo padrão para reindexar o vídeo.

## <a name="customize-language-models-by-correcting-transcripts"></a>Personalizar modelos de linguagem corrigindo transcrições

O Video Indexer suporta a personalização automática de modelos linguísticos com base nas correções reais que os utilizadores fazem às transcrições dos seus vídeos.

1. Para escamar correções a uma transcrição, abra o vídeo que pretende editar a partir dos seus Vídeos de Conta. Selecione o **separador Timeline.**

    ![Personalizar o separador de linha do tempo do modelo de linguagem — Índicer de Vídeo](./media/customize-language-model/timeline.png)

1. Selecione o ícone do lápis para editar a transcrição da sua transcrição.

    ![Personalizar transcrição de edição de modelo de linguagem — Índice de vídeo](./media/customize-language-model/edits.png)

    O Indexer de Vídeo captura todas as linhas que são corrigidas por si na transcrição do seu vídeo e adiciona-as automaticamente a um ficheiro de texto chamado "A partir de edições de transcrição". Estas edições são usadas para retreinar o modelo linguístico específico que foi usado para indexar este vídeo. 
    
    As edições que foram feitas na linha [temporal do widget](video-indexer-embed-widgets.md) também estão incluídas.
    
    Se não especificou um modelo de Linguagem ao indexar este vídeo, todas as edições para este vídeo serão armazenadas num modelo de Linguagem padrão chamado "Adaptações de Conta" dentro da linguagem detetada do vídeo.
    
    No caso de várias edições terem sido feitas para a mesma linha, apenas a última versão da linha corrigida será usada para atualizar o modelo idioma.  
    
    > [!NOTE]
    > Apenas são utilizadas correções texuais para a personalização. As correções que não envolvam palavras reais (por exemplo, marcas de pontuação ou espaços) não estão incluídas.
    
1. Verá que as correções de transcrição aparecem no separador idioma da página de personalização do modelo de conteúdo.

    ![Personalizar modelo de linguagem - Índice de Vídeo](./media/customize-language-model/customize.png)

   Para ver o ficheiro "A partir de edições de transcrição" para cada um dos seus modelos de Línguas, selecione-o para o abrir.

    ![A partir de edições de transcrição — Indexer de Vídeo](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Passos seguintes

[Personalizar modelo de linguagem usando APIs](customize-language-model-with-api.md)
