---
title: Copiar dados para o Microsoft Azure Data Box através do SMB | Documentos da Microsoft
description: Saiba como copiar dados para o Azure Data Box através de SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: alkohli
ms.openlocfilehash: 88ea0fee73441f98734058391e0599cf8e1142a3
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57403877"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Tutorial: Copiar dados para o Azure Data Box através de SMB

Este tutorial descreve como ligar e copiar dados do computador anfitrião com a IU web local.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ligar ao Data Box
> * Copiar dados para o Data Box


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Configurar o Azure Data Box](data-box-deploy-set-up.md).
2. Que recebeu o Data Box e é o estado da encomenda no portal **entregues**.
3. Tem um computador anfitrião com os dados que pretende copiar para o Data Box. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se uma ligação de 10 GbE não estiver disponível, utilize uma ligação de dados de 1 GbE, mas as velocidades de cópia serão afetadas.

## <a name="connect-to-data-box"></a>Ligar ao Data Box

Data Box com base na conta de armazenamento selecionada, cria até:
- Três partilhas para cada conta de armazenamento associada de GPv1 e GPv2.
- Um compartilhamento para armazenamento premium. 
- Um compartilhamento para conta de armazenamento de Blobs. 

Nas partilhas de blob de blocos e de blob de páginas, as entidades de primeiro nível são os contentores e as de segundo nível são os blobs. Nas partilhas de ficheiros do Azure, as entidades de primeiro nível são as partilhas e as de segundo nível são os ficheiros.

A tabela seguinte mostra o caminho UNC até as partilhas no seu URL de caminho Data Box e o armazenamento do Azure, onde os dados são carregados. O URL de caminho final do armazenamento do Azure pode derivar do caminho da partilha UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de blocos do Azure | <li>Caminho UNC para partilhas: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL de armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para partilhas: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL de armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Ficheiros do Azure       |<li>Caminho UNC para partilhas: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL de armazenamento do Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Se utilizar um computador de anfitrião do Windows Server, siga estes passos para ligar para o Data Box.

1. O primeiro passo é autenticar e iniciar uma sessão. Aceda a **Ligar e copiar**. Clique em **Obter credenciais** para obter as credenciais de acesso para as partilhas associadas à sua conta de armazenamento. 

    ![Obter credenciais de partilhas 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Na caixa de diálogo Aceder à partilha e copiar os dados, copie o **Nome de utilizador** e a **Palavra-passe** correspondentes à partilha. Clique em **OK**.
    
    ![Obter credenciais de partilhas 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Para aceder às partilhas associadas à conta de armazenamento (*devicemanagertest1* no exemplo a seguir) a partir do seu computador anfitrião, abra uma janela de comando. Na linha de comandos, escreva:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Os caminhos de partilha de acordo com seu formato de dados, são os seguintes:
    - Blob de blocos do Azure- `\\10.126.76.172\devicemanagertest1_BlockBlob`
    - BLOBs de página do Azure- `\\10.126.76.172\devicemanagertest1_PageBlob`
    - Ficheiros do Azure- `\\10.126.76.172\devicemanagertest1_AzFile`
    
4. Introduza a palavra-passe da partilha quando lhe for pedido. O exemplo seguinte mostra a ligação a uma partilha através do comando anterior.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Prima Windows + R. Na janela **Executar**, especifique o `\\<device IP address>`. Clique em **OK** para abrir o Explorador de ficheiros.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Deverá ver agora as partilhas como pastas.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)    

    **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada no blob de blocos e partilhas de blob de página representa um contentor para o qual os dados são carregados como blobs. Não é possível copiar o arquivos diretamente à *raiz* pasta na conta de armazenamento.
    
Se utilizar um cliente Linux, utilize o comando seguinte para montar a partilha SMB. O parâmetro de "vers=3.0,username" abaixo é a versão do SMB que suporte o anfitrião Linux. Plug-in a versão adequada no comando abaixo. Para versões do SMB que o Data Box suporta consulte [sistemas de ficheiros suportados por clientes Linux](https://docs.microsoft.com/en-us/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) 

    `sudo mount -t nfs -o vers=2.1 10.126.76.172:/devicemanagertest1_BlockBlob /home/databoxubuntuhost/databox`
    


## <a name="copy-data-to-data-box"></a>Copiar dados para o Data Box

Assim que estiver ligado às partilhas do Data Box, o passo seguinte é copiar dados. Antes de iniciar a cópia de dados, reveja as seguintes considerações:

- Certifique-se de que copia os dados para partilhas correspondentes para o formato de dados apropriados. Por exemplo, copie os dados de blobs de blocos para a partilha de blobs de blocos. Copie os VHDs para BLOBs de páginas. Se o formato de dados não corresponde ao tipo de partilha apropriadas, em seguida, num passo posterior, o carregamento de dados para o Azure irá falhar.
-  Ao copiar dados, certifique-se de que o tamanho dos dados está em conformidade com os limites de tamanho descritos a [armazenamento do Azure e limites do Data Box](data-box-limits.md).
- Se os dados, que estão a ser carregados pelo Data Box, forem carregados em simultâneo por outras aplicações fora do Data Box, isto pode resultar em falhas da tarefa de carregamento e danos nos dados.
- Recomendamos que:
    - Não utilize o SMB e NFS ao mesmo tempo.
    - Copie os mesmos dados para o mesmo destino final no Azure. 
     
  Nestes casos, não é possível determinar o resultado final.
- Sempre crie uma pasta para os ficheiros que pretende copiar abaixo da partilha e, em seguida, copie os ficheiros para essa pasta. A pasta criada no blob de blocos e partilhas de blob de página representa um contentor para o qual os dados são carregados como blobs. Não é possível copiar o arquivos diretamente à *raiz* pasta na conta de armazenamento.

Depois de se ligar à partilha de SMB, iniciar a cópia de dados. Pode utilizar qualquer ferramenta de cópia de ficheiros compatível com SMB, como o Robocopy, para copiar os dados. É possível iniciar várias tarefas com o Robocopy. Utilize o seguinte comando:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Os atributos são descritos na tabela seguinte.
    
|Atributo  |Descrição  |
|---------|---------|
|/e     |Copia os subdiretórios, incluindo diretórios vazios.         |
|/r:     |Especifica o número de repetições nas cópias falhadas.         |
|/w:     |Especifica o tempo de espera entre as repetições, em segundos.         |
|/is     |Inclui os mesmos ficheiros.         |
|/nfl     |Especifica que os nomes de ficheiro não tiver sessão iniciados.         |
|/ndl    |Especifica que os nomes de diretório não tiver sessão iniciados.        |
|/np     |Especifica que o progresso da operação de cópia (o número de ficheiros ou diretórios copiados até ao momento) não será apresentado. A apresentação do progresso reduz significativamente o desempenho.         |
|/MT     | Utilize multithreading (são recomendados 32 ou 64 threads). Esta opção não é utilizada com ficheiros encriptados. Pode ter de separar os ficheiros encriptados e não encriptados. No entanto, uma cópia de thread único reduz significativamente o desempenho.           |
|/fft     | Utilize para reduzir a granularidade de carimbo de data/hora para qualquer sistema de ficheiros.        |
|/b     | Copia os ficheiros no modo de Cópia de Segurança.        |
|/z    | Copia os ficheiros no modo de Reinício. Utilize se o ambiente for instável. Esta opção reduz o débito devido ao registo adicional.      |
| /zb     | Utiliza o modo de Reinício. Se o acesso for negado, esta opção utiliza o modo de Cópia de Segurança. Esta opção reduz o débito devido ao ponto de verificação.         |
|/efsraw     | Copia todos os ficheiros encriptados no modo EFS não processado. Utilize apenas com ficheiros encriptados.         |
|log+:<LogFile>| Anexa a saída ao ficheiro de registo existente.|    
 
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
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 sessões do Robocopy <br> 16 threads por sessões    |    3 sessões do Robocopy <br> 16 threads por sessões    |    2 sessões do Robocopy <br> 24 threads por sessões    |  |


Para obter mais informações sobre o comando do Robocopy, aceda a [Robocopy e alguns exemplos](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

Abra a pasta de destino para ver e verificar os ficheiros copiados. Se ocorrerem erros durante o processo de cópia, transfira os ficheiros de erro para resolução de problemas.

Para garantir a integridade dos dados, a soma de verificação é calculada inline à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço utilizado e o espaço livre no seu dispositivo.
    
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

