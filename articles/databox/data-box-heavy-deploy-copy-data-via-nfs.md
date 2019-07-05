---
title: Tutorial para copiar dados para o Azure dados caixa pesada por meio de NFS | Documentos da Microsoft
description: Saiba como copiar dados para o Azure dados caixa pesada por meio de NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 4361cee3d07408c3abb5031d2ab18c15c92c5e0a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595750"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-nfs"></a>Tutorial: Copiar dados para o Azure dados caixa pesada por meio de NFS

Este tutorial descreve como ligar e copiar dados do computador anfitrião através da web local da interface do Usuário para seu pesadas de caixa de dados do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar a dados caixa pesado
> * Copiar dados para dados de caixa pesadas

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Configurar a caixa de dados do Azure pesada](data-box-heavy-deploy-set-up.md).
2. Que recebeu pesadas de caixa de seus dados e é o estado da encomenda no portal **entregues**.
3. Possui um computador de anfitrião que tenha os dados que pretende que o copie para dados de caixa pesada. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-heavy-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Para mais rápidas velocidades de cópia, duas ligações de 40 GbE (um por nó) podem ser utilizadas em paralelo. Se não tiver ligação 40 GbE disponível, recomendamos que tenha, pelo menos, duas ligações de 10 GbE (um por nó). 

## <a name="connect-to-data-box-heavy"></a>Ligar a dados caixa pesado

Com base na conta de armazenamento selecionada, dados de caixa pesada cria até:
- Três partilhas para cada conta de armazenamento associada de GPv1 e GPv2.
- Um compartilhamento para armazenamento premium.
- Um compartilhamento para conta de armazenamento de Blobs.

Estas partilhas são criadas em ambos os nós do dispositivo.

Em partilhas de blob de página e BLOBs de bloco:
- Entidades de primeiro nível são contentores.
- Entidades de segundo nível são blobs.

Em partilhas de ficheiros do Azure:
- As entidades de primeiro nível são partilhas.
- Entidades de segundo nível são ficheiros.

A tabela seguinte mostra o caminho UNC até as partilhas no seu URL de caminho de dados caixa pesadas e armazenamento do Azure, onde os dados são carregados. O URL de caminho final do armazenamento do Azure pode derivar do caminho da partilha UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de blocos do Azure | <li>Caminho UNC para partilhas: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL de armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para partilhas: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL de armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Ficheiros do Azure       |<li>Caminho UNC para partilhas: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL de armazenamento do Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Se estiver a utilizar um computador de anfitrião do Linux, execute os seguintes passos para configurar o seu dispositivo para permitir o acesso para os clientes NFS.

1. Forneça os endereços IP dos clientes permitidos que podem aceder à partilha. Na IU Web local, aceda à página **Ligar e copiar**. Em **Definições de NFS**, clique em **Acesso de cliente NFS**. 

    ![Configurar o acesso de cliente NFS 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Forneça o endereço IP do cliente NFS e clique em **Adicionar**. Pode configurar o acesso para vários clientes NFS ao repetir este passo. Clique em **OK**.

    ![Configurar o acesso de cliente NFS 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Certifique-se de que o computador anfitrião do Linux tem uma [versão suportada](data-box-system-requirements.md) do cliente NFS instalada. Utilize a versão específica para a distribuição Linux. 

3. Depois de o cliente NFS estar instalado, utilize o comando seguinte para montar a partilha NFS no seu dispositivo Data Box:

    `sudo mount <Data Box Heavy device IP>:/<NFS share on Data Box Heavy device> <Path to the folder on local Linux computer>`

    O exemplo seguinte mostra como ligar através de NFS para uma partilha de dados de caixa pesada. O IP de pesadas de caixa de dados esteja `10.161.23.130`, a partilha `Mystoracct_Blob` é montado no ubuntuVM, montar o ponto a ser `/home/databoxheavyubuntuhost/databoxheavy`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`
    
    Para os clientes Mac, terá de adicionar uma opção adicional da seguinte forma: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`

    **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada no blob de blocos e partilhas de blob de página representa um contentor para o qual os dados são carregados como blobs. Não é possível copiar o arquivos diretamente à *raiz* pasta na conta de armazenamento.

## <a name="copy-data-to-data-box-heavy"></a>Copiar dados para dados de caixa pesadas

Assim que estiver ligado para as partilhas de dados de caixa pesada, a próxima etapa é copiar dados. Antes de iniciar a cópia de dados, reveja as seguintes considerações:

- Certifique-se de que copia os dados para partilhas que correspondem ao formato de dados apropriado. Por exemplo, copie os dados de blobs de blocos para a partilha de blobs de blocos. Copie VHDs para blobs de páginas. Se o formato de dados não corresponder à partilha apropriada (tipo de armazenamento), num passo posterior, o carregamento de dados para o Azure falhará.
-  Ao copiar dados, certifique-se de que o tamanho dos dados está em conformidade com os limites de tamanho descritos a [armazenamento do Azure e limites de dados de caixa pesada](data-box-heavy-limits.md). 
- Se os dados, o que está a ser carregados por dados caixa pesada, são carregados em simultâneo por outros aplicativos fora dados caixa pesada, isto pode resultar no carregamento de Corrupção de dados e de falhas de tarefa.
- Recomendamos que não utilize as opções SMB e NFS em simultâneo nem copie os mesmos dados para o mesmo destino final no Azure. Nesses casos, não é possível determinar o resultado final.
- **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada no blob de blocos e partilhas de blob de página representa um contentor para o qual os dados são carregados como blobs. Não é possível copiar o arquivos diretamente à *raiz* pasta na conta de armazenamento.
- Se diferencia maiúsculas de minúsculas directory nomes de arquivo e de uma partilha NFS para NFS nas pesadas de caixa de dados a ingerir: 
    - O caso é preservado no nome.
    - Os ficheiros são maiúsculas e minúsculas.
    
    Por exemplo, se copiar `SampleFile.txt` e `Samplefile.Txt`, o caso será mantido no nome quando copiados para o dispositivo, mas o segundo arquivo substituirá o primeiro como estes são considerados o mesmo arquivo.


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
> Os seguintes tipos de ficheiro do Linux não são suportados: links simbólicos, arquivos de caráter, ficheiros de bloco, sockets e pipes. Estes tipos de ficheiro irão resultar em falhas durante o **preparação para envio** passo.

Abra a pasta de destino para ver e verificar os ficheiros copiados. Se ocorrerem erros durante o processo de cópia, transfira os ficheiros de erro para resolução de problemas. Para obter mais informações, consulte [ver registos de erros durante a cópia de dados para dados de caixa pesada](data-box-logs.md#view-error-log-during-data-copy). Para obter uma lista detalhada de erros durante a cópia de dados, consulte [problemas de resolução de problemas de dados de caixa pesada](data-box-troubleshoot.md).

Para garantir a integridade dos dados, a soma de verificação é calculada inline à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço utilizado e o espaço livre no seu dispositivo.
    
   ![Verificar o espaço livre e utilizado no dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu sobre tópicos pesadas de caixa de dados do Azure, tais como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar a dados caixa pesado
> * Copiar dados para dados de caixa pesadas


Avance para o próximo tutorial para saber como enviar o Data Box para a Microsoft.

> [!div class="nextstepaction"]
> [Envie sua pesado de caixa de dados do Azure para a Microsoft](./data-box-heavy-deploy-picked-up.md)

