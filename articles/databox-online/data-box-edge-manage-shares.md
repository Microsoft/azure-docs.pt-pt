---
title: Azure Data Box Edge gestão de ações [ Azure Data Box Edge gestão de ações ] Microsoft Docs
description: Descreve como usar o portal Azure para gerir ações no seu Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: b25409c63806e203bd841b0373543b7cc2b96d9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79212932"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-edge"></a>Utilize o portal Azure para gerir ações no seu Azure Data Box Edge

Este artigo descreve como gerir ações no seu Azure Data Box Edge. Pode gerir o Azure Data Box Edge através do portal Azure ou através da UI web local. Utilize o portal Azure para adicionar, eliminar, atualizar ações ou sincronizar a chave de armazenamento para a conta de armazenamento associada às ações.

## <a name="about-shares"></a>Sobre as partilhas

Para transferir dados para o Azure, é necessário criar ações no seu Azure Data Box Edge. As ações que adiciona no dispositivo Data Box Edge podem ser ações locais ou partilhas que empurram dados para a nuvem.

 - **Ações locais**: Utilize estas ações quando quiser que os dados sejam processados localmente no dispositivo.
 - **Ações**: Utilize estas ações quando pretender que os dados do dispositivo sejam automaticamente empurrados para a sua conta de armazenamento na nuvem. Todas as funções em nuvem, tais como as chaves de armazenamento **Refresh** e **Sync,** aplicam-se às ações.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Adicionar uma partilha
> * Eliminar uma partilha
> * Atualizar partilhas
> * Sincronizar a chave de armazenamento


## <a name="add-a-share"></a>Adicionar uma partilha

Efetue os seguintes passos no portal do Azure para criar uma partilha.

1. No portal Azure, vá ao seu recurso Data Box Edge e depois vá ao **Gateway > Shares**. Selecione **+ Adicione a parte** na barra de comando.

    ![Selecione adicionar partilha](media/data-box-edge-manage-shares/add-share-1.png)

2. Em **Adicionar Partilha**, especifique as definições de partilha. Indique um nome exclusivo para a partilha.
    
    Os nomes das partilhas só podem conter números, letras minúsculas e hífenes. O nome da partilha tem de ter entre 3 e 63 carateres e começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen.

3. Selecione um **Tipo** para a partilha. O tipo pode ser **SMB** ou **NFS**, sendo que SMB é a predefinição. SMB é o padrão para clientes Windows, e NFS é utilizado para clientes Linux. Dependendo se escolher a partilha SMB ou NFS, as opções apresentadas são ligeiramente diferentes.

4. Forneça uma **Conta de armazenamento** onde reside a partilha. É criado um contentor na conta de armazenamento com o nome da partilha, se o contentor ainda não existir. Se o contentor já existir, é utilizado o contentor existente.

5. Na lista de dropdown, escolha o **serviço de Armazenamento** de blocos blob, page blob ou ficheiros. O tipo de serviço escolhido depende do formato no qual pretende que os dados residam no Azure. Por exemplo, neste caso, queremos que os dados residam como blocos blobs em Azure, daí selecionarmos **block Blob**. Se escolher o **Page Blob,** deve certificar-se de que os seus dados estão alinhados 512 bytes. Utilize a **blob página** para VHDs ou VHDX que estejam sempre 512 bytes alinhados.

   > [!IMPORTANT]
   > Certifique-se de que a conta de Armazenamento Azure que utiliza não tem políticas de imutabilidade definidas se estiver a usá-la com um dispositivo Azure Stack Edge ou Data Box Gateway. Para mais informações, consulte definir e gerir as políticas de [imutabilidade para armazenamento de blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).

6. Este passo depende de estar a criar uma partilha SMB ou NFS.
    - **Se criar uma partilha SMB** - no campo **Utilizador local com todos os privilégios**, escolha **Criar novo** ou **Utilizar existente**. Se criar um novo utilizador local, forneça o **nome de utilizador**, a **palavra-passe** e, em seguida, confirme a palavra-passe. Esta ação atribui as permissões ao utilizador local. Depois de ter atribuído as permissões aqui, pode utilizar o Explorador de Ficheiros para modificar estas permissões.

        ![Adicionar uma partilha SMB](media/data-box-edge-manage-shares/add-smb-share.png)

        Se marcar permitir apenas operações de leitura para esta partilha de dados, pode especificar utilizadores só de leitura.
    - **Se criar uma partilha NFS** - tem de fornecer os **Endereços IP dos clientes permitidos** que podem aceder à partilha.

        ![Adicionar uma partilha NFS](media/data-box-edge-manage-shares/add-nfs-share.png)

7. Para aceder facilmente às partilhas dos módulos de computação Edge, utilize o ponto de montagem local. Selecione **Utilize a parte com a computação Edge** para que a parte seja montada automaticamente após a sua criação. Quando esta opção é selecionada, o módulo Edge também pode usar a computação com o ponto de montagem local.

8. Clique em **Criar** para criar a partilha. Será notificado de que a criação da partilha está em curso. Depois de criar a partilha com as definições especificadas, o painel **Partilhas** é atualizado para refletir a nova partilha.

## <a name="add-a-local-share"></a>Adicione uma parte local

1. No portal Azure, vá ao seu recurso Data Box Edge e depois vá ao **Gateway > Shares**. Selecione **+ Adicione a parte** na barra de comando.

    ![Selecione adicionar partilha](media/data-box-edge-manage-shares/add-local-share-1.png)

2. Em **Adicionar Partilha**, especifique as definições de partilha. Indique um nome exclusivo para a partilha.
    
    Os nomes das partilhas só podem conter números, letras minúsculas e hífenes. O nome da partilha tem de ter entre 3 e 63 carateres e começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen.

3. Selecione um **Tipo** para a partilha. O tipo pode ser **SMB** ou **NFS**, sendo que SMB é a predefinição. SMB é o padrão para clientes Windows, e NFS é utilizado para clientes Linux. Dependendo se escolher a partilha SMB ou NFS, as opções apresentadas são ligeiramente diferentes.

4. Para aceder facilmente às partilhas dos módulos de computação Edge, utilize o ponto de montagem local. Selecione **Utilize a parte com a computação Edge** para que o módulo Edge possa utilizar a computação com o ponto de montagem local.

5. **Selecione Configure como ações locais edge**. Os dados em ações locais permanecerão localmente no dispositivo. Pode processar estes dados localmente.

6. No campo **de utilizadores locais de privilégio all,** escolha entre **Criar novo** ou **Utilizar existentes**.

7. Selecione **Criar**. 

    ![Criar uma parte local](media/data-box-edge-manage-shares/add-local-share-2.png)

    Vê uma notificação de que a criação de ações está em curso. Depois de criar a partilha com as definições especificadas, o painel **Partilhas** é atualizado para refletir a nova partilha.

    ![Ver atualizações Partilha a lâmina](media/data-box-edge-manage-shares/add-local-share-3.png)
    
    Selecione a parte para visualizar o ponto de montagem local para os módulos de computação Edge para esta partilha.

    ![Ver detalhes da partilha local](media/data-box-edge-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Monte uma parte

Se criou uma parte antes de configurar a computação no seu dispositivo Data Box Edge, terá de montar a parte. Dê os seguintes passos para montar uma parte.


1. No portal Azure, vá ao seu recurso Data Box Edge e depois vá ao **Gateway > Shares**. A partir da lista das ações, selecione a parte que pretende montar. A coluna **Utilizada para calcular** mostrará o estado de **desativado** para a parte selecionada.

    ![Selecionar partilha](media/data-box-edge-manage-shares/select-share-mount.png)

2. **Selecione Mount**.

    ![Selecione montagem](media/data-box-edge-manage-shares/select-mount.png)

3. Quando solicitado para confirmação, selecione **Sim**. Isto vai montar a parte.

    ![Confirmar montagem](media/data-box-edge-manage-shares/confirm-mount.png)

4. Depois de a parte ser montada, vá à lista de ações. Verá que a coluna **Usada para calcular** mostra o estado da partilha como **Ativado**.

    ![Partilhar montado](media/data-box-edge-manage-shares/share-mounted.png)

5. Selecione novamente a parte para ver o ponto de montagem local para a parte. O módulo de computação de borda utiliza este ponto de montagem local para a partilha.

    ![Ponto de montagem local para a partilha](media/data-box-edge-manage-shares/share-mountpoint.png)

## <a name="unmount-a-share"></a>Desmontar uma parte

Faça os seguintes passos no portal Azure para desmontar uma parte.

1. No portal Azure, vá ao seu recurso Data Box Edge e depois vá ao **Gateway > Shares**.

    ![Selecionar partilha](media/data-box-edge-manage-shares/select-share-unmount.png)

2. A partir da lista das ações, selecione a parte que pretende desfazer. Certifique-se de que a partilha que desmonta não é utilizada por nenhum módulo. Se a parte for utilizada por um módulo, então verá problemas com o módulo correspondente. **Selecione Unmount**.

    ![Selecione desmontar](media/data-box-edge-manage-shares/select-unmount.png)

3. Quando solicitado para confirmação, selecione **Sim**. Isto vai desmontar a parte.

    ![Confirmar desmontar](media/data-box-edge-manage-shares/confirm-unmount.png)

4. Depois de a parte ser desmontada, vá à lista de ações. Verá que **a coluna usada para calcular** mostra o estado da partilha como **Desativado**.

    ![Partilhar desmontado](media/data-box-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Eliminar uma partilha

Efetue os seguintes passos no portal do Azure para eliminar uma partilha.

1. Na lista de partilhas, selecione e clique na partilha que pretende eliminar.

    ![Selecionar partilha](media/data-box-edge-manage-shares/delete-share-1.png)

2. Clique em **Apagar**.

    ![Clicar em eliminar](media/data-box-edge-manage-shares/delete-share-2.png)

3. Quando lhe for pedida a confirmação, clique em **Sim**.

    ![Confirmar eliminação](media/data-box-edge-manage-shares/delete-share-3.png)

A lista de atualizações de ações para refletir a eliminação.


## <a name="refresh-shares"></a>Atualizar partilhas

A funcionalidade de atualização permite-lhe refrescar o conteúdo de uma parte. Quando atualiza uma partilha, é iniciada uma pesquisa para localizar todos os objetos do Azure, incluindo os blobs e ficheiros que foram adicionados à cloud desde a última atualização. Estes ficheiros adicionais são então descarregados para atualizar o conteúdo da partilha no dispositivo.

> [!IMPORTANT]
> - Não pode refrescar as ações locais.
> - As permissões e as listas de controlo de acesso (ACLs) não são preservadas através de uma operação de atualização. 

Efetue os seguintes passos no portal do Azure para atualizar uma partilha.

1.   No portal do Azure, aceda a **Partilhas**. Selecione e clique na partilha que pretende atualizar.

    ![Selecionar partilha](media/data-box-edge-manage-shares/refresh-share-1.png)

2.   Clique em **Atualizar**. 

    ![Clicar em atualizar](media/data-box-edge-manage-shares/refresh-share-2.png)
 
3.   Quando lhe for pedida a confirmação, clique em **Sim**. É iniciada uma tarefa para atualizar os conteúdos da partilha no local.

    ![Confirmar atualização](media/data-box-edge-manage-shares/refresh-share-3.png)
 
4.   Enquanto a atualização estiver em curso, a opção de atualização está desativada no menu de contexto. Clique na notificação da tarefa para ver o estado da tarefa de atualização.

5.   O tempo de atualização depende do número de ficheiros no contentor do Azure, bem como dos ficheiros no dispositivo. Assim que a atualização tiver sido concluída com êxito, o carimbo de data/hora da partilha é atualizado. Mesmo se a atualização tiver falhas parciais, a operação é considerada como concluída com êxito e o carimbo de data/hora é atualizado. Os registos de erro de atualização também são atualizados.

    ![Carimbo de data/hora atualizado](media/data-box-edge-manage-shares/refresh-share-4.png)
 
Se houver uma falha, é gerado um alerta. O alerta fornece detalhes sobre a causa e a recomendação para corrigir o problema. O alerta também contém ligações para um ficheiro com o resumo completo das falhas, incluindo os ficheiros que não foi possível atualizar ou eliminar.


## <a name="sync-storage-keys"></a>Sincronizar chaves de armazenamento

Se as chaves da conta de armazenamento tiverem sido rodadas, terá de sincronizar as chaves de acesso ao armazenamento. A sincronização ajuda o dispositivo a obter as chaves mais recente para a sua conta de armazenamento.

Efetue os seguintes passos no portal do Azure para sincronizar a chave de acesso ao armazenamento.

1. Aceda a **Descrição Geral** no seu recurso. Na lista de partilhas, escolha e clique numa partilha associada à conta de armazenamento que precisa de sincronizar.

    ![Selecione partilha com conta de armazenamento relevante](media/data-box-edge-manage-shares/sync-storage-key-1.png)

2. Clique em **Sincronizar chave de armazenamento**. Clique em **Sim** quando lhe for pedida a confirmação.

     ![Selecione chave de armazenamento Sync](media/data-box-edge-manage-shares/sync-storage-key-2.png)

3. Saia da caixa de diálogo depois de a sincronização estar concluída.

>[!NOTE]
> Apenas terá de efetuar este procedimento uma vez para uma conta de armazenamento fornecida. Não precisa de repetir esta ação para todas as partilhas associadas à mesma conta de armazenamento.


## <a name="next-steps"></a>Passos seguintes

- Saiba como [Gerir utilizadores através do portal do Azure](data-box-edge-manage-users.md).
