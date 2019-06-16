---
title: Utilizar o site do indexador de vídeo para personalizar um modelo de idioma - Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de idioma com o site do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f98cdcab2d108f8dd9d40e3770498ad17b2a8a88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799624"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Personalizar um modelo de idioma com o site do Video Indexer

O Video Indexer permite-lhe criar modelos de linguagem personalizados para personalizar o reconhecimento de voz através do carregamento de texto de adaptação, ou seja, o texto do domínio cujo vocabulário que pretende que o mecanismo para se adaptar às. Depois de preparar o seu modelo, que aparece no texto de adaptação de novas palavras serão reconhecidas. 

Para obter uma descrição detalhada e melhores práticas para modelos de idioma personalizado, consulte [personalizar um modelo de idioma com o indexador de vídeo](customize-language-model-overview.md).

Pode utilizar o site do indexador de vídeo para criar e editar os modelos de linguagem personalizados na sua conta, conforme descrito neste tópico. Também pode utilizar a API, conforme descrito em [modelo de idioma de personalizar com APIs](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Criar um modelo de idioma

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
2. Para personalizar um modelo na sua conta, clique nas **personalização do modelo de conteúdo** botão no canto superior direito da página.

   ![Personalizar o modelo de conteúdo](./media/content-model-customization/content-model-customization.png)

3. Selecione o **linguagem** separador.

    É apresentada uma lista dos idiomas com suporte. 

    ![Personalizar o modelo de idioma](./media/customize-language-model/customize-language-model.png)

4. Sob o idioma que pretende, clique em **modelo de adicionar**.
5. Escreva o nome para o modelo de idioma e clique em Inserir.

    Isso cria o modelo e dá a opção de carregar ficheiros de texto para o modelo.
6. Para adicionar um arquivo de texto, clique em **adicionar ficheiro**. Esta ação abre o Explorador de ficheiros.

7. Navegue até e selecione o ficheiro de texto. Pode adicionar vários ficheiros de texto para um modelo de idioma.

    Também pode adicionar um arquivo de texto ao clicar no **...**  botão à direita do modelo de idioma e selecionando **adicionar ficheiro**.
8. Depois de concluída a carregar os ficheiros de texto, clique no verde **Train** opção.

    ![Preparar o modelo de idioma](./media/customize-language-model/train-model.png)

O processo de treinamento pode demorar alguns minutos. Depois do treinamento estiver concluído, verá **Trained** junto ao modelo. Pode pré-visualizar, transferir e eliminar o ficheiro do modelo.

![Modelo de idioma com formação](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Usando um modelo de idioma num novo vídeo

Para utilizar o seu modelo de idioma num novo vídeo, efetue um dos seguintes procedimentos:

* Clique nas **carregar** botão no topo da página 

    ![Carregamento](./media/customize-language-model/upload.png)
* Remova o ficheiro de vídeo ou áudio no círculo ou procure o ficheiro

    ![Carregamento](./media/customize-language-model/upload2.png)

Isso lhe dará a opção de selecionar o **idioma de origem do vídeo**. Clique no menu pendente e selecione um modelo de idioma que criou na lista. Ele deverá indicar o idioma do seu modelo de idioma e o nome que deu a parênteses.

Clique nas **carregar** opção na parte inferior da página e seu novo vídeo será indexada com o seu modelo de idioma.

### <a name="using-a-language-model-to-reindex"></a>Utilizar um modelo de idioma para a reindexar

Para utilizar o seu modelo de linguagem reindexar um vídeo em sua coleção, aceda à sua **vídeos da conta** no [Video Indexer](https://www.videoindexer.ai/) home page e coloque o cursor sobre o nome do vídeo que pretende reindexar.

Vê as opções para editar o seu vídeo, eliminar, vídeo e reindexar o seu vídeo. Clique na opção reindexar o seu vídeo.

![Reindex](./media/customize-language-model/reindex1.png)

Isso lhe dá a opção de selecionar o **idioma de origem do vídeo** reindexar o seu vídeo. Clique no menu pendente e selecione um modelo de idioma que criou na lista. Ele deverá indicar o idioma do seu modelo de idioma e o nome que deu a parênteses.

![Reindex](./media/customize-language-model/reindex.png)

Clique nas **novamente de índice** botão e o seu vídeo irão ser reindexados com o modelo de idioma.

## <a name="edit-a-language-model"></a>Editar um modelo de idioma

Pode editar um modelo de idioma ao alterar o seu nome, adicionar ficheiros ao mesmo e eliminar ficheiros a partir do mesmo.

Se adicionar ou eliminar ficheiros do modelo de idioma, terá de preparar o modelo novamente ao clicar no verde **treinar** opção.

### <a name="rename-the-language-model"></a>Mudar o nome do modelo de idioma

Pode alterar o nome do modelo de idioma ao clicar em **...**  no lado direito da linguagem de modelo e selecionando **mudar o nome**. 

Introduza o tipo no novo nome e acessos.

### <a name="add-files"></a>Adicionar ficheiros

Para adicionar um arquivo de texto, clique em **adicionar ficheiro**. Esta ação abre o Explorador de ficheiros.

Navegue até e selecione o ficheiro de texto. Pode adicionar vários ficheiros de texto para um modelo de idioma.

Também pode adicionar um arquivo de texto ao clicar no **...**  botão à direita do modelo de idioma e selecionando **adicionar ficheiro**.

### <a name="delete-files"></a>Eliminar ficheiros

Para eliminar um ficheiro do modelo de idioma, clique no **...**  no lado direito do ficheiro de texto e selecione **eliminar**. Isso abre uma nova janela informá-lo de que a eliminação não pode ser anulada. Clique nas **eliminar** opção na nova janela.

Esta ação remove completamente o ficheiro do modelo de idioma.

## <a name="delete-a-language-model"></a>Eliminar um modelo de idioma

Para eliminar um modelo de idioma da sua conta, clique no **...**  no lado direito do modelo de idioma e selecione **eliminar**.

Isso abre uma nova janela informá-lo de que a eliminação não pode ser anulada. Clique nas **eliminar** opção na nova janela.

Esta ação remove completamente o modelo de idioma da sua conta. Qualquer vídeo que estava a utilizar o modelo de idioma eliminado irá manter o mesmo índice até novamente indexar o vídeo. Se voltar a indexar o vídeo, pode atribuir um novo modelo de idioma para o vídeo. Caso contrário, o Video Indexer irá utilizar o modelo padrão para voltar a indexar o vídeo. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Personalizar modelos de linguagem ao corrigir transcrições

O Video Indexer suporta a personalização automática de linguagem de modelos com base nos utilizadores correções real fazer transcrições dos seus vídeos.

1. Para fazer correções numa transcrição, abra o vídeo que pretende editar dos seus vídeos de conta. Selecione o **linha cronológica** separador.

    ![Personalizar o modelo de idioma](./media/customize-language-model/timeline.png)
1. Clique no ícone de lápis para editar as transcrições do seu transcrição. 

    ![Personalizar o modelo de idioma](./media/customize-language-model/edits.png)

    O Video Indexer captura todas as linhas que são corrigidas por si na transcrição do vídeo e adiciona-as automaticamente num arquivo de texto chamado "de edições de transcrição". Estas edições são utilizadas para voltar a preparar o modelo de idioma específico, que foi utilizado para este vídeo de índice. 
    
    Se não tiver especificado um modelo de idioma durante a indexação neste vídeo, todas as edições para este vídeo serão armazenadas num modelo de idioma padrão chamado adaptações de conta dentro do idioma detetado do vídeo. 
    
    No caso de várias edições de foram efetuadas para a mesma linha, apenas a última versão da linha corrigida será utilizada para atualizar o modelo de idioma.  
    
    > [!NOTE]
    > Apenas as correções textuais são utilizadas para a personalização. Isso significa que as correções que não envolvem palavras reais (por exemplo, marcas de pontuação ou espaços) não estão incluídas. 
    
1. Verá as correções de transcrição aparecem no separador de idioma da página de personalização do modelo de conteúdo.

    ![Personalizar o modelo de idioma](./media/customize-language-model/customize.png)

   Para ver o ficheiro "de transcrição edições" para cada um dos seus modelos de linguagem, clique nela para abri-lo. 

    ![A partir de edições de transcrição](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Passos Seguintes

[Personalizar o modelo de idioma com APIs](customize-language-model-with-api.md)
