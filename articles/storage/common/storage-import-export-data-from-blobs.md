---
title: Utilização de Importações/Exportações da Azure para exportar dados da Azure Blobs Microsoft Docs
description: Saiba como criar empregos de exportação no portal Azure para transferir dados da Azure Blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 09/17/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d9f7778d1dda159f3ab0c4548912370c85f94eff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91441876"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Utilizar o serviço Importar/Exportar do Microsoft Azure para exportar dados do Armazenamento de blobs do Azure

Este artigo fornece instruções passo a passo sobre como utilizar o serviço Azure Import/Export para exportar de forma segura grandes quantidades de dados a partir do armazenamento da Azure Blob. O serviço requer que envie unidades vazias para o datacenter Azure. O serviço exporta dados da sua conta de armazenamento para as unidades e, em seguida, envia os discos de volta.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar uma tarefa de exportação para transferir dados para fora do Azure Blob Storage, reveja cuidadosamente e complete a seguinte lista de pré-requisitos para este serviço.
Tens de o fazer:

- Tenha uma subscrição ativa do Azure que pode ser usada para o serviço de importação/exportação.
- Tenha pelo menos uma conta de Armazenamento Azure. Consulte a lista de contas de [armazenamento suportadas e tipos de armazenamento para o serviço de importação/exportação.](storage-import-export-requirements.md) Para obter informações sobre a criação de uma nova conta de armazenamento, consulte [Como Criar uma Conta de Armazenamento.](storage-account-create.md)
- Dispor de um número adequado de discos de [tipos suportados.](storage-import-export-requirements.md#supported-disks)
- Tenha uma conta FedEx/DHL. Se pretender utilizar uma transportadora que não seja a FedEx/DHL, contacte a equipa de Operações da Caixa de Dados da Azure `adbops@microsoft.com` em .
  - A conta deve ser válida, deve ter equilíbrio, e deve ter capacidades de envio de retorno.
  - Gere um número de rastreio para o trabalho de exportação.
  - Cada tarefa deve ter um número de controlo separado. Não são apoiados várias tarefas com o mesmo número de controlo.
  - Se não tiver uma conta transportadora, vá a:
    - [Criar uma conta FedEx,](https://www.fedex.com/en-us/create-account.html)ou
    - [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Passo 1: Criar um emprego de exportação

Execute os seguintes passos para criar uma função de exportação no portal Azure.

1. Faça login em <https://portal.azure.com/> .
2. Aceda a **todos os serviços > armazenamento > empregos de importação/exportação.**

    ![Ir para os postos de trabalho de importação/exportação](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Clique **em Criar Trabalho de Importação/Exportação.**

    ![Clique em Trabalho de importação/exportação](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. No **Básico:**

    - Selecione **Exportação de Azure**.
    - Insira um nome descritivo para o trabalho de exportação. Use o nome que escolher para acompanhar o progresso dos seus trabalhos.
        - O nome pode conter apenas letras minúsculas, números, hífens e sublinhados.
        - O nome deve começar com uma letra, e pode não conter espaços.
    - Selecione uma subscrição.
    - Insira ou selecione um grupo de recursos.

        ![Noções básicas](./media/storage-import-export-data-from-blobs/export-from-blob3.png)

5. Em **detalhes de trabalho:**

    - Selecione a conta de armazenamento onde residem os dados a exportar. Utilize uma conta de armazenamento perto do local onde está localizado.
    - A localização de entrega é automaticamente povoada com base na região da conta de armazenamento selecionada.
    - Especifique os dados blob que deseja exportar da sua conta de armazenamento para a sua unidade ou unidades em branco.
    - Opte por **exportar todos os** dados blob na conta de armazenamento.

         ![Exportar tudo](./media/storage-import-export-data-from-blobs/export-from-blob4.png)

    - Pode especificar quais recipientes e bolhas para exportar.
        - **Para especificar uma bolha para exportar:** Utilize o seletor **Igual A.** Especifique o caminho relativo para a bolha, começando pelo nome do recipiente. Utilize *$root* para especificar o recipiente de raiz.
        - **Para especificar todas as bolhas que começam com um prefixo:** Utilize o seletor **'Iniciar'.** Especifique o prefixo, começando com um corte para a frente '/'. O prefixo pode ser o prefixo do nome do recipiente, o nome completo do recipiente ou o nome completo do recipiente seguido do prefixo do nome blob. Tem de fornecer os caminhos blob em formato válido para evitar erros durante o processamento, como mostra esta imagem. Para mais informações, consulte [exemplos de caminhos de bolhas válidos.](#examples-of-valid-blob-paths)

           ![Exportar recipientes e bolhas selecionados](./media/storage-import-export-data-from-blobs/export-from-blob5.png)

    - Pode exportar a partir do ficheiro da lista de bolhas.

        ![Exportação a partir de ficheiro de lista de bolhas](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  

   > [!NOTE]
   > Se a bolha a exportar estiver a ser utilizada durante a cópia de dados, o serviço Azure Import/Export tira uma foto da bolha e copia o instantâneo.

6. Informações **de envio em troca:**

    - Selecione o transportador da lista de retirada. Se quiser utilizar uma transportadora diferente da FedEx/DHL, escolha uma opção existente a partir do dropdown. Contacte a equipa de Operações da Caixa de Dados da Azure `adbops@microsoft.com`  com as informações relativas à transportadora que pretende utilizar.
    - Introduza um número de conta transportadora válido que criou com essa transportadora. A Microsoft utiliza esta conta para enviar as unidades de volta para si assim que o seu trabalho de exportação estiver concluído.
    - Forneça um nome de contacto completo e válido, telefone, e-mail, endereço de rua, cidade, zip, estado/província e país/região.

        > [!TIP]
        > Em vez de especificar um endereço de e-mail para um único utilizador, forneça um e-mail de grupo. Isto garante que recebe notificações mesmo que um administrador saia.

7. Em **Resumo:**

    - Reveja os detalhes do trabalho.
    - Tome nota do nome do trabalho e disponibilize o endereço de envio do datacenter Azure para envio de discos para Azure.

        > [!NOTE]
        > Envie sempre os discos para o datacenter indicado no portal Azure. Se os discos forem enviados para o centro de dados errado, o trabalho não será processado.

    - Clique **em OK** para concluir a criação de emprego de exportação.

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Passo 2: Envie as unidades

Se não souber o número de unidades necessárias, vá ao [Verificar o número de unidades](#check-the-number-of-drives). Se souber o número de unidades, dirija-se aos carros.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Passo 3: Atualizar o trabalho com informações de rastreio

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Passo 4: Receber os discos

Quando o painel de instrumentos informa que o trabalho está concluído, os discos são enviados para si e o número de rastreio do envio está disponível no portal.

1. Depois de receber as unidades com dados exportados, precisa de obter as teclas BitLocker para desbloquear as unidades. Vá para o trabalho de exportação no portal Azure. Clique no **separador Importação/Exportação.**
2. Selecione e clique no seu trabalho de exportação da lista. Vá à **Encriptação** e copie as chaves.

   ![Ver chaves BitLocker para trabalho de exportação](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. Utilize as teclas BitLocker para desbloquear os discos.

A exportação está completa.

## <a name="step-5-unlock-the-disks"></a>Passo 5: Desbloquear os discos

Utilize o seguinte comando para desbloquear a unidade:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`  

Aqui está um exemplo da entrada da amostra.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

Neste momento, pode apagar o trabalho ou deixá-lo. Os empregos são automaticamente apagados após 90 dias.

## <a name="check-the-number-of-drives"></a>Verifique o número de unidades

Este passo *opcional* ajuda-o a determinar o número de unidades necessárias para o trabalho de exportação. Execute este passo num sistema Windows que executa uma [versão de SISTEMA Suportado](storage-import-export-requirements.md#supported-operating-systems).

1. [Descarregue a versão 1 do WAImportExport](https://www.microsoft.com/download/details.aspx?id=42659) no sistema Windows.
2. Desaperte a pasta predefinido `waimportexportv1` . Por exemplo, `C:\WaImportExportV1`.
3. Abra uma janela de linha PowerShell ou de linha de comando com privilégios administrativos. Para alterar o diretório para a pasta desapertado, execute o seguinte comando:

   `cd C:\WaImportExportV1`

4. Para verificar o número de discos necessários para as bolhas selecionadas, verifique o seguinte comando:

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Os parâmetros são descritos no quadro seguinte:

    |Parâmetro de linha de comando|Descrição|  
    |--------------------------|-----------------|  
    |**/logdir:**|Opcional. O diretório de registos. Os ficheiros de registo verboso são escritos neste diretório. Se não for especificado, o diretório atual é utilizado como diretório de registo.|  
    |**/sn:**|Obrigatório. O nome do armazém é responsável pelo trabalho de exportação.|  
    |**/sk:**|Só é necessário se não for especificado um SAS de contentor. A conta-chave para o armazenamento é a conta do trabalho de exportação.|  
    |**/csas:**|Só é necessário se não for especificada uma chave de conta de armazenamento. O contentor SAS para a listagem das bolhas a exportar na laboral de exportação.|  
    |**/ExportBlobListFile:**|Obrigatório. Caminho para o ficheiro XML que contém lista de caminhos blob ou prefixos de caminhos blob para as bolhas a exportar. O formato de ficheiro utilizado no `BlobListBlobPath` elemento na operação [Put Job](/rest/api/storageimportexport/jobs) da API do serviço de importação/exportação REST.|  
    |**/DriveSize:**|Obrigatório. O tamanho das unidades a utilizar para uma tarefa de exportação, *por exemplo,* 500 GB, 1,5 TB.|  

    Consulte um [exemplo do comando PreviewExport](#example-of-previewexport-command).

5. Verifique se pode ler/escrever para as unidades que serão enviadas para o trabalho de exportação.

### <a name="example-of-previewexport-command"></a>Exemplo do comando PreviewExport

O exemplo a seguir demonstra o `PreviewExport` comando:  

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```  

O ficheiro da lista de bolhas de exportação pode conter nomes blob e prefixos blob, como mostrado aqui:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

A Ferramenta de Importação/Exportação Azure lista todas as bolhas a exportar e calcula como embalá-las em unidades do tamanho especificado, tendo em conta as despesas necessárias, estimando depois o número de unidades necessárias para manter as bolhas e impulsionar as informações de utilização.  

Aqui está um exemplo da saída, com registos informativos omitidos:  

```powershell
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

## <a name="examples-of-valid-blob-paths"></a>Exemplos de caminhos de bolhas válidos

A tabela a seguir mostra exemplos de caminhos de bolhas válidos:

   | Seletor | Caminho da Bolha | Descrição |
   | --- | --- | --- |
   | Começa com |/ |Exporta todas as bolhas na conta de armazenamento |
   | Começa com |/$root/ |Exporta todas as bolhas no recipiente raiz |
   | Começa com |/livro |Exporta todas as bolhas em qualquer recipiente que comece com **livro** de prefixos |
   | Começa com |/música/ |Exporta todas as bolhas na **música** de contentores |
   | Começa com |/música/amor |Exporta todas as bolhas na **música** de recipiente que começam com **amor** prefixo |
   | Igual a |$root/logo.bmp |Exportações blob **logo.bmp** no recipiente raiz |
   | Igual a |vídeos/story.mp4 |Exportações blob **story.mp4** em **vídeos** de contentores |

## <a name="next-steps"></a>Passos seguintes

- [Ver o estado do trabalho e da unidade](storage-import-export-view-drive-status.md)
- [Rever os requisitos de importação/exportação](storage-import-export-requirements.md)
