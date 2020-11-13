---
title: Tutorial para copiar dados via SMB no Azure Data Box | Microsoft Docs
description: Neste tutorial, saiba como ligar e copiar os dados do computador anfitrião para o Azure Data Box por SMB através da IU Web local.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 7ecccd64921b2d95155318fe91c897725e340b7e
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94334674"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Tutorial: Copiar dados para o Azure Data Box via SMB

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box"></a>Copiar dados para o Azure Data Box

::: zone-end

::: zone target="docs"

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
2. Recebeu o Data Box e o estado da encomenda no portal é **Entregue**.
3. Tem um computador anfitrião com os dados que pretende copiar para o Data Box. O computador anfitrião tem de
   * Executar um [sistema operativo suportado](data-box-system-requirements.md).
   * Estar ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se uma ligação de 10 GbE não estiver disponível, utilize uma ligação de dados de 1 GbE, mas as velocidades de cópia serão afetadas.

## <a name="connect-to-data-box"></a>Ligar ao Data Box

Com base na conta de armazenamento selecionada, o Data Box cria até:

* Três partilhas para cada conta de armazenamento associada de GPv1 e GPv2.
* Uma partilha para o armazenamento premium.
* Uma partilha para a conta de armazenamento de blobs.

Nas partilhas de blob de blocos e de blob de páginas, as entidades de primeiro nível são os contentores e as de segundo nível são os blobs. Nas partilhas de ficheiros do Azure, as entidades de primeiro nível são as partilhas e as de segundo nível são os ficheiros.

A tabela a seguir mostra o caminho UNC para as partilhas no Data Box e o URL do caminho do Armazenamento do Microsoft Azure onde os dados são carregados. O URL do caminho de Armazenamento do Microsoft Azure final pode derivar do caminho da partilha UNC.
 
|Tipos de Armazenamento do Azure  | Partilhas de Data Box            |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de bloco do Azure | <li>Caminho UNC para as partilhas: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL de Armazenamento do Microsoft Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para as partilhas: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL do Armazenamento do Microsoft Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Ficheiros do Azure       |<li>Caminho UNC para as partilhas: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL de Armazenamento do Microsoft Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Se estiver a utilizar um computador anfitrião do Windows Server, execute estes passos para se ligar ao Data Box.

1. O primeiro passo é autenticar e iniciar uma sessão. Aceda a **Ligar e copiar**. Selecione **SMB** para obter as credenciais de acesso para as partilhas associadas à sua conta de armazenamento. 

    ![Obter credenciais para partilhas SMB](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Na caixa de diálogo Aceder à partilha e copiar os dados, copie o **Nome de utilizador** e a **Palavra-passe** correspondentes à partilha. Se a palavra-passe tiver carateres especiais, adicione aspas duplas antes e depois dela. Em seguida, selecione **OK**.
    
    ![Obter nome de utilizador e palavra-passe para partilhas](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Para aceder às partilhas associadas à conta de armazenamento ( *utsac1* no exemplo a seguir) a partir do computador anfitrião, abra uma janela de comandos. Na linha de comandos, escreva:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Consoante o seu formato de dados, os caminhos de partilha são os seguintes:
    - Blob de blocos do Azure – `\\10.126.76.138\utSAC1_202006051000_BlockBlob`
    - Blob de páginas do Azure – `\\10.126.76.138\utSAC1_202006051000_PageBlob`
    - Ficheiros do Azure – `\\10.126.76.138\utSAC1_202006051000_AzFile`

4. Introduza a palavra-passe da partilha quando lhe for pedido. O exemplo seguinte mostra a ligação a uma partilha através do comando anterior.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.138\utSAC1_202006051000_BlockBlob /u:testuser1
    Enter the password for 'testuser1' to connect to '10.126.76.138':
    The command completed successfully.
    ```

4. Prima Windows + R. Na janela **Executar** , especifique o `\\<device IP address>`. Selecione **OK** para abrir o Explorador de Ficheiros.
    
    ![Ligar à partilha através do Explorador de Ficheiros](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Agora deve conseguir ver as partilhas como pastas.
    
    ![Partilhas mostradas no Explorador de Ficheiros](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)

    **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada nas partilhas dos blobs de blocos e dos blobs de páginas representa um contentor para o qual os dados são carregados como blobs. Não pode copiar ficheiros diretamente para a pasta *raiz* na conta de armazenamento.
    
Se estiver a utilizar um cliente Linux, utilize o comando a seguir para montar a partilha SMB. O parâmetro “vers” abaixo é a versão do SMB compatível com o sistema anfitrião Linux. Ligue a versão apropriada no comando a seguir. Para as versões do SMB compatíveis com o Data Box, veja a secção [Sistemas de ficheiros suportados pelos clientes Linux](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients) 

```console
sudo mount -t nfs -o vers=2.1 10.126.76.138:/utSAC1_202006051000_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-to-data-box"></a>Copiar dados para o Data Box

Assim que estiver ligado às partilhas do Data Box, o passo seguinte é copiar os dados. Antes de começar a cópia de dados, reveja as seguintes considerações:

* Verifique se copia os dados para as partilhas que correspondem ao formato de dados apropriado. Por exemplo, copie os dados de blobs de blocos para a partilha de blobs de blocos. Copie os VHDs para o blob de páginas. Se o formato de dados não corresponder ao tipo de partilha apropriado, num passo posterior, o carregamento de dados para o Azure falhará.
* Crie sempre uma pasta na partilha para os ficheiros que quer copiar e, em seguida, copie os ficheiros para essa pasta. A pasta criada nas partilhas dos blobs de blocos e dos blobs de páginas representa um contentor para o qual os dados são carregados como blobs. Não pode copiar ficheiros diretamente para a pasta *raiz* na conta de armazenamento.
* Ao copiar os dados, confirme se o tamanho está em conformidade com os limites descritos em [Limites de tamanho da conta de armazenamento do Azure](data-box-limits.md#azure-storage-account-size-limits).
* Se quiser preservar os metadados (ACLs, carimbos de data/hora e atributos de ficheiro) quando transferir dados para os Ficheiros do Azure, siga as orientações em [Preservar ACLs, atributos e carimbos de data/hora de ficheiros com o Azure Data Box](data-box-file-acls-preservation.md)  
* Se os dados que estão a ser carregados pelo Data Box também estiverem a ser carregados em simultâneo por outra aplicação, fora do Data Box, isto pode resultar em falhas da tarefa de carregamento e danos nos dados.
* É recomendável que:
  * Não utilize o SMB e o NFS ao mesmo tempo.
  * Copie os mesmos dados para o mesmo destino final no Azure.
  Nestes casos, não é possível determinar o resultado final.

> [!IMPORTANT]
> Certifique-se de que mantém uma cópia dos dados de origem até poder confirmar que o Data Box transferiu os seus dados para o Armazenamento do Azure.

Depois de ligar à partilha SMB, inicie a cópia dos dados. Pode utilizar qualquer ferramenta de cópia de ficheiros compatível com SMB, como o Robocopy, para copiar os dados. É possível iniciar várias tarefas com o Robocopy. Utilize o seguinte comando:

```console
robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
```

Os atributos são descritos na tabela seguinte.
    
|Atributo  |Descrição  |
|---------|---------|
|/e     |Copia os subdiretórios, incluindo diretórios vazios.         |
|/r:     |Especifica o número de repetições nas cópias falhadas.         |
|/w:     |Especifica o tempo de espera entre as repetições, em segundos.         |
|/is     |Inclui os mesmos ficheiros.         |
|/nfl     |Especifica que os nomes de ficheiro não estão registados.         |
|/ndl    |Especifica que os nomes de diretório não estão registados.        |
|/np     |Especifica que o progresso da operação de cópia (o número de ficheiros ou diretórios copiados até ao momento) não será apresentado. A apresentação do progresso reduz significativamente o desempenho.         |
|/MT     | Utilize multithreading (são recomendados 32 ou 64 threads). Esta opção não é utilizada com ficheiros encriptados. Pode ter de separar os ficheiros encriptados e não encriptados. No entanto, uma cópia de thread único reduz significativamente o desempenho.           |
|/fft     | Utilize para reduzir a granularidade de carimbo de data/hora para qualquer sistema de ficheiros.        |
|/b     | Copia os ficheiros no modo de Cópia de Segurança.        |
|/z    | Copia os ficheiros no modo de Reinício. Utilize se o ambiente for instável. Esta opção reduz o débito devido ao registo adicional.      |
| /zb     | Utiliza o modo de Reinício. Se o acesso for negado, esta opção utiliza o modo de Cópia de Segurança. Esta opção reduz o débito devido ao ponto de verificação.         |
|/efsraw     | Copia todos os ficheiros encriptados no modo EFS não processado. Utilize apenas com ficheiros encriptados.         |
|log+:\<LogFile>| Anexa a saída ao ficheiro de registo existente.|    
 
O exemplo seguinte mostra a saída do comando do Robocopy para copiar ficheiros para o Data Box.

```output
C:\Users>robocopy

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:53 PM
        Simple Usage :: ROBOCOPY source destination /MIR

        source :: Source Directory (drive:\path or \\server\share\path).
        destination :: Destination Dir  (drive:\path or \\server\share\path).
                /MIR :: Mirror a complete directory tree.

    For more usage information run ROBOCOPY /?

    ****  /MIR can DELETE files as well as copy them !

C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\

        Files : *.*

        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60

    ------------------------------------------------------------------------------

    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------

                Total    Copied   Skipped  Mismatch    FAILED    Extras
    Dirs :         1         1         1         0         0         0
    Files :        17        17         0         0         0         0
    Bytes :     3.9 k     3.9 k         0         0         0         0
C:\Users>
```

Para otimizar o desempenho, utilize os seguintes parâmetros do Robocopy ao copiar os dados.

|    Plataforma    |    Principalmente ficheiros pequenos < 512 KB                           |    Principalmente ficheiros médios 512 KB - 1 MB                      |    Principalmente ficheiros grandes > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 sessões do Robocopy <br> 16 threads por sessões    |    3 sessões do Robocopy <br> 16 threads por sessões    |    2 sessões do Robocopy <br> 24 threads por sessões    |

Para obter mais informações sobre o comando do Robocopy, aceda a [Robocopy e alguns exemplos](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

Durante o processo de cópia, em caso de erros, verá uma notificação.

![Uma notificação de erro de cópia em Ligar e Copiar](media/data-box-deploy-copy-data/view-errors-1.png)

Selecione **Transferir lista de problemas**.

![Ligar, copiar e transferir a lista de problemas](media/data-box-deploy-copy-data/view-errors-2.png)

Abra a lista para ver os detalhes do erro e selecione o URL de resolução para ver a resolução recomendada.

![Ligar, copiar, transferir e ver erros](media/data-box-deploy-copy-data/view-errors-3.png)

Para obter mais informações, veja a secção Para obter mais informações, veja a secção [Ver registos de erros durante a cópia de dados para o Data Box](data-box-logs.md#view-error-log-during-data-copy). Para obter uma lista detalhada de erros durante a cópia de dados, veja a secção [Resolver problemas do Data Box](data-box-troubleshoot.md).

Para garantir a integridade dos dados, a soma de verificação é calculada inline à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço utilizado e o espaço livre no seu dispositivo.

![Verificar o espaço livre e utilizado no dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

Pode copiar dados do servidor de origem para o Data Box via SMB, NFS, REST, serviço de cópia de dados ou para discos geridos.

Em cada caso, confirme se os nomes das partilhas e das pastas e os tamanhos dos dados cumprem as diretrizes descritas nos [Limites de serviços do Armazenamento do Microsoft Azure e do Data Box](data-box-limits.md).

## <a name="copy-data-via-smb"></a>Copiar dados através de SMB

Para copiar dados através de SMB:

1. Se estiver a utilizar um sistema anfitrião do Windows, utilize o seguinte comando para se ligar às partilhas SMB:

    `\\<IP address of your device>\ShareName`

2. Para obter as credenciais de acesso de partilha, aceda à página **Ligar e copiar** na IU da Web local do Data Box.
3. Utilize uma ferramenta de cópia de ficheiros compatível com SMB, como o Robocopy, para copiar os dados para as partilhas. 

Para obter instruções passo a passo, aceda ao [Tutorial: Copiar dados para o Azure Data Box via SMB](data-box-deploy-copy-data.md).

## <a name="copy-data-via-nfs"></a>Copiar dados através de NFS

Para copiar dados através de NFS:

1. Se estiver a utilizar um sistema anfitrião NFS, utilize o seguinte comando para montar as partilhas NFS no Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Para obter as credenciais de acesso de partilha, aceda à página **Ligar e copiar** na IU da Web local do Data Box.
3. Utilize o comando `cp` ou `rsync` para copiar os dados.

Para obter instruções passo a passo, aceda ao [Tutorial: Copiar dados para o Azure Data Box via NFS](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-rest"></a>Copiar dados via REST

Para copiar dados via REST:

1. Para copiar dados através do Armazenamento de blobs do Data Box via APIs REST, pode ligar-se através de *http* ou *https*.
2. Para copiar dados para o Armazenamento de blobs do Data Box, pode utilizar o AzCopy.

Para obter instruções passo a passo, aceda ao [Tutorial: Copiar dados para o Armazenamento de blobs do Azure Data Box via APIs REST](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-data-copy-service"></a>Copiar dados através do serviço de cópia de dados

Para copiar dados através do serviço de cópia de dados:

1. Para copiar dados através do serviço de cópia de dados, tem de criar uma tarefa. Na IU da Web local do Data Box, aceda a **Gerir > Copiar dados > Criar**.
2. Preencha os parâmetros e crie uma tarefa.

Para obter instruções passo a passo, aceda ao [Tutorial: Utilizar o serviço de cópia de dados para copiar dados para o Azure Data Box](data-box-deploy-copy-data-via-copy-service.md).

## <a name="copy-data-to-managed-disks"></a>Copiar dados para discos geridos

Para copiar dados para discos geridos:

1. Ao encomendar o dispositivo Data Box, deve ter selecionado discos geridos como o destino de armazenamento.
2. Pode ligar-se ao Data Box via partilhas SMB ou NFS.
3. Em seguida, pode copiar os dados através das ferramentas SMB ou NFS.

Para obter instruções passo a passo, aceda ao [Tutorial: Utilizar o Data Box para importar dados como discos geridos no Azure](data-box-deploy-copy-data-from-vhds.md).

::: zone-end

::: zone target="docs"

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

::: zone-end