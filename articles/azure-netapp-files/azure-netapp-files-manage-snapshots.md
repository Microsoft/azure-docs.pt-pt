---
title: Gerir instantâneos utilizando ficheiros Azure NetApp Microsoft Docs
description: Descreve como criar, gerir e utilizar instantâneos utilizando ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/12/2020
ms.author: b-juche
ms.openlocfilehash: c64bc8bf265a8e3cc3c490827bdbd79661e3528a
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591752"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Gerir instantâneos com o Azure NetApp Files

O Azure NetApp Files suporta a criação de instantâneos a pedido e a utilização de políticas instantâneas para agendar a criação automática de instantâneos. Também pode restaurar uma imagem instantânea para um novo volume, restaurar um único ficheiro utilizando um cliente, ou reverter um volume existente utilizando um instantâneo.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Crie um instantâneo a pedido para um volume

Pode criar instantâneos de volume a pedido. 

1.  Vá ao volume para o que pretende criar uma imagem. Clique **em Snapshots**.

    ![Navegue para instantâneos](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Clique **+ Adicione instantâneo** para criar uma imagem a pedido para um volume.

    ![Adicionar instantâneo](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Na janela New Snapshot, forneça um nome para o novo instantâneo que está a criar.   

    ![Novo instantâneo](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Clique em **OK**. 

## <a name="manage-snapshot-policies"></a>Gerir políticas de instantâneo

Pode agendar as imagens de volume a serem tomadas automaticamente utilizando políticas de instantâneo. Também pode modificar uma política de instantâneos conforme necessário, ou eliminar uma política de instantâneos de que já não precisa.  

### <a name="register-the-feature"></a>Registar a funcionalidade

A funcionalidade **de política instantânea** está atualmente em pré-visualização. Se estiver a utilizar esta funcionalidade pela primeira vez, deverá registar a funcionalidade primeiro. 

1. Registar a função: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Verifique o estado do registo da funcionalidade: 

    > [!NOTE]
    > O **Estado de Registo** pode estar no estado até `Registering` 60 minutos antes de mudar para `Registered` . Aguarde até que o estado seja **registado** antes de continuar.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
Também pode utilizar [comandos Azure CLI](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` e registar a funcionalidade e exibir o estado de `az feature show` registo. 

### <a name="create-a-snapshot-policy"></a>Criar uma política instantânea 

Uma política instantânea permite especificar a frequência de criação instantânea em ciclos de hora, dia, semanais ou mensais. Também é necessário especificar o número máximo de instantâneos para reter para o volume.  

1.  A partir da vista da Conta NetApp, clique na **política Snapshot**.

    ![Navegação de política instantânea](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  Na janela Snapshot Policy, desapeiça o Estado de Política **para Ativar**. 

3.  Clique no separador **Hourly,** **Daily,** **Weekly** ou **Monthly** para criar políticas de instantâneos horárias, diárias, semanais ou mensais. Especificar o **número de instantâneos para manter**.  

    Consulte [os limites de recursos para ficheiros Azure NetApp](azure-netapp-files-resource-limits.md) sobre o número máximo de instantâneos permitidos para um volume. 

    O exemplo a seguir mostra a configuração da política de instantâneo de hora a hora. 

    ![Política instantânea de hora em hora](../media/azure-netapp-files/snapshot-policy-hourly.png)

    O exemplo a seguir mostra a configuração diária da política de instantâneos.

    ![Política instantânea diariamente](../media/azure-netapp-files/snapshot-policy-daily.png)

    O exemplo a seguir mostra a configuração semanal da política de instantâneos.

    ![Política instantânea semanal](../media/azure-netapp-files/snapshot-policy-weekly.png)

    O exemplo a seguir mostra a configuração da política de instantâneo mensal.

    ![Política de instantâneo mensal](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Clique em **Guardar**.  

Se precisar de criar políticas de instantâneo adicionais, repita o Passo 3.
As políticas que criou aparecem na página de política do Snapshot.

Se quiser um volume para utilizar a política de instantâneo, tem de [aplicar a política ao volume](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Aplicar uma política instantânea num volume

Se quiser que um volume utilize uma política de instantâneo que criou, tem de aplicar a política ao volume. 

Não é possível aplicar uma política instantânea a um volume de destino na replicação de regiões cruzadas.  

1.  Aceda à página **Volumes,** clique com o botão direito no volume a que pretende aplicar uma política de instantâneo e selecione **Editar**.

    ![Volumes menu de clique direito](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  Na janela Editar, sob **a política Snapshot,** selecione uma política para utilizar para o volume.  Clique **em OK** para aplicar a política.  

    ![Edição de política instantânea](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Modificar uma política instantânea 

Pode modificar uma política de instantâneo existente para alterar o estado de política, a frequência instantânea (hora, dia, dia, semanal ou mensal) ou o número de instantâneos a manter.  
 
1.  A partir da vista da Conta NetApp, clique na **política Snapshot**.

2.  Clique à direita na política de instantâneo que pretende modificar e, em seguida, **selecione Editar**.

    ![Menu de clique de política instantânea](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Faça as alterações na janela 'Política instantânea' que aparece e, em seguida, clique em **Guardar**. 

### <a name="delete-a-snapshot-policy"></a>Excluir uma política instantânea 

Pode apagar uma política de instantâneo que já não pretende manter.   

1.  A partir da vista da Conta NetApp, clique na **política Snapshot**.

2.  Clique à direita na política de instantâneo que pretende modificar e, em seguida, selecione **Delete**.

    ![Menu de clique de política instantânea](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Clique **em Sim** para confirmar que pretende eliminar a política de instantâneo.   

    ![Política instantânea eliminar confirmação](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="edit-the-hide-snapshot-path-option"></a>Edite a opção Hide Snapshot Path
A opção Hide Snapshot Path controla se o caminho instantâneo de um volume é visível. Durante a criação de um volume [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume) ou [SMB,](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) tem a opção de especificar se o caminho do instantâneo deve ser escondido. Pode posteriormente editar a opção Hide Snapshot Path, conforme necessário.  

> [!NOTE]
> Para um volume de [destino](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume) na replicação transversal, a opção Hide Snapshot Path é ativada por padrão e a definição não pode ser modificada. 

1. Para visualizar a definição da opção Hide Snapshot Path de um volume, selecione o volume. O campo **de caminho do instantâneo Oculta** mostra se a opção está ativada.   
    ![Screenshot que descreve o campo Hide Snapshot Path.](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. Para editar a opção Ocultar O caminho, clique em **Editar** na página de volume e modificar a opção **de caminho de ocultação** conforme necessário.   
    ![Screenshot que descreve a opção de instantâneo de volume editar.](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Restaurar um instantâneo para um novo volume

Atualmente, pode restaurar uma imagem apenas para um novo volume. 
1. Selecione **Snapshots** da lâmina volume para visualizar a lista de instantâneos. 
2. Clique com o botão direito no instantâneo para restaurar e **selecione Restaurar para novo volume** a partir da opção menu.  

    ![Restaurar o instantâneo para um novo volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. Na janela Criar um Volume, forneça informações sobre o novo volume:  
    * **Nome**   
        Especifique o nome do volume que está a criar.  
        
        O nome tem de ser exclusivo dentro de um grupo de recursos. Deve ter pelo menos três caracteres de comprimento.  Pode utilizar carateres alfanuméricos.

    * **Quota**  
        Especifique a quantidade de armazenamento lógico que pretende atribuir ao volume.  

    ![Restaurar para novo volume](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Clique **em Review+criar**.  Clique em **Criar**.   
    O novo volume utiliza o mesmo protocolo que o instantâneo utiliza.   
    O novo volume ao qual o instantâneo é restaurado aparece na lâmina Volumes.

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>Restaurar um ficheiro a partir de uma imagem usando um cliente

Se não quiser [restaurar a totalidade do instantâneo num volume,](#restore-a-snapshot-to-a-new-volume)tem a opção de restaurar um ficheiro a partir de uma imagem, utilizando um cliente que tenha o volume montado.  

O volume montado contém um diretório instantâneo denominado  `.snapshot` (em clientes NFS) ou `~snapshot` (em clientes SMB) que é acessível ao cliente. O diretório instantâneo contém subdireções correspondentes às imagens do volume. Cada subdiretório contém os ficheiros do instantâneo. Se eliminar acidentalmente ou substituir um ficheiro, pode restaurar o ficheiro no diretório de leitura-escrita dos pais copiando o ficheiro de uma subdiretório instantânea para o diretório de leitura. 

Se não vir o diretório instantâneo, pode ser escondido porque a opção Hide Snapshot Path está ativada. Pode [editar a opção Hide Snapshot Path](#edit-the-hide-snapshot-path-option) para desativá-la.  

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Restaurar um ficheiro utilizando um cliente Linux NFS 

1. Utilize o `ls` comando Linux para listar o ficheiro que pretende restaurar do `.snapshot` diretório. 

    Por exemplo:

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. Utilize o `cp` comando para copiar o ficheiro para o diretório dos pais.  

    Por exemplo: 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Restaurar um ficheiro utilizando um cliente Windows 

1. Se o `~snapshot` diretório do volume estiver escondido, [mostre itens ocultos](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) no diretório dos pais para visualizar `~snapshot` .

    ![Mostrar itens ocultos](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. Navegue para a subdiretória interior `~snapshot` para encontrar o ficheiro que pretende restaurar.  Clique com o direito no ficheiro. Selecione **Copiar**.  

    ![Copy file para restaurar](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Volte para o diretório dos pais. Clique com o botão direito no diretório dos pais e `Paste` selecione para colar o ficheiro ao diretório.

    ![Pasta para restaurar](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. Também pode clicar à direita no diretório dos pais, selecionar **Propriedades,** clicar no separador **Versões Anteriores** para ver a lista de instantâneos e selecione **Restaurar** um ficheiro.  

    ![Propriedades Versões anteriores](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="revert-a-volume-using-snapshot-revert"></a>Reverta um volume usando instantâneo reverter

A funcionalidade de reverte instantânea permite-lhe reverter rapidamente um volume para o estado em que estava quando uma determinada imagem foi tirada. Na maioria dos casos, reverter um volume é muito mais rápido do que restaurar ficheiros individuais de uma imagem para o sistema de ficheiros ativos. É também mais eficiente em termos de espaço em comparação com restaurar um instantâneo para um novo volume. 

Pode encontrar a opção Revert Volume no menu Snapshots de um volume. Depois de selecionar uma fotografia para a reversão, o Azure NetApp Files reverte o volume para os dados e os bicos de tempo que continha quando a fotografia selecionada foi tirada. 

> [!IMPORTANT]
> Os dados ativos do sistema de ficheiros e as imagens que foram tiradas após a fotografia selecionada serão perdidos. A operação snapshot reverte substituirá *todos os* dados do volume direcionado com os dados na imagem selecionada. Deve prestar atenção ao conteúdo instantâneo e à data de criação quando selecionar uma fotografia. Não pode desfazer a operação do instantâneo.

1. Aceda ao menu **Snapshots** de um volume.  Clique com o botão direito na imagem que pretende utilizar para a operação reverter. Selecione **o volume reverter**. 

    ![Screenshot que descreve o menu de clique direito de um instantâneo](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. Na janela Revert Volume para Snapshot, digite o nome do volume e clique em **Reverter**.   

    O volume é agora restaurado ao ponto no tempo do instantâneo selecionado.

    ![Screenshot que o volume reverte para a janela instantânea](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="delete-snapshots"></a>Eliminar instantâneos  

Pode apagar imagens que já não precisa de guardar. 

1. Aceda ao menu **Snapshots** de um volume. Clique com o botão direito na imagem que pretende eliminar. Selecione **Eliminar**.

    ![Screenshot que descreve o menu de clique direito de um instantâneo](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. Na janela Delete Snapshot, confirme que pretende eliminar a imagem clicando em **Sim**. 

    ![Screenshot que confirma a eliminação de instantâneos](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas das políticas de instantâneo](troubleshoot-snapshot-policies.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
* [Azure NetApp Files Snapshots 101 vídeo](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
