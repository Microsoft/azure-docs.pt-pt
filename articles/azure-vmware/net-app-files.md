---
title: Ficheiros NetApp para Solução VMware Azure
description: Utilize ficheiros Azure NetApp com VMs Azure VMware para migrar e sincronizar dados em servidores no local, VMs de Solução VMware Azure e infraestruturas em nuvem.
ms.topic: how-to
ms.date: 09/17/2020
ms.openlocfilehash: 993a67f82d5af971a4c4a0010bd9d19e2a3113b2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573927"
---
# <a name="netapp-files-for-azure-vmware-solution"></a>Ficheiros NetApp para Solução VMware Azure

Neste artigo, vamos percorrer os passos de integração dos Ficheiros Azure NetApp com cargas de trabalho baseadas em Azure VMware Solution. [O Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) é um serviço Azure para migrar e executar cargas de trabalho de ficheiros empresariais na nuvem sem alterações de código. Permite uma fácil migração de dados em todas as instalações e infraestruturas em nuvem. A sincronização rápida e reforçada de dados simplifica os cenários de migração e DevOps com capacidades como instantâneo snapshot, restauro e integração de Diretório Ativo.

## <a name="topology"></a>Topologia

Neste cenário, para testar e verificar a partilha de pool do Azure NetApp Files, o Azure NetApp Files está configurado no Azure com pool de capacidade, volume pool e sub-rede. Usamos o protocolo da NFS. Tanto os ficheiros Azure NetApp como a Azure VMware Solution são criados na mesma região de Azure, Leste dos EUA. A ligação à Solução VMware Azure é via Azure ExpressRoute.

![Ficheiros NetApp para topologia da Solução VMware Azure.](media/net-app-files/net-app-files-topology.png)

## <a name="prerequisites"></a>Pré-requisitos 

> [!div class="checklist"]
> * Assinatura Azure com Ficheiros Azure NetApp
> * Sub-rede para Ficheiro Azure NetApp
> * Linux VM na Solução VMware Azure
> * Windows VM na Solução VMware Azure

## <a name="verify-configuration-of-azure-netapp-files"></a>Verificar configuração de ficheiros Azure NetApp 

Primeiro, verifique a configuração dos Ficheiros Azure NetApp, criados em Azure num disco premium.

1. No portal Azure, em **ARMAZENAMENTO,** selecione **Ficheiros Azure NetApp**. Será apresentada uma lista dos seus ficheiros Azure NetApp configurados.

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Lista de ficheiros Azure NetApp."::: 

2. Se selecionar uma conta NetApp, pode visualizar várias definições. Por exemplo, se selecionar **Contoso-anf2,** verá as suas definições. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Lista de ficheiros Azure NetApp."::: 

3. Selecione **piscinas de capacidade** para verificar a piscina configurada. 

    :::image type="content" source="media/net-app-files/capacity-pools.png" alt-text="Lista de ficheiros Azure NetApp.":::

    Pode ver que a piscina de armazenamento está configurada como 4 TiB com disco premium.

4. Selecione **Volumes** (ver screenshot do passo 2), para visualizar os volumes criados sob a capacidade do pool.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Lista de ficheiros Azure NetApp.":::

5. Selecione um volume para visualizar a sua configuração.  

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Lista de ficheiros Azure NetApp.":::

    Pode ver que o volume de volume anfpool, com 200 GiB, foi criado como uma partilha de Ficheiros NetApp. Foi criada uma rede virtual IP privada para ficheiros Azure NetApp que dá o caminho NFS para montar nos VMs de Solução VMware Azure. 

## <a name="protocols-supported-by-azure-netapp-files"></a>Protocolos suportados por Ficheiros Azure NetApp  

Os ficheiros Azure NetApp suportam o bloqueio de mensagens do servidor (SMB) e o sistema de ficheiros de rede (NFS) de mapeamento. 

- **Partilha SMB**: Para implantar um volume SMB, é necessário criar primeiro uma ligação Ative Directory. Os controladores de domínio especificados devem ser acessíveis pela sub-rede delegada dos Ficheiros Azure NetApp para uma ligação bem sucedida. Uma vez configurado o Ative Directory na conta Azure NetApp Files, aparecerá como um item selecionável ao criar volumes SMB. 

- **NFS share**: Azure NetApp Files contribui para a criação de volumes utilizando NFS (NFSv3 e NFSv4.1), SMBv3 ou um protocolo duplo (NFSv3 e SMB). O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto. O NFS pode ser montado num servidor Linux utilizando uma linha de comando.

## <a name="create-azure-netapp-files"></a>Criar ficheiros Azure NetApp 

1. Faça login no [portal Azure](https://rc.portal.azure.com/#home). Ao abrigo dos Serviços Azure, selecione **ficheiros Azure NetApp**. 

2. **Selecione + Adicionar** para criar um novo volume de ficheiros Azure NetApp. 

3. Preencha os campos necessários (Nome, Subscrição, Grupo de Recursos e Localização) e selecione **Criar**. 

## <a name="add-capacity-pools-into-azure-netapp-files"></a>Adicione piscinas de capacidade em ficheiros Azure NetApp 

1. No portal Azure, selecione **Azure NetApp Files**, selecione **Capacity pools** e **+ Add pool**. 

2. Introduza os detalhes necessários para o nome do volume da piscina, nível de serviço e tamanho do disco (FQDN) ou IP e peso. Selecione **Adicionar**.

    :::image type="content" source="media/net-app-files/add-capacity-pool.png" alt-text="Lista de ficheiros Azure NetApp.":::

3. Para criar volumes sob a capacidade do pool, selecione **Volumes** do painel de pesquisa e selecione **+ Adicione volume**. 
 
4. Preencha os campos necessários, como mostrado na imagem seguinte.

    :::image type="content" source="media/net-app-files/create-a-volume.png" alt-text="Lista de ficheiros Azure NetApp.":::

5. Na página seguinte, selecione a partilha do tipo de protocolo. Selecione as versões se for uma partilha NFS e selecione o domínio Ative Directory se for uma partilha SMB.  

6. Se for uma partilha NFS, adicione o endereço IP de origem a partir do qual a partilha do tipo de protocolo será acedida. Selecione **Rever + criar**. 

    :::image type="content" source="media/net-app-files/select-volume-details.png" alt-text="Lista de ficheiros Azure NetApp.":::
 
    De acordo com os Ficheiros Azure NetApp no portal Azure, a sua quota NFS está pronta a ser utilizada.

    :::image type="content" source="media/net-app-files/share-ready.png" alt-text="Lista de ficheiros Azure NetApp.":::

## <a name="mount-an-nfs-file-share-on-your-azure-vmware-solution-vms"></a>Monte uma partilha de ficheiros NFS nos seus VMs de Solução VMware Azure

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-windows-vm"></a>Monte uma partilha de ficheiros NFS numa Solução VMware VMware Azure

- Abra um pedido de comando e executar o comando para mapear a partilha de ficheiros NFS. As imagens seguintes mostram a unidade de partilha mapeada num Windows VM em Azure VMware Solution.  

    :::image type="content" source="media/net-app-files/share-mapped-to-windows-vm.png" alt-text="Lista de ficheiros Azure NetApp.":::
 
    :::image type="content" source="media/net-app-files/mapped-to-windows-drive.png" alt-text="Lista de ficheiros Azure NetApp.":::

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-linux-vm"></a>Monte uma partilha de ficheiros NFS numa Solução Azure VMware Linux VM

#### <a name="setting-up-your-azure-instance"></a>Configurar o seu exemplo Azure

1. No seu portal Azure, associe a sua instância Azure a uma sub-rede definida na mesma rede virtual que o seu volume.

    > [!NOTE]
    > A sub-rede necessita de uma regra do grupo de segurança de rede que permita o tráfego nas portas NFS (2049, 111), UDP e TCP.

2. Abra um cliente SSH e ligue-se à sua instância Azure. Para obter mais informações, consulte [como utilizar as teclas SSH com o Windows no Azure](../virtual-machines/linux/ssh-from-windows.md).

3. Instale o cliente NFS no seu caso Azure:
   - No caso Red Hat Enterprise Linux ou SUSE Linux: `sudo yum install -y nfs-utils`
   - Num caso Ubuntu ou Debian: `sudo apt-get install nfs-common` 

#### <a name="mounting-your-file-system"></a>Montagem do seu sistema de ficheiros

1. Crie um novo diretório no seu caso Azure: `sudo mkdir ANFPOOL`

2. Selecione um endereço IP alvo de montagem.

3. Monte o seu sistema de ficheiros: `sudo mount -t nfs -o rw,hard,rsize=1048576,wsize=1048576,vers=3,tcp 10.22.3.4:/ANFPOOL ANFPOOL`

    :::image type="content" source="media/net-app-files/root-test.png" alt-text="Lista de ficheiros Azure NetApp.":::

## <a name="create-an-active-directory-connection-for-an-smb-share"></a>Criar uma ligação ative directory para uma participação SMB

1. Selecione uma conta NetApp no portal Azure.

2. Nos ficheiros Azure NetApp, selecione **ative directory connections**.

    :::image type="content" source="media/net-app-files/active-directory-connections.png" alt-text="Lista de ficheiros Azure NetApp."::: 

3. Selecione **Junte-se** para se juntar à participação do SMB no Ative Directory. A imagem que se segue mostra os detalhes de domínio da partilha SMB.

    :::image type="content" source="media/net-app-files/active-directory-connect-details.png" alt-text="Lista de ficheiros Azure NetApp."::: 

    A sua partilha de ficheiros Azure SMB está pronta a ser utilizada.  

    :::image type="content" source="media/net-app-files/smb-file-share-details.png" alt-text="Lista de ficheiros Azure NetApp."::: 

4. Mapear a partilha do SMB para o seu VM Solução VMware Windows Azure. Utilize o caminho de montagem SMB, como mostrado na imagem anterior. Para mais informações, consulte [Map uma unidade de rede no Windows 10](https://support.microsoft.com/help/4026635/windows-10-map-a-network-drive)

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre a [hierarquia de armazenamento dos Ficheiros Azure NetApp](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).
- Ver [gestão do ciclo de vida dos VMS de Solução VMware Azure](lifecycle-management-of-azure-vmware-solution-vms.md)
- Ver [Integr Azure VMware Solution em um hub e arquitetura falada](concepts-hub-and-spoke.md)
