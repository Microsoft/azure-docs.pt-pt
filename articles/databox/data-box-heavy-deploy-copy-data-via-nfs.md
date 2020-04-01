---
title: Tutorial para copiar dados para Caixa de Dados Azure Heavy via NFS Microsoft Docs
description: Saiba como copiar dados para a sua Caixa de Dados Azure Heavy via NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 4361cee3d07408c3abb5031d2ab18c15c92c5e0a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238987"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-nfs"></a>Tutorial: Copiar dados para Caixa de Dados Azure Pesado via NFS

Este tutorial descreve como conectar e copiar dados do seu computador anfitrião usando a UI web local para a sua Caixa de Dados Azure Heavy.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar ao Data Box Heavy
> * Copiar dados para o Data Box Heavy

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Completou o [Tutorial: Instale](data-box-heavy-deploy-set-up.md)a Caixa de Dados Azure Heavy .
2. Recebeu a sua Caixa de Dados Pesada e o estado da encomenda no portal é **Entregue**.
3. Tem um computador anfitrião com os dados que quer copiar para o Data Box Heavy. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-heavy-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Para velocidades de cópia mais rápidas, podem ser utilizadas em paralelo duas ligações de 40 GbE (uma por nó). Se não tiver uma ligação de 40 GbE disponível, recomendamos que tenha pelo menos duas ligações de 10 GbE (uma por nó). 

## <a name="connect-to-data-box-heavy"></a>Ligar ao Data Box Heavy

Com base na conta de armazenamento selecionada, o Data Box Heavy cria até:
- Três partilhas para cada conta de armazenamento associada de GPv1 e GPv2.
- Uma partilha para o armazenamento premium.
- Uma partilha para a conta de armazenamento de blobs.

Estas partilhas são criadas em ambos os nós do dispositivo.

Nas partilhas de blobs de blocos e blobs de páginas:
- As entidades de primeiro nível são contentores.
- As entidades de segundo nível são blobs.

Nas partilhas para os Ficheiros do Azure:
- As entidades de primeiro nível são partilhas.
- As entidades de segundo nível são ficheiros.

A tabela a seguir mostra o caminho UNC para as partilhas no Data Box Heavy e o URL do caminho do Armazenamento do Microsoft Azure onde os dados são carregados. O URL do caminho de Armazenamento do Microsoft Azure final pode derivar do caminho da partilha UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de bloco do Azure | <li>Caminho UNC para as partilhas: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL do Armazenamento do Microsoft Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para as partilhas: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL do Armazenamento do Microsoft Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Ficheiros do Azure       |<li>Caminho UNC para as partilhas: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL do Armazenamento do Microsoft Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Se estiver a utilizar um computador de hospedante Linux, execute os seguintes passos para configurar o seu dispositivo para permitir o acesso aos clientes NFS.

1. Forneça os endereços IP dos clientes permitidos que podem aceder à partilha. Na IU Web local, aceda à página **Ligar e copiar**. Em **Definições de NFS**, clique em **Acesso de cliente NFS**. 

    ![Configurar o acesso de cliente NFS 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Forneça o endereço IP do cliente NFS e clique em **Adicionar**. Pode configurar o acesso para vários clientes NFS ao repetir este passo. Clique em **OK**.

    ![Configurar o acesso de cliente NFS 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Certifique-se de que o computador anfitrião do Linux tem uma [versão suportada](data-box-system-requirements.md) do cliente NFS instalada. Utilize a versão específica para a distribuição Linux. 

3. Depois de o cliente NFS estar instalado, utilize o comando seguinte para montar a partilha NFS no seu dispositivo Data Box:

    `sudo mount <Data Box Heavy device IP>:/<NFS share on Data Box Heavy device> <Path to the folder on local Linux computer>`

    O exemplo que se segue mostra como ligar via NFS a uma parte pesada da Caixa de Dados. A Caixa de `10.161.23.130`Dados Heavy `Mystoracct_Blob` IP é , a parte é montada `/home/databoxheavyubuntuhost/databoxheavy`no ubuntuVM, sendo o ponto de montagem .

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`
    
    Para os clientes Mac, terá de adicionar uma opção adicional da seguinte forma: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`

    **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada nas partilhas dos blobs de blocos e dos blobs de páginas representa um contentor para o qual os dados são carregados como blobs. Não pode copiar ficheiros diretamente para a pasta *raiz* na conta de armazenamento.

## <a name="copy-data-to-data-box-heavy"></a>Copiar dados para o Data Box Heavy

Uma vez ligado si à Caixa de Dados As ações Heavy, o próximo passo é copiar dados. Antes de começar a cópia de dados, reveja as seguintes considerações:

- Certifique-se de que copia os dados para partilhas que correspondem ao formato de dados apropriado. Por exemplo, copie os dados de blobs de blocos para a partilha de blobs de blocos. Copiar VHDs para bolhas de página. Se o formato de dados não corresponder à partilha apropriada (tipo de armazenamento), num passo posterior, o carregamento de dados para o Azure falhará.
-  Ao copiar dados, certifique-se de que o tamanho dos dados está em conformidade com os limites de tamanho descritos nos [limites pesados](data-box-heavy-limits.md)de armazenamento e caixa de dados do Azure . 
- Se os dados, que estão a ser carregados pelo Data Box Heavy, forem carregados em simultâneo por outras aplicações fora do Data Box Heavy, poderá resultar em falhas da tarefa de carregamento e danos nos dados.
- Recomendamos que não utilize as opções SMB e NFS em simultâneo nem copie os mesmos dados para o mesmo destino final no Azure. Nesses casos, não é possível determinar o resultado final.
- **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada nas partilhas dos blobs de blocos e dos blobs de páginas representa um contentor para o qual os dados são carregados como blobs. Não pode copiar ficheiros diretamente para a pasta *raiz* na conta de armazenamento.
- Se ingerir um diretório sensível a casos e arquivar nomes de uma parte da NFS para nFS em Data Box Heavy: 
    - O caso está preservado no nome.
    - Os ficheiros são insensíveis aos casos.
    
    Por exemplo, se `SampleFile.txt` `Samplefile.Txt`copiar e , o caso será preservado no nome quando copiado para o dispositivo, mas o segundo ficheiro irá sobrepor-se ao primeiro, uma vez que estes são considerados o mesmo ficheiro.


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

    `sudo mkdir /mnt/databoxheavy`

 - Monte o volume.

    `sudo mount -t NFS4  //Databox-heavy-IP-Address/share_name /mnt/databoxheavy` 

 - Espelhe a estrutura do diretório de pastas.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databoxheavy`

 - Copie os ficheiros. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databoxheavy/{}`

     em que j especifica o número de paralelização, X = número de cópias paralelas

     Recomendamos que comece com 16 cópias paralelas e aumente o número de threads consoante os recursos disponíveis.

> [!IMPORTANT]
> Os seguintes tipos de ficheiros Linux não são suportados: links simbólicos, ficheiros de caracteres, ficheiros de blocos, tomadas e tubos. Estes tipos de ficheiros resultarão em falhas durante o preparar para o passo do **navio.**

Abra a pasta de destino para ver e verificar os ficheiros copiados. Se ocorrerem erros durante o processo de cópia, transfira os ficheiros de erro para resolução de problemas. Para obter mais informações, veja a secção [Ver registos de erros durante a cópia de dados para o Data Box Heavy](data-box-logs.md#view-error-log-during-data-copy). Para obter uma lista detalhada de erros durante a cópia de dados, veja a secção [Resolver problemas do Data Box Heavy](data-box-troubleshoot.md).

Para garantir a integridade dos dados, a soma de verificação é calculada inline à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço utilizado e o espaço livre no seu dispositivo.
    
   ![Verificar o espaço livre e utilizado no dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Heavy, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar ao Data Box Heavy
> * Copiar dados para o Data Box Heavy


Avance para o tutorial seguinte para saber como enviar o Data Box de volta para a Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box Heavy para a Microsoft](./data-box-heavy-deploy-picked-up.md)

