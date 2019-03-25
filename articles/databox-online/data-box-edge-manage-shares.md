---
title: Gestão da partilha Edge de caixa de dados do Azure | Documentos da Microsoft
description: Descreve como utilizar o portal do Azure para gerir partilhas no seu limite de caixa de dados do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: ec5fbffdf7df5ef3a952e21b79ab02f355fb8e29
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403651"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-edge"></a>Utilizar o portal do Azure para gerir partilhas no seu limite de caixa de dados do Azure

Este artigo descreve como gerir partilhas no seu limite de caixa de dados do Azure. Pode gerir o limite de caixa de dados do Azure através do portal do Azure ou através do local da interface do Usuário da web. Utilize o portal do Azure para adicionar, eliminar, atualizar partilhas ou sincronizar a chave de armazenamento para a conta de armazenamento associada com as partilhas.

## <a name="about-shares"></a>Sobre as partilhas

Para transferir dados para o Azure, terá de criar partilhas no seu limite de caixa de dados do Azure. As partilhas que adicionar ao dispositivo de limite de caixa de dados podem ser partilhas locais ou partilhas que enviar por push dados para a cloud.

 - **Partilhas locais**: Utilize estas partilhas quando pretender que os dados a ser processados localmente no dispositivo.
 - **Partilhas**: Utilize estas partilhas quando pretender que os dados do dispositivo para ser enviado automaticamente para a sua conta de armazenamento na cloud. Todas as cloud as funções, como **Atualize** e **sincronizar as chaves de armazenamento** aplicam-se para as partilhas.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Adicionar uma partilha
> * Eliminar uma partilha
> * Atualizar partilhas
> * Sincronizar a chave de armazenamento


## <a name="add-a-share"></a>Adicionar uma partilha

Efetue os seguintes passos no portal do Azure para criar uma partilha.

1. No portal do Azure, aceda ao seu recurso de borda de caixa de dados e, em seguida, aceda a **Gateway > partilhas**. Selecione **+ Adicionar partilha** na barra de comandos.

    ![Selecione Adicionar partilha](media/data-box-edge-manage-shares/add-share-1.png)

2. Em **Adicionar Partilha**, especifique as definições de partilha. Indique um nome exclusivo para a partilha.
    
    Os nomes das partilhas só podem conter números, letras minúsculas e hífenes. O nome da partilha tem de ter entre 3 e 63 carateres e começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen.

3. Selecione um **Tipo** para a partilha. O tipo pode ser **SMB** ou **NFS**, sendo que SMB é a predefinição. SMB é o padrão para clientes Windows, e NFS é utilizado para clientes Linux. Dependendo se escolher a partilha SMB ou NFS, as opções apresentadas são ligeiramente diferentes.

4. Forneça uma **Conta de armazenamento** onde reside a partilha. É criado um contentor na conta de armazenamento com o nome da partilha, se o contentor ainda não existir. Se o contentor já existir, é utilizado o contentor existente.

5. Na lista pendente, escolha o **serviço de armazenamento** do blob de blocos, BLOBs de páginas ou ficheiros. O tipo de serviço escolhido depende do formato no qual pretende que os dados residam no Azure. Por exemplo, neste caso, queremos que os dados residam como blobs de blocos no Azure, que, por conseguinte, selecionamos **Blob de blocos**. Se escolher **BLOBs de páginas**, tem de garantir que os dados estão alinhados de 512 bytes. Uso **BLOBs de páginas** para VHD ou VHDX que estão sempre alinhados de 512 bytes.

6. Este passo depende de estar a criar uma partilha SMB ou NFS.
    - **Se criar uma partilha SMB** - no campo **Utilizador local com todos os privilégios**, escolha **Criar novo** ou **Utilizar existente**. Se criar um novo utilizador local, forneça o **nome de utilizador**, a **palavra-passe** e, em seguida, confirme a palavra-passe. Esta ação atribui as permissões ao utilizador local. Depois de ter atribuído as permissões aqui, pode utilizar o Explorador de Ficheiros para modificar estas permissões.

        ![Adicionar uma partilha SMB](media/data-box-edge-manage-shares/add-smb-share.png)

        Se marcar permitir apenas operações de leitura para esta partilha de dados, pode especificar utilizadores só de leitura.
    - **Se criar uma partilha NFS** - tem de fornecer os **Endereços IP dos clientes permitidos** que podem aceder à partilha.

        ![Adicionar uma partilha NFS](media/data-box-edge-manage-shares/add-nfs-share.png)

7. Para aceder facilmente às partilhas de módulos de computação do Edge, utilize o ponto de montagem local. Selecione **utilize a partilha com a computação de borda** para que a partilha está automaticamente montado após a criação. Quando esta opção está selecionada, o módulo de borda também pode utilizar a computação com o ponto de montagem local.

8. Clique em **Criar** para criar a partilha. Será notificado de que a criação da partilha está em curso. Depois de criar a partilha com as definições especificadas, o painel **Partilhas** é atualizado para refletir a nova partilha.

## <a name="add-a-local-share"></a>Adicionar uma partilha local

1. No portal do Azure, aceda ao seu recurso de borda de caixa de dados e, em seguida, aceda a **Gateway > partilhas**. Selecione **+ Adicionar partilha** na barra de comandos.

    ![Selecione Adicionar partilha](media/data-box-edge-manage-shares/add-local-share-1.png)

2. Em **Adicionar Partilha**, especifique as definições de partilha. Indique um nome exclusivo para a partilha.
    
    Os nomes das partilhas só podem conter números, letras minúsculas e hífenes. O nome da partilha tem de ter entre 3 e 63 carateres e começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen.

3. Selecione um **Tipo** para a partilha. O tipo pode ser **SMB** ou **NFS**, sendo que SMB é a predefinição. SMB é o padrão para clientes Windows, e NFS é utilizado para clientes Linux. Dependendo se escolher a partilha SMB ou NFS, as opções apresentadas são ligeiramente diferentes.

4. Para aceder facilmente às partilhas de módulos de computação do Edge, utilize o ponto de montagem local. Selecione **utilize a partilha com a computação de borda** para que o módulo Edge pode utilizar a computação com o ponto de montagem local.

5. Selecione **configurar como partilhas de locais de borda**. Os dados em partilhas locais permanecerá localmente no dispositivo. Pode processar estes dados localmente.

6. Na **todos os usuários de locais de privilégio** campo, escolha de entre **criar nova** ou **utilizar existente**.

7. Selecione **Criar**. 

    ![Criar a partilha local](media/data-box-edge-manage-shares/add-local-share-2.png)

    Verá uma notificação a indicar que a criação de partilha está em curso. Depois de criar a partilha com as definições especificadas, o painel **Partilhas** é atualizado para refletir a nova partilha.

    ![Ver o painel de partilhas de atualizações](media/data-box-edge-manage-shares/add-local-share-3.png)
    
    Selecione a partilha para ver o ponto de montagem local para os módulos de computação do Edge para esse compartilhamento.

    ![Ver detalhes da partilha local](media/data-box-edge-manage-shares/add-local-share-4.png)


## <a name="unmount-a-share"></a>Desmonte uma partilha

Execute os seguintes passos no portal do Azure para desmontar uma partilha.

1. No portal do Azure, aceda ao seu recurso de borda de caixa de dados e, em seguida, aceda a **Gateway > partilhas**.

    ![Selecionar partilha](media/data-box-edge-manage-shares/select-share-unmount.png)

2. Na lista de partilhas de, selecione a partilha que pretende desmontar. Pretende certificar-se de que a partilha de que desmontar não é utilizada por quaisquer módulos. Se a partilha é utilizada por um módulo, em seguida, irá ver problemas com o módulo correspondente. Selecione **desmontar**.

    ![Selecione desmontar](media/data-box-edge-manage-shares/select-unmount.png)

3. Quando lhe for pedida confirmação, selecione **Sim**. Isto irá desmontar a partilha.

    ![Confirmar desmontar](media/data-box-edge-manage-shares/confirm-unmount.png)

4. Depois da partilha é desmontada, vá para a lista de partilhas. Verá que **utilizado para computação** coluna mostra o estado de partilha como **desativado**.

    ![Desmontar a partilha](media/data-box-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Eliminar uma partilha

Efetue os seguintes passos no portal do Azure para eliminar uma partilha.

1. Na lista de partilhas, selecione e clique na partilha que pretende eliminar.

    ![Selecionar partilha](media/data-box-edge-manage-shares/delete-share-1.png)

2. Clique em **Eliminar**.

    ![Clicar em eliminar](media/data-box-edge-manage-shares/delete-share-2.png)

3. Quando lhe for pedida a confirmação, clique em **Sim**.

    ![Confirmar eliminação](media/data-box-edge-manage-shares/delete-share-3.png)

A lista de atualizações de partilhas para refletir a eliminação.


## <a name="refresh-shares"></a>Atualizar partilhas

O recurso de atualização permite-lhe atualizar os conteúdos de uma partilha. Quando atualiza uma partilha, é iniciada uma pesquisa para localizar todos os objetos do Azure, incluindo os blobs e ficheiros que foram adicionados à cloud desde a última atualização. Esses arquivos adicionais, em seguida, são transferidos para atualizar os conteúdos da partilha no dispositivo.

> [!IMPORTANT]
> Não é possível atualizar partilhas locais.

Efetue os seguintes passos no portal do Azure para atualizar uma partilha.

1.  No portal do Azure, aceda a **Partilhas**. Selecione e clique na partilha que pretende atualizar.

    ![Selecionar partilha](media/data-box-edge-manage-shares/refresh-share-1.png)

2.  Clique em **Atualizar**. 

    ![Clicar em atualizar](media/data-box-edge-manage-shares/refresh-share-2.png)
 
3.  Quando lhe for pedida a confirmação, clique em **Sim**. É iniciada uma tarefa para atualizar os conteúdos da partilha no local.

    ![Confirmar atualização](media/data-box-edge-manage-shares/refresh-share-3.png)
 
4.  Enquanto a atualização estiver em curso, a opção de atualização está desativada no menu de contexto. Clique na notificação da tarefa para ver o estado da tarefa de atualização.

5.  O tempo de atualização depende do número de ficheiros no contentor do Azure, bem como dos ficheiros no dispositivo. Assim que a atualização tiver sido concluída com êxito, o carimbo de data/hora da partilha é atualizado. Mesmo se a atualização tiver falhas parciais, a operação é considerada como concluída com êxito e o carimbo de data/hora é atualizado. Os registos de erros de atualização também são atualizados.

    ![Carimbo de data/hora atualizado](media/data-box-edge-manage-shares/refresh-share-4.png)
 
Se houver uma falha, é gerado um alerta. O alerta fornece detalhes sobre a causa e a recomendação para corrigir o problema. O alerta também contém ligações para um ficheiro com o resumo completo das falhas, incluindo os ficheiros que não foi possível atualizar ou eliminar.


## <a name="sync-storage-keys"></a>Sincronizar chaves de armazenamento

Se as chaves da conta de armazenamento tiverem sido rodadas, terá de sincronizar as chaves de acesso ao armazenamento. A sincronização ajuda o dispositivo a obter as chaves mais recente para a sua conta de armazenamento.

Efetue os seguintes passos no portal do Azure para sincronizar a chave de acesso ao armazenamento.

1. Aceda a **Descrição Geral** no seu recurso. Na lista de partilhas, escolha e clique numa partilha associada à conta de armazenamento que precisa de sincronizar.

    ![Selecione a partilha com a conta de armazenamento relevantes](media/data-box-edge-manage-shares/sync-storage-key-1.png)

2. Clique em **Sincronizar chave de armazenamento**. Clique em **Sim** quando lhe for pedida a confirmação.

     ![Selecione a sincronizar a chave de armazenamento](media/data-box-edge-manage-shares/sync-storage-key-2.png)

3. Saia da caixa de diálogo depois de a sincronização estar concluída.

>[!NOTE]
> Apenas terá de efetuar este procedimento uma vez para uma conta de armazenamento fornecida. Não precisa de repetir esta ação para todas as partilhas associadas à mesma conta de armazenamento.


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [Gerir utilizadores através do portal do Azure](data-box-edge-manage-users.md).
