---
title: Personalize o modelo de idioma com o site do Indexer de Vídeo
titleSuffix: Azure Media Services
description: Saiba personalizar um modelo de Idioma com o website do Indexer de Vídeo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128089"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Personalize um modelo de idioma com o site do Indexer de Vídeo

O Indexer de vídeo permite criar modelos idiomas personalizados para personalizar o reconhecimento da fala através do upload de texto de adaptação, nomeadamente texto do domínio cujo vocabulário gostaria que o motor se adaptasse. Assim que treinar o seu modelo, serão reconhecidas novas palavras no texto de adaptação.

Para uma visão geral detalhada e boas práticas para modelos de idioma personalizados, consulte [Personalizar um modelo de Idioma com Indexer](customize-language-model-overview.md)de Vídeo .

Pode utilizar o website do Indexer de Vídeo para criar e editar modelos de Idioma personalizados na sua conta, conforme descrito neste tópico. Também pode utilizar a API, conforme descrito no [modelo Personalizar Language utilizando APIs](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Criar um modelo linguístico

1. Vá ao site do [Indexer](https://www.videoindexer.ai/) de Vídeo e inscreva-se.
2. Para personalizar um modelo na sua conta, selecione o botão de **personalização** do modelo Conteúdo no canto superior direito da página.

   ![Personalize o modelo de conteúdo no Indexer de Vídeo](./media/content-model-customization/content-model-customization.png)

3. Selecione o separador **Idioma.**

    Vê uma lista de línguas apoiadas.

    ![Lista de modelos de idiomas no Indexer de Vídeo](./media/customize-language-model/customize-language-model.png)

4. Sob o idioma que desejar, selecione **Adicionar modelo**.
5. Digite o nome para o modelo Idioma e entre.

    Este passo cria o modelo e dá a opção de enviar ficheiros de texto para o modelo.

6. Para adicionar um ficheiro de texto, selecione **Adicionar ficheiro**. O teu explorador de ficheiros vai abrir.

7. Navegue para e selecione o ficheiro de texto. Pode adicionar vários ficheiros de texto a um modelo Idioma.

    Também pode adicionar um ficheiro de texto selecionando o **botão ...** no lado direito do modelo Idioma e selecionando **ficheiro Adicionar**.

8. Assim que terminar o upload dos ficheiros de texto, selecione a opção **Comboio** verde.

    ![Modelo de linguagem de comboio em Indexer de vídeo](./media/customize-language-model/train-model.png)

O processo de treino pode demorar alguns minutos. Uma vez feito o treino, vê-se **treinado** ao lado do modelo. Pode pré-visualizar, transferir e eliminar o ficheiro do modelo.

![Modelo de linguagem treinado em Indexer de Vídeo](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Usando um modelo de linguagem em um novo vídeo

Para utilizar o seu modelo Idioma num novo vídeo, faça uma das seguintes ações:

* Selecione o botão **Upload** na parte superior da página.

    ![Carregador de botão Indexante de vídeo](./media/customize-language-model/upload.png)

* Largue o seu ficheiro de áudio ou vídeo para o círculo ou procure o seu ficheiro.

    ![Carregar media ficheiro sonorsocial Indexer vídeo](./media/customize-language-model/upload2.png)

É-lhe dada a opção de selecionar a linguagem fonte de **vídeo**. Selecione o drop-down e selecione um modelo Idioma que criou a partir da lista. Deve dizer a linguagem do seu modelo linguístico e o nome que lhe deu em parênteses.

Selecione a opção **Upload** na parte inferior da página e o seu novo vídeo será indexado utilizando o seu modelo Idioma.

### <a name="using-a-language-model-to-reindex"></a>Usando um modelo de linguagem para reindexar

Para utilizar o seu modelo De Idioma para reindexar um vídeo na sua coleção, vá aos vídeos da sua **Conta** na página inicial do [Indexer](https://www.videoindexer.ai/) de Vídeo e passe sobre o nome do vídeo que pretende reindexar.

Verá opções para editar o seu vídeo, apagar o vídeo e reindexar o seu vídeo. Selecione a opção de reindexar o seu vídeo.

![Reindex com Indexer de Vídeo](./media/customize-language-model/reindex1.png)

É-lhe dada a opção de selecionar o **idioma** de origem de vídeo para reindexar o seu vídeo com. Selecione o drop-down e selecione um modelo Idioma que criou a partir da lista. Deve dizer a linguagem do seu modelo linguístico e o nome que lhe deu em parênteses.

![Escolha linguagem de origem de vídeo — Reindexe um vídeo com indexante de vídeo](./media/customize-language-model/reindex.png)

Selecione o botão **Re-indexe** e o seu vídeo será reindexado utilizando o seu modelo Idioma.

## <a name="edit-a-language-model"></a>Editar um modelo de idioma

Pode editar um modelo Idioma alterando o seu nome, adicionando ficheiros e apagando ficheiros do mesmo.

Se adicionar ou eliminar ficheiros do modelo Idioma, terá de voltar a treinar o modelo selecionando a opção **Comboio** verde.

### <a name="rename-the-language-model"></a>Mude o nome do modelo Linguístico

Pode alterar o nome do modelo Idioma selecionando o botão elipse (**...**) no lado direito do modelo Idioma e selecionando O Nome de **Renome**.

Digite o novo nome e bata.

### <a name="add-files"></a>Adicionar ficheiros

Para adicionar um ficheiro de texto, selecione **Adicionar ficheiro**. O teu explorador de ficheiros vai abrir.

Navegue para e selecione o ficheiro de texto. Pode adicionar vários ficheiros de texto a um modelo Idioma.

Também pode adicionar um ficheiro de texto selecionando o botão de elipse **(...**) no lado direito do modelo Idioma e selecionando o **ficheiro Adicionar**.

### <a name="delete-files"></a>Eliminar ficheiros

Para eliminar um ficheiro do modelo Idioma, selecione o botão elipse (**...**) no lado direito do ficheiro de texto e selecione **Delete**. Aparece uma nova janela a dizer-te que a eliminação não pode ser desfeita. Selecione a opção **Eliminar** na nova janela.

Esta ação remove completamente o ficheiro do modelo Idioma.

## <a name="delete-a-language-model"></a>Eliminar um modelo de Linguagem

Para eliminar um modelo Idioma da sua conta, selecione o botão elipse (**...**) no lado direito do modelo Idioma e selecione **Delete**.

Aparece uma nova janela a dizer-te que a eliminação não pode ser desfeita. Selecione a opção **Eliminar** na nova janela.

Esta ação remove completamente o modelo Idioma da sua conta. Qualquer vídeo que estivesse a usar o modelo Idioma apagado manterá o mesmo índice até reindexar o vídeo. Se reindexar o vídeo, pode atribuir um novo modelo de Idioma ao vídeo. Caso contrário, o Indexer de Vídeo utilizará o seu modelo padrão para reindexar o vídeo.

## <a name="customize-language-models-by-correcting-transcripts"></a>Personalizar modelos de idioma corrigindo transcrições

O Indexer de vídeo suporta a personalização automática dos modelos Linguísticos com base nas correções reais que os utilizadores fazem às transcrições dos seus vídeos.

1. Para fazer correções a uma transcrição, abra o vídeo que pretende editar a partir dos vídeos da sua conta. Selecione o **separador Timeline.**

    ![Personalizar separador de linha do tempo do modelo de idioma — Indexante de vídeo](./media/customize-language-model/timeline.png)

1. Selecione o ícone do lápis para editar a transcrição da sua transcrição.

    ![Personalizar modelo de idioma editar transcrição — Indexador de Vídeo](./media/customize-language-model/edits.png)

    O Indexer de vídeo capta todas as linhas que são corrigidas por si na transcrição do seu vídeo e adiciona-as automaticamente a um ficheiro de texto chamado "From transcript edits". Estas edificações são usadas para retreinar o modelo linguístico específico que foi usado para indexar este vídeo.
    
    Se não especificou um modelo de Idioma ao indexar este vídeo, todas as edificações para este vídeo serão armazenadas num modelo de Idioma padrão chamado "Adaptações de Conta" dentro do idioma detetado do vídeo.
    
    Caso tenham sido feitas várias edições para a mesma linha, apenas a última versão da linha corrigida será utilizada para atualizar o modelo Idioma.  
    
    > [!NOTE]
    > Apenas são utilizadas correções texuais para a personalização. Correções que não envolvem palavras reais (por exemplo, marcas de pontuação ou espaços) não estão incluídas.
    
1. Verá que as correções de transcrição aparecem no separador Idioma da página de personalização do modelo de conteúdo.

    ![Personalizar modelo de idioma — Indexador de Vídeo](./media/customize-language-model/customize.png)

   Para ver o ficheiro "A partir de edagens de transcrição" para cada um dos seus modelos Idioma, selecione-o para o abrir.

    ![De edits de transcrição — Indexer de Vídeo](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Passos seguintes

[Personalize o modelo de idioma usando APIs](customize-language-model-with-api.md)
