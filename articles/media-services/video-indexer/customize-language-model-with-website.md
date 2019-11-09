---
title: Use o site Video Indexer para personalizar um modelo de linguagem – Azure
titleSuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de linguagem com o site Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 5d92cb02a0ac52b317cf9d4b6c8e0278f9291910
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838445"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Personalizar um modelo de linguagem com o site Video Indexer

Video Indexer permite criar modelos de linguagem personalizados para personalizar o reconhecimento de fala carregando texto de adaptação, ou seja, o texto do domínio cujo vocabulário você deseja que o mecanismo se adapte. Depois de treinar seu modelo, novas palavras que aparecem no texto de adaptação serão reconhecidas. 

Para obter uma visão geral detalhada e práticas recomendadas para modelos de linguagem personalizados, consulte [Personalizar um modelo de linguagem com Video indexer](customize-language-model-overview.md).

Você pode usar o site Video Indexer para criar e editar modelos de idioma personalizados em sua conta, conforme descrito neste tópico. Você também pode usar a API, conforme descrito em [Personalizar o modelo de linguagem usando APIs](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Criar um modelo de linguagem

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
2. Para personalizar um modelo em sua conta, clique no botão **personalização do modelo de conteúdo** no canto superior direito da página.

   ![Personalizar modelo de conteúdo](./media/content-model-customization/content-model-customization.png)

3. Selecione a guia **idioma** .

    Você verá uma lista de idiomas com suporte. 

    ![Personalizar modelo de linguagem](./media/customize-language-model/customize-language-model.png)

4. No idioma desejado, clique em **Adicionar modelo**.
5. Digite o nome do modelo de idioma e pressione Enter.

    Isso cria o modelo e dá a opção de carregar arquivos de texto para o modelo.
6. Para adicionar um arquivo de texto, clique em **Adicionar arquivo**. Isso abre o explorador de arquivos.

7. Navegue até e selecione o arquivo de texto. Você pode adicionar vários arquivos de texto a um modelo de idioma.

    Você também pode adicionar um arquivo de texto clicando no botão **...** no lado direito do modelo de idioma e selecionando **Adicionar arquivo**.
8. Quando terminar de carregar os arquivos de texto, clique na opção **trem** verde.

    ![Treinar modelo de idioma](./media/customize-language-model/train-model.png)

O processo de treinamento pode levar alguns minutos. Quando o treinamento for concluído, você verá **treinado** ao lado do modelo. Você pode visualizar, baixar e excluir o arquivo do modelo.

![Modelo de idioma treinado](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Usando um modelo de linguagem em um novo vídeo

Para usar seu modelo de idioma em um novo vídeo, siga um destes procedimentos:

* Clique no botão **carregar** na parte superior da página 

    ![Carregar](./media/customize-language-model/upload.png)
* Descarte seu arquivo de áudio ou vídeo no círculo ou procure o arquivo

    ![Carregar](./media/customize-language-model/upload2.png)

Isso lhe dará a opção de selecionar o **idioma da fonte de vídeo**. Clique no menu suspenso e selecione um modelo de idioma que você criou na lista. Ele deve dizer o idioma do seu modelo de idioma e o nome que você o forneceu entre parênteses.

Clique na opção **carregar** na parte inferior da página e o novo vídeo será indexado usando seu modelo de linguagem.

### <a name="using-a-language-model-to-reindex"></a>Usando um modelo de linguagem para reindexar

Para usar seu modelo de linguagem para reindexar um vídeo em sua coleção, acesse os vídeos da sua **conta** no [Video indexer](https://www.videoindexer.ai/) Home Page e passe o mouse sobre o nome do vídeo que você deseja reindexar.

Você verá opções para editar seu vídeo, excluir seu vídeo e reindexar seu vídeo. Clique na opção para reindexar o vídeo.

![Reindexar](./media/customize-language-model/reindex1.png)

Isso lhe dá a opção de selecionar o **idioma de origem do vídeo** com o qual reindexar o vídeo. Clique no menu suspenso e selecione um modelo de idioma que você criou na lista. Ele deve dizer o idioma do seu modelo de idioma e o nome que você o forneceu entre parênteses.

![Reindexar](./media/customize-language-model/reindex.png)

Clique no botão **reindexar** e seu vídeo será reindexado usando o modelo de linguagem.

## <a name="edit-a-language-model"></a>Editar um modelo de linguagem

Você pode editar um modelo de idioma alterando seu nome, adicionando arquivos a ele e excluindo arquivos dele.

Se você adicionar ou excluir arquivos do modelo de idioma, precisará treinar o modelo novamente clicando na opção de **trem** verde.

### <a name="rename-the-language-model"></a>Renomear o modelo de idioma

Você pode alterar o nome do modelo de idioma clicando em **...** no lado direito do modelo de idioma e selecionando **renomear**. 

Digite o novo nome e pressione Enter.

### <a name="add-files"></a>Adicionar arquivos

Para adicionar um arquivo de texto, clique em **Adicionar arquivo**. Isso abre o explorador de arquivos.

Navegue até e selecione o arquivo de texto. Você pode adicionar vários arquivos de texto a um modelo de idioma.

Você também pode adicionar um arquivo de texto clicando no botão **...** no lado direito do modelo de idioma e selecionando **Adicionar arquivo**.

### <a name="delete-files"></a>Eliminar ficheiros

Para excluir um arquivo do modelo de idioma, clique no botão **...** no lado direito do arquivo de texto e selecione **excluir**. Isso abre uma nova janela informando que a exclusão não pode ser desfeita. Clique na opção **excluir** na janela novo.

Essa ação remove o arquivo completamente do modelo de linguagem.

## <a name="delete-a-language-model"></a>Excluir um modelo de linguagem

Para excluir um modelo de idioma da sua conta, clique no botão **...** no lado direito do modelo de idioma e selecione **excluir**.

Isso abre uma nova janela informando que a exclusão não pode ser desfeita. Clique na opção **excluir** na janela novo.

Essa ação remove o modelo de idioma completamente da sua conta. Qualquer vídeo que estava usando o modelo de linguagem excluído manterá o mesmo índice até que você reindexe o vídeo. Se você reindexar o vídeo, poderá atribuir um novo modelo de linguagem ao vídeo. Caso contrário, Video Indexer usará seu modelo padrão para reindexar o vídeo. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Personalizar modelos de linguagem corrigindo transcrições

O Video Indexer dá suporte à personalização automática de modelos de linguagem com base nas correções reais que os usuários fazem nas transcrições de seus vídeos.

1. Para fazer correções em uma transcrição, abra o vídeo que você deseja editar de seus vídeos de conta. Selecione a guia **linha do tempo** .

    ![Personalizar modelo de linguagem](./media/customize-language-model/timeline.png)
1. Clique no ícone de lápis para editar a transcrição de sua transcrição. 

    ![Personalizar modelo de linguagem](./media/customize-language-model/edits.png)

    Video Indexer captura todas as linhas que são corrigidas por você na transcrição do vídeo e as adiciona automaticamente a um arquivo de texto chamado "das edições de transcrição". Essas edições são usadas para treinar novamente o modelo de linguagem específico que foi usado para indexar este vídeo. 
    
    Se você não especificou um modelo de linguagem ao indexar este vídeo, todas as edições desse vídeo serão armazenadas em um modelo de idioma padrão chamado adaptações de conta no idioma detectado do vídeo. 
    
    Caso várias edições tenham sido feitas na mesma linha, somente a última versão da linha corrigida será usada para atualizar o modelo de idioma.  
    
    > [!NOTE]
    > Somente correções textuais são usadas para a personalização. Isso significa que as correções que não envolvem palavras reais (por exemplo, marcas ou espaços de pontuação) não são incluídas. 
    
1. Você verá as correções de transcrição aparecerem na guia idioma da página personalização do modelo de conteúdo.

    ![Personalizar modelo de linguagem](./media/customize-language-model/customize.png)

   Para ver o arquivo "de edições de transcrição" para cada um dos seus modelos de idioma, clique nele para abri-lo. 

    ![De edições de transcrição](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Passos seguintes

[Personalizar o modelo de linguagem usando APIs](customize-language-model-with-api.md)
