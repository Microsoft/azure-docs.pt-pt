---
title: Tutorial para copiar dados de VHDs para Managed disks com Azure Data Box | Microsoft Docs
description: Saiba como copiar dados de VHDs de cargas de trabalho de VM locais para seu Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.openlocfilehash: 4b7182d1fa70a146da1c01273ffe1032f2982546
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240469"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>Tutorial: Usar Data Box para importar dados como discos gerenciados no Azure

Este tutorial descreve como usar o Azure Data Box para migrar VHDs locais para Managed disks no Azure. Os VHDs de VMs locais são copiados para Data Box como BLOBs de páginas e são carregados no Azure como discos gerenciados. Esses discos gerenciados podem ser anexados às VMs do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Examinar pré-requisitos
> * Ligar ao Data Box
> * Copiar dados para o Data Box


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Você concluiu o [tutorial: Configurar Azure Data Box](data-box-deploy-set-up.md).
2. Você recebeu sua Data Box e o status do pedido no portal é **entregue**.
3. Você está conectado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se uma conexão de 10 GbE não estiver disponível, use um link de dados de 1 GbE, mas as velocidades de cópia serão afetadas.
4. Você analisou o:

    - [Tamanhos de disco gerenciado com suporte nos limites de tamanho de objeto do Azure](data-box-limits.md#azure-object-size-limits).
    - [Introdução aos Azure Managed disks](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box"></a>Ligar ao Data Box

Com base nos grupos de recursos especificados, Data Box cria um compartilhamento para cada grupo de recursos associado. Por exemplo, se `mydbmdrg1` e `mydbmdrg2` foram criados quando você colocar o pedido, os seguintes compartilhamentos serão criados:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Em cada compartilhamento, as três pastas a seguir são criadas, que correspondem aos contêineres em sua conta de armazenamento.

- Premium SSD
- HDD Standard
- SSD Standard

A tabela a seguir mostra os caminhos UNC para os compartilhamentos no seu Data Box.
 
|        Protocolo de conexão           |             Caminho UNC para o compartilhamento                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<PremiumSSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardHDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardSSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<PremiumSSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardHDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardSSD>/file3.vhd` |

Com base em se você usa SMB ou NFS para se conectar a compartilhamentos de Data Box, as etapas para se conectar são diferentes.

> [!NOTE]
> Não há suporte para a conexão via REST para este recurso.

### <a name="connect-to-data-box-via-smb"></a>Conectar-se a Data Box via SMB

Se estiver usando um computador host do Windows Server, siga estas etapas para se conectar ao Data Box.

1. O primeiro passo é autenticar e iniciar uma sessão. Aceda a **Ligar e copiar**. Clique em **obter credenciais** para obter as credenciais de acesso para os compartilhamentos associados ao seu grupo de recursos. Você também pode obter as credenciais de acesso dos **detalhes do dispositivo** na portal do Azure.

    > [!NOTE]
    > As credenciais para todos os compartilhamentos para Managed disks são idênticas.

    ![Obter credenciais de partilhas 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Na caixa de diálogo acessar compartilhamento e copiar dados, copie o **nome de usuário** e a **senha** para o compartilhamento. Clique em **OK**.
    
    ![Obter credenciais de partilhas 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Para acessar os compartilhamentos associados ao seu recurso (*mydbmdrg1* no exemplo a seguir) do seu computador host, abra uma janela de comando. Na linha de comandos, escreva:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Os caminhos de compartilhamento UNC neste exemplo são os seguintes:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Introduza a palavra-passe da partilha quando lhe for pedido. O exemplo seguinte mostra a ligação a uma partilha através do comando anterior.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Prima Windows + R. Na janela **Executar**, especifique o `\\<device IP address>\<ShareName>`. Clique em **OK** para abrir o explorador de arquivos.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Agora você deve ver as seguintes pastas criadas em cada compartilhamento.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>Conectar-se a Data Box via NFS

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

Depois que você estiver conectado ao servidor de dados, a próxima etapa será copiar os dados. O arquivo VHD é copiado para a conta de armazenamento de preparo como blob de páginas. O blob de páginas é então convertido em um disco gerenciado e movido para um grupo de recursos.

Examine as seguintes considerações antes de começar a cópia de dados:

- Sempre copie os VHDs para uma das pastas precriadas. Se você copiar os VHDs fora dessas pastas ou em uma pasta que você criou, os VHDs serão carregados na conta de armazenamento do Azure como BLOBs de página e não em discos gerenciados.
- Somente os VHDs fixos podem ser carregados para criar discos gerenciados. Não há suporte para arquivos VHDX ou VHDs dinâmicos e diferenciais.
- Você só pode ter um disco gerenciado com um determinado nome em um grupo de recursos em todas as pastas criadas. Isso implica que os VHDs carregados para as pastas criadas antes devem ter nomes exclusivos. Verifique se o nome fornecido não corresponde a um disco gerenciado já existente em um grupo de recursos.
- Examine os limites de disco gerenciado em [limites de tamanho de objeto do Azure](data-box-limits.md#azure-object-size-limits).

Dependendo se você estiver se conectando via SMB ou NFS, poderá usar:

- [Copiar dados via SMB](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [Copiar dados via NFS](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

Aguarde a conclusão dos trabalhos de cópia. Verifique se os trabalhos de cópia foram concluídos sem erros antes de ir para a próxima etapa.

![Nenhum erro na página * * conectar e copiar * *](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Se houver erros durante o processo de cópia, baixe os logs da página **conectar e copiar** .

- Se você copiou um arquivo que não está com 512 bytes alinhado, o arquivo não será carregado como blob de páginas para sua conta de armazenamento de preparo. Você verá um erro nos logs. Remova o arquivo e copie um arquivo que tenha 512 bytes alinhados.

- Se você copiou um VHDX (esses arquivos não têm suporte) com um nome longo, verá um erro nos logs.

    ![Erro nos logs da página * * conectar e copiar * *](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Resolva os erros antes de prosseguir para a próxima etapa.

Para garantir a integridade dos dados, a soma de verificação é calculada inline à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço utilizado e o espaço livre no seu dispositivo.
    
![Verificar o espaço livre e utilizado no dashboard](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Quando o trabalho de cópia for concluído, você poderá ir para **preparação para o envio**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Examinar pré-requisitos
> * Ligar ao Data Box
> * Copiar dados para o Data Box


Avance para o próximo tutorial para aprender a enviar seus Data Box de volta à Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)

