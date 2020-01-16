---
title: Usando a importação/exportação do Azure para exportar dados de BLOBs do Azure | Microsoft Docs
description: Saiba como criar trabalhos de exportação no portal do Azure para transferir dados de BLOBs do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 8ce1e7d58ba69d9f36d3b37c1e48bfeebc5d8d65
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978564"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Usar o serviço de importação/exportação do Azure para exportar dados do armazenamento de BLOBs do Azure
Este artigo fornece instruções passo a passo sobre como usar o serviço de importação/exportação do Azure para exportar com segurança grandes quantidades de dados do armazenamento de BLOBs do Azure. O serviço exige que você envie unidades vazias para o datacenter do Azure. O serviço exporta dados de sua conta de armazenamento para as unidades e, em seguida, envia as unidades de volta.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um trabalho de exportação para transferir dados do armazenamento de BLOBs do Azure, Examine cuidadosamente e conclua a lista de pré-requisitos a seguir para esse serviço.
Você deve:

- Ter uma assinatura ativa do Azure que possa ser usada para o serviço de importação/exportação.
- Ter pelo menos uma conta de armazenamento do Azure. Consulte a lista de [contas de armazenamento e tipos de armazenamento com suporte para o serviço de importação/exportação](storage-import-export-requirements.md). Para obter informações sobre como criar uma nova conta de armazenamento, consulte [como criar uma conta de armazenamento](storage-account-create.md).
- Ter um número adequado de discos de [tipos com suporte](storage-import-export-requirements.md#supported-disks).
- Ter uma conta do FedEx/DHL. Se você quiser usar uma operadora diferente de FedEx/DHL, contate a equipe de operações Azure Data Box em `adbops@microsoft.com`.
    - A conta deve ser válida, deve ter um saldo e deve ter recursos de envio de retorno.
    - Gerar um número de rastreamento para o trabalho de exportação.
    - Cada trabalho deve ter um número de controle separado. Não há suporte para vários trabalhos com o mesmo número de acompanhamento.
    - Se você não tiver uma conta da operadora, vá para:
        - [Criar uma conta do FedEx](https://www.fedex.com/en-us/create-account.html)ou
        - [Crie uma conta da DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Etapa 1: criar um trabalho de exportação

Execute as etapas a seguir para criar um trabalho de exportação no portal do Azure.

1. Faça logon no https://portal.azure.com/.
2. Vá para **todos os serviços > armazenamento > trabalhos de importação/exportação**.

    ![Ir para trabalhos de importação/exportação](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Clique em **criar trabalho de importação/exportação**.

    ![Clique em trabalho de importação/exportação](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. Em **noções básicas**:

    - Selecione **exportar do Azure**.
    - Insira um nome descritivo para o trabalho de exportação. Use o nome que você escolher para acompanhar o progresso de seus trabalhos.
        - O nome pode conter apenas letras minúsculas, números, hifens e sublinhados.
        - O nome deve começar com uma letra e não pode conter espaços.
    - Selecionar uma subscrição.
    - Insira ou selecione um grupo de recursos.

        ![Noções básicas](./media/storage-import-export-data-from-blobs/export-from-blob3.png)

3. Em **detalhes do trabalho**:

    - Selecione a conta de armazenamento na qual os dados a serem exportados residem. Use uma conta de armazenamento perto do local onde você está localizado.
    - O local chegada é preenchido automaticamente com base na região da conta de armazenamento selecionada.
    - Especifique os dados de BLOB que você deseja exportar de sua conta de armazenamento para a unidade ou unidades em branco.
    - Escolha **exportar todos os** dados de blob na conta de armazenamento.

         ![Exportar tudo](./media/storage-import-export-data-from-blobs/export-from-blob4.png)

    - Você pode especificar quais contêineres e blobs exportar.
        - **Para especificar um blob a ser exportado**: Use o seletor **igual a** . Especifique o caminho relativo para o blob, começando com o nome do contêiner. Use *$root* para especificar o contêiner raiz.
        - **Para especificar todos os BLOBs começando com um prefixo**: Use o seletor **começa com** . Especifique o prefixo, começando com uma barra "/". O prefixo pode ser o prefixo do nome do contêiner, o nome completo do contêiner ou o nome completo do contêiner seguido pelo prefixo do nome do blob. Você deve fornecer os caminhos de BLOB em formato válido para evitar erros durante o processamento, conforme mostrado nesta captura de tela. Para obter mais informações, consulte [exemplos de caminhos de blob válidos](#examples-of-valid-blob-paths).

           ![Exportar contêineres e blobs selecionados](./media/storage-import-export-data-from-blobs/export-from-blob5.png)

    - Você pode exportar do arquivo de lista de BLOBs.

        ![Exportar do arquivo de lista de BLOBs](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  

   > [!NOTE]
   > Se o blob a ser exportado estiver em uso durante a cópia de dados, o serviço de importação/exportação do Azure tira um instantâneo do blob e copia o instantâneo.


4. Em **informações de envio de retorno**:

    - Selecione a transportadora na lista suspensa. Se você quiser usar uma operadora diferente de FedEx/DHL, escolha uma opção existente na lista suspensa. Contate a equipe de operações de Azure Data Box em `adbops@microsoft.com` com as informações sobre a transportadora que você planeja usar.
    - Insira um número de conta da transportadora válido que você criou com essa portadora. A Microsoft usa essa conta para enviar as unidades de volta para você quando o trabalho de exportação estiver concluído.
    - Forneça um nome de contato completo e válido, telefone, email, endereço, cidade, CEP, estado/província e país/região.

        > [!TIP]
        > Em vez de especificar um endereço de email para um único usuário, forneça um email do grupo. Isso garante que você receba notificações mesmo que um administrador saia.

5. Em **Resumo**:

    - Examine os detalhes do trabalho.
    - Anote o nome do trabalho e tenha fornecido o endereço de envio do datacenter do Azure para enviar discos para o Azure.

        > [!NOTE]
        > Sempre envie os discos para o datacenter anotados na portal do Azure. Se os discos forem enviados para o datacenter errado, o trabalho não será processado.

    - Clique em **OK** para concluir a criação do trabalho de exportação.

## <a name="step-2-ship-the-drives"></a>Etapa 2: enviar as unidades

Se você não souber o número de unidades de que precisa, vá para [verificar o número de unidades](#check-the-number-of-drives). Se você souber o número de unidades, vá para enviar as unidades.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Etapa 3: atualizar o trabalho com informações de rastreamento

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>Etapa 4: receber os discos
Quando o painel relata que o trabalho está concluído, os discos são enviados para você e o número de rastreamento para a remessa está disponível no Portal.

1. Depois de receber as unidades com dados exportados, você precisa obter as chaves do BitLocker para desbloquear as unidades. Vá para o trabalho de exportação no portal do Azure. Clique na guia **importar/exportar** .
2. Selecione e clique no trabalho de exportação na lista. Acesse **as chaves do BitLocker** e copie as chaves.

   ![Exibir chaves do BitLocker para o trabalho de exportação](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. Use as chaves do BitLocker para desbloquear os discos.

A exportação foi concluída. Neste momento, você pode excluir o trabalho ou ele será excluído automaticamente após 90 dias.


## <a name="check-the-number-of-drives"></a>Verificar o número de unidades

Essa etapa *opcional* ajuda a determinar o número de unidades necessárias para o trabalho de exportação. Execute esta etapa em um sistema Windows executando uma [versão do sistema operacional com suporte](storage-import-export-requirements.md#supported-operating-systems).

1. [Baixe a versão 1 do WAImportExport](https://www.microsoft.com/download/details.aspx?id=42659) no sistema do Windows.
2. Descompacte para a pasta padrão `waimportexportv1`. Por exemplo, `C:\WaImportExportV1`.
3. Abra uma janela de linha de comando ou PowerShell com privilégios administrativos. Para alterar o diretório para a pasta descompactada, execute o seguinte comando:

    `cd C:\WaImportExportV1`

4. Para verificar o número de discos necessários para os BLOBs selecionados, execute o seguinte comando:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Os parâmetros são descritos na tabela a seguir:

    |Parâmetro de linha de comando|Descrição|  
    |--------------------------|-----------------|  
    |**logdir**|Opcional. O diretório de log. Os arquivos de log detalhados são gravados nesse diretório. Se não for especificado, o diretório atual será usado como o diretório de log.|  
    |**/sn:**|Necessário. O nome da conta de armazenamento para o trabalho de exportação.|  
    |**/sk:**|Necessário somente se uma SAS do contêiner não for especificada. A chave de conta da conta de armazenamento para o trabalho de exportação.|  
    |**/csas:**|Necessário somente se uma chave de conta de armazenamento não for especificada. A SAS do contêiner para listar os BLOBs a serem exportados no trabalho de exportação.|  
    |**/ExportBlobListFile:**|Necessário. Caminho para o arquivo XML que contém a lista de caminhos de BLOB ou prefixos de caminho de BLOB para os BLOBs a serem exportados. O formato de arquivo usado no elemento `BlobListBlobPath` na operação [Put Job](/rest/api/storageimportexport/jobs) da API REST do serviço de importação/exportação.|  
    |**/DriveSize:**|Necessário. O tamanho das unidades a serem usadas para um trabalho de exportação, *por exemplo*, 500 GB, 1,5 TB.|  

    Consulte um [exemplo do comando PreviewExport](#example-of-previewexport-command).

5. Verifique se você pode ler/gravar nas unidades que serão enviadas para o trabalho de exportação.

### <a name="example-of-previewexport-command"></a>Exemplo de comando PreviewExport

O exemplo a seguir demonstra o comando `PreviewExport`:  

```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  

O arquivo de lista de blobs de exportação pode conter nomes de BLOB e prefixos de BLOB, como mostrado aqui:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

A ferramenta de importação/exportação do Azure lista todos os BLOBs a serem exportados e calcula como empacotar-os em unidades do tamanho especificado, levando em conta qualquer sobrecarga necessária e, em seguida, estima o número de unidades necessárias para manter os BLOBs e as informações de uso da unidade.  

Aqui está um exemplo da saída, com logs informativos omitidos:  

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

## <a name="examples-of-valid-blob-paths"></a>Exemplos de caminhos de blob válidos

A tabela a seguir mostra exemplos de caminhos de blob válidos:

   | Seletor | Caminho do blob | Descrição |
   | --- | --- | --- |
   | Começa com |/ |Exporta todos os BLOBs na conta de armazenamento |
   | Começa com |/$root/ |Exporta todos os BLOBs no contêiner raiz |
   | Começa com |/book |Exporta todos os BLOBs em qualquer contêiner que comece com o prefixo **Book** |
   | Começa com |Minhas |Exporta todos os BLOBs no contêiner **música** |
   | Começa com |/music/love |Exporta todos os BLOBs em **música** do contêiner que começam com o prefixo **Love** |
   | Igual a |$root/logo.bmp |Exporta o blob **logo. bmp** no contêiner raiz |
   | Igual a |videos/story.mp4 |Exporta o blob **Story. mp4** em **vídeos** de contêiner |

## <a name="next-steps"></a>Passos seguintes

* [Exibir o status do trabalho e da unidade](storage-import-export-view-drive-status.md)
* [Examinar os requisitos de importação/exportação](storage-import-export-requirements.md)
