---
title: Tutorial para copiar dados da Caixa de Dados do Azure via NFS Microsoft Docs
description: Saiba como copiar dados da sua Caixa de Dados Azure via NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 301c75df6bedf430af64bbeff63f2eb759691355
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86210480"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-nfs-preview"></a>Tutorial: Copiar dados da Caixa de Dados do Azure via NFS (Pré-visualização)

Este tutorial descreve como ligar e copiar dados da UI web local da sua Caixa de Dados a um servidor de dados no local via NFS. Os dados da sua Caixa de Dados são exportados da sua conta de Armazenamento Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Ligar ao Data Box
> * Copiar dados da Caixa de Dados

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Fez o pedido para a Caixa de Dados Azure.
    - Para obter uma encomenda de importação, consulte [Tutorial: Encomenda Caixa de Dados Azure](data-box-deploy-ordered.md).
    - Para uma encomenda de exportação, consulte [Tutorial: Encomenda Caixa de Dados Azure](data-box-deploy-export-ordered.md).
2. Recebeu o Data Box e o estado da encomenda no portal é **Entregue**.
3. Tem um computador anfitrião ao qual pretende copiar os dados da sua Caixa de Dados. O computador anfitrião tem de
   * Executar um [sistema operativo suportado](data-box-system-requirements.md).
   * Estar ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se uma ligação de 10 GbE não estiver disponível, utilize uma ligação de dados de 1 GbE, mas as velocidades de cópia serão afetadas.

## <a name="connect-to-data-box"></a>Ligar ao Data Box

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Se estiver a utilizar um computador anfitrião do Linux, execute os passos seguintes para configurar o Data Box para permitir o acesso aos clientes NFS.

1. Forneça os endereços IP dos clientes permitidos que podem aceder à partilha. Na IU Web local, aceda à página **Ligar e copiar**. Em **Definições de NFS**, clique em **Acesso de cliente NFS**. 

    ![Configurar o acesso de cliente NFS 1](media/data-box-deploy-export-copy-data/nfs-client-access-1.png)

2. Forneça o endereço IP do cliente NFS e clique em **Adicionar**. Pode configurar o acesso para vários clientes NFS ao repetir este passo. Clique em **OK**.

    ![Configurar o acesso de cliente NFS 2](media/data-box-deploy-export-copy-data/nfs-client-access-2.png)

2. Certifique-se de que o computador anfitrião do Linux tem uma [versão suportada](data-box-system-requirements.md) do cliente NFS instalada. Utilize a versão específica para a distribuição Linux. 

3. Depois de o cliente NFS estar instalado, utilize o comando seguinte para montar a partilha NFS no seu dispositivo Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    O exemplo seguinte mostra como ligar através de NFS a uma partilha do Data Box. O IP do dispositivo Data Box é `10.161.23.130`, a partilha `Mystoracct_Blob` está montada no ubuntuVM, sendo que o ponto de montagem é `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Para os clientes Mac, terá de adicionar uma opção adicional da seguinte forma: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada nas partilhas dos blobs de blocos e dos blobs de páginas representa um contentor para o qual os dados são carregados como blobs. Não pode copiar ficheiros diretamente para a pasta *raiz* na conta de armazenamento.

## <a name="copy-data-from-data-box"></a>Copiar dados da Caixa de Dados

Assim que estiver ligado às partilhas do Data Box, o passo seguinte é copiar os dados.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]

 Pode agora iniciar a cópia de dados. Se estiver a utilizar um computador anfitrião do Linux, utilize um utilitário de cópia semelhante ao Robocopy. Algumas das alternativas disponíveis no Linux são [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) ou [Ultracopier](https://ultracopier.first-world.info/).  

O comando `cp` é uma das melhores opções para copiar um diretório. Para obter mais informações sobre a utilização, aceda a [cp man pages](http://man7.org/linux/man-pages/man1/cp.1.html).

Se estiver a utilizar a opção rsync para uma cópia de múltiplos threads, siga estas diretrizes:

* Instale o pacote **CIFS Utils** ou **NFS Utils** consoante o sistema de ficheiros utilizado pelo cliente Linux.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Instale **Rsync** e **Parallel** (varia consoante a versão distribuída do Linux).

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

Assim que a cópia estiver concluída, vá ao **Painel de Instrumentos** e verifique o espaço utilizado e o espaço livre no seu dispositivo.

Pode agora proceder ao envio da sua Caixa de Dados para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Ligar ao Data Box
> * Copiar dados da Caixa de Dados

Avance para o tutorial seguinte para saber como enviar o Data Box de volta para a Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box para a Microsoft](./data-box-deploy-export-picked-up.md)
