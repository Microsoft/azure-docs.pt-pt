---
title: Tutorial para copiar dados de VHDs para os discos com pesada de caixa de dados do Azure geridos | Documentos da Microsoft
description: Saiba como copiar dados de VHDs de cargas de trabalho no local para o seu pesadas de caixa de dados do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: Heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 8065d29c0cb984244178d49fe8c8c5aa853ee682
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595764"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Tutorial: Utilize dados de caixa pesados para importar dados como managed disks no Azure

Este tutorial descreve como utilizar o pesadas de caixa de dados do Azure para migrar VHDs no local para os managed disks no Azure. Os VHDs das VMs no local são copiados para dados de caixa pesadas, como blobs de páginas e são carregados para o Azure como discos geridos. Estes geridos os discos podem então ser anexados a VMs do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Rever pré-requisitos
> * Ligar a dados caixa pesado
> * Copiar dados para dados de caixa pesadas


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Configurar a caixa de dados do Azure pesada](data-box-heavy-deploy-set-up.md).
2. Tiver recebido seu intenso de caixa de dados e é o estado da encomenda no portal **entregues**.
3. Está ligado a uma rede de alta velocidade. Para mais rápidas velocidades de cópia, duas ligações de 40 GbE (um por nó) podem ser utilizadas em paralelo. Se não tiver ligação 40 GbE disponível, recomendamos que tenha, pelo menos, duas ligações de 10 GbE (um por nó). 
4. Reviu o:

    - Suportado [geridos tamanhos de disco em limites de tamanho de objeto do Azure](data-box-heavy-limits.md#azure-object-size-limits).
    - [Introdução ao Azure managed disks](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Ligar a dados caixa pesado

Com base nos grupos de recursos especificados, dados de caixa pesada cria uma partilha para cada grupo de recursos associados por nó. Por exemplo, se `mydbmdrg1` e `mydbmdrg2` foram criados quando colocar a ordem, são criadas as partilhas seguintes:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Dentro de cada partilha, as pastas de três seguintes são criadas que correspondem aos contentores na sua conta de armazenamento.

- SSD Premium
- HDD Standard
- SSD Standard

A tabela seguinte mostra os caminhos UNC para as partilhas no seu intenso de caixa de dados.
 
|        Protocolo da ligação           |             Caminho UNC para a partilha                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Os passos para estabelecer ligação com base em se utilizar o SMB ou NFS para ligar a partilhas de dados de caixa pesada, são diferentes.

> [!NOTE]
> - A ligação através de REST não é suportada para esta funcionalidade.
> - Repita as instruções de connect para ligar para o segundo nó de dados de caixa pesada.

### <a name="connect-to-data-box-heavy-via-smb"></a>Ligar ao intenso de caixa de dados através de SMB

Se utilizar um computador de anfitrião do Windows Server, siga estes passos para ligar para os dados de caixa pesada.

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


### <a name="connect-to-data-box-heavy-via-nfs"></a>Ligar a dados caixa pesado por meio de NFS

Se estiver a utilizar um computador de anfitrião do Linux, execute os seguintes passos para configurar o seu dispositivo para permitir o acesso para os clientes NFS.

1. Forneça os endereços IP dos clientes permitidos que podem aceder à partilha. Na IU Web local, aceda à página **Ligar e copiar**. Em **Definições de NFS**, clique em **Acesso de cliente NFS**.

    ![Configurar o acesso de cliente NFS 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Forneça o endereço IP do cliente NFS e clique em **Adicionar**. Pode configurar o acesso para vários clientes NFS ao repetir este passo. Clique em **OK**.

    ![Configurar o acesso de cliente NFS 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Certifique-se de que o computador anfitrião do Linux tem uma [versão suportada](data-box-system-requirements.md) do cliente NFS instalada. Utilize a versão específica para a distribuição Linux.

3. Depois de instalar o cliente NFS, utilize o seguinte comando para montar a partilha NFS no seu dispositivo:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    O exemplo seguinte mostra como ligar através de NFS para uma partilha de caixa de dados ou dados de caixa pesada. O dispositivo de caixa de dados ou dados de caixa pesada IP está `169.254.250.200`, a partilha `mydbmdrg1_MDisk` é montado no ubuntuVM, montar o ponto a ser `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Copiar dados para dados de caixa pesadas

Quando estiver ligado ao servidor de dados, a próxima etapa é copiar dados. O ficheiro VHD é copiado para a conta de armazenamento de teste como BLOBs de páginas. O blob de página, em seguida, é convertido num disco gerido e movido para um grupo de recursos.

Reveja as seguintes considerações antes de iniciar a cópia de dados:

- Copie sempre os VHDs a uma das pastas pré-criada. Se copiar os VHDs fora essas pastas ou numa pasta que criou, os VHDs serão carregados para a conta de armazenamento do Azure como blobs de páginas e discos não geridos.
- Apenas os VHDs fixos podem ser carregados para criar discos geridos. Não são suportados ficheiros VHDX ou VHDs dinâmicos e de diferenciação.
- Só pode ter um disco gerido com um determinado nome num grupo de recursos em todas as pastas pré-criada. Isso implica que os VHDs carregados para as pastas pré-criada devem ter nomes exclusivos. Certifique-se de que o nome fornecido não corresponde a um disco gerido já existente num grupo de recursos.
- Reveja os limites de disco gerido na [limites de tamanho de objeto do Azure](data-box-heavy-limits.md#azure-object-size-limits).

Dependendo se está a ligar através de SMB ou NFS, pode usar:

- [Copiar dados através de SMB](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Copiar dados por meio de NFS](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

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

Neste tutorial, aprendeu sobre tópicos pesadas de caixa de dados do Azure, tais como:

> [!div class="checklist"]
> * Rever pré-requisitos
> * Ligar a dados caixa pesado
> * Copiar dados para dados de caixa pesadas


Avance para o próximo tutorial para saber como enviar seu intenso de caixa de dados à Microsoft.

> [!div class="nextstepaction"]
> [Envie sua pesado de caixa de dados do Azure para a Microsoft](./data-box-heavy-deploy-picked-up.md)

