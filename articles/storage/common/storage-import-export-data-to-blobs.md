---
title: Utilização da Azure Import/Export para transferir dados para Azure Blobs Microsoft Docs
description: Saiba como criar empregos de importação e exportação no portal Azure para transferir dados de e para Azure Blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 6d12c0ce0df44c37f4e7df49df2c11301513917c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514210"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Utilizar o serviço Azure Import/Export para importar dados para a Azure Blob Storage

Este artigo fornece instruções passo a passo sobre como utilizar o serviço Azure Import/Export para importar de forma segura grandes quantidades de dados para o armazenamento da Azure Blob. Para importar dados para a Azure Blobs, o serviço requer que envie unidades de disco encriptadas contendo os seus dados para um datacenter Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar uma função de importação para transferir dados para a Azure Blob Storage, reveja cuidadosamente e complete a seguinte lista de pré-requisitos para este serviço.
Tens de o fazer:

* Tenha uma subscrição ativa do Azure que pode ser usada para o serviço de importação/exportação.
* Tenha pelo menos uma conta de Armazenamento Azure com um recipiente de armazenamento. Consulte a lista de contas de [armazenamento suportadas e tipos de armazenamento para o serviço de importação/exportação.](storage-import-export-requirements.md)
  * Para obter informações sobre a criação de uma nova conta de armazenamento, consulte [Como Criar uma Conta de Armazenamento.](storage-account-create.md)
  * Para obter informações sobre o recipiente de armazenamento, vá para [criar um recipiente de armazenamento](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
* Dispor de um número adequado de discos de [tipos suportados.](storage-import-export-requirements.md#supported-disks)
* Dispor de um sistema Windows com uma [versão de SISTEMA Suportada.](storage-import-export-requirements.md#supported-operating-systems)
* Ativar o BitLocker no sistema Windows. Ver [Como ativar o BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* [Descarregue a versão 1 mais recente do WAImportExport](https://www.microsoft.com/download/details.aspx?id=42659) no sistema Windows. A versão mais recente da ferramenta possui atualizações de segurança para permitir um protetor externo para a tecla BitLocker e a funcionalidade de modo de desbloqueio atualizado.

  * Desaperte a pasta predefinido `waimportexportv1` . Por exemplo, `C:\WaImportExportV1`.
* Tenha uma conta FedEx/DHL. Se pretender utilizar uma transportadora que não seja a FedEx/DHL, contacte a equipa de Operações da Caixa de Dados da Azure `adbops@microsoft.com` em .  
  * A conta deve ser válida, deve ter equilíbrio, e deve ter capacidades de envio de retorno.
  * Gere um número de rastreio para o trabalho de exportação.
  * Cada tarefa deve ter um número de controlo separado. Não são apoiados várias tarefas com o mesmo número de controlo.
  * Se não tiver uma conta transportadora, vá a:
    * [Criar uma conta FedEX,](https://www.fedex.com/en-us/create-account.html)ou
    * [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Passo 1: Preparar as unidades

Este passo gera um ficheiro de diário. O ficheiro do diário armazena informações básicas, tais como o número de série de unidade, a chave de encriptação e os detalhes da conta de armazenamento.

Execute os seguintes passos para preparar as unidades.

1. Ligue as suas unidades de disco ao sistema Windows através de conectores SATA.
2. Crie um único volume NTFS em cada unidade. Atribua uma carta de unidade ao volume. Não utilize pontos de montagem.
3. Ativar a encriptação BitLocker no volume NTFS. Se utilizar um sistema Windows Server, utilize as instruções em Como ativar o [BitLocker no Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
4. Copie os dados para o volume encriptado. Utilize arrastar e largar ou Robocopia ou qualquer ferramenta de cópia. Um ficheiro diário *(.jrn)* é criado na mesma pasta onde funciona a ferramenta.

   Se a unidade estiver bloqueada e necessitar de desbloquear a unidade, os passos para desbloquear podem ser diferentes dependendo da sua caixa de utilização.

   * Se tiver adicionado dados a uma unidade pré-encriptada (a ferramenta WAImportExport não foi utilizada para encriptação), utilize a tecla BitLocker (uma palavra-passe numérica que especifique) no popup para desbloquear a unidade.

   * Se tiver adicionado dados a uma unidade encriptada pela ferramenta WAImportExport, utilize o seguinte comando para desbloquear a unidade:

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Abra uma janela de linha PowerShell ou de linha de comando com privilégios administrativos. Para alterar o diretório para a pasta desapertado, execute o seguinte comando:

    `cd C:\WaImportExportV1`
6. Para obter a chave BitLocker da unidade, executar o seguinte comando:

    `manage-bde -protectors -get <DriveLetter>:`
7. Para preparar o disco, executar o seguinte comando. **Dependendo do tamanho dos dados, isto pode demorar várias horas a dias.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Um ficheiro de diário é criado na mesma pasta onde executou a ferramenta. São também criados dois outros ficheiros - um ficheiro *.xml* (pasta onde executa a ferramenta) e um ficheiro *drive-manifest.xml* (pasta onde os dados residem).

    Os parâmetros utilizados são descritos no quadro seguinte:

    |Opção  |Descrição  |
    |---------|---------|
    |/j:     |O nome do ficheiro do diário, com a extensão .jrn. Um ficheiro de diário é gerado por unidade. Recomendamos que utilize o número de série do disco como nome de ficheiro do diário.         |
    |/id:     |A identificação da sessão. Utilize um número de sessão único para cada instância do comando.      |
    |/t:     |A carta de condução do disco a ser enviada. Por exemplo, `D` dirija.         |
    |/bk:     |A chave BitLocker para a unidade. Sua senha numérica a partir da saída de `manage-bde -protectors -get D:`      |
    |/srcdir:     |A letra de acionamento do disco a ser enviada é seguida de `:\` . Por exemplo, `D:\`.         |
    |/dstdir:     |O nome do contentor de destino no Azure Storage.         |
    |/blobtype:     |Esta opção especifica o tipo de bolhas para as quais pretende importar os dados. Para bolhas de bloco, isto é `BlockBlob` e para bolhas de página, é `PageBlob` .         |
    |/skipwrite:     |A opção que especifica que não há novos dados necessários para serem copiados e os dados existentes no disco devem ser preparados.          |
    |/enablecontmd5:     |A opção, quando ativada, garante que o MD5 é calculado e definido como `Content-md5` propriedade em cada bolha. Utilize esta opção apenas se pretender utilizar o `Content-md5` campo depois de os dados serem enviados para a Azure. <br> Esta opção não afeta a verificação de integridade dos dados (que ocorre por padrão). A definição aumenta o tempo necessário para carregar dados para a nuvem.          |
8. Repita o passo anterior para cada disco que precisa de ser enviado. Um ficheiro de diário com o nome fornecido é criado para cada execução da linha de comando.

    > [!IMPORTANT]
    > * Juntamente com o ficheiro do diário, `<Journal file name>_DriveInfo_<Drive serial ID>.xml` um ficheiro também é criado na mesma pasta onde reside a ferramenta. O ficheiro .xml é utilizado no lugar do ficheiro do diário quando se cria um trabalho se o ficheiro do diário for demasiado grande.

## <a name="step-2-create-an-import-job"></a>Passo 2: Criar um trabalho de importação

Execute os seguintes passos para criar uma função de importação no portal Azure.

1. Faça login em https://portal.azure.com/ .
2. Aceda a **todos os serviços > armazenamento > empregos de importação/exportação.**

    ![Ir para os postos de trabalho de importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Clique **em Criar Trabalho de Importação/Exportação.**

    ![Clique em Criar Trabalho de Importação/Exportação](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. No **Básico:**

   * Selecione **Import into Azure**.
   * Introduza um nome descritivo para o trabalho de importação. Use o nome para acompanhar o progresso dos seus trabalhos.
       * O nome pode conter apenas letras minúsculas, números e hífenes.
       * O nome deve começar com uma letra, e pode não conter espaços.
   * Selecione uma subscrição.
   * Insira ou selecione um grupo de recursos.  

     ![Criar trabalho de importação - Passo 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

5. Em **detalhes de trabalho:**

   * Faça o upload dos ficheiros do diário de unidade que obteve durante o passo de preparação da unidade. Se `waimportexport.exe version1` for utilizado, carrehe um ficheiro para cada unidade que preparou. Se o tamanho do ficheiro do diário exceder 2 MB, então pode utilizar o `<Journal file name>_DriveInfo_<Drive serial ID>.xml` também criado com o ficheiro do diário.
   * Selecione a conta de armazenamento de destino onde os dados irão residir.
   * A localização de entrega é automaticamente povoada com base na região da conta de armazenamento selecionada.

   ![Criar trabalho de importação - Passo 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

6. Informações **de envio em troca:**

   * Selecione o transportador da lista de retirada. Se quiser utilizar uma transportadora diferente da FedEx/DHL, escolha uma opção existente a partir do dropdown. Contacte a equipa de Operações da Caixa de Dados da Azure `adbops@microsoft.com`  com as informações relativas à transportadora que pretende utilizar.
   * Introduza um número de conta transportadora válido que criou com essa transportadora. A Microsoft utiliza esta conta para enviar as unidades de volta para si assim que o seu trabalho de importação estiver concluído. Se não tiver um número de conta, crie uma conta de transportadora [FedEx](https://www.fedex.com/us/oadr/) ou [DHL.](https://www.dhl.com/)
   * Forneça um nome de contacto completo e válido, telefone, e-mail, endereço de rua, cidade, zip, estado/província e país/região.

       > [!TIP]
       > Em vez de especificar um endereço de e-mail para um único utilizador, forneça um e-mail de grupo. Isto garante que recebe notificações mesmo que um administrador saia.

     ![Criar trabalho de importação - Passo 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

7. No **Resumo:**

   * Reveja as informações de trabalho fornecidas no resumo. Tome nota do nome do trabalho e do endereço de envio do datacenter Azure para enviar discos de volta a Azure. Esta informação é usada mais tarde na etiqueta de envio.
   * Clique **em OK** para criar o trabalho de importação.

     ![Criar trabalho de importação - Passo 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-optional-configure-customer-managed-key"></a>Passo 3 (Opcional): Configurar chave gerida pelo cliente

Salte este passo e vá para o próximo passo se quiser utilizar a chave gerida pela Microsoft para proteger as teclas BitLocker para as unidades. Para configurar a sua própria chave para proteger a chave BitLocker, siga as instruções em [chaves geridas pelo cliente com cofre de chave Azure para importação/exportação Azure no portal Azure](storage-import-export-encryption-key-portal.md)

## <a name="step-4-ship-the-drives"></a>Passo 4: Envie as unidades

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Passo 5: Atualizar o trabalho com informações de rastreio

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Passo 6: Verificar o upload de dados para o Azure

Acompanhe o trabalho até à conclusão. Uma vez concluído o trabalho, verifique se os seus dados foram enviados para a Azure. Elimine os dados no local apenas depois de ter verificado que o upload foi bem sucedido.

## <a name="next-steps"></a>Passos seguintes

* [Ver o estado do trabalho e da unidade](storage-import-export-view-drive-status.md)
* [Rever os requisitos de importação/exportação](storage-import-export-requirements.md)
