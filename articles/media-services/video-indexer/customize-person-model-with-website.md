---
title: Personalize um modelo de Pessoa com site do Indexer de Vídeo
titleSuffix: Azure Media Services
description: Saiba como personalizar um modelo pessoa com o website do Índice de Vídeo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2020
ms.author: juliako
ms.openlocfilehash: 890f8c159c3e8e8d0b4164cf218f320551ea63ec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97628940"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personalize um modelo de Pessoa com o site do Indexer de Vídeo

O Video Indexer suporta o reconhecimento de celebridades por conteúdos de vídeo. A funcionalidade de reconhecimento de celebridades abrange aproximadamente um milhão de rostos com base em fontes de dados comumente solicitadas, tais como IMDB, Wikipedia e principais influenciadores do LinkedIn. Para obter uma visão geral detalhada, consulte [Personalizar um modelo pessoa no Índice de Vídeo](customize-person-model-overview.md).

Pode utilizar o website Do Indexer de Vídeo para editar rostos que foram detetados num vídeo, conforme descrito neste tópico. Também pode utilizar a API, conforme descrito no [modelo Personalizar uma Pessoa utilizando APIs](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Gestão central dos modelos pessoais na sua conta

1. Para ver, editar e eliminar os modelos Pessoa na sua conta, navegue no site do Indexer de Vídeo e inscreva-se.
1. Selecione o botão de personalização do modelo de conteúdo à esquerda da página.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Personalizar modelo de conteúdo":::
1. Selecione o separador Pessoas.

    Verá o modelo De Pessoa Padrão na sua conta. O modelo 'Pessoa Predefinida' contém quaisquer rostos que possa ter editado ou alterado nas perceções dos seus vídeos para os quais não especificou um modelo personalizado de Pessoa durante a indexação.

    Se criou outros modelos pessoais, também estarão listados nesta página.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-face-model/content-model-customization-people-tab.png" alt-text="Personalizar as pessoas":::

## <a name="create-a-new-person-model"></a>Criar um novo modelo pessoa

1. Selecione o botão **modelo + Adicionar** à direita.
1. Insira o nome do modelo. Agora pode adicionar novas pessoas e rostos ao novo modelo Pessoa.
1. Selecione o botão do menu da lista e escolha **+ Adicionar pessoa.**

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-face-model/add-new-person.png" alt-text="Adicione um peron":::

## <a name="add-a-new-person-to-a-person-model"></a>Adicione uma nova pessoa a um modelo de Pessoa

> [!NOTE]
> O Video Indexer permite-lhe adicionar várias pessoas com o mesmo nome num modelo de Pessoa. No entanto, recomenda-se que dê nomes únicos a cada pessoa no seu modelo para usabilidade e clareza.

1. Para adicionar um novo rosto a um modelo Pessoa, selecione o botão do menu da lista ao lado do modelo Pessoa a que pretende adicionar o rosto.
1. **Selecione + Adicionar pessoa** no menu.

    Um pop-up irá levá-lo a preencher os detalhes da Pessoa. Digite o nome da pessoa e selecione o botão de verificação.

    Em seguida, pode escolher entre o seu explorador de ficheiros ou arrastar e deixar cair as imagens faciais do rosto. O Indexante de Vídeo irá tomar todos os tipos padrão de ficheiros de imagem (ex: JPG, PNG, e muito mais).

    O Video Indexer pode detetar ocorrências desta pessoa nos vídeos futuros que indexa e os vídeos atuais que já tinha indexado, utilizando o modelo Pessoa ao qual adicionou esta nova face. O reconhecimento da pessoa nos seus vídeos atuais pode demorar algum tempo a fazer efeito, uma vez que este é um processo de lote.

## <a name="rename-a-person-model"></a>Mudar o nome de um modelo de Pessoa

Pode mudar o nome de qualquer modelo pessoa na sua conta, incluindo o modelo Pessoa Padrão. Mesmo que rebatize o seu modelo De Pessoa predefinido, ele continuará a servir como o modelo de pessoa padrão na sua conta.

1. Selecione o botão do menu da lista ao lado do modelo Pessoa que pretende renomear.
1. **Selecione Renomear** o menu.
1. Selecione o nome atual do modelo e escreva o seu novo nome.
1. Selecione o botão de verificação para que o seu modelo seja renomeado.

## <a name="delete-a-person-model"></a>Excluir um modelo de Pessoa

Pode eliminar qualquer modelo pessoa que tenha criado na sua conta. No entanto, não é possível eliminar o modelo de pessoa predefinido.

1. **Selecione Excluir** do menu.

    Um pop-up aparecerá e notificou-o de que esta ação irá eliminar o modelo Pessoa e todas as pessoas e ficheiros que contém. Esta ação não pode ser desfeita.
1. Se tiver a certeza, selecione apagar novamente.

> [!NOTE]
> Os vídeos existentes que foram indexados usando este modelo (agora eliminado) Person não suportam a capacidade de atualizar os nomes das faces que aparecem no vídeo. Só poderá editar os nomes das caras nestes vídeos depois de os reindexar usando outro modelo da Pessoa. Se reindex sem especificar um modelo Pessoa, o modelo predefinido será utilizado.

## <a name="manage-existing-people-in-a-person-model"></a>Gerir as pessoas existentes num modelo de Pessoa

Para ver o conteúdo de qualquer um dos seus modelos Pessoas, selecione a seta ao lado do nome do modelo Pessoa. A queda mostra-vos todas as pessoas desse modelo de Pessoa em particular. Se selecionar o botão do menu da lista ao lado de cada uma das pessoas, vê gerir, renomear e eliminar opções.  

![O Screenshot mostra um menu contextual com opções para Gerir, Renomear e Eliminar.](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Mudar o nome de uma pessoa

1. Para renomear uma pessoa no seu modelo Pessoa, selecione o botão do menu da lista e escolha **O nome do** menu da lista.
1. Selecione o nome atual da pessoa e escreva o seu novo nome.
1. Selecione o botão de verificação e a pessoa será renomeada.

### <a name="delete-a-person"></a>Excluir uma pessoa

1. Para eliminar uma pessoa do modelo Pessoa, selecione o botão do menu da lista e escolha **Eliminar** no menu da lista.
1. Um pop-up diz-lhe que esta ação irá apagar a pessoa e que esta ação não pode ser desfeita.
1. **Selecione Eliminar** novamente e isto removerá a pessoa do modelo Pessoa.

### <a name="manage-a-person"></a>Gerir uma pessoa

Se selecionar **Gerir,** vê a janela **de detalhes da Pessoa** com todas as faces a que este modelo Pessoa está a ser treinado. Estes rostos provêm de ocorrências dessa pessoa em vídeos que usam este modelo Pessoa ou de imagens que fez o upload manual.

> [!TIP]
> Pode chegar à janela **de detalhes da Pessoa** clicando no nome da pessoa ou clicando em **Manage**, como mostrado acima.

#### <a name="add-a-face"></a>Adicione um rosto

Pode adicionar mais rostos à pessoa selecionando **Adicionar imagens**.

#### <a name="delete-a-face"></a>Apagar um rosto

Selecione a imagem deseja eliminar e clique em **Eliminar**.

#### <a name="rename-and-delete-the-person"></a>Renomear e eliminar a pessoa 

Pode utilizar o painel de gestão para mudar o nome da pessoa e eliminar a pessoa do modelo Pessoa.

## <a name="use-a-person-model-to-index-a-video"></a>Use um modelo de Pessoa para indexar um vídeo

Pode utilizar um modelo Pessoa para indexar o seu novo vídeo atribuindo o modelo Pessoa durante o upload do vídeo.

Para utilizar o seu modelo Pessoa num novo vídeo, faça os seguintes passos:

1. Selecione o botão **Upload** no direito da página.
1. Largue o seu ficheiro de vídeo ou navegue para o seu ficheiro.
1. Selecione a seta **de opções Avançadas.**
1. Selecione o drop-down e selecione o modelo Pessoa que criou.
1. Selecione a opção **Upload** na parte inferior da página e o seu novo vídeo será indexado usando o seu modelo Pessoa.

Se não especificar um modelo Pessoa durante o upload, o Indexante de Vídeo indexará o vídeo utilizando o modelo Pessoa Padrão na sua conta.

## <a name="use-a-person-model-to-reindex-a-video"></a>Use um modelo de Pessoa para reindexar um vídeo

Para utilizar um modelo De Pessoa para reindexar um vídeo na sua coleção, vá aos vídeos da sua conta na página inicial do Video Indexer e paire sobre o nome do vídeo que pretende reindexar.

Vê opções para editar, excluir e reindexar o seu vídeo.

1. Selecione a opção de reindexar o seu vídeo.

    ![A screenshot mostra vídeos de conta e a opção de reindexar o seu vídeo.](./media/customize-face-model/reindex.png)

    Agora pode selecionar o modelo Pessoa para reindexar o seu vídeo com.
1. Selecione o drop-down e selecione o modelo Pessoa que pretende utilizar.
1. Selecione o botão **Reindex** e o seu vídeo será reexexuado utilizando o modelo Pessoa.

Quaisquer novas edições que fizeres aos rostos detetados e reconhecidas no vídeo que acabaste de reindexar será guardada no modelo Pessoa que usaste para reindexar o vídeo.

## <a name="managing-people-in-your-videos"></a>Gerir pessoas nos seus vídeos

Pode gerir os rostos que são detetados e pessoas que são reconhecidas nos vídeos que indexa, editando e apagando rostos.

Apagar um rosto remove um rosto específico das perceções do vídeo.

Editar um rosto renomea um rosto que é detetado e possivelmente reconhecido no seu vídeo. Quando edita um rosto no seu vídeo, esse nome é guardado como uma entrada de pessoa no modelo Pessoa que foi atribuído ao vídeo durante o upload e indexação.

Se não atribuir um modelo pessoa ao vídeo durante o upload, a sua edição é guardada no modelo de pessoa padrão da sua conta.

### <a name="edit-a-face"></a>Editar um rosto

> [!NOTE]
> Se um modelo de Pessoa tiver duas ou mais pessoas diferentes com o mesmo nome, não poderá marcar esse nome nos vídeos que usam esse modelo Pessoa. Só poderá fazer alterações a pessoas que partilhem esse nome no separador Pessoas da página de personalização do modelo de conteúdo no Video Indexer. Por esta razão, recomenda-se que dê nomes únicos a cada pessoa no seu modelo Pessoa.

1. Aceda ao site do Video Indexer e inicie sessão.
1. Procure um vídeo que pretenda visualizar e editar na sua conta.
1. Para editar um rosto no seu vídeo, vá ao separador Insights e selecione o ícone do lápis no canto superior direito da janela.

    ![A screenshot mostra um vídeo com uma face desconhecida para selecionar.](./media/customize-face-model/edit-face.png)

1. Selecione qualquer uma das faces detetadas e altere os seus nomes de "#X Desconhecida" (ou o nome que foi previamente atribuído ao rosto).
1. Depois de escrever o novo nome, selecione o ícone de verificação ao lado do novo nome. Esta ação salva o novo nome e reconhece e dá nome a todas as ocorrências deste rosto nos seus outros vídeos atuais e nos futuros vídeos que envia. O reconhecimento do rosto nos seus outros vídeos atuais pode demorar algum tempo a fazer efeito, uma vez que este é um processo de lote.

Se nomear um rosto com o nome de uma pessoa existente no modelo Pessoa que o vídeo está a usar, as imagens faciais detetadas deste vídeo dessa pessoa fundir-se-ão com o que já existe no modelo. Se nomear um rosto com um novo nome, uma nova entrada de Pessoa é criada no modelo Pessoa que o vídeo está a usar.

### <a name="delete-a-face"></a>Apagar um rosto

Para eliminar uma face detetada no seu vídeo, vá ao painel Insights e selecione o ícone do lápis no canto superior direito do painel. Selecione a opção **Eliminar** por baixo do nome do rosto. Esta ação remove o rosto detetado do vídeo. O rosto da pessoa ainda será detetado nos outros vídeos em que aparece, mas também pode apagar a cara desses vídeos depois de ter sido indexada.

A pessoa, se tivesse sido nomeada, também continuará a existir no modelo Pessoa que foi usado para indexar o vídeo a partir do qual apagou o rosto a menos que elimine especificamente a pessoa do modelo Pessoa.

## <a name="next-steps"></a>Passos seguintes

[Personalizar modelo pessoa usando APIs](customize-person-model-with-api.md)
