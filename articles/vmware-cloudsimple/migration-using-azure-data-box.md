---
title: Azure VMware Solution - migração usando Azure Data Box
description: Como utilizar a Caixa de Dados Azure para migrar em massa dados para a Solução VMware Azure.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab772bd9cb415045ef70cb4cf9a518791befb192
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741937"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migração de dados para Azure VMware Solution usando Azure Data Box

A solução de nuvem Microsoft Azure Data Box permite enviar terabytes (TBs) de dados para a Azure de uma forma rápida, barata e fiável. A transferência segura de dados é acelerada pelo envio de um dispositivo de armazenamento Data Box proprietário. Cada dispositivo de armazenamento tem uma capacidade máxima de armazenamento utilizável de 80 TB e é transportado para o seu centro de dados por uma transportadora regional. O aparelho tem um invólucro acidentado para proteger e proteger os seus dados durante o trânsito.

Ao utilizar a Data Box, pode migrar em massa os seus dados VMware para a sua nuvem privada. Os dados do ambiente VMware vSphere no local são copiados para a Caixa de Dados através do protocolo Sistema de Ficheiros de Rede (NFS). A migração de dados em massa envolve guardar uma cópia pontual de máquinas virtuais, configuração e dados associados para a Data Box e, em seguida, enviá-lo manualmente para Azure.

Neste artigo, aprende-se:

* A configurar a Caixa de Dados.
* Copiando dados do ambiente VMware no local para a Caixa de Dados através de NFS.
* Preparando-se para a devolução da Caixa de Dados.
* Preparar dados blob para copiar para Azure VMware Solution.
* Copiando os dados do Azure para a sua nuvem privada.

## <a name="scenarios"></a>Cenários

Utilizar a Caixa de Dados nos seguintes cenários para a migração de dados em massa:

* Para migrar uma grande quantidade de dados do local para a Azure VMware Solution. Este método estabelece uma linha de base e sincroniza diferenças sobre a rede.
* Para migrar um grande número de máquinas virtuais que estão desligadas (máquinas virtuais frias).
* Para migrar dados de máquinas virtuais para configurar ambientes de desenvolvimento e teste.
* Para migrar um grande número de modelos de máquinas virtuais, ficheiros ISO e discos de máquinas virtuais.

## <a name="before-you-begin"></a>Antes de começar

* Verifique os pré-requisitos e [encomende a Caixa de Dados](../databox/data-box-deploy-ordered.md) através do seu portal Azure. Durante o processo de encomenda, deve selecionar uma conta de armazenamento que permita o armazenamento da Blob. Depois de receber o dispositivo Data Box, conecte-o à sua rede no local e [instale o dispositivo](../databox/data-box-deploy-set-up.md) com um endereço IP que seja acessível a partir da sua rede de gestão vSphere.

* Crie uma rede virtual e uma conta de armazenamento na mesma região onde a sua Solução VMware Azure é a provisionada.

* Crie uma [ligação de rede virtual Azure](cloudsimple-azure-network-connection.md) da sua nuvem privada para a rede virtual onde a conta de armazenamento é criada seguindo os passos da [rede virtual Connect Azure para cloudSimple usando ExpressRoute](virtual-network-connection.md).

## <a name="set-up-data-box-for-nfs"></a>Configurar caixa de dados para NFS

Ligue-se à sua Internet local da Data Box seguindo os passos na secção "Ligar ao seu dispositivo" [tutorial: Cabo e ligar-se à sua Caixa de Dados Azure](../databox/data-box-deploy-set-up.md).  Configure a Caixa de Dados para permitir o acesso aos clientes NFS:

1. Na uI web local, vá à página **'Ligar e copiar'.** Nas **definições de NFS,** selecione **o acesso ao cliente NFS**. 

    ![Configurar o acesso de cliente NFS 1](media/nfs-client-access.png)

2. Introduza o endereço IP dos anfitriões VMware ESXi e selecione **Adicionar**. Pode configurar o acesso a todos os anfitriões do seu cluster vSphere repetindo este passo. Selecione **OK**.

    ![Configurar o acesso de cliente NFS 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada nas partilhas dos blobs de blocos e dos blobs de páginas representa um contentor para o qual os dados são carregados como blobs. Não é possível copiar ficheiros diretamente para a pasta *raiz* na conta de armazenamento.

Nas partilhas de blob de blocos e de blob de páginas, as entidades de primeiro nível são os contentores e as de segundo nível são os blobs. Ao abrigo de ações para a Azure Files, as entidades de primeiro nível são ações, e entidades de segundo nível são ficheiros.

A tabela a seguir mostra o caminho UNC para as partilhas no Data Box e o URL do caminho do Armazenamento do Microsoft Azure onde os dados são carregados. O URL do caminho de Armazenamento do Microsoft Azure final pode derivar do caminho da partilha UNC.
 
| Blobs e Ficheiros | Caminho e URL |
|---------------- | ------------ |
| Blobs de bloco do Azure | <li>Caminho UNC para as partilhas: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL de Armazenamento do Microsoft Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para as partilhas: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL do Armazenamento do Microsoft Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Ficheiros do Azure       |<li>Caminho UNC para as partilhas: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL de Armazenamento do Microsoft Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Utilize bolhas do Bloco Azure para copiar dados VMware.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Monte a NFS partilhar como uma loja de dados no seu cluster vCenter no local e copiar os dados

A partilha NFS da sua Caixa de Dados deve ser montada como uma loja de dados no seu vCenter cluster ou vMware ESXi, a fim de copiar os dados para a loja de dados NFS:

1. Inicie sessão no seu servidor vCenter no local.

2. Clique no botão direito **Datacenter**, selecione **Armazenamento,** selecione **New Datastore** e, em seguida, selecione **Next**.

   ![Adicionar nova loja de dados](media/databox-migration-add-datastore.png)

3. No passo 1 do assistente add datastore, selecione **NFS** sob **tipo**.

   ![Adicionar nova loja de dados - tipo](media/databox-migration-add-datastore-type.png)

4. No passo 2 do assistente, selecione **NFS 3** como a versão NFS e, em seguida, selecione **Seguinte**.

   ![Adicionar nova loja de dados - versão NFS](media/databox-migration-add-datastore-nfs-version.png)

5. No passo 3 do assistente, especifique o nome da datastore, do caminho e do servidor. Pode utilizar o endereço IP da sua Caixa de Dados para o servidor. O caminho da pasta estará no `/<StorageAccountName_BlockBlob>/<ContainerName>/` formato.

   ![Adicionar nova loja de dados - configuração NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. No passo 4 do assistente, selecione os anfitriões ESXi onde pretende que a datastore seja montada e, em seguida, selecione **Next**.  Num cluster, selecione todos os anfitriões para garantir a migração das máquinas virtuais.

   ![Adicionar nova loja de dados - Selecione anfitriões](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. No passo 5 do assistente, reveja o resumo e **selecione Terminar**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Copiar dados para a data box NFS datastore

As máquinas virtuais podem ser migradas ou clonadas para a nova datastore.  Quaisquer máquinas virtuais não utilizadas que pretenda migrar podem ser migradas para a data-loja data Box NFS utilizando a opção **de armazenamento vMotion.** As máquinas virtuais ativas podem ser clonadas na data box NFS datastore.

* Identifique e enuqueia as máquinas virtuais que podem ser **movidas.**
* Identifique e enuque não se regise as máquinas virtuais que devem ser **clonadas.**

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Mover uma máquina virtual para uma datastore de caixa de dados

1. Clique com o botão direito na máquina virtual que pretende mover-se para a datastore da Caixa de Dados e, em seguida, **selecione Migrar**.

    ![Migrar máquina virtual](media/databox-migration-vm-migrate.png)

2. Selecione **Alterar o armazenamento apenas** para o tipo de migração e, em seguida, selecione **Seguinte**.

    ![Migrar máquina virtual - apenas armazenamento](media/databox-migration-vm-migrate-change-storage.png)

3. Selecione **databox-datastore** como destino e, em seguida, selecione **Seguinte**.

    ![Migrar máquina virtual - selecione datastore](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Reveja as informações e **selecione Acabamento**.

5. Repita os passos 1 a 4 para máquinas virtuais adicionais.

> [!TIP]
> Pode selecionar várias máquinas virtuais que estão no mesmo estado de alimentação (ligadas ou desligadas) e migrar a granel.

A máquina virtual será migrada para a loja de dados NFS a partir da Data Box. Depois de migrar todas as máquinas virtuais, pode desligar (desligar) as máquinas virtuais ativas em preparação para a migração de dados para a Solução VMware Azure.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Clone uma máquina virtual ou um modelo de máquina virtual para a datastore data box

1. Clique com o direito numa máquina virtual ou num modelo de máquina virtual que pretende clonar. Selecione   >  **Clone Clone para Máquina Virtual**.

    ![Clone de máquina virtual](media/databox-migration-vm-clone.png)

2. Selecione um nome para a máquina virtual clonada ou para o modelo de máquina virtual.

3. Selecione a pasta onde pretende colocar o objeto clonado e, em seguida, selecione **Next**.

4. Selecione o cluster ou o conjunto de recursos onde pretende colocar o objeto clonado e, em seguida, selecione **Next**.

5. Selecione **databox-datastore** como o local de armazenamento e, em seguida, selecione **Seguinte**.

    ![Clone de máquina virtual - selecione datastore](media/databox-migration-vm-clone-select-datastore.png)

6. Se pretender personalizar quaisquer opções para o objeto clonado, selecione as opções de personalização e, em seguida, selecione **Next**.

7. Reveja as configurações e **selecione Acabamento**.

8. Repita os passos 1 a 7 para máquinas virtuais adicionais ou modelos de máquinas virtuais.

As máquinas virtuais serão clonadas e armazenadas na loja de dados NFS a partir da Data Box. Depois de clonadas as máquinas virtuais, certifique-se de que são desligadas em preparação para a migração de dados para a Azure VMware Solution.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Copie ficheiros ISO para a data-loja de dados da Caixa de Dados

1. A partir do seu uI web vCenter, vá para **o Storage**.  Selecione **databox-datastore** e, em seguida, selecione **Ficheiros**. Crie uma nova pasta para armazenar ficheiros ISO.

    ![Copy ISO - crie uma nova pasta](media/databox-migration-create-folder.png)

2. Forneça um nome para a pasta onde os ficheiros ISO serão armazenados.

3. Clique duas vezes na pasta recém-criada para abri-la.

4. Selecione **Upload Files** e, em seguida, selecione os ficheiros ISO que pretende carregar.
    
    ![Copy ISO - carregar ficheiros](media/databox-migration-upload-iso.png)

> [!TIP]
> Se já tiver ficheiros ISO na sua loja de dados no local, pode selecionar os ficheiros e **copiar** os ficheiros para a data nfs da Data Box.


## <a name="prepare-data-box-for-return"></a>Preparar caixa de dados para devolução

Depois de todos os dados de máquinas virtuais, dados do modelo de máquina virtual e quaisquer ficheiros ISO são copiados para a datastore da Caixa de Dados NFS, pode desligar a datastore do seu vCenter. Todas as máquinas virtuais e modelos de máquinas virtuais devem ser removidos do inventário antes de desligar a datastore.

### <a name="remove-objects-from-inventory"></a>Remover objetos do inventário

1. A partir do seu uI web vCenter, vá para **o Storage**. Selecione **databox-datastore** e, em seguida, selecione **VMs**.

    ![Remover máquinas virtuais do inventário - desligado](media/databox-migration-select-databox-vm.png)

2. Certifique-se de que todas as máquinas virtuais estão desligadas.

3. Selecione todas as máquinas virtuais, clique com o botão direito e, em seguida, **selecione Remover do inventário**.

    ![Remover máquinas virtuais do inventário](media/databox-migration-remove-vm-from-inventory.png)

4. Selecione **modelos VM em pastas** e, em seguida, repita o passo 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Remover a data box NFS datastore do vCenter

A data-loja NFS da Caixa de Dados deve ser desligada dos anfitriões VMware ESXi antes de se preparar para a devolução.

1. A partir do seu uI web vCenter, vá para **o Storage**.

2. Clique à direita na **Databox-Datastore** e selecione **Unmount Datastore**.

    ![Desmontar datastore da Caixa de Dados](media/databox-migration-unmount-datastore.png)

3. Selecione todos os anfitriões ESXi onde a loja de dados está montada e selecione **OK**.

    ![Desmonte a datastore da Caixa de Dados - selecione anfitriões](media/databox-migration-unmount-datastore-select-hosts.png)

4. Reveja e aceite quaisquer avisos e selecione **OK**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Prepare a Caixa de Dados para a devolução e, em seguida, devolva-a

Siga os passos descritos no artigo [Caixa de Dados de Retorno Azure e verifique o upload de dados para a Azure](../databox/data-box-deploy-picked-up.md) para devolver a Caixa de Dados. Verifique o estado da cópia de dados na sua conta de armazenamento Azure. Depois de o estado aparecer como concluído, pode verificar os dados na sua conta de armazenamento Azure.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Copiar dados do armazenamento Azure para Azure VMware Solution

Os dados copiados para o seu dispositivo Data Box estarão disponíveis na sua conta de armazenamento Azure após o estado da encomenda da sua Caixa de Dados aparecer conforme concluído. Os dados podem agora ser copiados para a sua Solução Azure VMware. Os dados na conta de armazenamento devem ser copiados para a loja de dados vSAN da sua nuvem privada utilizando o protocolo NFS. 

Em primeiro lugar, copie os dados de armazenamento blob para um disco gerido numa máquina virtual Linux em Azure utilizando **a AzCopy**. Disponibilize o disco gerido através do NFS, monte a partilha NFS como uma loja de dados na sua nuvem privada e, em seguida, copie os dados. Este método permite uma cópia mais rápida dos dados para a sua nuvem privada.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Copie os dados para a sua nuvem privada usando uma máquina virtual Linux e discos geridos e, em seguida, exporte como NFS share

1. Crie uma [máquina virtual Linux](../virtual-machines/linux/quick-create-portal.md) em Azure, na mesma região onde a sua conta de armazenamento é criada e tem uma ligação de rede virtual Azure à sua nuvem privada.

2. Crie um disco gerido cuja capacidade de armazenamento seja superior à quantidade de dados blob e [anexe-o à sua máquina virtual Linux](../virtual-machines/linux/attach-disk-portal.md).  Se a quantidade de dados blob for maior do que a capacidade do maior disco gerido disponível, os dados devem ser copiados em várias etapas ou utilizando vários discos geridos.

3. Ligue-se à máquina virtual Linux e monte o disco gerido.

4. Instale [o AzCopy na sua máquina virtual Linux](../storage/common/storage-use-azcopy-v10.md).

5. Descarregue os dados do seu armazenamento Azure Blob para o disco gerido utilizando a AzCopy.  Sintaxe de comando: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` .  `<storage-account-name>`Substitua-o pelo nome da sua conta de armazenamento Azure e `<container-name>` pelo recipiente que detém os dados copiados através da Caixa de Dados.

6. Instale o servidor NFS na sua máquina virtual Linux:

    - Numa distribuição Ubuntu/Debian: `sudo apt install nfs-kernel-server` .
    - Numa distribuição da Enterprise Linux: `sudo yum install nfs-utils` .

7. Altere a permissão da pasta no seu disco gerido onde os dados do armazenamento Azure Blob foram copiados.  Altere as permissões para todas as pastas que pretende exportar como uma quota NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Atribua permissões para endereços IP do cliente para aceder à partilha NFS editando o `/etc/exports` ficheiro.

    ```bash
    sudo vi /etc/exports
    ```
    
    Introduza as seguintes linhas no ficheiro para cada IP anfitrião ESXi da sua nuvem privada.  Se estiver a criar partilhas para várias pastas, adicione todas as pastas.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exporte as ações da NFS utilizando o `sudo exportfs -a` comando.

10. Reinicie o servidor de kernel NFS utilizando o `sudo systemctl restart nfs-kernel-server` comando.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Monte a máquina virtual Linux NFS compartilhar como uma loja de dados em um cluster vCenter nuvem privada e, em seguida, copiar dados

A partilha NFS da sua máquina virtual Linux deve ser montada como uma loja de dados no seu cluster vCenter de nuvem privada. Depois de montado, os dados podem ser copiados da loja de dados NFS para a loja de dados vSAN da nuvem privada.

1. Inicie sessão no servidor vCenter de nuvem privada.

2. Clique no botão direito **Datacenter**, selecione **Armazenamento,** selecione **New Datastore** e, em seguida, selecione **Next**.

   ![Adicionar nova loja de dados](media/databox-migration-add-datastore.png)

3. No passo 1 do assistente Add Datastore, selecione o tipo **NFS.**

   ![Adicionar nova loja de dados - tipo](media/databox-migration-add-datastore-type.png)

4. No passo 2 do assistente, selecione **NFS 3** como a versão NFS e, em seguida, selecione **Seguinte**.

   ![Adicionar nova loja de dados - versão NFS](media/databox-migration-add-datastore-nfs-version.png)

5. No passo 3 do assistente, especifique o nome da datastore, do caminho e do servidor.  Pode utilizar o endereço IP da sua máquina virtual Linux para o servidor.  O caminho da pasta estará no `/<folder>/<subfolder>/` formato.

   ![Adicionar nova loja de dados - configuração NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. No passo 4 do assistente, selecione os anfitriões ESXi onde pretende que a datastore seja montada e, em seguida, selecione **Next**.  Num cluster, selecione todos os anfitriões para garantir a migração das máquinas virtuais.

   ![Adicionar nova loja de dados - Selecione anfitriões](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. No passo 5 do assistente, reveja o resumo e, em seguida, **selecione Termine**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Adicione máquinas virtuais e modelos de máquinas virtuais de uma loja de dados NFS ao inventário

1. A partir da sua nuvem privada vCenter web UI, vá para **o Storage**.  Selecione uma loja de dados NFS de máquina virtual Linux e, em seguida, selecione **Ficheiros**.

    ![Selecione ficheiros da loja de dados NFS](media/databox-migration-datastore-select-files.png)

2. Selecione uma pasta que contenha uma máquina virtual ou um modelo de máquina virtual.  No painel de detalhes, selecione um ficheiro .vmx para uma máquina virtual ou um ficheiro .vmtx para um modelo de máquina virtual.

3. **Selecione Registar VM** para registar a máquina virtual no seu vCenter de nuvem privada.

    ![Registar máquina virtual](media/databox-migration-datastore-register-vm.png)

4. Selecione o datacenter, pasta e cluster/grupo de recursos onde deseja que a máquina virtual seja registada.

4. Repita os passos 3 e 4 para todas as máquinas virtuais e modelos de máquinas virtuais.

5. Aceda à pasta que contém os ficheiros ISO.  Selecione os ficheiros ISO e, em seguida, selecione **Copy para** copiar os ficheiros para uma pasta na sua loja de dados vSAN.

As máquinas virtuais e os modelos de máquinas virtuais estão agora disponíveis no seu vCenter de nuvem privada. Estas máquinas virtuais devem ser transferidas da loja de dados NFS para a loja de dados vSAN antes de as ligar. Pode utilizar a opção **de armazenamento vMotion** e selecionar a loja de dados vSAN como alvo para as máquinas virtuais.

Os modelos de máquina virtual devem ser clonados da sua loja de dados NFS da sua máquina virtual Linux para a sua loja de dados vSAN.

### <a name="clean-up-your-linux-virtual-machine"></a>Limpe a sua máquina virtual Linux

Depois de todos os dados são copiados para a sua nuvem privada, pode remover a loja de dados NFS da sua nuvem privada:

1. Certifique-se de que todas as máquinas e modelos virtuais são movidos e clonados para a sua loja de dados vSAN.

2. Remova do inventário todos os modelos de máquinas virtuais da loja de dados NFS.

3. Desmonte a loja de dados da máquina virtual Linux do seu vCenter de nuvem privada.

4. Elimine a máquina virtual e o disco gerido do Azure.

5. Se não quiser guardar os dados que foram transferidos pela Data Box na sua conta de armazenamento, elimine a conta de armazenamento Azure.  
    


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a Caixa de Dados.](../databox/data-box-overview.md)
* Saiba mais sobre diferentes opções para [migrar cargas de trabalho para a sua nuvem privada.](migrate-workloads.md)
