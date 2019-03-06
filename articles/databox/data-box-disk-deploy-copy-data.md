---
title: Copiar dados para o Microsoft Azure Data Box Disk | Microsoft Docs
description: Utilize este tutorial para aprender a copiar dados para o Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 02/26/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 6a008072fc88b9dc800b792c13a6c77c31b31e51
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410031"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Tutorial: Copiar dados para o disco do Azure Data Box e certifique-se

Este tutorial descreve como copiar dados do computador anfitrião e, em seguida, gerar somas de verificação para verificar a integridade dos dados.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Copiar dados para o Data Box Disk
> * Verificar os dados

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:
- Concluiu o [Tutorial: Instalar e configurar o seu disco do Azure Data Box](data-box-disk-deploy-set-up.md).
- Os discos são desbloqueados e ligados a um computador cliente.
- O computador cliente que é utilizado para copiar dados para os discos tem de executar um [Sistema operativo suportado](data-box-disk-system-requirements.md##supported-operating-systems-for-clients).
- Certifique-se de que o tipo de armazenamento pretendido para os seus dados corresponde aos [Tipos de armazenamento suportados](data-box-disk-system-requirements.md#supported-storage-types-for-upload).
- Revisão [geridos limites de disco em limites de tamanho de objeto do Azure](data-box-disk-limits.md#azure-object-size-limits).


## <a name="copy-data-to-disks"></a>Copiar dados para discos

Antes de copiar os dados para os discos, reveja as seguintes considerações:

- É da sua responsabilidade assegurar que copia os dados para pastas que correspondem ao formato de dados apropriado. Por exemplo, copie os dados de blobs de blocos para a pasta de blobs de blocos. Se o formato de dados não corresponder à pasta apropriada (tipo de armazenamento), num passo posterior, o carregamento de dados para o Azure falhará.
- Ao copiar os dados, certifique-se de que o respetivo tamanho está em conformidade com os limites descritos em [Limites de armazenamento do Azure e do Data Box Disk](data-box-disk-limits.md).
- Se os dados, que estão a ser carregados pelo Data Box Disk, forem carregados em simultâneo por outras aplicações fora do Data Box Disk, isto pode resultar em falhas da tarefa de carregamento e danos nos dados.

Se tiver especificado os discos geridos na ordem, reveja as seguintes considerações adicionais:

- Só pode ter um disco gerido com um determinado nome num grupo de recursos em todas as pastas pré-criada e em todo o disco de caixa de dados. Isso implica que os VHDs carregados para as pastas pré-criada devem ter nomes exclusivos. Certifique-se de que o nome fornecido não corresponde a um disco gerido já existente num grupo de recursos. Se os VHDs têm os mesmos nomes, em seguida, apenas um VHD é convertido para disco gerido com esse nome. Os outros VHDs são carregados como blobs de páginas a conta de armazenamento de teste.
- Copie sempre os VHDs a uma das pastas pré-criada. Se copiar os VHDs fora essas pastas ou numa pasta que criou, os VHDs são carregados para a conta de armazenamento do Azure, como blobs de páginas e discos não geridos.
- Apenas os VHDs fixos podem ser carregados para criar discos geridos. VHDs dinâmicos, VHDs diferenciais ou VHDX não são suportados ficheiros.


Execute os seguintes passos para ligar e copiar dados do seu computador para o Data Box Disk.

1. Veja o conteúdo da unidade desbloqueada. A lista de pré-criada pastas e subpastas na unidade é diferente de acordo com as opções selecionadas ao colocar a ordem de disco Data Box.

    |Destino de armazenamento selecionada  |Tipo de conta de armazenamento|Tipo de conta de armazenamento de teste |Pastas e subpastas  |
    |---------|---------|---------|------------------|
    |Conta de armazenamento     |Contas GPv1 ou GPv2                 | ND | BlockBlob <br> PageBlob <br> AzureFile        |
    |Conta de armazenamento     |Conta de armazenamento de BLOBs         | ND | BlockBlob        |
    |Managed disks     |ND | Contas GPv1 ou GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |Conta de armazenamento <br> Managed disks     |Contas GPv1 ou GPv2 | Contas GPv1 ou GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |Conta de armazenamento <br> Managed disks    |Conta de armazenamento de BLOBs | Contas GPv1 ou GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    Uma captura de ecrã do exemplo de um pedido onde foi especificada uma conta de armazenamento GPv2 é mostrada abaixo:

    ![Conteúdo da unidade de disco](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Copiar os dados que precisam de ser importados como blobs de blocos para *BlockBlob* pasta. Da mesma forma, copiar os dados como VHD/VHDX *PageBlob* pasta e os dados para *AzureFile* pasta.

    É criado um contentor na conta de armazenamento do Azure para cada subpasta nas pastas BlockBlob e PageBlob. Todos os ficheiros nas pastas BlockBlob e PageBlob são copiados para um contentor `$root` predefinido na conta de armazenamento do Azure. Quaisquer ficheiros no contentor `$root` são sempre carregados como blobs de blocos.

   Copiar ficheiros para uma pasta dentro *AzureFile* pasta. Uma sub-pasta dentro *AzureFile* pasta cria uma partilha de ficheiros. Ficheiros copiados diretamente para o *AzureFile* falharão de pasta e são carregados como blobs de blocos.

    Se existirem ficheiros e pastas no diretório de raiz, tem de movê-los para uma pasta diferente antes de iniciar a cópia de dados.

    > [!IMPORTANT]
    > Todos os contentores, blobs e nomes de ficheiros deverá obedecer [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Se estas regras não forem seguidas, o carregamento de dados para o Azure falhará.

3. Ao copiar arquivos, certifique-se de que arquivos excede ~4.7 TiB para blobs de blocos, TiB de cerca de 8 para blobs de páginas e ~ 1 TiB para ficheiros do Azure. 
4. Pode utilizar a funcionalidade de arrastar e largar no Explorador de Ficheiros para copiar os dados. Também pode utilizar qualquer ferramenta de cópia de ficheiros compatível SMB, por exemplo, o Robocopy para copiar os dados. É possível iniciar várias tarefas de cópia com o seguinte comando do Robocopy:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    Os parâmetros e as opções do comando são apresentados da seguinte forma:
    
    |Parâmetros/Opções  |Descrição |
    |--------------------|------------|
    |Origem            | Especifica o caminho para o diretório de origem.        |
    |Destino       | Especifica o caminho para o diretório de destino.        |
    |/E                  | Copia os subdiretórios, incluindo diretórios vazios. |
    |/MT[:N]             | Cria cópias com múltiplos threads com N threads, em que N é um número inteiro entre 1 e 128. <br>O valor predefinido para N é 8.        |
    |/R: <N>             | Especifica o número de repetições nas cópias falhadas. O valor predefinido de N é 1.000.000 (um milhão de repetições).        |
    |/W: <N>             | Especifica o tempo de espera entre as repetições, em segundos. O valor predefinido de N é 30 (30 segundos de tempo de espera).        |
    |/NFL                | Especifica que os nomes de ficheiro não se destinam a ser registados.        |
    |/NDL                | Especifica que os nomes de diretório não se destinam a ser registados.        |
    |/FFT                | Assume as horas dos ficheiros FAT (precisão de dois segundos).        |
    |/Log:<Log File>     | Escreve a saída do estado no ficheiro de registo (substitui o ficheiro de registo existente).         |

    É possível utilizar vários discos em paralelo com várias tarefas em execução em cada disco.

6. Verifique o estado de cópia quando a tarefa está em curso. O exemplo seguinte mostra a saída do comando do Robocopy para copiar ficheiros para o Data Box Disk.

    ```
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    Para otimizar o desempenho, utilize os seguintes parâmetros do Robocopy ao copiar os dados.

    |    Plataforma    |    Principalmente ficheiros pequenos < 512 KB                           |    Principalmente ficheiros médios 512 KB - 1 MB                      |    Principalmente ficheiros grandes > 1 MB                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
    |    Data Box Disk        |    4 Robocopy sessões * <br> 16 threads por sessões    |    2 Robocopy sessões * <br> 16 threads por sessões    |    2 Robocopy sessões * <br> 16 threads por sessões    |  |
    
    **Cada sessão de Robocopy pode ter um máximo de 7000 diretórios e arquivos de 150 milhões.*
    
    >[!NOTE]
    > Os parâmetros sugeridos acima baseiam-se no ambiente utilizado nos testes inhouse.
    
    Para obter mais informações sobre o comando do Robocopy, aceda a [Robocopy e alguns exemplos](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

6. Abra a pasta de destino para ver e verificar os ficheiros copiados. Se ocorrerem erros durante o processo de cópia, transfira os ficheiros de registo para resolução de problemas. Os ficheiros de registo estão localizados como especificado no comando robocopy.
 
### <a name="split-and-copy-data-to-disks"></a>Dividir e copiar dados para discos

Este procedimento opcional pode ser utilizado quando utilizar vários discos e dispuser de um grande conjunto de dados que tenha de ser dividido e copiado para todos os discos. A ferramenta de Cópia Dividida do Data Box ajuda a dividir e a copiar os dados num computador Windows.

>[!IMPORTANT]
> Ferramenta de cópia de divisão de caixa de dados também valida os seus dados. Se usar a ferramenta de cópia de divisão de caixa de dados para copiar dados, pode ignorar o [etapa de validação](#validate-data).
> Ferramenta de cópia de divisão não é suportada com discos geridos.

1. No seu computador Windows, certifique-se de que tem a ferramenta de Cópia Dividida do Data Box transferida e extraída numa pasta local. Esta ferramenta foi transferida quando transferiu o conjunto de ferramentas do Data Box Disk para Windows.
2. Abra o Explorador de Ficheiros. Tome nota da unidade de origem de dados e das letras de unidade atribuídas ao Data Box Disk. 

     ![Dados de cópia dividida](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Identificar os dados de origem a copiar. Por exemplo, neste caso:
    - Foram identificados os dados de Blob de bloco seguintes.

         ![Dados de cópia dividida](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - Foram identificados os dados de Blob de página seguintes.

         ![Dados de cópia dividida](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Aceda à pasta na qual o software é extraído. Localize o `SampleConfig.json` ficheiro nessa pasta. Este é um ficheiro só de leitura que pode modificar e guardar.

   ![Dados de cópia dividida](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. Modificar o `SampleConfig.json` ficheiro.
 
    - Forneça um nome de tarefa. Esta ação cria uma pasta no Data Box Disk que vai passar a ser o contentor da conta de armazenamento do Azure associada a estes discos. O nome da tarefa tem de seguir as convenções de nomenclatura de contentores do Azure. 
    - Forneça um caminho de origem, tornando a nota no formato de caminho no `SampleConfigFile.json`. 
    - Introduza as letras de unidade correspondentes aos discos de destino. Os dados são obtidos a partir do caminho de origem e copiados para vários discos.
    - Indique um caminho para os ficheiros de registo. Por predefinição, é enviado para o diretório atual onde o `.exe` está localizado.

     ![Dados de cópia dividida](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. Para validar o formato de arquivo, aceda a `JSONlint`. Guarde o ficheiro como `ConfigFile.json`. 

     ![Dados de cópia dividida](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Abra uma janela de Linha de Comandos. 

8. Execute o `DataBoxDiskSplitCopy.exe`. Type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Dados de cópia dividida](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Carregue na tecla Enter para continuar a executar o script.

    ![Dados de cópia dividida](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. Quando o conjunto de dados é dividido e copiado, o resumo da ferramenta de Cópia Dividida da sessão de cópia é apresentado. É apresentada abaixo uma saída de exemplo.

    ![Dados de cópia dividida](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Certifique-se de que os dados são divididos por todos os discos de destino. 
 
    ![Dividir os dados de cópia](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![divisão copiar dados](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    Se examinar o conteúdo do `n:` unidade adicional, verá que são criadas duas subpastas correspondente para o blob de blocos e BLOBs de páginas formatar dados.
    
     ![Dados de cópia dividida](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Se a sessão de cópia falhar, em seguida, para recuperar e retomar, utilize o comando seguinte:

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

Depois de concluída a cópia de dados, pode avançar para validar os seus dados. Se utilizou a ferramenta de cópia de divisão, ignore a validação (cópia de divisão ferramenta também valida) e avance para o próximo tutorial.


## <a name="validate-data"></a>Validar dados

Se não utilizou a ferramenta de divisão de cópia para copiar dados, terá de validar os seus dados. Para verificar os dados, execute os seguintes passos.

1. Execute o `DataBoxDiskValidation.cmd` para validação da soma de verificação na pasta *DataBoxDiskImport* da unidade.
    
    ![Resultado da ferramenta de validação do Data Box Disk](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Escolha a opção adequada. **Recomendamos que valide sempre os ficheiros e gere somas de verificação, ao selecionar a opção 2**. Consoante o tamanho dos dados, este passo pode demorar algum tempo. Assim que o script estiver concluído, saia da janela de comandos. Se correrem erros durante a validação e a geração da soma de verificação, será notificado e também é fornecida uma ligação para os registos de erros.

    ![Saída da soma de verificação](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - Repor a ferramenta entre duas execuções.
    > - Utilize a opção 1, se lidando com um grande conjunto de dados que contém os arquivos pequenos (~ KBs). Esta opção apenas valida os arquivos, como a geração de soma de verificação pode demorar muito tempo e o desempenho poderia ser muito lento.

3. Se estiver a utilizar vários discos, execute o comando para cada disco.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Copiar dados para o Data Box Disk
> * Verificar a integridade dos dados

Avance para o tutorial seguinte para saber como devolver o Data Box Disk e verificar o carregamento de dados para o Azure.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box de volta para a Microsoft](./data-box-disk-deploy-picked-up.md)