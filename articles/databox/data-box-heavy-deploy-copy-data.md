---
title: Tutorial para copiar dados via SMB em Azure Data Box Heavy | Microsoft Docs
description: Saiba como copiar dados para seu Azure Data Box Heavy via SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 4267b8299e13f1705b218e65b268c45bd5a658e2
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240305"
---
::: zone target = "docs"

# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Tutorial: Copiar dados para Azure Data Box Heavy via SMB

::: zone-end

::: zone target = "chromeless"

## <a name="copy-data-to-azure-data-box-heavy"></a>Copiar dados para Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

Este tutorial descreve como se conectar e copiar dados do seu computador host usando a interface do usuário da Web local.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conectar-se ao Data Box Heavy
> * Copiar dados para Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Você pode copiar dados do seu servidor de origem para seu Data Box por meio de SMB, NFS, REST, serviço de cópia de dados ou para discos gerenciados.

Em cada caso, verifique se os nomes de compartilhamento e pasta e o tamanho dos dados seguem as diretrizes descritas nos [limites do serviço de armazenamento e data Box Heavy do Azure](data-box-heavy-limits.md).

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Você concluiu o [tutorial: Configurar Azure Data Box Heavy](data-box-deploy-set-up.md).
2. Você recebeu sua Data Box Heavy e o status do pedido no portal é **entregue**.
3. Você tem um computador host que tem os dados que deseja copiar para Data Box Heavy. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Para velocidades de cópia mais rápidas, conexões 2 40-GbE (um por nó) podem ser utilizadas em paralelo. Se você não tiver a conexão 40-GbE disponível, recomendamos que você tenha pelo menos conexões 2 10-GbE (uma por nó).
   

## <a name="connect-to-data-box-heavy-shares"></a>Conectar-se a compartilhamentos de Data Box Heavy

Com base na conta de armazenamento selecionada, Data Box Heavy cria até:
- Três partilhas para cada conta de armazenamento associada de GPv1 e GPv2.
- Um compartilhamento para armazenamento Premium.
- Um compartilhamento para a conta de armazenamento de BLOBs.

Esses compartilhamentos são criados em ambos os nós do dispositivo.

Em blob de blocos e compartilhamentos de blob de páginas:
- As entidades de primeiro nível são contêineres.
- As entidades de segundo nível são BLOBs.

Em compartilhamentos para os arquivos do Azure:
- As entidades de primeiro nível são compartilhamentos.
- As entidades de segundo nível são arquivos.

A tabela a seguir mostra o caminho UNC para os compartilhamentos no Data Box Heavy e a URL do caminho de armazenamento do Azure onde os dados são carregados. A URL final do caminho de armazenamento do Azure pode ser derivada do caminho de compartilhamento UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de blocos do Azure | <li>Caminho UNC para compartilhamentos:`\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL de armazenamento do Azure:`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para compartilhamentos:`\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL de armazenamento do Azure:`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Ficheiros do Azure       |<li>Caminho UNC para compartilhamentos:`\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL de armazenamento do Azure:`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

As etapas para se conectar usando um cliente Windows ou Linux são diferentes.

> [!NOTE]
> Siga as mesmas etapas para se conectar a ambos os nós do dispositivo em paralelo.

### <a name="connect-on-a-windows-system"></a>Conectar-se em um sistema Windows

Se estiver usando um computador host do Windows Server, siga estas etapas para se conectar ao Data Box Heavy.

1. O primeiro passo é autenticar e iniciar uma sessão. Aceda a **Ligar e copiar**. Clique em **Obter credenciais** para obter as credenciais de acesso para as partilhas associadas à sua conta de armazenamento.

    ![Obter credenciais de partilhas 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. Na caixa de diálogo Aceder à partilha e copiar os dados, copie o **Nome de utilizador** e a **Palavra-passe** correspondentes à partilha. Clique em **OK**.
    
    ![Obter credenciais de partilhas 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Para acessar os compartilhamentos associados à sua conta de armazenamento (*databoxe2etest* no exemplo a seguir) do seu computador host, abra uma janela de comando. Na linha de comandos, escreva:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Consoante o seu formato de dados, os caminhos de partilha são os seguintes:
    - Blob de blocos do Azure-`\\10.100.10.100\databoxe2etest_BlockBlob`
    - Blob de páginas do Azure-`\\10.100.10.100\databoxe2etest_PageBlob`
    - Arquivos do Azure-`\\10.100.10.100\databoxe2etest_AzFile`
    
4. Introduza a palavra-passe da partilha quando lhe for pedido. O exemplo seguinte mostra a ligação a uma partilha através do comando anterior.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Prima Windows + R. Na janela **Executar**, especifique o `\\<device IP address>`. Clique em **OK** para abrir o explorador de arquivos.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Agora você deve ver os compartilhamentos como pastas.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada em blobs de blocos e blobs de páginas representa um contêiner para o qual os dados são carregados como BLOBs. Não é possível copiar arquivos diretamente para a pasta *raiz* na conta de armazenamento.
    
### <a name="connect-on-a-linux-system"></a>Conectar-se em um sistema Linux

Se estiver usando um cliente Linux, use o comando a seguir para montar o compartilhamento SMB.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

O `vers` parâmetro é a versão do SMB com suporte no host do Linux. Conecte a versão apropriada no comando acima.

Para versões do SMB às quais o Data Box Heavy dá suporte, consulte [sistemas de arquivos com suporte para clientes Linux](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Copiar dados para Data Box Heavy

Quando você estiver conectado aos compartilhamentos de Data Box Heavy, a próxima etapa será copiar dados.

### <a name="copy-considerations"></a>Considerações de cópia

Antes de começar a cópia de dados, examine as seguintes considerações:

- Certifique-se de copiar os dados para compartilhamentos que correspondam ao formato de dados apropriado. Por exemplo, copie os dados de blobs de blocos para a partilha de blobs de blocos. Copie os VHDs para o blob de páginas.

    Se o formato de dados não corresponder ao tipo de compartilhamento apropriado, em uma etapa posterior, o carregamento de dados para o Azure falhará.
-  Ao copiar dados, verifique se o tamanho dos dados está de acordo com os limites de tamanho descritos nos [limites de armazenamento e data Box Heavy do Azure](data-box-heavy-limits.md).
- Se os dados, que estão sendo carregados pelo Data Box Heavy, forem carregados simultaneamente por outros aplicativos fora do Data Box Heavy, isso poderá resultar em falhas de trabalho de upload e corrupção de dados.
- Recomendamos que:
    - Você não usa SMB e NFS ao mesmo tempo.
    - Copie os mesmos dados para o mesmo destino final no Azure.
     
  Nesses casos, o resultado final não pode ser determinado.
- Sempre crie uma pasta para os arquivos que você pretende copiar sob o compartilhamento e, em seguida, copie os arquivos para essa pasta. A pasta criada em blobs de blocos e blobs de páginas representa um contêiner para o qual os dados são carregados como BLOBs. Não é possível copiar arquivos diretamente para a pasta *raiz* na conta de armazenamento.

Depois de se conectar ao compartilhamento SMB, comece a cópia de dados.

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
    |/nfl    |Especifica que os nomes de arquivo não são registrados.         |
    |/ndl    |Especifica que os nomes de diretório não são registrados.        |
    |/np     |Especifica que o progresso da operação de cópia (o número de ficheiros ou diretórios copiados até ao momento) não será apresentado. A apresentação do progresso reduz significativamente o desempenho.         |
    |/MT     | Utilize multithreading (são recomendados 32 ou 64 threads). Esta opção não é utilizada com ficheiros encriptados. Pode ter de separar os ficheiros encriptados e não encriptados. No entanto, uma cópia de thread único reduz significativamente o desempenho.           |
    |/fft    | Utilize para reduzir a granularidade de carimbo de data/hora para qualquer sistema de ficheiros.        |
    |/b      | Copia os ficheiros no modo de Cópia de Segurança.        |
    |/z      | Copia os ficheiros no modo de Reinício. Utilize se o ambiente for instável. Esta opção reduz o débito devido ao registo adicional.      |
    | /zb    | Utiliza o modo de Reinício. Se o acesso for negado, esta opção utiliza o modo de Cópia de Segurança. Esta opção reduz o débito devido ao ponto de verificação.         |
    |/efsraw | Copia todos os ficheiros encriptados no modo EFS não processado. Utilize apenas com ficheiros encriptados.         |
    |log +:\<logfile >| Anexa a saída ao ficheiro de registo existente.|
    
 
    O exemplo a seguir mostra a saída do comando Robocopy para copiar arquivos para o Data Box Heavy.

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

2. Para otimizar o desempenho, utilize os seguintes parâmetros do Robocopy ao copiar os dados. (Os números abaixo representam os cenários de melhor caso).

    | Plataforma    | Principalmente ficheiros pequenos < 512 KB    | Principalmente arquivos médios 512 KB-1 MB  | Principalmente ficheiros grandes > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 sessões do Robocopy <br> 24 threads por sessões | 6 sessões do Robocopy <br> 16 threads por sessões | 6 sessões do Robocopy <br> 16 threads por sessões |


    Para obter mais informações sobre o comando do Robocopy, aceda a [Robocopy e alguns exemplos](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

3. Abra a pasta de destino para ver e verificar os ficheiros copiados.

    ![Exibir arquivos copiados](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. À medida que os dados são copiados:

    - Os nomes de arquivo, os tamanhos e o formato são validados para garantir que eles atendam os limites de armazenamento e de objeto do Azure, bem como as convenções de nomenclatura de contêiner e arquivo do Azure.
    - Para garantir a integridade dos dados, a soma de verificação também é calculada em linha.

    Se ocorrerem erros durante o processo de cópia, transfira os ficheiros de erro para resolução de problemas. Selecione o ícone de seta para baixar os arquivos de erro.

    ![Baixar arquivos de erro](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Para obter mais informações, consulte [Exibir logs de erros durante a cópia de dados para data Box Heavy](data-box-logs.md#view-error-log-during-data-copy). Para obter uma lista detalhada de erros durante a cópia de dados, consulte [solucionar problemas data Box Heavy](data-box-troubleshoot.md).

5. Abra o arquivo de erro no bloco de notas. O arquivo de erro a seguir indica que os dados não estão alinhados corretamente.

    ![Abrir arquivo de erro](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Para um blob de páginas, os dados precisam ser alinhados a 512 bytes. Depois que esses dados forem removidos, o erro será resolvido conforme mostrado na captura de tela a seguir.

    ![Erro resolvido](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Depois que a cópia for concluída, vá para a página **exibir painel** . Verifique o espaço usado e o espaço livre em seu dispositivo.
    
    ![Verificar o espaço livre e utilizado no dashboard](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Repita as etapas acima para copiar dados no segundo nó do dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu sobre tópicos Azure Data Box Heavy como:

> [!div class="checklist"]
> * Conectar-se ao Data Box Heavy
> * Copiar dados para Data Box Heavy


Avance para o próximo tutorial para aprender a enviar seus Data Box Heavy de volta à Microsoft.

> [!div class="nextstepaction"]
> [Envie seu Azure Data Box Heavy para a Microsoft](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>Copiar dados através de SMB

1. Se estiver usando um host do Windows, use o seguinte comando para se conectar aos compartilhamentos SMB:

    `\\<IP address of your device>\ShareName`

2. Para obter as credenciais de acesso de partilha, aceda à página **Ligar e copiar** na IU da Web local do Data Box.

3. Use uma ferramenta de cópia de arquivo compatível com SMB, como o Robocopy, para copiar dados para compartilhamentos.

Para obter instruções passo a passo, acesse [tutorial: Copie dados para Azure Data Box via SMB](data-box-heavy-deploy-copy-data.md).

### <a name="copy-data-via-nfs"></a>Copiar dados através de NFS

1. Se estiver usando um host NFS, use o seguinte comando para montar os compartilhamentos NFS:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Para obter as credenciais de acesso de compartilhamento, vá para a **página conectar & Copiar** na interface do usuário da Web local do data Box Heavy.
3. Use `cp` o `rsync` comando ou para copiar seus dados. 
4. Repita essas etapas para se conectar e copiar dados para o segundo nó do seu Data Box Heavy.

Para obter instruções passo a passo, acesse [tutorial: Copie dados para Azure Data Box via NFS](data-box-heavy-deploy-copy-data-via-nfs.md).

### <a name="copy-data-via-rest"></a>Copiar dados via REST

1. Para copiar dados usando Data Box armazenamento de BLOBs por meio de APIs REST, você pode se conectar via *http* ou *https*.
2. Para copiar dados para Data Box armazenamento de BLOBs, você pode usar AzCopy.
3. Repita essas etapas para se conectar e copiar dados para o segundo nó do seu Data Box Heavy.

Para obter instruções passo a passo, acesse [tutorial: Copie dados para Azure Data Box armazenamento de BLOBs por](data-box-heavy-deploy-copy-data-via-rest.md)meio de APIs REST.

### <a name="copy-data-via-data-copy-service"></a>Copiar dados por meio do serviço de cópia de dados

1. Para copiar dados usando o serviço de cópia de dados, você precisa criar um trabalho. Na interface do usuário da Web local do seu Data Box Heavy, vá para **gerenciar > copiar dados > criar**.
2. Preencha os parâmetros e crie um trabalho.
3. Repita essas etapas para se conectar e copiar dados para o segundo nó do seu Data Box Heavy.

Para obter instruções passo a passo, acesse [tutorial: Use o serviço de cópia de dados para copiar dados](data-box-heavy-deploy-copy-data-via-copy-service.md)em Azure data Box Heavy.

### <a name="copy-data-to-managed-disks"></a>Copiar dados para discos gerenciados

1. Ao solicitar o dispositivo Data Box Heavy, você deve ter selecionado Managed disks como seu destino de armazenamento.
2. Você pode se conectar a Data Box Heavy por meio de compartilhamentos SMB ou NFS.
3. Em seguida, você pode copiar dados por meio de ferramentas SMB ou NFS.
4. Repita essas etapas para se conectar e copiar dados para o segundo nó do seu Data Box Heavy.

Para obter instruções passo a passo, acesse [tutorial: Use Data Box para importar dados pesados como discos gerenciados no](data-box-heavy-deploy-copy-data-from-vhds.md)Azure.

::: zone-end


