---
title: Tutorial para copiar dados via SMB em Azure Data Box | Microsoft Docs
description: Saiba como copiar dados para seu Azure Data Box via SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: alkohli
ms.openlocfilehash: 9f5ccc255310ca42ef39586860c0861b945ac6e9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098889"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Tutorial: Copiar dados para Azure Data Box via SMB

::: zone-end

::: zone target="chromeless"

# <a name="copy-data-to-azure-data-box"></a>Copiar dados para Azure Data Box

::: zone-end

::: zone target="docs"

Este tutorial descreve como se conectar e copiar dados do seu computador host usando a interface do usuário da Web local.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar ao Data Box
> * Copiar dados para o Data Box


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Você concluiu o [tutorial: Configurar Azure Data Box](data-box-deploy-set-up.md).
2. Você recebeu sua Data Box e o status do pedido no portal é **entregue**.
3. Tem um computador anfitrião com os dados que pretende copiar para o Data Box. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se uma conexão de 10 GbE não estiver disponível, use um link de dados de 1 GbE, mas as velocidades de cópia serão afetadas.

## <a name="connect-to-data-box"></a>Ligar ao Data Box

Com base na conta de armazenamento selecionada, Data Box cria até:
- Três partilhas para cada conta de armazenamento associada de GPv1 e GPv2.
- Um compartilhamento para armazenamento Premium.
- Um compartilhamento para a conta de armazenamento de BLOBs.

Nas partilhas de blob de blocos e de blob de páginas, as entidades de primeiro nível são os contentores e as de segundo nível são os blobs. Nas partilhas de ficheiros do Azure, as entidades de primeiro nível são as partilhas e as de segundo nível são os ficheiros.

A tabela a seguir mostra o caminho UNC para os compartilhamentos no Data Box e a URL do caminho de armazenamento do Azure onde os dados são carregados. A URL final do caminho de armazenamento do Azure pode ser derivada do caminho de compartilhamento UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de blocos do Azure | <li>Caminho UNC para compartilhamentos:`\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL de armazenamento do Azure:`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para compartilhamentos:`\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL de armazenamento do Azure:`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Ficheiros do Azure       |<li>Caminho UNC para compartilhamentos:`\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL de armazenamento do Azure:`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Se estiver usando um computador host do Windows Server, siga estas etapas para se conectar ao Data Box.

1. O primeiro passo é autenticar e iniciar uma sessão. Aceda a **Ligar e copiar**. Clique em **Obter credenciais** para obter as credenciais de acesso para as partilhas associadas à sua conta de armazenamento. 

    ![Obter credenciais de partilhas 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Na caixa de diálogo Aceder à partilha e copiar os dados, copie o **Nome de utilizador** e a **Palavra-passe** correspondentes à partilha. Clique em **OK**.
    
    ![Obter credenciais de partilhas 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Para acessar os compartilhamentos associados à sua conta de armazenamento (*devicemanagertest1* no exemplo a seguir) do seu computador host, abra uma janela de comando. Na linha de comandos, escreva:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Consoante o seu formato de dados, os caminhos de partilha são os seguintes:
    - Blob de blocos do Azure-`\\10.126.76.172\devicemanagertest1_BlockBlob`
    - Blob de páginas do Azure-`\\10.126.76.172\devicemanagertest1_PageBlob`
    - Arquivos do Azure-`\\10.126.76.172\devicemanagertest1_AzFile`
    
4. Introduza a palavra-passe da partilha quando lhe for pedido. O exemplo seguinte mostra a ligação a uma partilha através do comando anterior.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Prima Windows + R. Na janela **Executar**, especifique o `\\<device IP address>`. Clique em **OK** para abrir o explorador de arquivos.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Agora você deve ver os compartilhamentos como pastas.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)    

    **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada em blobs de blocos e blobs de páginas representa um contêiner para o qual os dados são carregados como BLOBs. Não é possível copiar arquivos diretamente para a pasta *raiz* na conta de armazenamento.
    
Se estiver usando um cliente Linux, use o comando a seguir para montar o compartilhamento SMB. O parâmetro "versa" abaixo é a versão do SMB com suporte do seu host Linux. Conecte a versão apropriada no comando a seguir. Para versões do SMB que o Data Box dá suporte, consulte [sistemas de arquivos com suporte para clientes Linux](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) 

    `sudo mount -t nfs -o vers=2.1 10.126.76.172:/devicemanagertest1_BlockBlob /home/databoxubuntuhost/databox`
    


## <a name="copy-data-to-data-box"></a>Copiar dados para o Data Box

Quando você estiver conectado aos compartilhamentos de Data Box, a próxima etapa será copiar dados. Antes de começar a cópia de dados, examine as seguintes considerações:

- Certifique-se de copiar os dados para compartilhamentos que correspondam ao formato de dados apropriado. Por exemplo, copie os dados de blobs de blocos para a partilha de blobs de blocos. Copie os VHDs para o blob de páginas. Se o formato de dados não corresponder ao tipo de compartilhamento apropriado, em uma etapa posterior, o carregamento de dados para o Azure falhará.
-  Ao copiar dados, verifique se o tamanho dos dados está de acordo com os limites de tamanho descritos nos [limites de armazenamento e data box do Azure](data-box-limits.md).
- Se os dados, que estão a ser carregados pelo Data Box, forem carregados em simultâneo por outras aplicações fora do Data Box, isto pode resultar em falhas da tarefa de carregamento e danos nos dados.
- Recomendamos que:
    - Você não usa SMB e NFS ao mesmo tempo.
    - Copie os mesmos dados para o mesmo destino final no Azure. 
     
  Nesses casos, o resultado final não pode ser determinado.
- Sempre crie uma pasta para os arquivos que você pretende copiar sob o compartilhamento e, em seguida, copie os arquivos para essa pasta. A pasta criada em blobs de blocos e blobs de páginas representa um contêiner para o qual os dados são carregados como BLOBs. Não é possível copiar arquivos diretamente para a pasta *raiz* na conta de armazenamento.

Depois de se conectar ao compartilhamento SMB, comece a cópia de dados. Pode utilizar qualquer ferramenta de cópia de ficheiros compatível com SMB, como o Robocopy, para copiar os dados. É possível iniciar várias tarefas com o Robocopy. Utilize o seguinte comando:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Os atributos são descritos na tabela seguinte.
    
|Atributo  |Descrição  |
|---------|---------|
|/e     |Copia os subdiretórios, incluindo diretórios vazios.         |
|/r:     |Especifica o número de repetições nas cópias falhadas.         |
|/w:     |Especifica o tempo de espera entre as repetições, em segundos.         |
|/is     |Inclui os mesmos ficheiros.         |
|/nfl     |Especifica que os nomes de arquivo não são registrados.         |
|/ndl    |Especifica que os nomes de diretório não são registrados.        |
|/np     |Especifica que o progresso da operação de cópia (o número de ficheiros ou diretórios copiados até ao momento) não será apresentado. A apresentação do progresso reduz significativamente o desempenho.         |
|/MT     | Utilize multithreading (são recomendados 32 ou 64 threads). Esta opção não é utilizada com ficheiros encriptados. Pode ter de separar os ficheiros encriptados e não encriptados. No entanto, uma cópia de thread único reduz significativamente o desempenho.           |
|/fft     | Utilize para reduzir a granularidade de carimbo de data/hora para qualquer sistema de ficheiros.        |
|/b     | Copia os ficheiros no modo de Cópia de Segurança.        |
|/z    | Copia os ficheiros no modo de Reinício. Utilize se o ambiente for instável. Esta opção reduz o débito devido ao registo adicional.      |
| /zb     | Utiliza o modo de Reinício. Se o acesso for negado, esta opção utiliza o modo de Cópia de Segurança. Esta opção reduz o débito devido ao ponto de verificação.         |
|/efsraw     | Copia todos os ficheiros encriptados no modo EFS não processado. Utilize apenas com ficheiros encriptados.         |
|log +:\<logfile >| Anexa a saída ao ficheiro de registo existente.|    
 
O exemplo seguinte mostra a saída do comando do Robocopy para copiar ficheiros para o Data Box.
    
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
       

Para otimizar o desempenho, utilize os seguintes parâmetros do Robocopy ao copiar os dados.

|    Plataforma    |    Principalmente ficheiros pequenos < 512 KB                           |    Principalmente ficheiros médios 512 KB - 1 MB                      |    Principalmente ficheiros grandes > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 sessões do Robocopy <br> 16 threads por sessões    |    3 sessões do Robocopy <br> 16 threads por sessões    |    2 sessões do Robocopy <br> 24 threads por sessões    |


Para obter mais informações sobre o comando do Robocopy, aceda a [Robocopy e alguns exemplos](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

Abra a pasta de destino para ver e verificar os ficheiros copiados. Se ocorrerem erros durante o processo de cópia, transfira os ficheiros de erro para resolução de problemas. Para obter mais informações, consulte [Exibir logs de erros durante a cópia de dados para data Box](data-box-logs.md#view-error-log-during-data-copy). Para obter uma lista detalhada de erros durante a cópia de dados, consulte [solucionar problemas data Box](data-box-troubleshoot.md).

Para garantir a integridade dos dados, a soma de verificação é calculada inline à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço utilizado e o espaço livre no seu dispositivo.
    
   ![Verificar o espaço livre e utilizado no dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

Você pode copiar dados do seu servidor de origem para seu Data Box por meio de SMB, NFS, REST, serviço de cópia de dados ou para discos gerenciados.

Em cada caso, verifique se os nomes de compartilhamento e pasta e o tamanho dos dados seguem as diretrizes descritas nos [limites do serviço de armazenamento e data box do Azure](data-box-limits.md).

## <a name="copy-data-via-smb"></a>Copiar dados através de SMB

1. Se estiver usando um host do Windows, use o seguinte comando para se conectar aos compartilhamentos SMB:

    `\\<IP address of your device>\ShareName`

2. Para obter as credenciais de acesso de partilha, aceda à página **Ligar e copiar** na IU da Web local do Data Box.
3. Use uma ferramenta de cópia de arquivo compatível com SMB, como o Robocopy, para copiar dados para compartilhamentos. 

Para obter instruções passo a passo, acesse [tutorial: Copie dados para Azure Data Box via SMB](data-box-deploy-copy-data.md).

## <a name="copy-data-via-nfs"></a>Copiar dados através de NFS

1. Se estiver usando um host NFS, use o seguinte comando para montar os compartilhamentos NFS em seu Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Para obter as credenciais de acesso de partilha, aceda à página **Ligar e copiar** na IU da Web local do Data Box.
3. Use `cp` o `rsync` comando ou para copiar seus dados.

Para obter instruções passo a passo, acesse [tutorial: Copie dados para Azure Data Box via NFS](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-rest"></a>Copiar dados via REST

1. Para copiar dados usando Data Box armazenamento de BLOBs por meio de APIs REST, você pode se conectar via *http* ou *https*.
2. Para copiar dados para Data Box armazenamento de BLOBs, você pode usar AzCopy.

Para obter instruções passo a passo, acesse [tutorial: Copie dados para Azure Data Box armazenamento de BLOBs por](data-box-deploy-copy-data-via-nfs.md)meio de APIs REST.

## <a name="copy-data-via-data-copy-service"></a>Copiar dados por meio do serviço de cópia de dados

1. Para copiar dados usando o serviço de cópia de dados, você precisa criar um trabalho. Na interface do usuário da Web local do seu Data Box, vá para **gerenciar > copiar dados > criar**. 
2. Preencha os parâmetros e crie um trabalho.

Para obter instruções passo a passo, acesse [tutorial: Use o serviço de cópia de dados para copiar dados](data-box-deploy-copy-data-via-copy-service.md)em Azure data box.

## <a name="copy-data-to-managed-disks"></a>Copiar dados para discos gerenciados

1. Ao solicitar o dispositivo Data Box, você deve ter selecionado Managed disks como seu destino de armazenamento.
2. Você pode se conectar a Data Box por meio de compartilhamentos SMB ou NFS.
3. Em seguida, você pode copiar dados por meio de ferramentas SMB ou NFS.

Para obter instruções passo a passo, acesse [tutorial: Use Data Box para importar dados como discos gerenciados no](data-box-deploy-copy-data-from-vhds.md)Azure.

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar ao Data Box
> * Copiar dados para o Data Box


Avance para o próximo tutorial para aprender a enviar seus Data Box de volta à Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)

::: zone-end

