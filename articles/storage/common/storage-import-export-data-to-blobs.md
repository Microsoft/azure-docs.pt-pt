---
title: A utilização de importar/exportar do Azure para transferir dados para Blobs do Azure | Documentos da Microsoft
description: Saiba como criar a importar e exportar tarefas no portal do Azure para transferir dados para e de Blobs do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/11/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: e826c7a3fc12e819fd6f145d42b7381087d5970c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000240"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Utilize o serviço importar/exportar do Azure para importar dados para armazenamento de Blobs do Azure

Este artigo fornece instruções passo a passo sobre como utilizar o serviço importar/exportar do Azure para importar com segurança grandes quantidades de dados para o armazenamento de Blobs do Azure. Para importar dados para Blobs do Azure, o serviço exige que envie encriptadas unidades de disco que contém os dados num Datacenter do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar uma tarefa de importação para transferir dados para o armazenamento de Blobs do Azure, com cuidado reveja e conclua a lista seguinte de pré-requisitos para este serviço. Tem de:

- Ter uma subscrição do Azure Active Directory que pode ser utilizada para o serviço de importação/exportação.
- Ter pelo menos uma conta de armazenamento do Azure com um contentor de armazenamento. Ver a lista de [contas de armazenamento e tipos de armazenamento suportadas para o serviço importar/exportar](storage-import-export-requirements.md). 
    - Para obter informações sobre como criar uma nova conta de armazenamento, consulte [como criar uma conta de armazenamento](storage-quickstart-create-account.md). 
    - Para obter informações sobre o contentor de armazenamento, aceda a [criar um contentor de armazenamento](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Têm um número adequado de discos [tipos suportados](storage-import-export-requirements.md#supported-disks). 
- Ter um sistema Windows em execução uma [versão do SO suportado](storage-import-export-requirements.md#supported-operating-systems). 
- Ative o BitLocker no sistema Windows. Ver [como ativar o BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Baixe o versão 1 do WAImportExport](https://aka.ms/waiev1) no sistema Windows. Deszipe o para a pasta predefinida `waimportexportv1`. Por exemplo, `C:\WaImportExportV1`.
- Ter uma conta de FedEx/DHL.  
    - A conta tem de ser válida, deve ter o saldo e tem de ter capacidades de envio de devolução.
    - Gere um número de controlo para a tarefa de exportação.
    - Cada tarefa deve ter um número de controlo separado. Várias tarefas com o mesmo número de controlo não são suportadas.
    - Se não tiver uma conta da transportadora, aceda a:
        - [Criar uma conta de FedEX](https://www.fedex.com/en-us/create-account.html), ou 
        - [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Passo 1: Preparar as unidades

Este passo gera um ficheiro de diário. O ficheiro de diário armazena informações básicas, tais como o número de série da unidade, chave de encriptação e detalhes da conta de armazenamento. 

Execute os seguintes passos para preparar as unidades.

1.  Ligar a unidades de disco para o sistema do Windows através de conectores SATA.
1.  Crie um único volume NTFS em cada unidade. Atribua uma letra de unidade ao volume. Não utilize mountpoints.
2.  Ative a encriptação de disco BitLocker no NTFS volume. Se utilizar um sistema Windows Server, utilize as instruções em [como ativar o BitLocker no Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Copie dados para volume criptografado. Usar o arrastar e soltar ou Robocopy ou dessas ferramentas de cópia.
4.  Abra uma janela do PowerShell ou da linha de comandos com privilégios administrativos. Para alterar o diretório para a pasta descompactada, execute o seguinte comando:
    
    `cd C:\WaImportExportV1`
5.  Para obter a chave do BitLocker da unidade, execute o seguinte comando:
    
    ` manage-bde -protectors -get <DriveLetter>: `
6.  Para preparar o disco, execute o seguinte comando. **Dependendo do tamanho de dados, isto pode demorar várias horas a dias.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /sk:<Storage account key> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /skipwrite 
    ```
    É criado um ficheiro de diário na mesma pasta onde executou a ferramenta. Também são criados dois outros arquivos - um *. XML* ficheiro (em que executou a ferramenta de pasta) e um *unidade-manifest. XML* ficheiro (a pasta onde residem a dados).
    
    Os parâmetros utilizados são descritos na tabela a seguir:

    |Opção  |Descrição  |
    |---------|---------|
    |/j:     |O nome do ficheiro de diário, com a extensão de jrn. Um ficheiro de diário é gerado por unidade. Recomendamos que utilize o número de série do disco como o nome de ficheiro de diário.         |
    |/id:     |O ID de sessão. Utilize um número de sessão exclusiva para cada instância do comando.      |
    |/sk:     |A chave de conta de armazenamento do Azure.         |
    |/t:     |A letra de unidade de disco para ser enviado. Por exemplo, unidade `D`.         |
    |/bk:     |A chave do BitLocker para a unidade. A palavra-passe numérica da saída de ` manage-bde -protectors -get D: `      |
    |/srcdir:     |A letra de unidade de disco para ser enviado seguido `:\`. Por exemplo, `D:\`.         |
    |/dstdir:     |O nome do contentor de destino no armazenamento do Azure.         |
    |/skipwrite:     |É a opção que especifica que não existe nenhum novos dados devem ser copiados e dados existentes no disco para estar preparado.          |
7. Repita o passo anterior para cada disco que precisa para ser enviado. É criado um ficheiro de diário com o nome fornecido para cada execução da linha de comandos.
    
    > [!IMPORTANT]
    > - Em conjunto com o ficheiro de diário, um `<Journal file name>_DriveInfo_<Drive serial ID>.xml` arquivo também é criado na mesma pasta onde a ferramenta reside. O ficheiro. xml é usado no lugar do ficheiro de diário, ao criar uma tarefa se o ficheiro de diário é demasiado grande. 

## <a name="step-2-create-an-import-job"></a>Passo 2: Criar uma tarefa de importação

Execute os seguintes passos para criar uma tarefa de importação no portal do Azure.

1. Inicie sessão no https://portal.azure.com/.
2. Aceda a **todos os serviços > armazenamento > tarefas de importação/exportação**. 
    
    ![Vá para tarefas de importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Clique em **criar tarefa de importação/exportação**.

    ![Clique em tarefa de importação/exportação de criar](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Na **Noções básicas**:

   - Selecione **importar para o Azure**.
   - Introduza um nome descritivo para a tarefa de importação. Utilize o nome para controlar o progresso das suas tarefas.
       - O nome pode conter apenas letras minúsculas, números e hífenes.
       - O nome tem de começar com uma letra e não pode conter espaços.
   - Selecione uma subscrição.
   - Introduza ou selecione um grupo de recursos.  

     ![Criar tarefa de importação - passo 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. Na **detalhes da tarefa**:

    - Carregue os ficheiros de diário de unidade que obteve durante o passo de preparação de unidade. Se `waimportexport.exe version1` foi usado, carregar um ficheiro para cada unidade que preparou. Se o tamanho do ficheiro de diário excede 2 MB, em seguida, pode utilizar o `<Journal file name>_DriveInfo_<Drive serial ID>.xml` também criou com o ficheiro de diário. 
    - Selecione a conta de armazenamento de destino onde os dados irão residir. 
    - A localização de redução é preenchida automaticamente com base na região da conta de armazenamento selecionada.
   
   ![Criar tarefa de importação - passo 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. Na **devolver informações sobre o envio**:

   - Selecione a operadora na lista pendente.
   - Introduza um número de conta de operadora válida que tenha criado com esse operadora. A Microsoft utiliza esta conta para enviar as unidades-se ao assim que a tarefa de importação estiver concluída. Se não tiver um número de conta, crie uma [FedEx](https://www.fedex.com/us/oadr/) ou [DHL](http://www.dhl.com/) conta da transportadora.
   - Forneça um nome de contato completo e válido, telefone, e-mail, rua, cidade, zip, estado/província e país/região. 
        
       > [!TIP] 
       > Em vez de especificar um endereço de e-mail para um único utilizador, forneça um e-mail de grupo. Isto garante que recebe as notificações, mesmo que sai de um administrador.

     ![Criar tarefa de importação - passo 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. Na **resumo**:

   - Reveja as informações de tarefa fornecidas no resumo. Anote o nome da tarefa e o endereço para envio discos novamente para o Azure de envio do datacenter do Azure. Estas informações são utilizadas mais tarde na etiqueta de envio.
   - Clique em **OK** para criar a tarefa de importação.

     ![Criar tarefa de importação - passo 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>Passo 3: Envie os discos 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Passo 4: Atualizar a tarefa com informações de registo

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Passo 5: Verificar o carregamento de dados para o Azure

Controle a tarefa até à conclusão. Quando a tarefa estiver concluída, certifique-se de que os seus dados tem carregado para o Azure. Elimine os dados no local apenas após ter verificado que o carregamento foi concluída com êxito.

## <a name="next-steps"></a>Passos Seguintes

* [Ver o estado de tarefa e a unidade](storage-import-export-view-drive-status.md)
* [Reveja os requisitos de importação/exportação](storage-import-export-requirements.md)


