---
title: 'Tutorial: Cópia de VHDs para discos geridos'
titleSuffix: Azure Data Box Heavy
description: Saiba como copiar dados de VHDs das cargas de trabalho VM no local para a sua Caixa de Dados Azure Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 01031159d1894c7cb5f36b48f268186dff21fd22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77471334"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Tutorial: Use data box pesado para importar dados como discos geridos em Azure

Este tutorial descreve como usar a Caixa de Dados Azure Heavy para migrar vHDs no local para discos geridos em Azure. Os VHDs dos VMs no local são copiados para Data Box Heavy como bolhas de página e são enviados para a Azure como discos geridos. Estes discos geridos podem então ser ligados aos VMs Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Rever pré-requisitos
> * Ligar ao Data Box Heavy
> * Copiar dados para o Data Box Heavy


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Configurar o Azure Data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Recebeu o Data Box Heavy e o estado da encomenda no portal é **Entregue**.
3. Está ligado a uma rede de alta velocidade. Para velocidades de cópia mais rápidas, podem ser utilizadas em paralelo duas ligações de 40 GbE (uma por nó). Se não tiver uma ligação de 40 GbE disponível, recomendamos que tenha pelo menos duas ligações de 10 GbE (uma por nó). 
4. Revendo o:

    - Tamanhos [de disco geridos suportados nos limites do tamanho do objeto Azure](data-box-heavy-limits.md#azure-object-size-limits).
    - [Introdução aos discos geridos Azure](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Ligar ao Data Box Heavy

Com base nos grupos de recursos especificados, a Data Box Heavy cria uma partilha para cada grupo de recursos associado por nó. Por exemplo, se `mydbmdrg1` e `mydbmdrg2` foram criadas ao fazer a encomenda, são criadas as seguintes ações:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Dentro de cada ação, são criadas as três pastas que correspondem a contentores na sua conta de armazenamento.

- SSD Premium
- HDD Standard
- SSD Standard

A tabela seguinte mostra os caminhos da UNC para as ações da sua Caixa de Dados Pesada.
 
|        Protocolo de ligação           |             Caminho da UNC para a partilha                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Com base no facto de utilizar SMB ou NFS para ligar a data box ações pesadas, os passos para ligar são diferentes.

> [!NOTE]
> - A ligação via REST não é suportada para esta função.
> - Repita as instruções de ligação para ligar ao segundo nó de Data Box Heavy.

### <a name="connect-to-data-box-heavy-via-smb"></a>Ligue-se à Caixa de Dados Pesada via SMB

Se estiver a utilizar um computador anfitrião do Windows Server, execute estes passos para se ligar ao Data Box Heavy.

1. O primeiro passo é autenticar e iniciar uma sessão. Aceda a **Ligar e copiar**. Clique **em Obter credenciais** para obter as credenciais de acesso para as ações associadas ao seu grupo de recursos. Também pode obter as credenciais de acesso dos detalhes do **Dispositivo** no portal Azure.

    > [!NOTE]
    > As credenciais de todas as ações para discos geridos são idênticas.

    ![Obter credenciais de partilhas 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. A partir da caixa de diálogo de dados de partilha de acesso e cópia, copie o **nome de utilizador** e a **palavra-passe** para a partilha. Clique em **OK**.
    
    ![Obter credenciais de partilhas 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Para aceder às ações associadas ao seu recurso *(mydbmdrg1* no exemplo seguinte) a partir do computador anfitrião, abra uma janela de comando. Na linha de comandos, escreva:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Os seus caminhos de partilha da UNC neste exemplo são os seguintes:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Introduza a palavra-passe da partilha quando lhe for pedido. O exemplo seguinte mostra a ligação a uma partilha através do comando anterior.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Prima Windows + R. Na janela **Executar**, especifique o `\\<device IP address>\<ShareName>`. Clique em **OK** para abrir o Explorador de Ficheiros.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Deve agora ver as seguintes pastas pré-fundamentadas dentro de cada ação.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Ligue-se à Caixa de Dados Pesada via NFS

Se estiver a utilizar um computador anfitrião Linux, execute os seguintes passos para configurar o seu dispositivo para permitir o acesso aos clientes NFS.

1. Forneça os endereços IP dos clientes permitidos que podem aceder à partilha. Na IU Web local, aceda à página **Ligar e copiar**. Em **Definições de NFS**, clique em **Acesso de cliente NFS**.

    ![Configurar o acesso de cliente NFS 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Forneça o endereço IP do cliente NFS e clique em **Adicionar**. Pode configurar o acesso para vários clientes NFS ao repetir este passo. Clique em **OK**.

    ![Configurar o acesso de cliente NFS 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Certifique-se de que o computador anfitrião do Linux tem uma [versão suportada](data-box-system-requirements.md) do cliente NFS instalada. Utilize a versão específica para a distribuição Linux.

3. Uma vez instalado o cliente NFS, utilize o seguinte comando para montar a partilha NFS no seu dispositivo:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    O exemplo a seguir mostra como ligar via NFS a uma caixa de dados ou uma caixa de dados pesada. A Caixa de Dados ou caixa de dados dispositivo pesado IP é `169.254.250.200` , a parte é montada no `mydbmdrg1_MDisk` ubuntuVM, sendo o ponto de montagem `/home/databoxubuntuhost/databox` .

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Copiar dados para o Data Box Heavy

Depois de estar ligado ao servidor de dados, o próximo passo é copiar dados. O ficheiro VHD é copiado para a conta de armazenamento de encenação como bolha de página. A bolha de página é então convertida para um disco gerido e transferida para um grupo de recursos.

Reveja as seguintes considerações antes de iniciar a cópia de dados:

- Copie sempre os VHDs para uma das pastas pré-criadas. Se copiar os VHDs fora destas pastas ou numa pasta que criou, os VHDs serão enviados para a conta de Armazenamento Azure como bolhas de página e discos não geridos.
- Apenas os VHDs fixos podem ser carregados para criar discos geridos. Os ficheiros VHDX ou VHDs dinâmicos e diferentes não são suportados.
- Só é possível ter um disco gerido com um nome próprio num grupo de recursos em todas as pastas pré-criadas. o que implica que os VHDs carregados para as pastas pré-criadas devem ter nomes exclusivos. Verifique se o nome fornecido não corresponde a um disco gerido já existente num grupo de recursos.
- Reveja os limites do disco gerido nos limites do [tamanho do objeto Azure](data-box-heavy-limits.md#azure-object-size-limits).

Dependendo se está a ligar via SMB ou NFS, pode utilizar:

- [Copiar dados através de SMB](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Copiar dados através de NFS](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Espera que os trabalhos de cópia terminem. Certifique-se de que os trabalhos de cópia terminaram sem erros antes de passar ao passo seguinte.

![Sem erros na página **Connect e copy**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Se houver erros durante o processo de cópia, descarregue os registos da página **'Ligar' e copiar.**

- Se copiou um ficheiro que não está alinhado com 512 bytes, o ficheiro não é carregado como bolha de página na sua conta de armazenamento de encenação. Verá um erro nos registos. Retire o ficheiro e copie um ficheiro que esteja alinhado 512 bytes.

- Se copiar um VHDX (estes ficheiros não são suportados) com um nome comprido, verá um erro nos registos.

    ![Erro nos registos a partir da página **Connect e copy**](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Resolva os erros antes de seguir para o próximo passo.

Para garantir a integridade dos dados, a soma de verificação é calculada inline à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço utilizado e o espaço livre no seu dispositivo.
    
![Verificar o espaço livre e utilizado no dashboard](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Uma vez terminado o trabalho de cópia, pode ir para **preparar-se para enviar.**


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Heavy, como:

> [!div class="checklist"]
> * Rever pré-requisitos
> * Ligar ao Data Box Heavy
> * Copiar dados para o Data Box Heavy


Avance para o tutorial seguinte para saber como enviar o Data Box Heavy de volta para a Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box Heavy para a Microsoft](./data-box-heavy-deploy-picked-up.md)

