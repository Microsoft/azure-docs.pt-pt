---
title: Personalize um modelo de pessoa com o site do Indexer de Vídeo
titleSuffix: Azure Media Services
description: Aprenda a personalizar um modelo Pessoa com o website do Indexer de Vídeo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f29e651f5c8542722f0dc2c9878184ac0d2a6a1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499969"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personalize um modelo pessoa com o site do Indexer de Vídeo

O Indexer de vídeo apoia o reconhecimento de celebridades por conteúdos de vídeo. A funcionalidade de reconhecimento de celebridades abrange cerca de um milhão de rostos com base em fontes de dados comumente solicitadas, tais como IMDB, Wikipedia e influenciadores de topo do LinkedIn. Para uma visão geral detalhada, consulte [Personalizar um modelo pessoa no Indexer](customize-person-model-overview.md)de Vídeo .

Pode utilizar o site do Indexer de Vídeo para editar rostos que foram detetados num vídeo, como descrito neste tópico. Também pode utilizar a API, conforme descrito no [modelo Personalizar uma Pessoa utilizando APIs](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Gestão central dos modelos Pessoa na sua conta

1. Para visualizar, editar e eliminar os modelos Pessoa na sua conta, navegue no site do Indexer de Vídeo e inscreva-se.

2. Selecione o botão de personalização do modelo de conteúdo no canto superior direito da página.

    ![Personalização do modelo de conteúdo](./media/customize-face-model/content-model-customization.png)

3. Selecione o separador Pessoas.

    Verá o modelo Pessoa Padrão na sua conta. O modelo Pessoa Padrão detém quaisquer rostos que possa ter editado ou alterado nas ideias dos seus vídeos para os quais não especificou um modelo pessoa personalizado durante a indexação.

    Se criar outros modelos pessoa, também serão listados nesta página.

    ![Personalização do modelo de conteúdo](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Criar um novo modelo Pessoa

1. Selecione o botão **modelo + Adicionar.**

    ![Adicione um novo modelo de pessoa](./media/customize-face-model/add-new-person.png)

2. Introduza o nome do modelo e selecione o botão de verificação ao lado do nome.

    ![Adicione um novo modelo de pessoa](./media/customize-face-model/add-new-person2.png)

    Criaste um novo modelo de Pessoa. Agora pode adicionar rostos ao novo modelo Pessoa.

3. Selecione o botão de menu da lista e escolha **+ Adicionar pessoa**.

    ![Adicione um novo modelo de pessoa](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>Adicione uma nova pessoa a um modelo pessoa

> [!NOTE]
> O Indexer de Vídeo permite-lhe adicionar várias pessoas com o mesmo nome num modelo Pessoa. No entanto, recomenda-se que dê nomes únicos a cada pessoa no seu modelo para usabilidade e clareza.

1. Para adicionar um novo rosto a um modelo Pessoa, selecione o botão de menu da lista ao lado do modelo Pessoa a que pretende adicionar o rosto.

1. Selecione **+ Adicionar pessoa** no menu.

    ![Adicione um novo rosto à pessoa](./media/customize-face-model/add-new-face.png)

    Um pop-up irá instá-lo a preencher os detalhes da Pessoa. Digite o nome da pessoa e selecione o botão de verificação.

    ![Adicione um novo rosto à pessoa](./media/customize-face-model/add-new-face2.png)

    Em seguida, pode escolher entre o seu explorador de ficheiros ou arrastar e largar as imagens faciais do rosto. O Indexer de Vídeo terá todos os tipos de ficheiros de imagem padrão (ex: JPG, PNG e muito mais).

    O Indexer de Vídeo pode detetar ocorrências desta pessoa nos futuros vídeos que indexa e nos vídeos atuais que já tinha indexado, utilizando o modelo Pessoa ao qual adicionou este novo rosto. O reconhecimento da pessoa nos seus vídeos atuais pode demorar algum tempo a fazer efeito, uma vez que se trata de um processo de lote.

## <a name="rename-a-person-model"></a>Mudar o nome de um modelo pessoa

Pode mudar o nome de qualquer modelo pessoa na sua conta, incluindo o modelo Pessoa Padrão. Mesmo que mude o nome do seu modelo Pessoa padrão, continuará a servir como modelo de pessoa padrão na sua conta.

1. Selecione o botão de menu da lista ao lado do modelo Pessoa que pretende mudar o nome.
2. Selecione **Renomear** a partir do menu.

    ![Renomear um modelo de pessoa](./media/customize-face-model/rename-person.png)

3. Selecione o nome atual do modelo e escreva o seu novo nome.

    ![Renomear um modelo de pessoa](./media/customize-face-model/rename-person2.png)

4. Selecione o botão de verificação para o seu modelo ser renomeado.

## <a name="delete-a-person-model"></a>Eliminar um modelo Pessoa

Pode eliminar qualquer modelo pessoa que tenha criado na sua conta. No entanto, não pode eliminar o seu modelo de pessoa Padrão.

1. **Selecione Excluir** do menu.

    ![Eliminar um modelo de pessoa](./media/customize-face-model/delete-person.png)

    Um pop-up aparecerá e notificará-o de que esta ação irá apagar o modelo Pessoa e todas as pessoas e ficheiros que contém. Esta ação não pode ser desfeita.

    ![Eliminar um modelo de pessoa](./media/customize-face-model/delete-person2.png)

1. Se tiver a certeza, selecione apagar novamente.

> [!NOTE]
> Os vídeos existentes que foram indexados usando este modelo (agora eliminado) pessoa não suportam a capacidade de atualizar os nomes dos rostos que aparecem no vídeo. Só poderá editar os nomes das caras nestes vídeos depois de os reindexar usando outro modelo Pessoa. Se reindexar sem especificar um modelo Pessoa, o modelo predefinido será utilizado.

## <a name="manage-existing-people-in-a-person-model"></a>Gerir as pessoas existentes num modelo pessoa

Para ver o conteúdo de qualquer um dos seus modelos Pessoa, selecione a seta ao lado do nome do modelo Pessoa. A entrega mostra-te todas as pessoas do modelo da Pessoa em particular. Se selecionar o botão de menu da lista ao lado de cada uma das pessoas, verá gerir, mudar o nome e eliminar opções.  

![Adicione um novo rosto à pessoa](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Mude o nome de uma pessoa

1. Para mudar o nome de uma pessoa no seu modelo Pessoa, selecione o botão de menu da lista e escolha **O Nome De Renomear** no menu da lista.
1. Selecione o nome atual da pessoa e escreva o seu novo nome.
1. Selecione o botão de verificação e a pessoa será renomeada.

### <a name="delete-a-person"></a>Eliminar uma pessoa

1. Para eliminar uma pessoa do seu modelo Pessoa, selecione o botão de menu da lista e escolha **eliminar** no menu da lista.
1. Um pop-up diz-lhe que esta ação vai apagar a pessoa e que esta ação não pode ser desfeita.
1. **Selecione Apagar** novamente e isto removerá a pessoa do modelo Pessoa.

### <a name="manage-a-person"></a>Gerir uma pessoa

Se selecionar **Manage,** verá todos os rostos de que este modelo pessoa está a ser treinado. Estes rostos provêm de ocorrências dessa pessoa em vídeos que usam este modelo Pessoa ou de imagens que você carregou manualmente.

Pode adicionar mais rostos à pessoa selecionando **Imagens Adicionar**.

Pode utilizar o painel de gestão para mudar o nome da pessoa e para eliminar a pessoa do modelo Pessoa.

## <a name="use-a-person-model-to-index-a-video"></a>Use um modelo pessoa para indexar um vídeo

Pode utilizar um modelo Pessoa para indexar o seu novo vídeo atribuindo o modelo Pessoa durante o upload do vídeo.

Para utilizar o modelo Pessoa num novo vídeo, faça os seguintes passos:

1. Selecione o botão **Upload** na parte superior da página.

    ![Faça upload de um modelo de pessoa](./media/customize-face-model/upload.png)

1. Largue o seu ficheiro de vídeo no círculo ou navegue para o seu ficheiro.
1. Selecione a seta de **opções Avançadas.**

    ![Faça upload de um modelo de pessoa](./media/customize-face-model/upload2.png)

1. Selecione o drop-down e selecione o modelo Pessoa que criou.

    ![Faça upload de um modelo de pessoa](./media/customize-face-model/upload3.png)

1. Selecione a opção **Upload** na parte inferior da página e o seu novo vídeo será indexado utilizando o seu modelo Pessoa.

Se não especificar um modelo Pessoa durante o upload, o Indexer de vídeo indexará o vídeo utilizando o modelo Pessoa Padrão na sua conta.

## <a name="use-a-person-model-to-reindex-a-video"></a>Use um modelo pessoa para reindexar um vídeo

Para utilizar um modelo Pessoa para reindexar um vídeo na sua coleção, vá aos vídeos da sua conta na página inicial do Indexer de Vídeo e passe sobre o nome do vídeo que pretende reindexar.

Vê opções para editar, apagar e reindexar o seu vídeo.

1. Selecione a opção de reindexar o seu vídeo.

    ![Use o modelo da Pessoa para reindexar um vídeo](./media/customize-face-model/reindex.png)

    Agora pode selecionar o modelo Pessoa para reindexar o seu vídeo.
1. Selecione o drop-down e selecione o modelo Pessoa que pretende utilizar.

    ![Use o modelo da Pessoa para reindexar um vídeo](./media/customize-face-model/reindex2.png)

1. Selecione o botão **Reindex** e o seu vídeo será reindexado utilizando o modelo Pessoa.

Quaisquer novas edições que fizer estoiras para as caras detetadas e reconhecidas no vídeo que acabou de reindexar serão guardadas no modelo Pessoa que usou para reindexar o vídeo.

## <a name="managing-people-in-your-videos"></a>Gerir pessoas nos seus vídeos

Você pode gerir os rostos que são detetados e pessoas que são reconhecidas nos vídeos que indexa, editando e apagando rostos.

A eliminação de um rosto remove um rosto específico das visões do vídeo.

Editar um rosto renomea um rosto que é detetado e possivelmente reconhecido no seu vídeo. Quando edita um rosto no seu vídeo, esse nome é guardado como uma entrada de pessoa no modelo Pessoa que foi atribuído ao vídeo durante o upload e indexação.

Se não atribuir um modelo Pessoa ao vídeo durante o upload, a sua edição é guardada no modelo de pessoa padrão da sua conta.

### <a name="edit-a-face"></a>Editar um rosto

> [!NOTE]
> Se um modelo pessoa tiver duas ou mais pessoas diferentes com o mesmo nome, não poderá marcar esse nome dentro dos vídeos que usam esse modelo Pessoa. Você só será capaz de fazer alterações em pessoas que compartilham esse nome no separador Pessoas da página de personalização do modelo de conteúdo no Indexer de Vídeo. Por esta razão, recomenda-se que dê nomes únicos a cada pessoa no seu modelo Pessoa.

1. Aceda ao site do Video Indexer e inicie sessão.
1. Procure um vídeo que queira ver e editar na sua conta.
1. Para editar um rosto no seu vídeo, vá ao separador Insights e selecione o ícone do lápis no canto superior direito da janela.

    ![Editar um rosto no seu vídeo](./media/customize-face-model/edit-face.png)

1. Selecione qualquer um dos rostos detetados e mude os seus nomes de "#X Desconhecido" (ou o nome que foi previamente atribuído ao rosto).
1. Depois de digitar o novo nome, selecione o ícone de verificação ao lado do novo nome. Esta ação guarda o novo nome e reconhece e dá nomes a todas as ocorrências deste rosto nos seus outros vídeos atuais e nos futuros vídeos que envia. O reconhecimento do rosto nos seus outros vídeos atuais pode levar algum tempo a fazer efeito, uma vez que se trata de um processo de lote.

Se nomear um rosto com o nome de uma pessoa existente no modelo Pessoa que o vídeo está a usar, as imagens faciais detetadas deste vídeo dessa pessoa fundir-se-ão com o que já existe no modelo. Se nomear um rosto com um novo nome, é criada uma nova entrada de Pessoa no modelo Pessoa que o vídeo está a usar.

![Editar um rosto no seu vídeo](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Apagar um rosto

Para eliminar um rosto detetado no seu vídeo, vá ao painel Insights e selecione o ícone do lápis no canto superior direito do painel. Selecione a opção **Eliminar** por baixo do nome do rosto. Esta ação remove o rosto detetado do vídeo. O rosto da pessoa ainda será detetado nos outros vídeos em que aparece, mas também pode apagar o rosto desses vídeos depois de terem sido indexados.

A pessoa, se tivesse sido nomeada, também continuará a existir no modelo Pessoa que foi usado para indexar o vídeo a partir do qual apagou o rosto a menos que apague especificamente a pessoa do modelo Pessoa.

![Apague um rosto no seu vídeo](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>Passos seguintes

[Personalizar modelo pessoa usando APIs](customize-person-model-with-api.md)
