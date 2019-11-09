---
title: Usar o site Video Indexer para personalizar um modelo Person-Azure
titleSuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo Person com o site Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 060d94d6181e894c18d268845b48eb802c52730c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838281"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personalizar um modelo Person com o site Video Indexer

Video Indexer dá suporte ao reconhecimento de celebridade para conteúdo de vídeo. O recurso de reconhecimento de celebridade abrange aproximadamente 1 milhão rostos com base na fonte de dados solicitada comumente, como o IMDB, a Wikipédia e os principais influenciadores do LinkedIn. Para obter uma visão geral detalhada, consulte [Personalizar um modelo Person no video indexer](customize-person-model-overview.md).

Você pode usar o site Video Indexer para editar faces que foram detectadas em um vídeo, conforme descrito neste tópico. Você também pode usar a API, conforme descrito em [Personalizar um modelo Person usando APIs](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Gerenciamento central de modelos de pessoa em sua conta

1. Para exibir, editar e excluir os modelos de pessoa em sua conta, navegue até o site Video Indexer e entre.
2. Clique no botão personalização do modelo de conteúdo no canto superior direito da página.

    ![Personalização do modelo de conteúdo](./media/customize-face-model/content-model-customization.png)
3. Selecione a guia pessoas.

    Você verá o modelo de pessoa padrão em sua conta. O modelo Person padrão mantém qualquer faces que você tenha editado ou alterado nas informações de seus vídeos para os quais você não especificou um modelo Person personalizado durante a indexação. 

    Se você criou outros modelos de pessoa, eles também serão listados nesta página. 

    ![Personalização do modelo de conteúdo](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Criar um novo modelo de pessoa

1. Clique no botão **+ Adicionar modelo** .

    ![Adicionar uma nova pessoa](./media/customize-face-model/add-new-person.png)
2. Insira o nome do modelo e clique no botão de seleção ao lado do nome.

    ![Adicionar uma nova pessoa](./media/customize-face-model/add-new-person2.png)

    Você criou um novo modelo de pessoa. Agora você pode adicionar faces ao novo modelo Person.
3. Clique no botão de menu lista e escolha **+ Adicionar pessoa**.

    ![Adicionar uma nova pessoa](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Adicionar uma nova pessoa a um modelo Person

> [!NOTE]
> Video Indexer permite adicionar várias pessoas com o mesmo nome em um modelo Person. No entanto, é recomendável que você forneça nomes de unque para cada pessoa em seu modelo para usabilidade e clareza.

1. Para adicionar uma nova face a um modelo Person, clique no botão de menu lista ao lado do modelo Person ao qual você deseja adicionar a face.
1. Clique em **+ Adicionar pessoa** no menu.

    ![Adicionar uma nova face à pessoa](./media/customize-face-model/add-new-face.png)
 
    Um pop-up solicitará que você preencha os detalhes da pessoa. Digite o nome da pessoa e clique no botão de seleção.

    ![Adicionar uma nova face à pessoa](./media/customize-face-model/add-new-face2.png)
 
Você pode escolher no explorador de arquivos ou arrastar e soltar as imagens de face da face. Video Indexer usará todos os tipos de arquivo de imagem padrão (por exemplo, JPG, PNG e muito mais).

Video Indexer deve ser capaz de detectar ocorrências dessa pessoa nos vídeos futuros que você indexar e os vídeos atuais já indexados, usando o modelo Person ao qual você adicionou essa nova face. O reconhecimento da pessoa em seus vídeos atuais pode levar algum tempo para entrar em vigor, pois esse é um processo em lotes.


## <a name="rename-a-person-model"></a>Renomear um modelo Person

Você pode renomear qualquer modelo Person em sua conta, incluindo o modelo Person padrão. Mesmo que você renomeie o modelo Person padrão, ele ainda servirá como o modelo Person padrão em sua conta.

1. Clique no botão de menu lista ao lado do modelo Person que você deseja renomear.
2. Clique em **renomear** no menu.

    ![Renomear uma pessoa](./media/customize-face-model/rename-person.png)
3. Clique no nome atual do modelo e digite seu novo nome.

    ![Renomear uma pessoa](./media/customize-face-model/rename-person2.png)
4. Clique no botão de verificação para que seu modelo seja renomeado.

## <a name="delete-a-person-model"></a>Excluir um modelo Person

Você pode excluir qualquer modelo de pessoa que você criou em sua conta. No entanto, você não pode excluir o modelo de pessoa padrão.

1. Clique em **excluir** no menu.

    ![Excluir uma pessoa](./media/customize-face-model/delete-person.png)
    
    Um pop-up será exibido e você será notificado de que essa ação excluirá o modelo Person e todas as pessoas e os arquivos que ele contém. Esta ação não pode ser desfeita. 

    ![Excluir uma pessoa](./media/customize-face-model/delete-person2.png)
1. Se tiver certeza, clique em excluir novamente.

> [!NOTE]
> Os vídeos existentes que foram indexados usando esse modelo de pessoa (agora excluído) não oferecerão suporte para a capacidade de atualizar os nomes das faces que aparecem no vídeo. Você poderá editar os nomes de rostos nestes vídeos somente depois de reindexá-los usando outro modelo de pessoa. Se você reindexar sem especificar um modelo Person, o modelo padrão será usado. 

## <a name="manage-existing-people-in-a-person-model"></a>Gerenciar pessoas existentes em um modelo de pessoa

Para examinar o conteúdo de qualquer um dos seus modelos de pessoa, clique na seta ao lado do nome do modelo Person.
A lista suspensa mostra todas as pessoas desse modelo Person específico. Se você clicar no botão de menu lista ao lado de cada pessoa, verá as opções gerenciar, renomear e excluir.  

![Adicionar uma nova face à pessoa](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Renomear uma pessoa

1. Para renomear uma pessoa em seu modelo de pessoa, clique no botão de menu lista e escolha **renomear** no menu lista.
1. Clique no nome atual da pessoa e digite seu novo nome.
1. Clique no botão de seleção e a pessoa será renomeada.

### <a name="delete-a-person"></a>Excluir uma pessoa

1. Para excluir uma pessoa do modelo Person, clique no botão de menu lista e escolha **excluir** no menu lista.
1. Um pop-up informa que essa ação excluirá a pessoa e que essa ação não pode ser desfeita.
1. Clique em excluir novamente e isso removerá a pessoa do modelo Person.

### <a name="manage-a-person"></a>Gerenciar uma pessoa

Se você clicar em **gerenciar**, verá todas as faces das quais esse modelo de pessoa está sendo treinado. Esses rostos são provenientes de ocorrências dessa pessoa em vídeos que usam esse modelo de pessoa ou de imagens que você carregou manualmente. 

Você pode adicionar mais faces à pessoa clicando em Adicionar imagens.

Você pode usar o painel gerenciar para renomear a pessoa e excluir a pessoa do modelo Person.

## <a name="use-a-person-model-to-index-a-video"></a>Usar um modelo Person para indexar um vídeo

Você pode usar um modelo Person para indexar seu novo vídeo atribuindo o modelo Person durante o upload do vídeo.

Para usar seu modelo de pessoa em um novo vídeo, faça o seguinte:

1. Clique no botão **carregar** na parte superior da página.

    ![Carregar](./media/customize-face-model/upload.png)
1. Solte o arquivo de vídeo no círculo ou procure o arquivo.
1. Clique na seta opções avançadas.

    ![Carregar](./media/customize-face-model/upload2.png)
1. Clique na lista suspensa e selecione o modelo Person que você criou.

    ![Carregar](./media/customize-face-model/upload3.png)
1. Clique na opção carregar na parte inferior da página e o novo vídeo será indexado usando seu modelo Person.

Se você não especificar um modelo de pessoa durante o carregamento, Video Indexer indexará o vídeo usando o modelo de pessoa padrão em sua conta.

## <a name="use-a-person-model-to-reindex-a-video"></a>Usar um modelo Person para reindexar um vídeo 

Para usar um modelo Person para reindexar um vídeo em sua coleção, vá para seus vídeos de conta no Video Indexer home page e passe o mouse sobre o nome do vídeo que você deseja reindexar.

Você vê opções para editar, excluir e reindexar seu vídeo. 

1. Clique na opção para reindexar o vídeo.

    ![Reindexar](./media/customize-face-model/reindex.png)

    Agora você pode selecionar o modelo Person com o qual reindexar seu vídeo.
1. Clique na lista suspensa e selecione o modelo Person que você deseja usar. 

    ![Reindexar](./media/customize-face-model/reindex2.png)

1. Clique no botão **reindexar** e seu vídeo será reindexado usando seu modelo Person.

Todas as novas edições feitas nas faces detectadas e reconhecidas no vídeo que você acabou de reindexar serão salvas no modelo Person que você usou para reindexar o vídeo.

## <a name="managing-people-in-your-videos"></a>Gerenciando pessoas em seus vídeos

Você pode gerenciar as faces detectadas e as pessoas que foram reconhecidas nos vídeos que você indexa editando e excluindo rostos.

A exclusão de uma face remove uma face específica das informações do vídeo.

Editar uma face, renomeia uma face que é detectada e possivelmente reconhecida em seu vídeo. Quando você edita uma face em seu vídeo, esse nome é salvo como uma entrada Person no modelo Person que foi atribuído ao vídeo durante o upload e a indexação.

Se você não atribuir um modelo Person ao vídeo durante o carregamento, a edição será salva no modelo de pessoa padrão da sua conta.

### <a name="edit-a-face"></a>Editar uma face


> [!NOTE]
> Se um modelo de pessoa tiver duas ou mais pessoas diferentes com o mesmo nome, você não poderá marcar esse nome nos vídeos que usam esse modelo de pessoa. Você só poderá fazer alterações em pessoas que compartilham esse nome na guia pessoas da página personalização do modelo de conteúdo no Video Indexer. Por esse motivo, é recomendável que você forneça nomes exclusivos para cada pessoa em seu modelo de pessoa.

1. Navegue até o site Video Indexer e entre.
1. Procure um vídeo que você deseja exibir e editar em sua conta.
1. Para editar uma face em seu vídeo, vá para a guia insights e clique no ícone de lápis no canto superior direito da janela.

    ![Editar uma face em seu vídeo](./media/customize-face-model/edit-face.png)
1. Clique em qualquer uma das faces detectadas e altere seus nomes de "desconhecido #X" (ou o nome que foi atribuído anteriormente à face). 
1. Depois de digitar o novo nome, clique no ícone de verificação ao lado do novo nome. Isso salva o novo nome e reconhece e nomeia todas as ocorrências dessa face em seus outros vídeos atuais e nos vídeos futuros que você carrega. O reconhecimento da face em seus outros vídeos atuais pode levar algum tempo para entrar em vigor, pois esse é um processo em lotes.

Se você nomear uma face com o nome de uma pessoa existente no modelo Person que o vídeo está usando, isso mesclará as imagens de face detectadas desse vídeo da pessoa com o que já existe no modelo. Se você nomear uma face com um nome completamente novo, isso criará uma nova entrada de pessoa no modelo Person que o vídeo está usando. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Excluir uma face

Para excluir uma face detectada em seu vídeo, vá para o painel insights e clique no ícone de lápis no canto superior direito do painel. Clique na opção Excluir abaixo do nome da face. Isso removerá a aparência detectada do vídeo. A face da pessoa ainda será detectada nos outros vídeos em que aparece, mas você poderá excluir a face desses vídeos também depois que eles tiverem sido indexados. A pessoa, se tiver sido nomeada, também continuará existindo no modelo Person que foi usado para indexar o vídeo do qual você excluiu a face, a menos que você exclua especificamente a pessoa do modelo Person.

![Excluir uma face em seu vídeo](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Passos seguintes

[Personalizar o modelo Person usando APIs](customize-person-model-with-api.md)
