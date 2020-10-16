---
title: Tutorial para copiar dados via SMB no Azure Data Box Heavy | Microsoft Docs
description: Neste tutorial, saiba como ligar e copiar os dados do computador anfitrião para o Azure Data Box Heavy por SMB através da IU Web local.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 77dc64d9660f9a0bf66559c4a5a976362cf1acd0
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951643"
---
::: zone target = "docs"

# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Tutorial: Copiar dados para o Azure Data Box Heavy via SMB

::: zone-end

::: zone target = "chromeless"

## <a name="copy-data-to-azure-data-box-heavy"></a>Copiar dados para o Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

Este tutorial descreve como ligar e copiar dados do computador anfitrião com a IU Web local.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ligar ao Data Box Heavy
> * Copiar dados para o Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Pode copiar dados do servidor de origem para o Data Box via SMB, NFS, REST, serviço de cópia de dados ou para discos geridos.

Em cada caso, confirme se os nomes das partilhas e das pastas e os tamanhos dos dados cumprem as diretrizes descritas nos [Limites de serviços do Armazenamento do Microsoft Azure e do Data Box Heavy](data-box-heavy-limits.md).

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Configurar o Azure Data Box Heavy](data-box-deploy-set-up.md).
2. Recebeu o Data Box Heavy e o estado da encomenda no portal é **Entregue**.
3. Tem um computador anfitrião com os dados que quer copiar para o Data Box Heavy. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Para velocidades de cópia mais rápidas, podem ser utilizadas em paralelo duas ligações de 40 GbE (uma por nó). Se não tiver uma ligação de 40 GbE disponível, recomendamos que tenha pelo menos duas ligações de 10 GbE (uma por nó).
   

## <a name="connect-to-data-box-heavy-shares"></a>Ligar às partilhas do Data Box Heavy

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
 
| Armazenamento           | Caminho UNC                                                                       |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de bloco do Azure | <li>Caminho UNC para as partilhas: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL de Armazenamento do Microsoft Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para as partilhas: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL do Armazenamento do Microsoft Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Ficheiros do Azure       |<li>Caminho UNC para as partilhas: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL de Armazenamento do Microsoft Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Os passos para se ligar através de um cliente Windows ou Linux são diferentes.

> [!NOTE]
> Siga os mesmos passos para se ligar a ambos os nós do dispositivo em paralelo.

### <a name="connect-on-a-windows-system"></a>Ligar-se num sistema Windows

Se estiver a utilizar um computador anfitrião do Windows Server, execute estes passos para se ligar ao Data Box Heavy.

1. O primeiro passo é autenticar e iniciar uma sessão. Aceda a **Ligar e copiar**. Clique em **Obter credenciais** para obter as credenciais de acesso para as partilhas associadas à sua conta de armazenamento.

    ![Obter credenciais de partilhas](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. Na caixa de diálogo Aceder à partilha e copiar os dados, copie o **Nome de utilizador** e a **Palavra-passe** correspondentes à partilha. Clique em **OK**.
    
    ![Obter credenciais de partilhas 2](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Para aceder às partilhas associadas à conta de armazenamento (*databoxe2etest* no exemplo a seguir) a partir do computador anfitrião, abra uma janela de comando. Na linha de comandos, escreva:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Consoante o seu formato de dados, os caminhos de partilha são os seguintes:
    - Blob de blocos do Azure – `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Blob de páginas do Azure – `\\10.100.10.100\databoxe2etest_PageBlob`
    - Ficheiros do Azure – `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Introduza a palavra-passe da partilha quando lhe for pedido. O exemplo seguinte mostra a ligação a uma partilha através do comando anterior.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Prima Windows + R. Na janela **Executar**, especifique o `\\<device IP address>`. Clique em **OK** para abrir o Explorador de Ficheiros.
    
    ![Ligar à partilha através do Explorador de Ficheiros](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Agora deve conseguir ver as partilhas como pastas.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada nas partilhas dos blobs de blocos e dos blobs de páginas representa um contentor para o qual os dados são carregados como blobs. Não pode copiar ficheiros diretamente para a pasta *raiz* na conta de armazenamento.
    
### <a name="connect-on-a-linux-system"></a>Ligar-se num sistema Linux

Se estiver a utilizar um cliente Linux, utilize o comando a seguir para montar a partilha SMB.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

O parâmetro `vers` é a versão do SMB compatível com o sistema anfitrião Linux. Ligue a versão apropriada no comando acima.

Para as versões do SMB compatíveis com o Data Box Heavy, veja a secção [Sistemas de ficheiros suportados pelos clientes Linux](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Copiar dados para o Data Box Heavy

Assim que estiver ligado às partilhas do Data Box Heavy, o passo seguinte é copiar os dados.

### <a name="copy-considerations"></a>Considerações relacionadas com a cópia

Antes de começar a cópia de dados, reveja as seguintes considerações:

- Verifique se copia os dados para as partilhas que correspondem ao formato de dados apropriado. Por exemplo, copie os dados de blobs de blocos para a partilha de blobs de blocos. Copie os VHDs para o blob de páginas.

    Se o formato de dados não corresponder ao tipo de partilha apropriado, num passo posterior, o carregamento de dados para o Azure falhará.
-  Ao copiar os dados, confirme se o tamanho está em conformidade com os limites descritos em [Limites de armazenamento do Azure e do Data Box Heavy](data-box-heavy-limits.md).
- Se os dados, que estão a ser carregados pelo Data Box Heavy, forem carregados em simultâneo por outras aplicações fora do Data Box Heavy, poderá resultar em falhas da tarefa de carregamento e danos nos dados.
- É recomendável que:
    - Não utilize o SMB e o NFS ao mesmo tempo.
    - Copie os mesmos dados para o mesmo destino final no Azure.
     
  Nestes casos, não é possível determinar o resultado final.
- Crie sempre uma pasta para os ficheiros que quer copiar na partilha e, em seguida, copie os ficheiros para essa pasta. A pasta criada nas partilhas dos blobs de blocos e dos blobs de páginas representa um contentor para o qual os dados são carregados como blobs. Não pode copiar ficheiros diretamente para a pasta *raiz* na conta de armazenamento.

Depois de ligar à partilha SMB, inicie a cópia dos dados.

1. Pode utilizar qualquer ferramenta de cópia de ficheiros compatível com SMB, como o Robocopy, para copiar os dados. É possível iniciar várias tarefas com o Robocopy. Utilize o seguinte comando:
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    Os atributos são descritos na tabela seguinte.
    
    |Atributo  |Descrição  |
    |---------|---------|
    |/e      |Copia os subdiretórios, incluindo diretórios vazios.         |
    |/r:     |Especifica o número de repetições nas cópias falhadas.         |
    |/w:     |Especifica o tempo de espera entre as repetições, em segundos.         |
    |/is     |Inclui os mesmos ficheiros.         |
    |/nfl    |Especifica que os nomes de ficheiro não estão registados.         |
    |/ndl    |Especifica que os nomes de diretório não estão registados.        |
    |/np     |Especifica que o progresso da operação de cópia (o número de ficheiros ou diretórios copiados até ao momento) não será apresentado. A apresentação do progresso reduz significativamente o desempenho.         |
    |/MT     | Utilize multithreading (são recomendados 32 ou 64 threads). Esta opção não é utilizada com ficheiros encriptados. Pode ter de separar os ficheiros encriptados e não encriptados. No entanto, uma cópia de thread único reduz significativamente o desempenho.           |
    |/fft    | Utilize para reduzir a granularidade de carimbo de data/hora para qualquer sistema de ficheiros.        |
    |/b      | Copia os ficheiros no modo de Cópia de Segurança.        |
    |/z      | Copia os ficheiros no modo de Reinício. Utilize se o ambiente for instável. Esta opção reduz o débito devido ao registo adicional.      |
    | /zb    | Utiliza o modo de Reinício. Se o acesso for negado, esta opção utiliza o modo de Cópia de Segurança. Esta opção reduz o débito devido ao ponto de verificação.         |
    |/efsraw | Copia todos os ficheiros encriptados no modo EFS não processado. Utilize apenas com ficheiros encriptados.         |
    |log+:\<LogFile>| Anexa a saída ao ficheiro de registo existente.|
    
 
    O exemplo seguinte mostra a saída do comando robocopy para copiar ficheiros para o Data Box Heavy.

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
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

2. Para otimizar o desempenho, utilize os seguintes parâmetros do Robocopy ao copiar os dados. (Os números abaixo representam os melhores cenários).

    | Plataforma    | Principalmente ficheiros pequenos < 512 KB    | Principalmente ficheiros médios de 512 KB a 1 MB  | Principalmente ficheiros grandes > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 sessões do Robocopy <br> 24 threads por sessões | 6 sessões do Robocopy <br> 16 threads por sessões | 6 sessões do Robocopy <br> 16 threads por sessões |


    Para obter mais informações sobre o comando do Robocopy, aceda a [Robocopy e alguns exemplos](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

3. Abra a pasta de destino para ver e verificar os ficheiros copiados.

    ![Ver os ficheiros copiados](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. À medida que os dados são copiados:

    - Os nomes, tamanhos e formato dos ficheiros são validados para garantir que cumprem os limites de armazenamento e de objetos do Azure, bem como as convenções de nomenclatura dos contentores e dos ficheiros do Azure.
    - Para garantir a integridade dos dados, a soma de verificação também é calculada inline.

    Se ocorrerem erros durante o processo de cópia, transfira os ficheiros de erro para resolução de problemas. Selecione o ícone de seta para transferir os ficheiros de erro.

    ![Transferir ficheiros de erro](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Para obter mais informações, veja a secção [Ver registos de erros durante a cópia de dados para o Data Box Heavy](data-box-logs.md#view-error-log-during-data-copy). Para obter uma lista detalhada de erros durante a cópia de dados, veja a secção [Resolver problemas do Data Box Heavy](data-box-troubleshoot.md).

5. Abra o ficheiro de erros no Bloco de notas. O ficheiro de erros a seguir indica que os dados não estão corretamente alinhados.

    ![Abrir o ficheiro de erros](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Num blob de páginas, os dados têm de ter um alinhamento de 512 bytes. Uma vez estes dados removidos, o erro ficará resolvido conforme mostrado na seguinte captura de ecrã.

    ![Erro resolvido](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Uma vez concluída a cópia, aceda à página **Ver Dashboard**. Verifique o espaço utilizado e o espaço livre no dispositivo.
    
    ![Verificar o espaço livre e utilizado no dashboard](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Repita os passos acima para copiar dados para o segundo nó do dispositivo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Heavy, como:

> [!div class="checklist"]
> * Ligar ao Data Box Heavy
> * Copiar dados para o Data Box Heavy


Avance para o tutorial seguinte para saber como enviar o Data Box Heavy de volta para a Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box Heavy para a Microsoft](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>Copiar dados através de SMB

1. Se estiver a utilizar um sistema anfitrião do Windows, utilize o seguinte comando para se ligar às partilhas SMB:

    `\\<IP address of your device>\ShareName`

2. Para obter as credenciais de acesso de partilha, aceda à página **Ligar e copiar** na IU da Web local do Data Box.

3. Utilize uma ferramenta de cópia de ficheiros compatível com SMB, como o Robocopy, para copiar os dados para as partilhas.

Para obter instruções passo a passo, aceda ao [Tutorial: Copiar dados para o Azure Data Box via SMB](data-box-heavy-deploy-copy-data.md).

### <a name="copy-data-via-nfs"></a>Copiar dados através de NFS

1. Se estiver a utilizar um sistema anfitrião NFS, utilize o seguinte comando para montar as partilhas NFS:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Para obter as credenciais de acesso às partilhas, aceda à página **Ligar e copiar** na IU da Web local do Data Box Heavy.
3. Utilize o comando `cp` ou `rsync` para copiar os dados. 
4. Repita estes passos para se ligar e copiar os dados para o segundo nó do Data Box Heavy.

Para obter instruções passo a passo, aceda ao [Tutorial: Copiar dados para o Azure Data Box via NFS](data-box-heavy-deploy-copy-data-via-nfs.md).

### <a name="copy-data-via-rest"></a>Copiar dados via REST

1. Para copiar dados através do Armazenamento de blobs do Data Box via APIs REST, pode ligar-se através de *http* ou *https*.
2. Para copiar dados para o Armazenamento de blobs do Data Box, pode utilizar o AzCopy.
3. Repita estes passos para se ligar e copiar os dados para o segundo nó do Data Box Heavy.

Para obter instruções passo a passo, aceda ao [Tutorial: Copiar dados para o Armazenamento de blobs do Azure Data Box via APIs REST](data-box-heavy-deploy-copy-data-via-rest.md).

### <a name="copy-data-via-data-copy-service"></a>Copiar dados através do serviço de cópia de dados

1. Para copiar dados através do serviço de cópia de dados, tem de criar uma tarefa. Na IU da Web local do Data Box Heavy, aceda a **Gerir > Copiar dados > Criar**.
2. Preencha os parâmetros e crie uma tarefa.
3. Repita estes passos para se ligar e copiar os dados para o segundo nó do Data Box Heavy.

Para obter instruções passo a passo, aceda ao [Tutorial: Utilizar o serviço de cópia de dados para copiar dados para o Azure Data Box Heavy](data-box-heavy-deploy-copy-data-via-copy-service.md).

### <a name="copy-data-to-managed-disks"></a>Copiar dados para discos geridos

1. Ao encomendar o dispositivo Data Box Heavy, deve ter selecionado discos geridos como o destino de armazenamento.
2. Pode ligar-se ao Data Box Heavy via partilhas SMB ou NFS.
3. Em seguida, pode copiar os dados através das ferramentas SMB ou NFS.
4. Repita estes passos para se ligar e copiar os dados para o segundo nó do Data Box Heavy.

Para obter instruções passo a passo, aceda ao [Tutorial: Utilizar o Data Box Heavy para importar dados como discos geridos no Azure](data-box-heavy-deploy-copy-data-from-vhds.md).

::: zone-end


