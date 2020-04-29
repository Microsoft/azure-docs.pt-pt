---
title: Utilização da Importação/Exportação Azure para transferir dados para a Azure Blobs [ Microsoft Docs
description: Aprenda a criar postos de trabalho de importação e exportação no portal Azure para transferir dados de e para o Azure Blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 570c663861361a19190f6fb5d608b6aa029a0885
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80282499"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Utilize o serviço azure import/exportação para importar dados para o armazenamento de Blob Azure

Este artigo fornece instruções passo a passo sobre como utilizar o serviço de importação/exportação Azure para importar com segurança grandes quantidades de dados para o armazenamento de Azure Blob. Para importar dados para o Azure Blobs, o serviço requer que envie discos encriptados contendo os seus dados para um datacenter Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um trabalho de importação para transferir dados para o Armazenamento De Blob Azure, reveja cuidadosamente e complete a seguinte lista de pré-requisitos para este serviço.
Tens de o fazer:

* Tenha uma subscrição Azure ativa que pode ser utilizada para o serviço de importação/exportação.
* Tenha pelo menos uma conta de Armazenamento Azure com um recipiente de armazenamento. Consulte a lista de contas de armazenamento suportadas e tipos de [armazenamento para o serviço de importação/exportação](storage-import-export-requirements.md).
  * Para obter informações sobre a criação de uma nova conta de armazenamento, consulte [Como Criar uma Conta de Armazenamento](storage-account-create.md).
  * Para obter informações sobre o recipiente de armazenamento, vá criar [um recipiente](../blobs/storage-quickstart-blobs-portal.md#create-a-container)de armazenamento .
* Distenha um número adequado de discos de [tipos suportados](storage-import-export-requirements.md#supported-disks).
* Tenha um sistema Windows executando uma [versão Sistema Operativo suportada](storage-import-export-requirements.md#supported-operating-systems).
* Ativar o BitLocker no sistema Windows. Ver [Como ativar o BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* Faça o download da mais recente versão 1 do [WAImportExport](https://www.microsoft.com/download/details.aspx?id=42659) no sistema Windows. A versão mais recente da ferramenta tem atualizações de segurança para permitir um protetor externo para a tecla BitLocker e a funcionalidade de modo de desbloqueio atualizado.

  * Desapertar para `waimportexportv1`a pasta predefinida . Por exemplo, `C:\WaImportExportV1`.
* Tenha uma conta FedEx/DHL. Se pretender utilizar uma transportadora que não a FedEx/DHL, `adbops@microsoft.com`contacte a equipa de Operações de Dados da Azure em .  
  * A conta deve ser válida, deve ter saldo, e deve ter capacidades de envio de retorno.
  * Gere um número de rastreio para o trabalho de exportação.
  * Cada trabalho deve ter um número de rastreamento separado. Não são apoiados múltiplos trabalhos com o mesmo número de rastreio.
  * Se não tiver uma conta de transportadora, vá a:
    * [Criar uma conta FedEX,](https://www.fedex.com/en-us/create-account.html)ou
    * [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Passo 1: Preparar as unidades

Este passo gera um ficheiro de diário. O ficheiro do diário armazena informações básicas, tais como número de série de unidade, chave de encriptação e detalhes da conta de armazenamento.

Execute os seguintes passos para preparar as unidades.

1. Ligue as suas unidades de disco ao sistema Windows através de conectores SATA.
2. Crie um único volume NTFS em cada unidade. Atribuir uma carta de condução ao volume. Não utilize pontos de montagem.
3. Ativar a encriptação BitLocker no volume NTFS. Se utilizar um sistema de servidor windows, utilize as instruções em [Como ativar o BitLocker no Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
4. Copiar dados para volume encriptado. Utilize arrasto e queda ou robocopia ou qualquer ferramenta de cópia. Um ficheiro de diário *(.jrn)* é criado na mesma pasta onde executa a ferramenta.

   Se a unidade estiver bloqueada e tiver de desbloquear a unidade, os passos para desbloquear podem ser diferentes dependendo da sua caixa de utilização.

   * Se tiver adicionado dados a uma unidade pré-encriptada (a ferramenta WAImportExport não foi utilizada para encriptação), utilize a tecla BitLocker (uma palavra-passe numérica que especifica) no popup para desbloquear a unidade.

   * Se tiver adicionado dados a uma unidade encriptada pela ferramenta WAImportExport, utilize o seguinte comando para desbloquear a unidade:

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Abra uma janela powerShell ou linha de comando com privilégios administrativos. Para alterar o diretório para a pasta desapertada, execute o seguinte comando:

    `cd C:\WaImportExportV1`
6. Para obter a chave BitLocker da unidade, executar o seguinte comando:

    `manage-bde -protectors -get <DriveLetter>:`
7. Para preparar o disco, execute o seguinte comando. **Dependendo do tamanho dos dados, isto pode demorar várias horas a dias.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Um ficheiro de diário é criado na mesma pasta onde executou a ferramenta. São também criados dois outros ficheiros - um ficheiro *.xml* (pasta onde executa a ferramenta) e um ficheiro *drive-manifest.xml* (pasta onde os dados residem).

    Os parâmetros utilizados são descritos na tabela seguinte:

    |Opção  |Descrição  |
    |---------|---------|
    |/j:     |O nome do ficheiro do diário, com a extensão .jrn. Um ficheiro de diário é gerado por unidade. Recomendamos que utilize o número de série do disco como nome de ficheiro do diário.         |
    |/id:     |A identificação da sessão. Utilize um número de sessão único para cada instância do comando.      |
    |/t:     |A carta de condução do disco a ser enviada. Por exemplo, `D`conduzir .         |
    |/bk:     |A chave BitLocker para a unidade. Sua senha numérica da saída de`manage-bde -protectors -get D:`      |
    |/srcdir:     |A letra de unidade do disco `:\`a ser enviada seguida por . Por exemplo, `D:\`.         |
    |/dstdir:     |O nome do contentor de destino em Azure Storage.         |
    |/blobtype:     |Esta opção especifica o tipo de bolhas para as quais pretende importar os dados. Para bolhas de bloco, `BlockBlob` isto é e para `PageBlob`bolhas de página, é .         |
    |/skipwrite:     |A opção que especifica que não existem novos dados necessários para serem copiados e os dados existentes no disco devem ser preparados.          |
    |/enablecontentmd5:     |A opção quando ativada, garante que o MD5 é calculado e definido como `Content-md5` propriedade em cada bolha. Utilize esta opção apenas `Content-md5` se pretender utilizar o campo depois de os dados serem enviados para o Azure. <br> Esta opção não afeta a verificação da integridade dos dados (que ocorre por defeito). A definição aumenta o tempo de envio de dados para a nuvem.          |
8. Repita o passo anterior para cada disco que precisa de ser enviado. Um ficheiro de diário com o nome fornecido é criado para cada execução da linha de comando.

    > [!IMPORTANT]
    > * Juntamente com o `<Journal file name>_DriveInfo_<Drive serial ID>.xml` ficheiro do diário, é também criado um ficheiro na mesma pasta onde a ferramenta reside. O ficheiro .xml é utilizado no lugar do ficheiro do diário ao criar um trabalho se o ficheiro do diário for demasiado grande.

## <a name="step-2-create-an-import-job"></a>Passo 2: Criar um emprego de importação

Execute os seguintes passos para criar um trabalho de importação no portal Azure.

1. Iniciar sessão para https://portal.azure.com/.
2. Vá a **Todos os serviços > armazenamento > empregos de importação/exportação.**

    ![Ir para empregos de importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Clique em **Criar Import/Exportar Trabalho**.

    ![Clique em Criar Import/exportação](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. No **Básico:**

   * Selecione **Importar para Azure**.
   * Insira um nome descritivo para o trabalho de importação. Use o nome para acompanhar o progresso dos seus trabalhos.
       * O nome pode conter apenas letras minúsculas, números e hífenes.
       * O nome deve começar com uma letra, e não pode conter espaços.
   * Selecione uma subscrição.
   * Insira ou selecione um grupo de recursos.  

     ![Criar emprego de importação - Passo 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

5. Em **detalhes de trabalho:**

   * Faça upload dos ficheiros do diário de unidade que obteve durante a fase de preparação da unidade. Se `waimportexport.exe version1` foi utilizado, faça upload de um ficheiro para cada unidade que preparou. Se o tamanho do ficheiro do diário exceder `<Journal file name>_DriveInfo_<Drive serial ID>.xml` 2 MB, então pode utilizar o também criado com o ficheiro do diário.
   * Selecione a conta de armazenamento de destino onde os dados irão residir.
   * O local de entrega é automaticamente povoado com base na região da conta de armazenamento selecionada.

   ![Criar emprego de importação - Passo 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

6. Em **Troca informações de envio:**

   * Selecione o porta-aviões da lista de dropdown. Se quiser utilizar uma transportadora que não a FedEx/DHL, escolha uma opção existente a partir do dropdown. Contacte a equipa de `adbops@microsoft.com` Operações de Caixa de Dados azure com as informações relativas à transportadora que pretende utilizar.
   * Introduza um número de conta transportadora válido que criou com essa transportadora. A Microsoft usa esta conta para enviar as unidades de volta para si assim que o seu trabalho de importação estiver completo. Se não tiver um número de conta, crie uma conta de porta-aviões [FedEx](https://www.fedex.com/us/oadr/) ou [DHL.](https://www.dhl.com/)
   * Forneça um nome de contacto completo e válido, telefone, e-mail, endereço de rua, cidade, zip, estado/província e país/região.

       > [!TIP]
       > Em vez de especificar um endereço de e-mail para um único utilizador, forneça um e-mail de grupo. Isto garante que recebe notificações mesmo que um administrador saia.

     ![Criar emprego de importação - Passo 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

7. No **resumo:**

   * Reveja as informações de trabalho fornecidas no resumo. Tome nota do nome do trabalho e do endereço de envio do Centro de Dados Azure para enviar discos de volta para Azure. Esta informação é utilizada posteriormente no rótulo de envio.
   * Clique **em OK** para criar o trabalho de importação.

     ![Criar emprego de importação - Passo 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-optional-configure-customer-managed-key"></a>Passo 3 (Opcional): Configure a chave gerida pelo cliente

Salte este passo e vá para o próximo passo se quiser usar a chave gerida pela Microsoft para proteger as suas teclas BitLocker para as unidades. Para configurar a sua própria chave para proteger a chave BitLocker, siga as instruções em Chaves geridas pelo cliente Configurado com cofre de [chave Azure para importação/exportação azure no portal Azure](storage-import-export-encryption-key-portal.md)

## <a name="step-4-ship-the-drives"></a>Passo 4: Envie as unidades

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Passo 5: Atualizar o trabalho com informações de rastreio

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Passo 6: Verificar o upload de dados para o Azure

Acompanhe o trabalho até à conclusão. Uma vez concluída a função, verifique se os seus dados foram enviados para o Azure. Apague os dados no local apenas depois de ter verificado que o upload foi bem sucedido.

## <a name="next-steps"></a>Passos seguintes

* [Ver o estado do trabalho e da unidade](storage-import-export-view-drive-status.md)
* [Rever os requisitos de importação/exportação](storage-import-export-requirements.md)
