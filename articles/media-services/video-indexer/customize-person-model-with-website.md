---
title: Utilizar o site do indexador de vídeo para personalizar um modelo de pessoa - Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de pessoa com o site do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: 8dd535d97e40fe1dd4358d782db60940af1dd95d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58892845"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personalizar um modelo de pessoa com o site do Video Indexer

O Video Indexer oferece suporte a reconhecimento de celebridade para conteúdos de vídeo. O recurso de reconhecimento de celebridade abrange aproximadamente os rostos de um milhão com base na origem de dados frequentemente pedidos como IMDB, Wikipedia e influenciadores principais do LinkedIn. Para obter uma descrição detalhada, consulte [personalizar um modelo de pessoa Video Indexer](customize-person-model-overview.md).

Pode utilizar o site de indexador de vídeos para editar rostos que foram detetados um vídeo, conforme descrito neste tópico. Também pode utilizar a API, conforme descrito em [personalizar um modelo de pessoa com APIs](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Gerenciamento central dos modelos de pessoa em sua conta

1. Para ver, editar e eliminar os modelos de pessoa na sua conta, navegue para o site do Video Indexer e inicie sessão.
2. Clique no botão de personalização do modelo de conteúdo no canto superior direito da página.

    ![Personalização do modelo de conteúdo](./media/customize-face-model/content-model-customization.png)
3. Selecione o separador de pessoas.

    Verá o modelo predefinido pessoa na sua conta. O modelo de pessoa predefinido contém qualquer rostos pode tiver editada ou alterada nas informações dos seus vídeos para o qual não tiver especificado um modelo de pessoa personalizado durante a indexação. 

    Se tiver criado outros modelos de pessoa, estes serão apresentados nesta página também. 

    ![Personalização do modelo de conteúdo](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Criar um novo modelo de pessoa

1. Clique nas **+ adicionar modelo** botão.

    ![Adicionar uma nova pessoa](./media/customize-face-model/add-new-person.png)
2. Introduza o nome do modelo e clique no botão de verificação junto ao nome.

    ![Adicionar uma nova pessoa](./media/customize-face-model/add-new-person2.png)

    Criou um novo modelo de pessoa. Agora pode adicionar rostos para o novo modelo de pessoa.
3. Clique no botão de menu de lista e escolha **+ Adicionar pessoa**.

    ![Adicionar uma nova pessoa](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Adicionar uma nova pessoa para um modelo de pessoa

> [!NOTE]
> O Video Indexer permite-lhe adicionar várias pessoas com o mesmo nome em modelos de pessoa. No entanto, é recomendável que dê nomes unque a cada pessoa no seu modelo para efeitos de clareza e usabilidade.

1. Para adicionar uma nova face a um modelo de pessoa, clique no botão de menu de lista junto ao modelo de pessoa que pretende adicionar a face para.
1. Clique em **+ Adicionar pessoa** no menu.

    ![Adicionar uma nova face à pessoa](./media/customize-face-model/add-new-face.png)
 
    Um pop-up irá solicitar-lhe o preenchimento detalhes da pessoa. Escreva o nome da pessoa e clique no botão de verificação.

    ![Adicionar uma nova face à pessoa](./media/customize-face-model/add-new-face2.png)
 
Em seguida, pode escolher a partir do Explorador de ficheiros ou arraste e largue as imagens de rosto do mostrador da. O Video Indexer levará a imagem padrão de todos os tipos de ficheiro (ex: JPG, PNG e muito mais).

O Video Indexer deve ser capaz de detetar ocorrências desta pessoa nos vídeos futuros desse índice e os vídeos atuais que já tinha indexada, usando o modelo de pessoa à qual adicionou esta nova face a. Reconhecimento da pessoa em seus vídeos atuais poderá demorar algum tempo a entrar em vigor, como se trata de um processo em lotes.


## <a name="rename-a-person-model"></a>Mudar o nome de um modelo de pessoa

Pode mudar o nome de modelo qualquer pessoa na sua conta, incluindo o modelo predefinido pessoa. Mesmo se renomear seu modelo de pessoa padrão, ele ainda irá servir como o modelo de pessoa padrão na sua conta.

1. Clique no botão de menu de lista junto ao modelo de pessoa que pretende mudar o nome.
2. Clique em **mudar o nome** no menu.

    ![Mudar o nome de uma pessoa](./media/customize-face-model/rename-person.png)
3. Clique no nome do modelo atual e escreva o nome do novo.

    ![Mudar o nome de uma pessoa](./media/customize-face-model/rename-person2.png)
4. Clique no botão de verificação para o modelo ser mudado.

## <a name="delete-a-person-model"></a>Eliminar um modelo de pessoa

Pode eliminar qualquer modelo de pessoa que criou na sua conta. No entanto, não é possível eliminar o seu modelo de pessoa padrão.

1. Clique em **eliminar** no menu.

    ![Eliminar uma pessoa](./media/customize-face-model/delete-person.png)
    
    Um pop-up serão apresentados e notificá-lo de que esta ação irá eliminar o modelo de pessoa e todas as pessoas e os ficheiros nele contidos. Esta ação não pode ser anulada. 

    ![Eliminar uma pessoa](./media/customize-face-model/delete-person2.png)
1. Se tiver certeza, clique em eliminar novamente.

> [!NOTE]
> Os vídeos existentes que foram indexados usando esse modelo de pessoa (agora eliminado) não irão suportar a capacidade para atualizar os nomes dos caras que aparecem no vídeo. Será capaz de editar os nomes de faces nestes vídeos apenas depois de reindexá-los usando o modelo de outra pessoa. Se a reindexar sem especificar um modelo de pessoa, o modelo padrão será utilizado. 

## <a name="manage-existing-people-in-a-person-model"></a>Gerir as pessoas existentes num modelo de pessoa

Para ver o conteúdo de qualquer um dos seus modelos de pessoa, clique na seta junto ao nome do modelo de pessoa.
Na lista suspensa mostra todas as pessoas nesse modelo de pessoa específico. Se clicar no botão de menu de lista junto a cada uma das pessoas, veja gerir, mudar o nome e opções de eliminar.  

![Adicionar uma nova face à pessoa](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Mudar o nome de uma pessoa

1. Para mudar o nome de uma pessoa no seu modelo de pessoa, clique no botão de menu de lista e escolha **mudar o nome** no menu de lista.
1. Clique no nome atual da pessoa e escreva o nome do novo.
1. Clique no botão de verificação e será possível mudar o nome da pessoa.

### <a name="delete-a-person"></a>Eliminar uma pessoa

1. Para eliminar uma pessoa do seu modelo de pessoa, clique no botão de menu de lista e escolha **eliminar** no menu de lista.
1. Um pop-up indica que esta ação irá eliminar a pessoa e que esta ação não pode ser anulada.
1. Clique em eliminar novamente e esta ação irá remover a pessoa a partir do modelo de pessoa.

### <a name="manage-a-person"></a>Gerir uma pessoa

Se clicar em **gerir**, verá todos os rostos que esse modelo de pessoa está a ser preparado a partir de. Estes rostos vêm de ocorrências de que a pessoa nos vídeos que usa esse modelo de pessoa ou de imagens que têm de ser carregados manualmente. 

Pode adicionar mais rostos para a pessoa ao clicar em Adicionar imagens.

Pode utilizar o painel de gerenciar para mudar o nome da pessoa e eliminar a pessoa do modelo de pessoa.

## <a name="use-a-person-model-to-index-a-video"></a>Utilize um modelo de pessoa para um vídeo de índice

Pode utilizar um modelo de pessoa para indexar o seu novo vídeo ao atribuir o modelo de pessoa durante o carregamento do vídeo.

Para utilizar o seu modelo de pessoa num novo vídeo, faça o seguinte:

1. Clique nas **carregar** botão no topo da página.

    ![Carregar](./media/customize-face-model/upload.png)
1. Remover o seu ficheiro de vídeo no círculo ou procure o ficheiro.
1. Clique na seta de opções avançadas.

    ![Carregar](./media/customize-face-model/upload2.png)
1. Clique na lista pendente e selecione o modelo de pessoa que criou.

    ![Carregar](./media/customize-face-model/upload3.png)
1. Clique na opção de carregamento na parte inferior da página, e seu novo vídeo será indexado com o seu modelo de pessoa.

Se não especificar um modelo de pessoa durante o carregamento, o Video Indexer serão indexar o vídeo com o modelo predefinido pessoa na sua conta.

## <a name="use-a-person-model-to-reindex-a-video"></a>Utilizar um modelo de pessoa reindexar um vídeo 

Para utilizar um modelo de pessoa reindexar um vídeo em sua coleção, aceda aos seus vídeos de conta na home page do indexador de vídeos e coloque o cursor sobre o nome do vídeo que pretende reindexar.

Vê as opções para editar, eliminar e reindexar o seu vídeo. 

1. Clique na opção reindexar o seu vídeo.

    ![Reindex](./media/customize-face-model/reindex.png)

    Agora, pode selecionar o modelo de pessoa reindexar o seu vídeo.
1. Clique na lista pendente e selecione o modelo de pessoa que pretende utilizar. 

    ![Reindex](./media/customize-face-model/reindex2.png)

1. Clique nas **reindexar** botão e o seu vídeo irão ser reindexados com o seu modelo de pessoa.

As novas edições que fizer para rostos detetados e reconhecido no vídeo que acabou de ser reindexados serão guardadas no modelo de pessoa que utilizou para a reindexar o vídeo.

## <a name="managing-people-in-your-videos"></a>Gerenciamento de pessoas nos seus vídeos

Pode gerenciar os rostos que são detetados e as pessoas que foram reconhecidas nos vídeos que indexar por edição e exclusão de rostos.

A eliminar um rosto, remove um rosto específico de informações do vídeo.

Editar um rosto, muda o nome de um rosto que é detectado e possivelmente reconhecido no seu vídeo. Ao editar um rosto no seu vídeo, esse nome é salvo como uma entrada de person no modelo de pessoa que foi atribuída ao vídeo durante o carregamento e a indexação.

Se não atribuir um modelo de pessoa para o vídeo durante o carregamento, a sua edição é salva no modelo de pessoa da sua conta predefinida.

### <a name="edit-a-face"></a>Editar um rosto


> [!NOTE]
> Se um modelo de pessoa tiver duas ou mais pessoas diferentes com o mesmo nome, não será capaz de marcar esse nome nos vídeos que utilizam esse modelo de pessoa. Só poderá efetuar alterações às pessoas que partilham esse nome no separador de pessoas da página de personalização do modelo de conteúdo no Video Indexer. Por esse motivo, recomenda-se que dê nomes exclusivos a cada pessoa no seu modelo de pessoa.

1. Navegue para o site do Video Indexer e inicie sessão.
1. Procure um vídeo que pretende ver e editar na sua conta.
1. Para editar um rosto no seu vídeo, vá para o separador de informações e clique no ícone de lápis no canto superior direito da janela.

    ![Editar um rosto no seu vídeo](./media/customize-face-model/edit-face.png)
1. Clique em qualquer um dos rostos detetados e alterar os respetivos nomes de "#X desconhecido" (ou o nome que anteriormente era atribuído para o rosto). 
1. Depois de escrever o novo nome, clique no ícone de verificação junto ao nome do novo. Isto guarda o novo nome e reconhece e nomes de todas as ocorrências deste face nos seus outros vídeos atuais e no futuro vídeos que carrega. Reconhecimento de rosto nos seus outros vídeos atuais poderá demorar algum tempo a entrar em vigor, como se trata de um processo em lotes.

Se nomeia um rosto com o nome de uma pessoa existente no modelo de pessoa que o vídeo está a utilizar, mesclará as imagens de rostos detetados através deste vídeo de que a pessoa com o que já existe no modelo. Se nomeia um rosto com um nome totalmente novo, isto irá criar uma nova entrada de Person no modelo de pessoa que o vídeo está a utilizar. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Eliminar um rosto

Para eliminar um rosto detetado no seu vídeo, vá para o painel de informações e clique no ícone de lápis no canto superior direito do painel. Clique na opção de eliminação sob o nome do mostrador da. Esta ação irá remover que detetados face do vídeo. Face da pessoa ainda será detetado aos outros vídeos em que aparece, mas pode eliminar a face desses vídeos também depois que eles têm sido indexados. As pessoas, se tinha sido nomeado, também continuará a existir no modelo de pessoa que foi utilizado para o vídeo a partir do qual eliminou a face, a menos que excluir especificamente a pessoa do modelo de pessoa de índice.

![Eliminar um rosto no seu vídeo](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Passos Seguintes

[Personalizar o modelo de pessoa com APIs](customize-person-model-with-api.md)
