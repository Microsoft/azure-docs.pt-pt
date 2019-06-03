---
title: Tutorial para copiar dados através de SMB em pesadas de caixa de dados do Azure | Documentos da Microsoft
description: Saiba como copiar dados para o Azure dados caixa pesada através de SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: 6bc7af30d409fb7add321953aa2e956a0a3c1840
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427773"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb-preview"></a>Tutorial: Copiar dados para o Azure dados caixa pesada através de SMB (pré-visualização)

Este tutorial descreve como ligar e copiar dados do computador anfitrião com a IU web local.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ligar a dados caixa pesado
> * Copiar dados para dados de caixa pesadas


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Configurar a caixa de dados do Azure pesada](data-box-deploy-set-up.md).
2. Tiver recebido seu intenso de caixa de dados e é o estado da encomenda no portal **entregues**.
3. Possui um computador de anfitrião que tenha os dados que pretende que o copie para dados de caixa pesada. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Para mais rápidas velocidades de cópia, duas ligações de 40 GbE (um por nó) podem ser utilizadas em paralelo. Se não tiver ligação 40 GbE disponível, recomendamos que tenha, pelo menos, duas ligações de 10 GbE (um por nó).

## <a name="connect-to-data-box-heavy-shares"></a>Ligar a partilhas de dados de caixa pesadas

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
| Blobs de blocos do Azure | <li>Caminho UNC para partilhas: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL de armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para partilhas: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL de armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Ficheiros do Azure       |<li>Caminho UNC para partilhas: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL de armazenamento do Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Os passos para ligar através de um Windows ou um cliente Linux são diferentes.

> [!NOTE]
> Siga os mesmos passos para ligar a nós do dispositivo em paralelo.

### <a name="connect-on-a-windows-system"></a>Ligue-se num sistema Windows

Se utilizar um computador de anfitrião do Windows Server, siga estes passos para ligar para os dados de caixa pesada.

1. O primeiro passo é autenticar e iniciar uma sessão. Aceda a **Ligar e copiar**. Clique em **Obter credenciais** para obter as credenciais de acesso para as partilhas associadas à sua conta de armazenamento.

    ![Obter credenciais de partilhas 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. Na caixa de diálogo Aceder à partilha e copiar os dados, copie o **Nome de utilizador** e a **Palavra-passe** correspondentes à partilha. Clique em **OK**.
    
    ![Obter credenciais de partilhas 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Para aceder às partilhas associadas à conta de armazenamento (*databoxe2etest* no exemplo a seguir) a partir do seu computador anfitrião, abra uma janela de comando. Na linha de comandos, escreva:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Consoante o seu formato de dados, os caminhos de partilha são os seguintes:
    - Blob de blocos do Azure- `\\10.100.10.100\databoxe2etest_BlockBlob`
    - BLOBs de página do Azure- `\\10.100.10.100\databoxe2etest_PageBlob`
    - Ficheiros do Azure- `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Introduza a palavra-passe da partilha quando lhe for pedido. O exemplo seguinte mostra a ligação a uma partilha através do comando anterior.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Prima Windows + R. Na janela **Executar**, especifique o `\\<device IP address>`. Clique em **OK** para abrir o Explorador de ficheiros.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Deverá ver agora as partilhas como pastas.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Crie sempre uma pasta para os ficheiros que pretende copiar na partilha e, em seguida, copie os ficheiros para essa pasta**. A pasta criada no blob de blocos e partilhas de blob de página representa um contentor para o qual os dados são carregados como blobs. Não é possível copiar o arquivos diretamente à *raiz* pasta na conta de armazenamento.
    
### <a name="connect-on-a-linux-system"></a>Ligue-se num sistema Linux

Se utilizar um cliente Linux, utilize o comando seguinte para montar a partilha SMB.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

O `vers` parâmetro é a versão do SMB que suporte o anfitrião Linux. Plug-in a versão adequada no comando acima.

Para versões do SMB que suporta a pesadas de caixa de dados, consulte [sistemas de ficheiros suportados por clientes Linux](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Copiar dados para dados de caixa pesadas

Assim que estiver ligado às partilhas de dados de caixa pesada, a próxima etapa é copiar dados.

### <a name="copy-considerations"></a>Considerações de cópia

Antes de iniciar a cópia de dados, reveja as seguintes considerações:

- Certifique-se de que copia os dados para partilhas correspondentes para o formato de dados apropriados. Por exemplo, copie os dados de blobs de blocos para a partilha de blobs de blocos. Copie os VHDs para BLOBs de páginas.

    Se o formato de dados não corresponde ao tipo de partilha apropriadas, em seguida, num passo posterior, o carregamento de dados para o Azure irá falhar.
-  Ao copiar dados, certifique-se de que o tamanho dos dados está em conformidade com os limites de tamanho descritos a [armazenamento do Azure e limites de dados de caixa pesada](data-box-heavy-limits.md).
- Se os dados, o que está a ser carregados por dados caixa pesada, são carregados em simultâneo por outros aplicativos fora dados caixa pesada, isto pode resultar no carregamento de Corrupção de dados e de falhas de tarefa.
- Recomendamos que:
    - Não utilize o SMB e NFS ao mesmo tempo.
    - Copie os mesmos dados para o mesmo destino final no Azure.
     
  Nestes casos, não é possível determinar o resultado final.
- Sempre crie uma pasta para os ficheiros que pretende copiar abaixo da partilha e, em seguida, copie os ficheiros para essa pasta. A pasta criada no blob de blocos e partilhas de blob de página representa um contentor para o qual os dados são carregados como blobs. Não é possível copiar o arquivos diretamente à *raiz* pasta na conta de armazenamento.

Depois de se ligar à partilha de SMB, iniciar a cópia de dados.

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
    |/nfl    |Especifica que os nomes de ficheiro não tiver sessão iniciados.         |
    |/ndl    |Especifica que os nomes de diretório não tiver sessão iniciados.        |
    |/np     |Especifica que o progresso da operação de cópia (o número de ficheiros ou diretórios copiados até ao momento) não será apresentado. A apresentação do progresso reduz significativamente o desempenho.         |
    |/MT     | Utilize multithreading (são recomendados 32 ou 64 threads). Esta opção não é utilizada com ficheiros encriptados. Pode ter de separar os ficheiros encriptados e não encriptados. No entanto, uma cópia de thread único reduz significativamente o desempenho.           |
    |/fft    | Utilize para reduzir a granularidade de carimbo de data/hora para qualquer sistema de ficheiros.        |
    |/b      | Copia os ficheiros no modo de Cópia de Segurança.        |
    |/z      | Copia os ficheiros no modo de Reinício. Utilize se o ambiente for instável. Esta opção reduz o débito devido ao registo adicional.      |
    | /zb    | Utiliza o modo de Reinício. Se o acesso for negado, esta opção utiliza o modo de Cópia de Segurança. Esta opção reduz o débito devido ao ponto de verificação.         |
    |/efsraw | Copia todos os ficheiros encriptados no modo EFS não processado. Utilize apenas com ficheiros encriptados.         |
    |log+:\<LogFile>| Anexa a saída ao ficheiro de registo existente.|
    
 
    O exemplo a seguir mostra a saída do comando robocopy para copiar ficheiros para os dados de caixa pesada.

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

2. Para otimizar o desempenho, utilize os seguintes parâmetros do Robocopy ao copiar os dados. (Os números a seguir representam cenários de casos de melhor.)

    | Plataforma    | Principalmente ficheiros pequenos < 512 KB    | Principalmente médios ficheiros 512 KB - 1 MB  | Principalmente ficheiros grandes > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 sessões de Robocopy <br> 24 threads por sessões | 6 sessões de Robocopy <br> 16 threads por sessões | 6 sessões de Robocopy <br> 16 threads por sessões |


    Para obter mais informações sobre o comando do Robocopy, aceda a [Robocopy e alguns exemplos](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

3. Abra a pasta de destino para ver e verificar os ficheiros copiados.

    ![Ver os ficheiros copiados](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Como os dados são copiados:

    - Os nomes de ficheiros, tamanhos e formato são avaliadas para garantir a que ir ao encontro os limites de objeto e o armazenamento do Azure, bem como ficheiros do Azure e convenções de nomenclatura de contentor.
    - Para garantir a integridade dos dados, soma de verificação também é calculada inline.

    Se ocorrerem erros durante o processo de cópia, transfira os ficheiros de erro para resolução de problemas. Selecione o ícone de seta para transferir os ficheiros de erro.

    ![Transferir os ficheiros de erro](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Para obter mais informações, consulte [ver registos de erros durante a cópia de dados para dados de caixa pesada](data-box-logs.md#view-error-log-during-data-copy-to-data-box). Para obter uma lista detalhada de erros durante a cópia de dados, consulte [problemas de resolução de problemas de dados de caixa pesada](data-box-troubleshoot.md).

5. Abra o ficheiro de erro no bloco de notas. O seguinte ficheiro de erro indica que os dados não estão alinhados corretamente.

    ![Erro ao abrir ficheiro](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Para um blob de página, os dados têm de ter 512 bytes alinhados. Depois de remover estes dados, o erro é resolvido conforme mostrado na captura de ecrã seguinte.

    ![Erro resolvido](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Depois da cópia estiver concluída, aceda ao **ver o Dashboard** página. Verifique se o espaço utilizado e o espaço livre no seu dispositivo.
    
    ![Verificar o espaço livre e utilizado no dashboard](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Repita os passos acima para copiar os dados para o segundo nó do dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu sobre tópicos pesadas de caixa de dados do Azure, tais como:

> [!div class="checklist"]
> * Ligar a dados caixa pesado
> * Copiar dados para dados de caixa pesadas


Avance para o próximo tutorial para saber como enviar seu intenso de caixa de dados à Microsoft.

> [!div class="nextstepaction"]
> [Envie sua pesado de caixa de dados do Azure para a Microsoft](./data-box-heavy-deploy-picked-up.md)

