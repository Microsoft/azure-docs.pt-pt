---
title: Solução do Azure VMware por CloudSimple-migração usando Azure Data Box
description: Migração de dados em massa para a solução do Azure VMware por CloudSimple usando Azure Data Box
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5f19b98fbbbad2f43227bfa2f73eab47bf7b1699
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817465"
---
# <a name="migrating-data-to-azure-vmware-solution-by-cloudsimple-using-azure-data-box"></a>Migrando dados para a solução VMware do Azure por CloudSimple usando Azure Data Box

A solução cloud Microsoft Azure Data Box permite enviar terabytes de dados para o Azure de forma rápida, económica e fiável. A transferência segura de dados é acelerada pelo envio de um dispositivo de armazenamento Data Box proprietário. Cada dispositivo de armazenamento tem uma capacidade máxima de armazenamento utilizável de 80 TB e é transportado para o seu datacenter através de uma operadora regional. O dispositivo é fornecido numa caixa robusta para proteger os dados durante o transporte.

Usando Azure Data Box, você pode migrar a massa de seus dados do VMware para sua nuvem privada.  Os dados do ambiente vSphere local são copiados para o Data Box usando o protocolo NFS.  A migração de dados em massa envolve a cópia de uma cópia pontual de máquinas virtuais, configuração e dados associados para o Data Box e enviados para o Azure.

Neste artigo, você aprenderá sobre:

* Configurando Azure Data Box.
* Copiar dados do ambiente VMware local para o Data Box usando o NFS.
* Preparando para retornar de Azure Data Box.
* Preparar dados de BLOB para cópia na solução VMware do Azure por CloudSimple.
* Copiar os dados do Azure para sua nuvem privada.

## <a name="scenarios"></a>Cenários

Azure Data Box deve ser usado nos cenários a seguir para a migração de dados em massa.

* Migre uma grande quantidade de dados do local para a solução do Azure VMware por CloudSimple para serem usados como uma linha de base e diferenças de sincronização pela rede.
* Migre uma grande quantidade de máquinas virtuais desligadas (máquinas virtuais frias).
* Migre dados de máquina virtual para configurar ambientes de desenvolvimento e teste.
* Migre um grande número de modelos de máquina virtual, ISOs, discos de máquina virtual.

## <a name="before-you-begin"></a>Antes de começar

* Verifique os pré-requisitos e [solicite data Box](../databox/data-box-deploy-ordered.md) de seu portal do Azure.  Durante o processo de pedido, você deve selecionar uma conta de armazenamento, que permite o armazenamento de BLOBs.  Depois de receber o Data Box, conecte-o à sua rede local e [Configure o data Box](../databox/data-box-deploy-set-up.md) com um endereço IP acessível da sua rede de gerenciamento de vSphere.

* Crie uma rede virtual e uma conta de armazenamento na mesma região do Azure em que sua solução do Azure VMware por CloudSimple é provisionada.

* Crie uma [conexão de rede virtual do Azure](cloudsimple-azure-network-connection.md) de sua nuvem privada para a rede virtual na qual a conta de armazenamento é criada usando as etapas mencionadas no artigo [conectar a rede virtual do Azure ao CloudSimple usando o ExpressRoute](virtual-network-connection.md)

## <a name="set-up-azure-data-box-for-nfs"></a>Configurar Azure Data Box para NFS

Conecte-se ao seu Azure Data Box interface do usuário da Web local usando as etapas mencionadas na seção **conectar-se ao seu dispositivo** no artigo [Tutorial: Cabo e conecte-se ao seu Azure Data Box @ no__t-0.  Configure Data Box para permitir o acesso a clientes NFS.

1. Na IU Web local, aceda à página **Ligar e copiar**. Em **Definições de NFS**, clique em **Acesso de cliente NFS**. 

    ![Configurar o acesso de cliente NFS 1](media/nfs-client-access.png)

2. Insira o endereço IP dos hosts VMware ESXi e clique em **Adicionar**. Você pode configurar o acesso para todos os hosts no cluster vSphere repetindo esta etapa. Clique em **OK**.

    ![Configurar o acesso de cliente NFS 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada nas partilhas dos blobs de blocos e dos blobs de páginas representa um contentor para o qual os dados são carregados como blobs. Não pode copiar ficheiros diretamente para a pasta *raiz* na conta de armazenamento.

Nas partilhas de blob de blocos e de blob de páginas, as entidades de primeiro nível são os contentores e as de segundo nível são os blobs. Nas partilhas de ficheiros do Azure, as entidades de primeiro nível são as partilhas e as de segundo nível são os ficheiros.

A tabela a seguir mostra o caminho UNC para as partilhas no Data Box e o URL do caminho do Armazenamento do Microsoft Azure onde os dados são carregados. O URL do caminho de Armazenamento do Microsoft Azure final pode derivar do caminho da partilha UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de bloco do Azure | <li>Caminho UNC para as partilhas: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL do Armazenamento do Microsoft Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para as partilhas: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL do Armazenamento do Microsoft Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Ficheiros do Azure       |<li>Caminho UNC para as partilhas: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL de Armazenamento do Microsoft Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Use blobs de blocos do Azure para copiar dados do VMware.

## <a name="mount-nfs-share-as-a-datastore-on-on-premises-vcenter-cluster-and-copy-data"></a>Montar o compartilhamento NFS como um armazenamento de dados no cluster do vCenter local

O compartilhamento de NFS do seu Azure Data Box deve ser montado como um armazenamento de dados em seu cluster do vCenter local ou VMware ESXi host para copiar os mesmos.  Depois de montados, os dados podem ser copiados para o armazenamento do repositório NFS.

1. Faça logon em seu servidor do vCenter local.

2. Clique com o botão direito do mouse no **datacenter**, selecione **armazenamento** adicionar e clique em **novo repositório** de armazenamento e clique em **Avançar**

   ![Adicionar novo repositório de armazenamento](media/databox-migration-add-datastore.png)

3. Na etapa 1 do assistente para adicionar repositório de armazenamento, selecione o tipo **NFS**.

   ![Adicionar novo repositório de armazenamento-tipo](media/databox-migration-add-datastore-type.png)

4. Na etapa 2, selecione **NFS 3** como versão de NFS e clique em **Avançar**.

   ![Adicionar novo repositório de armazenamento-versão do NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Na etapa 3, especifique o nome do repositório de armazenamento, o caminho e o servidor.  Você pode usar o endereço IP do seu Data Box para o servidor.  O caminho da pasta estará no formato `/<StorageAccountName_BlockBlob>/<ContainerName>/`.

   ![Adicionar novo repositório de armazenamento-configuração de NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. Na etapa 4, selecione os hosts ESXi nos quais você deseja que o repositório de armazenamento seja montado e clique em **Avançar**.  Em um cluster, selecione todos os hosts para garantir a migração de máquinas virtuais.

   ![Adicionar novo repositório de armazenamento-selecionar hosts](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Na etapa 5, examine o resumo e clique em **concluir**

## <a name="copy-data-to-data-box-nfs-datastore"></a>Copiar dados para o armazenamento de Data Box NFS

As máquinas virtuais podem ser migradas ou clonadas para o novo repositório de armazenamento.  Todas as máquinas virtuais não utilizadas, que precisam ser migradas, podem ser migradas para Data Box armazenamento NFS usando o **Storage VMotion**.  As máquinas virtuais ativas podem ser **clonadas** para o repositório de armazenamento do data Box NFS.

* Identifique a lista de máquinas virtuais que podem ser **movidas**.
* Identifique a lista de máquinas virtuais que devem ser **clonadas**.

### <a name="move-virtual-machine-to-data-box-datastore"></a>Mover máquina virtual para Data Box repositório de armazenamento

1. Clique com o botão direito do mouse na máquina virtual, que você deseja mover para Data Box repositório de armazenamento e selecione **migrar**.

    ![Migrar máquina virtual](media/databox-migration-vm-migrate.png)

2. Selecione **alterar armazenamento somente** para o tipo de migração e clique em **Avançar**.

    ![Migrar máquina virtual-somente armazenamento](media/databox-migration-vm-migrate-change-storage.png)

3. Selecione Data Box repositório de armazenamento como o destino e clique em **Avançar**.

    ![Migrar máquina virtual-selecionar repositório de armazenamento](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Examine as informações e clique em **concluir**.

5. Repita as etapas de 1 a 4 para outras máquinas virtuais.

> [!TIP]
> Você pode selecionar várias máquinas virtuais no mesmo estado de energia (ligado ou desligado) e migrá-las em massa.

O armazenamento da máquina virtual será migrado para o repositório de armazenamento NFS de Azure Data Box.  Depois que todas as máquinas virtuais forem migradas, você poderá desligar as máquinas virtuais ativadas em preparação para a migração de dados para a solução VMware do Azure.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-data-box-datastore"></a>Clonar uma máquina virtual ou um modelo de máquina virtual para Data Box repositório de armazenamento

1. Clique com o botão direito do mouse em uma máquina virtual ou modelo de máquina virtual que você deseja clonar.  Selecione **clonar**, **clonar para máquina virtual**

    ![Clone da máquina virtual](media/databox-migration-vm-clone.png)

2. Selecione um nome para a máquina virtual clonada ou o modelo de máquina virtual.

3. Selecione a pasta onde você deseja posicionar o objeto clonado e clique em **Avançar**.

4. Selecione o cluster ou o pool de recursos em que você deseja o objeto clonado e clique em **Avançar**.

5. Selecione o repositório de armazenamento do Azure Data Box NFS como o local de repositório e clique em **Avançar**.

    ![Clonagem de máquina virtual – selecionar repositório de armazenamento](media/databox-migration-vm-clone-select-datastore.png)

6. Selecione as opções de personalização se desejar personalizar as opções para o objeto clonado e clique em **Avançar**.

7. Examine as configurações e clique em **concluir**.

8. Repita as etapas de 1 a 7 para máquinas virtuais ou modelos de máquina virtual adicionais.

As máquinas virtuais serão clonadas e armazenadas no repositório de armazenamento NFS de Azure Data Box.  Depois que as máquinas virtuais forem clonadas, verifique se as máquinas virtuais clonadas estão desligadas na preparação para a migração de dados para a solução VMware do Azure.

### <a name="copy-iso-files-to-data-box-datastore"></a>Copiar arquivos ISO para Data Box repositório de armazenamento

1. Na interface do usuário da Web do vCenter local, navegue até **armazenamento**.  Selecione Data Box repositório de armazenamento NFS e clique em **arquivos**.  Crie uma **nova pasta** para armazenar arquivos ISO.

    ![Copiar ISO-criar nova pasta](media/databox-migration-create-folder.png)

2. Dê um nome para a pasta em que os arquivos ISO serão armazenados.

3. Clique duas vezes na pasta recém-criada para acessar o conteúdo.

4. Clique em **carregar arquivos** e selecione o ISOs que você deseja carregar.
    
    ![Copiar arquivos de upload ISO](media/databox-migration-upload-iso.png)

> [!TIP]
> Se você já tiver arquivos ISO em seu repositório de armazenamento local, poderá selecionar os arquivos e **copiar para** o repositório de armazenamento do data Box NFS.


## <a name="prepare-azure-data-box-for-return"></a>Preparar Azure Data Box para retorno

Depois que todos os dados da máquina virtual, os dados do modelo de máquina virtual e os arquivos ISO forem copiados para o Data Box armazenamento do NFS, o armazenamento de dados poderá ser desconectado do vCenter.  Todas as máquinas virtuais e modelos de máquina virtual devem ser removidos do inventário antes que o repositório de armazenamento possa ser desconectado.

### <a name="remove-objects-from-inventory"></a>Remover objetos do inventário

1. Na interface do usuário da Web do vCenter local, navegue até **armazenamento**.  Selecione Data Box repositório de armazenamento NFS e clique em **VMs**.

2. Verifique se todas as máquinas virtuais estão desligadas.

    ![Remover máquinas virtuais do estoque-desligado](media/databox-migration-select-databox-vm.png)

3. Selecione todas as máquinas virtuais, clique com o botão direito do mouse e selecione **remover do inventário**.

    ![Remover máquinas virtuais do inventário](media/databox-migration-remove-vm-from-inventory.png)

4. Selecione **modelos de VM em pastas** nos botões superior e repita a etapa 3. 

### <a name="remove-azure-data-box-nfs-datastore-from-vcenter"></a>Remover Azure Data Box repositório de armazenamento NFS do vCenter

Data Box repositório de armazenamento NFS deve ser desconectado de VMware ESXi hosts antes de se preparar para o retorno.

1. Na interface do usuário da Web do vCenter local, navegue até **armazenamento**.

2. Clique com o botão direito do mouse no repositório de armazenamento do Data Box NFS e selecione **desmontar repositório de armazenamento**.

    ![Desmontar Data Box repositório de armazenamento](media/databox-migration-unmount-datastore.png)

3. Selecione todos os hosts ESXi nos quais o repositório de armazenamento está montado e clique em **OK**.

    ![Desmontar Data Box repositório de armazenamento-selecionar hosts](media/databox-migration-unmount-datastore-select-hosts.png)

4. Examine e aceite todos os avisos e clique em **OK**.

### <a name="prepare-data-box-for-return-and-return-the-data-box"></a>Preparar Data Box para retornar e retornar o Data Box

Siga as etapas descritas no artigo [retornar Azure data box e verificar o carregamento de dados no Azure](../databox/data-box-deploy-picked-up.md) para retornar o data box.  Verifique o status da cópia de dados para sua conta de armazenamento do Azure e, depois que o status mostrar concluído, você poderá verificar os dados em sua conta de armazenamento do Azure.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution-by-cloudsimple"></a>Copiar dados do armazenamento do Azure para a solução do Azure VMware por CloudSimple

Os dados copiados para seu Azure Data Box estarão disponíveis em sua conta de armazenamento do Azure quando o status do pedido de seu Data Box for exibido como concluído.  Os dados agora podem ser copiados para sua solução do Azure VMware por CloudSimple.  Os dados na conta de armazenamento devem ser copiados usando o protocolo NFS para o armazenamento do dados vSAN de sua nuvem privada.  Primeiro, copie os dados do repositório de BLOB para um disco gerenciado em uma máquina virtual Linux no Azure usando o **AzCopy**.  Disponibilize o disco gerenciado por meio do protocolo NFS e monte o compartilhamento NFS como um armazenamento de dados em sua nuvem privada e copie-os.  Esse método permite uma cópia mais rápida dos dados para sua nuvem privada. 

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-export-as-nfs-share"></a>Copiar dados para sua nuvem privada usando uma máquina virtual Linux e discos gerenciados e exportar como compartilhamento NFS

1. Crie uma [máquina virtual do Linux](../virtual-machines/linux/quick-create-portal.md) no Azure na mesma região em que sua conta de armazenamento é criada e tenha uma conexão de rede virtual do Azure com sua nuvem privada.

2. Crie um disco gerenciado, que seja maior que a quantidade de dados de BLOB e [anexe-o à sua máquina virtual Linux](../virtual-machines/linux/attach-disk-portal.md).  Se a quantidade de dados de blob for maior do que o maior disco gerenciado disponível, os dados deverão ser copiados em várias etapas ou usando vários discos gerenciados.

3. Conecte-se à máquina virtual Linux e monte o disco gerenciado.

4. Instale [o AzCopy em sua máquina virtual Linux](../storage/common/storage-use-azcopy-v10.md).

5. Baixe os dados do armazenamento de BLOBs do Azure no disco gerenciado usando o AzCopy.  Sintaxe do comando: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  Substitua `<storage-account-name>` pelo nome da sua conta de armazenamento do Azure e `<container-name>` pelo contêiner, que contém os dados copiados usando Azure Data Box.

6. Instale o servidor NFS em sua máquina virtual Linux.

    1. Em uma distribuição do Ubuntu/Debian: `sudo apt install nfs-kernel-server`.
    2. Em uma distribuição Enterprise Linux: `sudo yum install nfs-utils`.

7. Altere a permissão da pasta no disco gerenciado onde os dados do repositório de blob do Azure foram copiados.  Altere as permissões para todas as pastas que você deseja exportar como compartilhamento de NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Atribua permissões para que os endereços IP do cliente acessem o compartilhamento NFS editando o arquivo `/etc/exports`.

    ```bash
    sudo vi /etc/exports
    ```
    
    Insira as seguintes linhas no arquivo para cada IP de host ESXi da sua nuvem privada.  Se você estiver criando compartilhamentos para várias pastas, adicione todas as pastas.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exporte os compartilhamentos NFS usando o comando `sudo exportfs -a`.

10. Reinicie o servidor de kernel NFS usando o comando `sudo systemctl restart nfs-kernel-server`.


### <a name="mount-linux-virtual-machine-nfs-share-as-a-datastore-on-private-cloud-vcenter-cluster-and-copy-data"></a>Montar o compartilhamento NFS de máquina virtual Linux como um armazenamento de dados no cluster vCenter de nuvem privada e copiar

O compartilhamento de NFS da sua máquina virtual Linux deve ser montado como um armazenamento de dados em seu cluster do vCenter na nuvem privada para copiar o dado.  Depois de montados, os dados podem ser copiados do repositório de armazenamento NFS para o armazenamento de dados vSAN da nuvem privada.

1. Faça logon em seu vCenter Server de nuvem privada.

2. Clique com o botão direito do mouse no **datacenter**, selecione **armazenamento** adicionar e clique em **novo repositório** de armazenamento e clique em **Avançar**

   ![Adicionar novo repositório de armazenamento](media/databox-migration-add-datastore.png)

3. Na etapa 1 do assistente para adicionar repositório de armazenamento, selecione o tipo **NFS**.

   ![Adicionar novo repositório de armazenamento-tipo](media/databox-migration-add-datastore-type.png)

4. Na etapa 2, selecione **NFS 3** como versão de NFS e clique em **Avançar**.

   ![Adicionar novo repositório de armazenamento-versão do NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Na etapa 3, especifique o nome do repositório de armazenamento, o caminho e o servidor.  Você pode usar o endereço IP de sua máquina virtual Linux para o servidor.  O caminho da pasta estará no formato `/<folder>/<subfolder>/`.

   ![Adicionar novo repositório de armazenamento-configuração de NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. Na etapa 4, selecione os hosts ESXi nos quais você deseja que o repositório de armazenamento seja montado e clique em **Avançar**.  Em um cluster, selecione todos os hosts para garantir a migração de máquinas virtuais.

   ![Adicionar novo repositório de armazenamento-selecionar hosts](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Na etapa 5, examine o resumo e clique em **concluir**

### <a name="add-virtual-machines-and-virtual-machine-templates-from-nfs-datastore-to-the-inventory"></a>Adicionar máquinas virtuais e modelos de máquina virtual do repositório de armazenamento NFS ao inventário

1. Em sua interface do usuário da Web da nuvem privada do vCenter, navegue até **armazenamento**.  Selecione repositório de armazenamento NFS de máquina virtual Linux e clique em **arquivos**.

    ![Selecionar arquivos do repositório de armazenamento NFS](media/databox-migration-datastore-select-files.png)

2. Selecione uma pasta que contenha uma máquina virtual ou um modelo de máquina virtual.  No painel de detalhes, selecione `.vmx` arquivo para uma máquina virtual ou um arquivo `.vmtx` para um modelo de máquina virtual.

3. Clique em **registrar VM** para registrar a máquina virtual em sua nuvem privada vCenter.

    ![Registrar máquina virtual](media/databox-migration-datastore-register-vm.png)

4. Selecione o datacenter, a pasta e o pool de recursos/cluster em que você deseja que a máquina virtual seja registrada.

4. Repita as etapas 3 e 4 para todas as máquinas virtuais e modelos de máquina virtual.

5. Navegue até a pasta que contém os arquivos ISO.  Selecione arquivos ISO e **copie para** uma pasta no armazenamento de seus vSAN.

As máquinas virtuais e os modelos de máquina virtual agora estão disponíveis em sua nuvem privada vCenter.  Essas máquinas virtuais devem ser movidas do repositório de armazenamento NFS para o vSAN datastore antes de ligá-las.  Você pode fazer um Storage vMotion das máquinas virtuais e selecionar o armazenamento de repositório vSAN como o destino para a máquina virtual.

Os modelos de máquina virtual devem ser clonados do repositório de armazenamento NFS da máquina virtual Linux para o armazenamento de seus vSAN.

### <a name="clean-up-of-your-linux-virtual-machine"></a>Limpar sua máquina virtual Linux

Depois que todos os dados forem copiados para a sua nuvem privada, você poderá remover o armazenamento NFS de sua nuvem privada.

1. Verifique se todas as máquinas virtuais e modelos foram movidos e clonados para o armazenamento de seus vSAN.

2. Remova todos os modelos de máquina virtual do repositório de armazenamento NFS do inventário.

3. Desmonte o repositório de armazenamento de máquina virtual Linux do vCenter de nuvem privada.

4. Exclua a máquina virtual e o disco gerenciado do Azure.

5. Se você não quiser manter os dados transferidos usando Azure Data Box em sua conta de armazenamento, exclua a conta de armazenamento do Azure.  
    


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Azure data Box](../databox/data-box-overview.md)
* Saiba mais sobre as diferentes opções para [migrar cargas de trabalho para a nuvem privada](migrate-workloads.md)
