---
title: Importar dados a partir de VHDs e copiar para discos geridos com o Microsoft Azure Data Box | Documentos da Microsoft
description: Saiba como copiar dados a partir de VHD de cargas de trabalho no local para o Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alkohli
ms.openlocfilehash: 1358751c71dfba39574807c9b232957227980f5d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887515"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>Tutorial: Utilize o Data Box para importar dados como managed disks no Azure

Este tutorial descreve como utilizar o Azure Data Box para migrar VHDs no local para os managed disks no Azure. Os VHDs das VMs no local são copiados para a caixa de dados como blobs de páginas e são carregados para o Azure como discos geridos. Estes geridos os discos podem então ser anexados a VMs do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Rever pré-requisitos
> * Ligar ao Data Box
> * Copiar dados para o Data Box


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Configurar o Azure Data Box](data-box-deploy-set-up.md).
2. Que recebeu o Data Box e é o estado da encomenda no portal **entregues**.
3. Está ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se uma ligação de 10 GbE não estiver disponível, utilize uma ligação de dados de 1 GbE, mas as velocidades de cópia são afetadas.
4. Reviu o:

    - Suportado [geridos tamanhos de disco em limites de tamanho de objeto do Azure](data-box-limits.md#azure-object-size-limits).
    - [Introdução ao Azure managed disks](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box"></a>Ligar ao Data Box

Com base nos grupos de recursos especificados, o Data Box cria uma partilha para cada grupo de recursos associados. Por exemplo, se `mydbmdrg1` e `mydbmdrg2` foram criados quando colocar a ordem, são criadas as partilhas seguintes:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Dentro de cada partilha, as seguintes quatro pastas são criadas que correspondem aos contentores na sua conta de armazenamento.

- SSD Premium
- HDD Standard
- SSD Standard

A tabela seguinte mostra os caminhos UNC para as partilhas em sua caixa de dados.
 
|        Protocolo da ligação           |             Caminho UNC para a partilha                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Os passos para estabelecer ligação com base em se utilizar o SMB ou NFS para se conectar a compartilhamentos de Data Box, são diferentes.

> [!NOTE]
> A ligação através de REST não é suportada para esta funcionalidade.

### <a name="connect-to-data-box-via-smb"></a>Ligar ao Data Box através de SMB

Se utilizar um computador de anfitrião do Windows Server, siga estes passos para ligar para o Data Box.

1. O primeiro passo é autenticar e iniciar uma sessão. Aceda a **Ligar e copiar**. Clique em **obter credenciais** para obter as credenciais de acesso para as partilhas associadas do seu grupo de recursos. Também pode obter as credenciais de acesso a partir da **detalhes do dispositivo** no portal do Azure.

    > [!NOTE]
    > As credenciais para todas as partilhas para discos geridos são idênticas.

    ![Obter credenciais de partilhas 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Partilha de acesso e a caixa de diálogo de dados de cópia, a cópia a **nome de utilizador** e o **palavra-passe** para a partilha. Clique em **OK**.
    
    ![Obter credenciais de partilhas 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Para aceder as partilhas associadas ao seu recurso (*mydbmdrg1* no exemplo a seguir) a partir do seu computador anfitrião, abra uma janela de comando. Na linha de comandos, escreva:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Os caminhos UNC de partilha neste exemplo são os seguintes:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Introduza a palavra-passe da partilha quando lhe for pedido. O exemplo seguinte mostra a ligação a uma partilha através do comando anterior.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Prima Windows + R. Na janela **Executar**, especifique o `\\<device IP address>\<ShareName>`. Clique em **OK** para abrir o Explorador de ficheiros.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Deve ver o seguinte pré-criada pastas dentro de cada partilha.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>Ligar ao Data Box por meio de NFS

Se estiver a utilizar um computador anfitrião do Linux, execute os passos seguintes para configurar o Data Box para permitir o acesso aos clientes NFS.

1. Forneça os endereços IP dos clientes permitidos que podem aceder à partilha. Na IU Web local, aceda à página **Ligar e copiar**. Em **Definições de NFS**, clique em **Acesso de cliente NFS**.

    ![Configurar o acesso de cliente NFS 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Forneça o endereço IP do cliente NFS e clique em **Adicionar**. Pode configurar o acesso para vários clientes NFS ao repetir este passo. Clique em **OK**.

    ![Configurar o acesso de cliente NFS 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Certifique-se de que o computador anfitrião do Linux tem uma [versão suportada](data-box-system-requirements.md) do cliente NFS instalada. Utilize a versão específica para a distribuição Linux.

3. Depois de o cliente NFS estar instalado, utilize o comando seguinte para montar a partilha NFS no seu dispositivo Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    O exemplo seguinte mostra como ligar através de NFS a uma partilha do Data Box. O IP do dispositivo Data Box é `169.254.250.200`, a partilha `mydbmdrg1_MDisk` está montada no ubuntuVM, sendo que o ponto de montagem é `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Copiar dados para o Data Box

Quando estiver ligado ao servidor de dados, a próxima etapa é copiar dados. O ficheiro VHD é copiado para a conta de armazenamento de teste como BLOBs de páginas. O blob de página, em seguida, é convertido num disco gerido e movido para um grupo de recursos.

Reveja as seguintes considerações antes de iniciar a cópia de dados:

- Copie sempre os VHDs a uma das pastas pré-criada. Se copiar os VHDs fora essas pastas ou numa pasta que criou, os VHDs serão carregados para a conta de armazenamento do Azure como blobs de páginas e discos não geridos.
- Apenas os VHDs fixos podem ser carregados para criar discos geridos. Não são suportados ficheiros VHDX ou VHDs dinâmicos e de diferenciação.
- Só pode ter um disco gerido com um determinado nome num grupo de recursos em todas as pastas pré-criada. Isso implica que os VHDs carregados para as pastas pré-criada devem ter nomes exclusivos. Certifique-se de que o nome fornecido não corresponde a um disco gerido já existente num grupo de recursos.
- Reveja os limites de disco gerido na [limites de tamanho de objeto do Azure](data-box-limits.md#azure-object-size-limits).

Dependendo se está a ligar através de SMB ou NFS, pode usar:

- [Copiar dados através de SMB](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [Copiar dados por meio de NFS](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

Aguarde que as tarefas de cópia concluir. Certifique-se de que as tarefas de cópia tem concluído sem erros antes de ir para o passo seguinte.

![Não existem erros em * * página ligar e copiar * *](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Se houver erros durante o processo de cópia, transferir os registos a partir da **Connect e a cópia** página.

- Se copiar um ficheiro que é não 512 bytes alinhadas, o ficheiro não está carregado como BLOBs de páginas à sua conta de armazenamento de teste. Verá um erro nos registos. Remova o ficheiro e copie um ficheiro que é de 512 bytes alinhados.

- Se um VHDX (esses arquivos não são suportados) que copiou com um nome longo, verá um erro nos registos.

    ![Erro nos registos de * * página ligar e copiar * *](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Resolva os erros antes de prosseguir para o passo seguinte.

Para garantir a integridade dos dados, a soma de verificação é calculada inline à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço utilizado e o espaço livre no seu dispositivo.
    
![Verificar o espaço livre e utilizado no dashboard](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Quando a tarefa de cópia for concluída, pode aceder à **preparação para envio**.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Rever pré-requisitos
> * Ligar ao Data Box
> * Copiar dados para o Data Box


Avance para o próximo tutorial para saber como enviar o Data Box para a Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)

