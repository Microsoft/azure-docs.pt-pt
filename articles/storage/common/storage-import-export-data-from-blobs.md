---
title: Importar/exportar do Azure a utilizar para exportar os dados dos Blobs do Azure | Documentos da Microsoft
description: Aprenda a criar tarefas de exportação no portal do Azure para transferir os dados dos Blobs do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: fc02e830953f8612a077fb219c7fef4e86bc3827
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59263843"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Utilize o serviço importar/exportar do Azure para exportar dados do armazenamento de Blobs do Azure
Este artigo fornece instruções passo a passo sobre como utilizar o serviço importar/exportar do Azure em segurança exportar grandes quantidades de dados do armazenamento de Blobs do Azure. O serviço exige que envie discos vazios para o datacenter do Azure. O serviço exporta os dados da sua conta de armazenamento para as unidades e, em seguida, é fornecido as unidades de volta.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar uma tarefa de exportação para transferir dados para fora do armazenamento de Blobs do Azure, com cuidado reveja e conclua a lista seguinte de pré-requisitos para este serviço. Tem de:

- Ter uma subscrição do Azure Active Directory que pode ser utilizada para o serviço de importação/exportação.
- Ter, pelo menos, uma conta de armazenamento do Azure. Ver a lista de [contas de armazenamento e tipos de armazenamento suportadas para o serviço importar/exportar](storage-import-export-requirements.md). Para obter informações sobre como criar uma nova conta de armazenamento, consulte [como criar uma conta de armazenamento](storage-quickstart-create-account.md).
- Têm um número adequado de discos [tipos suportados](storage-import-export-requirements.md#supported-disks).
- Ter uma conta de FedEx/DHL. Se pretender utilizar uma operadora que não seja FedEx/DHL, contacte a equipa de operações de caixa de dados do Azure em `adbops@microsoft.com`. 
    - A conta tem de ser válida, deve ter o saldo e tem de ter capacidades de envio de devolução.
    - Gere um número de controlo para a tarefa de exportação.
    - Cada tarefa deve ter um número de controlo separado. Várias tarefas com o mesmo número de controlo não são suportadas. 
    - Se não tiver uma conta da transportadora, aceda a:
        - [Criar uma conta de FedEX](https://www.fedex.com/en-us/create-account.html), ou 
        - [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Passo 1: Criar uma tarefa de exportação

Execute os seguintes passos para criar uma tarefa de exportação no portal do Azure.

1. Inicie sessão no https://portal.azure.com/.
2. Aceda a **todos os serviços > armazenamento > tarefas de importação/exportação**. 

    ![Vá para tarefas de importação/exportação](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Clique em **criar tarefa de importação/exportação**.

    ![Clique em tarefa de importação/exportação](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. Na **Noções básicas**:
    
    - Selecione **exportar do Azure**. 
    - Introduza um nome descritivo para a tarefa de exportação. Utilize o nome que escolher para controlar o progresso das suas tarefas. 
        - O nome pode conter apenas letras minúsculas, números, hífenes e carateres de sublinhado.
        - O nome tem de começar com uma letra e não pode conter espaços. 
    - Selecione uma subscrição.
    - Introduza ou selecione um grupo de recursos.

        ![Noções básicas](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. Na **detalhes da tarefa**:

    - Selecione a conta de armazenamento onde residem os dados sejam exportados. Utilize uma conta de armazenamento perto de onde estão localizados.
    - A localização de redução é preenchida automaticamente com base na região da conta de armazenamento selecionada. 
    - Especifique os dados de BLOBs que pretende exportar a partir de sua conta de armazenamento à sua unidade em branco ou unidades. 
    - Optar por **exportar todas** dados na conta de armazenamento de Blobs.
    
         ![Exportar todo](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - Pode especificar quais os contentores e blobs a exportar.
        - **Para especificar um blob para exportar**: Utilize o **igual a** Seletor. Especifique o caminho relativo para o blob, começando com o nome do contentor. Uso *$root* para especificar o contentor de raiz.
        - **Para especificar todos os blobs, começando com um prefixo**: Utilize o **começa por** Seletor. Especifique o prefixo, começando com uma barra "/". O prefixo pode ser o prefixo do nome do contentor, o nome do contentor completo ou o nome de contentores completa, seguido pelo prefixo do nome do blob. Tem de fornecer os caminhos dos BLOBs no formato válido para evitar erros durante o processamento, conforme mostrado nesta captura de ecrã. Para obter mais informações, consulte [exemplos de caminhos dos BLOBs válido](#examples-of-valid-blob-paths). 
   
           ![Exportar a contentores e blobs selecionados](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - Pode exportar a partir do ficheiro de lista de Blobs.

        ![Exportar a partir do ficheiro da lista blob](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > Se o blob a ser exportado está a ser utilizado durante a cópia de dados, o serviço importar/exportar do Azure tira um instantâneo do blob e copia o instantâneo.
 

4. Na **devolver informações sobre o envio**:

    - Selecione a operadora na lista pendente. Se pretender utilizar uma operadora que não seja FedEx/DHL, escolha uma opção existente na lista pendente. Equipe de operações de caixa de dados do contacto do Azure em `adbops@microsoft.com` com as informações sobre a operadora que pretende utilizar.
    - Introduza um número de conta de operadora válida que tenha criado com esse operadora. A Microsoft utiliza esta conta para enviar as unidades-se ao assim que a tarefa de importação estiver concluída. 
    - Forneça um nome de contato completo e válido, telefone, e-mail, rua, cidade, zip, estado/província e país/região.

        > [!TIP] 
        > Em vez de especificar um endereço de e-mail para um único utilizador, forneça um e-mail de grupo. Isto garante que recebe as notificações, mesmo que sai de um administrador.
   
5. Na **resumo**:

    - Reveja os detalhes da tarefa.
    - Tome nota do nome da tarefa e datacenter do Azure fornecido envio endereço para envio de discos para o Azure. 

        > [!NOTE] 
        > Envie sempre os discos para o Centro de dados indicado no portal do Azure. Se os discos são enviados para o datacenter errado, a tarefa não será processada.

    - Clique em **OK** para concluir a criação da tarefa de exportação.

## <a name="step-2-ship-the-drives"></a>Passo 2: Envie os discos

Se não souber o número de unidades de que precisa, vá para o [verificar o número de unidades](#check-the-number-of-drives). Se sabe o número de unidades, avance para enviar as unidades.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Passo 3: Atualizar a tarefa com informações de registo

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>Passo 4: Receber os discos
Quando o dashboard de relatórios que a tarefa estiver concluída, os discos são enviados para si e o número de controlo para o envio está disponível no portal.

1. Depois de receber as unidades com dados exportados, terá de obter as chaves do BitLocker para desbloquear as unidades. Vá para a tarefa de exportação no portal do Azure. Clique em **importação/exportação** separador. 
2. Selecione e clique em sua tarefa de exportação na lista. Aceda a **chaves do BitLocker** e copie as chaves.
   
   ![Exibir as chaves do BitLocker para a tarefa de exportação](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. Utilize as chaves do BitLocker para desbloquear os discos.

A exportação foi concluída. Neste momento, pode eliminar a tarefa ou automaticamente é eliminado após 90 dias.


## <a name="check-the-number-of-drives"></a>Verifique o número de unidades

Isso *opcional* passo ajuda a determina o número de unidades necessário para a tarefa de exportação. Efetue este procedimento num sistema Windows em execução uma [versão do SO suportado](storage-import-export-requirements.md#supported-operating-systems).

1. [Baixe o versão 1 do WAImportExport](https://aka.ms/waiev1) no sistema Windows. 
2. Deszipe o para a pasta predefinida `waimportexportv1`. Por exemplo, `C:\WaImportExportV1`.
3. Abra uma janela do PowerShell ou da linha de comandos com privilégios administrativos. Para alterar o diretório para a pasta descompactada, execute o seguinte comando:
    
    `cd C:\WaImportExportV1`

4. Para verificar o número de discos necessários para os blobs selecionados, execute o seguinte comando:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Os parâmetros são descritos na tabela a seguir:
    
    |Parâmetro da linha de comandos|Descrição|  
    |--------------------------|-----------------|  
    |**/logdir:**|Opcional. O diretório de registo. Arquivos de log detalhado são gravados para este diretório. Se não for especificado, o diretório atual é utilizado como o diretório de registo.|  
    |**/sn:**|Necessário. O nome da conta de armazenamento para a tarefa de exportação.|  
    |**/sk:**|Obrigatório apenas se não for especificado um SAS de contentor. A chave de conta para a conta de armazenamento para a tarefa de exportação.|  
    |**/csas:**|Obrigatório apenas se não for especificada uma chave de conta de armazenamento. O contentor de SAS para listar os blobs para ser exportado na tarefa de exportação.|  
    |**/ExportBlobListFile:**|Necessário. Caminho para o XML que contêm lista de caminhos dos BLOBs de ficheiros ou prefixos de caminho para os blobs ser exportada de Blobs. O formato de ficheiro utilizado na `BlobListBlobPath` elemento no [colocar tarefa](/rest/api/storageimportexport/jobs) operação de REST API do serviço de importação/exportação.|  
    |**/DriveSize:**|Necessário. O tamanho das unidades a utilizar para uma tarefa de exportação *por exemplo,*, 500 GB, 1,5 TB.|  

    Veja uma [exemplo do comando PreviewExport](#example-of-previewexport-command).
 
5. Verifique se pode leitura/gravação para as unidades que serão enviadas para a tarefa de exportação.

### <a name="example-of-previewexport-command"></a>Exemplo de comando PreviewExport

O exemplo seguinte demonstra a `PreviewExport` comando:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
O ficheiro de lista de blob de exportação pode conter nomes de BLOBs e BLOBs de prefixos, conforme mostrado aqui:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

A ferramenta de importação/exportação do Azure apresenta uma lista de todos os blobs para ser exportada calcula como empacotá-los em unidades de tamanho especificado, tendo em conta a qualquer sobrecarga necessária, e estima o número de unidades necessária para armazenar os blobs e as informações de utilização da unidade.  
  
Eis um exemplo da saída, com registos informativas omitido:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```

## <a name="examples-of-valid-blob-paths"></a>Exemplos de caminhos dos BLOBs válido

A tabela seguinte mostra exemplos de caminhos dos BLOBs válido:
   
   | Seletor | Caminho do blob | Descrição |
   | --- | --- | --- |
   | Começa com |/ |Exporta todos os blobs na conta de armazenamento |
   | Começa com |/$root / |Exporta todos os blobs no contentor de raiz |
   | Começa com |/Book |Exporta todos os blobs em qualquer contentor que começa com prefixo **livro** |
   | Começa com |/Music/ |Exporta todos os blobs no contentor **música** |
   | Começa com |/ música/amor |Exporta todos os blobs no contentor **música** que comecem com prefixo **gosta de usar** |
   | Igual a |$root/logo.bmp |Blob de exportações **logo.bmp** no contentor de raiz |
   | Igual a |videos/story.mp4 |Blob de exportações **story.mp4** num contentor **vídeos** |

## <a name="next-steps"></a>Passos Seguintes

* [Ver o estado de tarefa e a unidade](storage-import-export-view-drive-status.md)
* [Reveja os requisitos de importação/exportação](storage-import-export-requirements.md)


