---
title: Tutorial para copiar dados para Azure Data Box via NFS| Microsoft Docs
description: Neste tutorial, aprenda a ligar e copiar dados do seu computador anfitrião para a Azure Data Box utilizando NFS com a UI web local.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: d53a619dc6ca5fb0f43f6097664f50bf22943928
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678885"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Tutorial: Copiar dados para Azure Data Box via NFS

Este tutorial descreve como ligar e copiar dados do computador anfitrião com a IU Web local.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Ligar ao Data Box
> * Copiar dados para o Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Configurar o Azure Data Box](data-box-deploy-set-up.md).
2. Recebeu o seu Data Box e o estado da encomenda no portal é **Entregue**.
3. Tem um computador anfitrião com os dados que pretende copiar para o Data Box. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se uma ligação de 10 GbE não estiver disponível, pode ser utilizada uma ligação de dados de 1 GbE, mas as velocidades de cópia serão impactadas. 

## <a name="connect-to-data-box"></a>Ligar ao Data Box

Com base na conta de armazenamento selecionada, o Data Box cria até:
- Três partilhas para cada conta de armazenamento associada de GPv1 e GPv2.
- Uma partilha para o armazenamento premium. 
- Uma partilha para a conta de armazenamento de blobs. 

Nas partilhas de blob de blocos e de blob de páginas, as entidades de primeiro nível são os contentores e as de segundo nível são os blobs. Nas partilhas de ficheiros do Azure, as entidades de primeiro nível são as partilhas e as de segundo nível são os ficheiros.

A tabela a seguir mostra o caminho UNC para as partilhas no Data Box e o URL do caminho do Armazenamento do Microsoft Azure onde os dados são carregados. O URL do caminho de Armazenamento do Microsoft Azure final pode derivar do caminho da partilha UNC.
 
| Tipo de armazenamento Azure| Partilhas de Data Box                                       |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de bloco do Azure | <li>Caminho UNC para as partilhas: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL de Armazenamento do Microsoft Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para as partilhas: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL do Armazenamento do Microsoft Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Ficheiros do Azure       |<li>Caminho UNC para as partilhas: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL de Armazenamento do Microsoft Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Se estiver a utilizar um computador anfitrião do Linux, execute os passos seguintes para configurar o Data Box para permitir o acesso aos clientes NFS.

1. Forneça os endereços IP dos clientes permitidos que podem aceder à partilha. Na IU Web local, aceda à página **Ligar e copiar**. Em **Definições de NFS**, clique em **Acesso de cliente NFS**. 

    ![Configure o acesso ao cliente NFS](media/data-box-deploy-copy-data/nfs-client-access-1.png)

2. Forneça o endereço IP do cliente NFS e clique em **Adicionar**. Pode configurar o acesso para vários clientes NFS ao repetir este passo. Clique em **OK**.

    ![Configure endereço IP de um cliente NFS](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Certifique-se de que o computador anfitrião do Linux tem uma [versão suportada](data-box-system-requirements.md) do cliente NFS instalada. Utilize a versão específica para a distribuição Linux. 

3. Depois de o cliente NFS estar instalado, utilize o comando seguinte para montar a partilha NFS no seu dispositivo Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    O exemplo seguinte mostra como ligar através de NFS a uma partilha do Data Box. O IP do dispositivo Data Box é `10.161.23.130`, a partilha `Mystoracct_Blob` está montada no ubuntuVM, sendo que o ponto de montagem é `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Para os clientes Mac, terá de adicionar uma opção adicional da seguinte forma: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada nas partilhas dos blobs de blocos e dos blobs de páginas representa um contentor para o qual os dados são carregados como blobs. Não pode copiar ficheiros diretamente para a pasta *raiz* na conta de armazenamento.

## <a name="copy-data-to-data-box"></a>Copiar dados para o Data Box

Assim que estiver ligado às partilhas do Data Box, o passo seguinte é copiar os dados. Antes de começar a cópia de dados, reveja as seguintes considerações:

* Certifique-se de que copia os dados para partilhas que correspondem ao formato de dados apropriado. Por exemplo, copie os dados de blobs de blocos para a partilha de blobs de blocos. Copie VHDs para bolhas de página. Se o formato de dados não corresponder à partilha apropriada (tipo de armazenamento), num passo posterior, o carregamento de dados para o Azure falhará.
*  Ao copiar dados, certifique-se de que o tamanho dos dados está em conformidade com os limites de tamanho descritos nos limites de tamanho da [conta de armazenamento Azure](data-box-limits.md#azure-storage-account-size-limits).
* Se os dados, que estão a ser carregados pelo Data Box, forem carregados em simultâneo por outras aplicações fora do Data Box, isto pode resultar em falhas da tarefa de carregamento e danos nos dados.
* Recomendamos que não utilize as opções SMB e NFS em simultâneo nem copie os mesmos dados para o mesmo destino final no Azure. Nesses casos, não é possível determinar o resultado final.
* **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada nas partilhas dos blobs de blocos e dos blobs de páginas representa um contentor para o qual os dados são carregados como blobs. Não pode copiar ficheiros diretamente para a pasta *raiz* na conta de armazenamento.
* Se ingerir diretório sensível a casos e apresentar nomes de ficheiros de uma participação da NFS para a NFS na Caixa de Dados:
  * O caso está preservado no nome.
  * Os ficheiros são insensíveis a casos.

    Por exemplo, se copiar `SampleFile.txt` `Samplefile.Txt` e, o caso será preservado no nome quando copiado para Data Box, mas o segundo ficheiro substituirá o primeiro, uma vez que estes são considerados o mesmo ficheiro.

> [!IMPORTANT]
> Certifique-se de que mantém uma cópia dos dados de origem até poder confirmar que o Data Box transferiu os seus dados para o Armazenamento do Azure.

Se estiver a utilizar um computador anfitrião do Linux, utilize um utilitário de cópia semelhante ao Robocopy. Algumas das alternativas disponíveis no Linux [`rsync`](https://rsync.samba.org/) [são, FreeFileSync,](https://www.freefilesync.org/) [Unison](https://www.cis.upenn.edu/~bcpierce/unison/)ou [Ultracopier.](https://ultracopier.first-world.info/)  

O comando `cp` é uma das melhores opções para copiar um diretório. Para obter mais informações sobre a utilização, aceda a [cp man pages](http://man7.org/linux/man-pages/man1/cp.1.html).

Se utilizar `rsync` a opção para uma cópia com vários fios, siga estas diretrizes:

* Instale o pacote **CIFS Utils** ou **NFS Utils** consoante o sistema de ficheiros utilizado pelo cliente Linux.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Instalação `rsync` e **Paralelo** (varia consoante a versão distribuída do Linux).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* Crie um ponto de montagem.

    `sudo mkdir /mnt/databox`

* Monte o volume.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* Espelhe a estrutura do diretório de pastas.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* Copie os ficheiros.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     em que j especifica o número de paralelização, X = número de cópias paralelas

     Recomendamos que comece com 16 cópias paralelas e aumente o número de threads consoante os recursos disponíveis.

> [!IMPORTANT]
> Os seguintes tipos de ficheiros Linux não são suportados: ligações simbólicas, ficheiros de caracteres, ficheiros de blocos, tomadas e tubos. Estes tipos de ficheiros resultarão em falhas durante o passo **de preparação para o envio.**

Durante o processo de cópia, em caso de erros, verá uma notificação.

![Transferir e ver erros em Ligar e Copiar](media/data-box-deploy-copy-data/view-errors-1.png)

Selecione **Transferir lista de problemas**.

![Faça o download da lista de problemas para um erro de cópia](media/data-box-deploy-copy-data/view-errors-2.png)

Abra a lista para ver os detalhes do erro e selecione o URL de resolução para ver a resolução recomendada.

![Problemas numa lista de problemas de erro de cópia](media/data-box-deploy-copy-data/view-errors-3.png)

Para obter mais informações, veja a secção Para obter mais informações, veja a secção [Ver registos de erros durante a cópia de dados para o Data Box](data-box-logs.md#view-error-log-during-data-copy). Para obter uma lista detalhada de erros durante a cópia de dados, veja a secção [Resolver problemas do Data Box](data-box-troubleshoot.md).

Para garantir a integridade dos dados, a soma de verificação é calculada inline à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço utilizado e o espaço livre no seu dispositivo.

   ![Verificar o espaço livre e utilizado no dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Ligar ao Data Box
> * Copiar dados para o Data Box

Avance para o tutorial seguinte para saber como enviar o Data Box de volta para a Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)
