---
title: Gestão de ações da Azure Stack Edge Pro GPU Microsoft Docs
description: Descreve como usar o portal Azure para gerir ações no seu GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 44f22f365699ddc345849df78d6e27c2703d785b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904685"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-stack-edge-pro"></a>Utilize o portal Azure para gerir ações no seu Azure Stack Edge Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este artigo descreve como gerir ações no seu Azure Stack Edge Pro. Pode gerir o Azure Stack Edge Pro através do portal Azure ou através da UI web local. Utilize o portal Azure para adicionar, eliminar, atualizar ações ou sincronizar a chave de armazenamento para a conta de armazenamento associada às ações.

## <a name="about-shares"></a>Sobre as partilhas

Para transferir dados para o Azure, precisa de criar ações no seu Azure Stack Edge Pro. As ações que adiciona no dispositivo Azure Stack Edge Pro podem ser ações locais ou partilhas que empurram os dados para a nuvem.

 - **Ações locais**: Utilize estas ações quando pretender que os dados sejam tratados localmente no dispositivo.
 - **Ações**: Utilize estas ações quando pretender que os dados do dispositivo sejam automaticamente empurrados para a sua conta de armazenamento na nuvem. Todas as funções de cloud, tais como **as teclas de armazenamento** **Refresh** e Sync, aplicam-se às ações.


## <a name="add-a-share"></a>Adicionar uma partilha

Efetue os seguintes passos no portal do Azure para criar uma partilha.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá ao **Gateway > Shares**. **Selecione + Adicione a partilha** na barra de comando.

    ![Selecione adicionar a partilha](media/azure-stack-edge-j-series-manage-shares/add-share-1.png)

2. Em **Adicionar Partilha**, especifique as definições de partilha. Indique um nome exclusivo para a partilha.
    
    Os nomes das partilhas só podem conter números, letras minúsculas e hífenes. O nome da partilha tem de ter entre 3 e 63 carateres e começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen.

3. Selecione um **Tipo** para a partilha. O tipo pode ser **SMB** ou **NFS**, sendo que SMB é a predefinição. SMB é o padrão para clientes Windows, e NFS é utilizado para clientes Linux. Dependendo se escolher a partilha SMB ou NFS, as opções apresentadas são ligeiramente diferentes.

4. Forneça uma **Conta de armazenamento** onde reside a partilha. É criado um contentor na conta de armazenamento com o nome da partilha, se o contentor ainda não existir. Se o contentor já existir, é utilizado o contentor existente.

5. Na lista de dropdown, escolha o **serviço de Armazenamento** a partir de blob de bloco, blob de página ou ficheiros. O tipo de serviço escolhido depende do formato no qual pretende que os dados residam no Azure. Por exemplo, neste caso, queremos que os dados residam como blobs de bloco em Azure, daí **selecionarmos Block Blob**. Se escolher **o Page Blob,** deve certificar-se de que os seus dados estão alinhados com 512 bytes. Utilize **o blob page** para VHDs ou VHDX que estejam sempre alinhados com 512 bytes.

6. Este passo depende de estar a criar uma partilha SMB ou NFS.
    - **Se criar uma partilha SMB** - no campo **Utilizador local com todos os privilégios**, escolha **Criar novo** ou **Utilizar existente**. Se criar um novo utilizador local, forneça o **nome de utilizador**, a **palavra-passe** e, em seguida, confirme a palavra-passe. Esta ação atribui as permissões ao utilizador local. Depois de ter atribuído as permissões aqui, pode utilizar o Explorador de Ficheiros para modificar estas permissões.

        ![Adicionar uma partilha SMB](media/azure-stack-edge-j-series-manage-shares/add-smb-share.png)

        Se marcar permitir apenas operações de leitura para esta partilha de dados, pode especificar utilizadores só de leitura.
    - **Se criar uma partilha NFS** - tem de fornecer os **Endereços IP dos clientes permitidos** que podem aceder à partilha.

        ![Adicionar uma partilha NFS](media/azure-stack-edge-j-series-manage-shares/add-nfs-share.png)

7. Para aceder facilmente às ações dos módulos de computação Edge, utilize o ponto de montagem local. **Selecione Utilize a partilha com o cálculo Edge** para que a partilha seja montada automaticamente após a sua criação. Quando esta opção é selecionada, o módulo Edge também pode utilizar o cálculo com o ponto de montagem local.

8. Clique em **Criar** para criar a partilha. Será notificado de que a criação da partilha está em curso. Depois de criar a partilha com as definições especificadas, o painel **Partilhas** é atualizado para refletir a nova partilha.

## <a name="add-a-local-share"></a>Adicione uma parte local

1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá ao **Gateway > Shares**. **Selecione + Adicione a partilha** na barra de comando.

    ![Selecione adicionar a partilha](media/azure-stack-edge-j-series-manage-shares/add-local-share-1.png)

2. Em **Adicionar Partilha**, especifique as definições de partilha. Indique um nome exclusivo para a partilha.
    
    Os nomes de partilha só podem conter números, letras minúsculas e maiúsculas e hífenes. O nome da partilha tem de ter entre 3 e 63 carateres e começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen.

3. Selecione um **Tipo** para a partilha. O tipo pode ser **SMB** ou **NFS**, sendo que SMB é a predefinição. SMB é o padrão para clientes Windows, e NFS é utilizado para clientes Linux. Dependendo se escolher a partilha SMB ou NFS, as opções apresentadas são ligeiramente diferentes.

   > [!IMPORTANT]
   > Certifique-se de que a conta de Armazenamento Azure que utiliza não tem políticas de imutabilidade definidas se estiver a usá-la com um dispositivo Azure Stack Edge Pro ou Data Box Gateway. Para obter mais informações, consulte [Definir e gerir políticas de imutabilidade para armazenamento de bolhas](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).

4. Para aceder facilmente às ações dos módulos de computação Edge, utilize o ponto de montagem local. Selecione **Utilize a partilha com o cálculo Edge** para que o módulo Edge possa utilizar o cálculo com o ponto de montagem local.

5. Selecione **Configurar como ações locais edge**. Os dados em ações locais permanecerão localmente no dispositivo. Pode processar estes dados localmente.

6. No campo de **utilizadores local de todos os privilégios,** escolha entre **criar novo** ou **utilizar existente.**

7. Selecione **Criar**. 

    ![Criar ações locais](media/azure-stack-edge-j-series-manage-shares/add-local-share-2.png)

    Vê uma notificação de que a criação de ações está em curso. Depois de criar a partilha com as definições especificadas, o painel **Partilhas** é atualizado para refletir a nova partilha.

    ![Ver atualizações Partilhar lâmina](media/azure-stack-edge-j-series-manage-shares/add-local-share-3.png)
    
    Selecione a partilha para ver o ponto de montagem local para os módulos de computação Edge para esta partilha.

    ![Ver detalhes de partilha local](media/azure-stack-edge-j-series-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Monte uma parte

Se criou uma parte antes de configurar o computamento no seu dispositivo Azure Stack Edge Pro, terá de montar a partilha. Tome os seguintes passos para montar uma parte.


1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá ao **Gateway > Shares**. Na lista das ações, selecione a parte que pretende montar. A coluna **usada para cálculo** mostrará o estado de **desativado** para a parte selecionada.

    ![Selecionar partilha](media/azure-stack-edge-j-series-manage-shares/mount-share-1.png)

2. Selecione **Mount**.

    ![Selecione montagem](media/azure-stack-edge-j-series-manage-shares/mount-share-2.png)

3. Quando solicitado para confirmação, selecione **Sim**. Isto vai montar a parte.

    ![Confirmar montagem](media/azure-stack-edge-j-series-manage-shares/mount-share-3.png)

4. Depois de a parte ser montada, vá para a lista de ações. Verá que a coluna **Usada para cálculo** mostra o estado da partilha como **Ativado**.

    ![Partilhar montado](media/azure-stack-edge-j-series-manage-shares/mount-share-4.png)

5. Selecione a partilha novamente para ver o ponto de montagem local para a partilha. O módulo de computação Edge utiliza este ponto de montagem local para a partilha.

    ![Ponto de montagem local para a parte](media/azure-stack-edge-j-series-manage-shares/mount-share-5.png)

## <a name="unmount-a-share"></a>Desmonte uma parte

Faça os seguintes passos no portal Azure para desmontar uma parte.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá ao **Gateway > Shares**.

    ![Selecionar partilha](media/azure-stack-edge-j-series-manage-shares/unmount-share-1.png)

2. Na lista das ações, selecione a parte que pretende desmontar. Certifique-se de que a parte que desmonta não é utilizada por nenhum módulo. Se a partilha for utilizada por um módulo, verá problemas com o módulo correspondente. Selecione **Desmonte**.

    ![Selecione desmonte](media/azure-stack-edge-j-series-manage-shares/unmount-share-2.png)

3. Quando solicitado para confirmação, selecione **Sim**. Isto vai desmontar a parte.

    ![Confirmar desmontagem](media/azure-stack-edge-j-series-manage-shares/unmount-share-3.png)

4. Depois de a parte não ser desmontada, vá para a lista de ações. Verá que a coluna **usada para computação** mostra o estado da partilha como **Desativada**.

    ![Partilha desmontada](media/azure-stack-edge-j-series-manage-shares/unmount-share-4.png)

## <a name="delete-a-share"></a>Eliminar uma partilha

Efetue os seguintes passos no portal do Azure para eliminar uma partilha.

1. Na lista de partilhas, selecione e clique na partilha que pretende eliminar.

    ![Selecionar partilha](media/azure-stack-edge-j-series-manage-shares/delete-share-1.png)

2. Clique em **Eliminar**.

    ![Clicar em eliminar](media/azure-stack-edge-j-series-manage-shares/delete-share-2.png)

3. Quando lhe for pedida a confirmação, clique em **Sim**.

    ![Confirmar eliminação](media/azure-stack-edge-j-series-manage-shares/delete-share-3.png)

A lista de atualizações de ações para refletir a supressão.

## <a name="refresh-shares"></a>Atualizar partilhas

A funcionalidade de atualização permite-lhe refrescar o conteúdo de uma partilha. Quando atualiza uma partilha, é iniciada uma pesquisa para localizar todos os objetos do Azure, incluindo os blobs e ficheiros que foram adicionados à cloud desde a última atualização. Estes ficheiros adicionais são depois descarregados para atualizar o conteúdo da partilha no dispositivo.

> [!IMPORTANT]
> - Não pode refrescar as ações locais.
> - As permissões e as listas de controlo de acesso (ACLs) não são preservadas através de uma operação de atualização. 

Efetue os seguintes passos no portal do Azure para atualizar uma partilha.

1.  No portal do Azure, aceda a **Partilhas**. Selecione e clique na partilha que pretende atualizar.

    ![Selecionar partilha](media/azure-stack-edge-j-series-manage-shares/refresh-share-1.png)

2.  Clique em **Atualizar**. 

    ![Clicar em atualizar](media/azure-stack-edge-j-series-manage-shares/refresh-share-2.png)
 
3.  Quando lhe for pedida a confirmação, clique em **Sim**. É iniciada uma tarefa para atualizar os conteúdos da partilha no local.

    ![Confirmar atualização](media/azure-stack-edge-j-series-manage-shares/refresh-share-3.png)
 
4.   Enquanto a atualização estiver em curso, a opção de atualização está desativada no menu de contexto. Clique na notificação da tarefa para ver o estado da tarefa de atualização.

5.   O tempo de atualização depende do número de ficheiros no contentor do Azure, bem como dos ficheiros no dispositivo. Assim que a atualização tiver sido concluída com êxito, o carimbo de data/hora da partilha é atualizado. Mesmo se a atualização tiver falhas parciais, a operação é considerada como concluída com êxito e o carimbo de data/hora é atualizado. Os registos de erro de atualização também são atualizados.

![Carimbo de data/hora atualizado](media/azure-stack-edge-j-series-manage-shares/refresh-share-4.png)
 
Se houver uma falha, é gerado um alerta. O alerta fornece detalhes sobre a causa e a recomendação para corrigir o problema. O alerta também contém ligações para um ficheiro com o resumo completo das falhas, incluindo os ficheiros que não foi possível atualizar ou eliminar.

## <a name="sync-pinned-files"></a>Sync arquivos pinned 

Para sincronizar automaticamente os ficheiros fixados, faça os seguintes passos no portal Azure:
 
1. Selecione uma conta de armazenamento Azure existente. 

2. Vá a **Recipientes** e selecione **+ Recipiente** para criar um recipiente. Nomeie este recipiente como *novo detido.* Deite o **nível de acesso público** ao Contentor.

    ![Sincronização automatizada para ficheiros presos 1](media/azure-stack-edge-j-series-manage-shares/image-1.png)

3. Selecione o nome do recipiente e deite os seguintes metadados:  

    - Nome = "Pinned" 
    - Valor = "Verdadeiro" 

    ![Sincronização automatizada para ficheiros presos 2](media/azure-stack-edge-j-series-manage-shares/image-2.png)
 
4. Crie uma nova partilha no seu dispositivo. Mapeá-lo para o recipiente fixado, escolhendo a opção de recipiente existente. Marque a parte como lido apenas. Crie um novo utilizador e especifique o nome de utilizador e uma senha correspondente para esta partilha.  

    ![Sincronização automatizada para ficheiros presos 3](media/azure-stack-edge-j-series-manage-shares/image-3.png)
 
5. Do portal Azure, navegue até ao recipiente que criou. Faça o upload do ficheiro que pretende ser fixado no novo estado que tem os metadados definidos para serem fixados. 

6. Selecione **Os dados de Atualização** no portal Azure para que o dispositivo descarregue a política de fixação para esse recipiente de armazenamento Azure em particular.  

    ![Sincronização automatizada para ficheiros presos 4](media/azure-stack-edge-j-series-manage-shares/image-4.png)
 
7. Aceda à nova partilha que foi criada no dispositivo. O ficheiro que foi enviado para a conta de armazenamento é agora descarregado para a parte local. 

    Sempre que o aparelho estiver desligado ou reconectado, ativa a atualização. A atualização só vai trazer os ficheiros que mudaram. 


## <a name="sync-storage-keys"></a>Sincronizar chaves de armazenamento

Se as chaves da conta de armazenamento tiverem sido rodadas, terá de sincronizar as chaves de acesso ao armazenamento. A sincronização ajuda o dispositivo a obter as chaves mais recente para a sua conta de armazenamento.

Efetue os seguintes passos no portal do Azure para sincronizar a chave de acesso ao armazenamento.

1. Aceda a **Descrição Geral** no seu recurso. Na lista de partilhas, escolha e clique numa partilha associada à conta de armazenamento que precisa de sincronizar.

    ![Selecione partilhar com conta de armazenamento relevante](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-1.png)

2. Clique em **Sincronizar chave de armazenamento**. Clique em **Sim** quando lhe for pedida a confirmação.

     ![Selecione a chave de armazenamento sync](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-2.png)

3. Saia da caixa de diálogo depois de a sincronização estar concluída.

>[!NOTE]
> Apenas terá de efetuar este procedimento uma vez para uma conta de armazenamento fornecida. Não precisa de repetir esta ação para todas as partilhas associadas à mesma conta de armazenamento.


## <a name="next-steps"></a>Passos seguintes

- Saiba como [Gerir utilizadores através do portal do Azure](azure-stack-edge-j-series-manage-users.md).
