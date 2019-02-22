---
title: Copiar dados para o Microsoft Azure Data Box através do NFS | Documentos da Microsoft
description: Saiba como copiar dados para o Azure Data Box por meio de NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: alkohli
ms.openlocfilehash: 35a041216bf24a4c6ab73f9d5c3e85dff38a4501
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588114"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Tutorial: Copiar dados para o Azure Data Box por meio de NFS

Este tutorial descreve como ligar e copiar dados do computador anfitrião com a IU web local.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar ao Data Box
> * Copiar dados para o Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Configurar o Azure Data Box](data-box-deploy-set-up.md).
2. Recebeu o seu Data Box e o estado da encomenda no portal é **Entregue**.
3. Tem um computador anfitrião com os dados que pretende copiar para o Data Box. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se uma ligação de 10 GbE não estiver disponível, pode ser utilizada uma ligação de dados de 1 GbE, mas as velocidades de cópia serão afetadas. 

## <a name="connect-to-data-box"></a>Ligar ao Data Box

Data Box com base na conta de armazenamento selecionada, cria até:
- Três partilhas para cada conta de armazenamento associada de GPv1 e GPv2.
- Uma partilha para uma conta de armazenamento de blobs ou premium. 

Nas partilhas de blob de blocos e de blob de páginas, as entidades de primeiro nível são os contentores e as de segundo nível são os blobs. Nas partilhas de ficheiros do Azure, as entidades de primeiro nível são as partilhas e as de segundo nível são os ficheiros.

A tabela seguinte mostra o caminho UNC até as partilhas no seu URL de caminho Data Box e o armazenamento do Azure, onde os dados são carregados. O URL de caminho final do armazenamento do Azure pode derivar do caminho da partilha UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de blocos do Azure | <li>Caminho UNC para partilhas: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL de armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para partilhas: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL de armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Ficheiros do Azure       |<li>Caminho UNC para partilhas: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL de armazenamento do Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Se estiver a utilizar um computador anfitrião do Linux, execute os passos seguintes para configurar o Data Box para permitir o acesso aos clientes NFS.

1. Forneça os endereços IP dos clientes permitidos que podem aceder à partilha. Na IU Web local, aceda à página **Ligar e copiar**. Em **Definições de NFS**, clique em **Acesso de cliente NFS**. 

    ![Configurar o acesso de cliente NFS 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Forneça o endereço IP do cliente NFS e clique em **Adicionar**. Pode configurar o acesso para vários clientes NFS ao repetir este passo. Clique em **OK**.

    ![Configurar o acesso de cliente NFS 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Certifique-se de que o computador anfitrião do Linux tem uma [versão suportada](data-box-system-requirements.md) do cliente NFS instalada. Utilize a versão específica para a distribuição Linux. 

3. Depois de o cliente NFS estar instalado, utilize o comando seguinte para montar a partilha NFS no seu dispositivo Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    O exemplo seguinte mostra como ligar através de NFS a uma partilha do Data Box. O IP do dispositivo Data Box é `10.161.23.130`, a partilha `Mystoracct_Blob` está montada no ubuntuVM, sendo que o ponto de montagem é `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Para os clientes Mac, terá de adicionar uma opção adicional da seguinte forma: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada no blob de blocos e partilhas de blob de página representa um contentor para o qual os dados são carregados como blobs. Não é possível copiar o arquivos diretamente à *raiz* pasta na conta de armazenamento.

## <a name="copy-data-to-data-box"></a>Copiar dados para o Data Box

Assim que estiver ligado às partilhas do Data Box, o passo seguinte é copiar os dados. Antes de iniciar a cópia de dados, reveja as seguintes considerações:

- Certifique-se de que copia os dados para partilhas que correspondem ao formato de dados apropriado. Por exemplo, copie os dados de blobs de blocos para a partilha de blobs de blocos. Copie VHDs para blobs de páginas. Se o formato de dados não corresponder à partilha apropriada (tipo de armazenamento), num passo posterior, o carregamento de dados para o Azure falhará.
-  Ao copiar os dados, certifique-se de que o respetivo tamanho está em conformidade com os limites descritos em [Limites de armazenamento do Azure e do Data Box](data-box-limits.md). 
- Se os dados, que estão a ser carregados pelo Data Box, forem carregados em simultâneo por outras aplicações fora do Data Box, isto pode resultar em falhas da tarefa de carregamento e danos nos dados.
- Recomendamos que não utilize as opções SMB e NFS em simultâneo nem copie os mesmos dados para o mesmo destino final no Azure. Nesses casos, não é possível determinar o resultado final.
- **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada no blob de blocos e partilhas de blob de página representa um contentor para o qual os dados são carregados como blobs. Não é possível copiar o arquivos diretamente à *raiz* pasta na conta de armazenamento.

Se estiver a utilizar um computador anfitrião do Linux, utilize um utilitário de cópia semelhante ao Robocopy. Algumas das alternativas disponíveis no Linux são [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) ou [Ultracopier](https://ultracopier.first-world.info/).  

O comando `cp` é uma das melhores opções para copiar um diretório. Para obter mais informações sobre a utilização, aceda a [cp man pages](http://man7.org/linux/man-pages/man1/cp.1.html).

Se estiver a utilizar a opção rsync para uma cópia de múltiplos threads, siga estas diretrizes:

 - Instale o pacote **CIFS Utils** ou **NFS Utils** consoante o sistema de ficheiros utilizado pelo cliente Linux.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  Instale **Rsync** e **Parallel** (varia consoante a versão distribuída do Linux).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - Crie um ponto de montagem.

    `sudo mkdir /mnt/databox`

 - Monte o volume.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

 - Espelhe a estrutura do diretório de pastas.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

 - Copie os ficheiros. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     em que j especifica o número de paralelização, X = número de cópias paralelas

     Recomendamos que comece com 16 cópias paralelas e aumente o número de threads consoante os recursos disponíveis.

- Para garantir a integridade dos dados, a soma de verificação é calculada inline à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço utilizado e o espaço livre no seu dispositivo.
    
   ![Verificar o espaço livre e utilizado no dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar ao Data Box
> * Copiar dados para o Data Box


Avance para o próximo tutorial para saber como enviar o Data Box para a Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)

