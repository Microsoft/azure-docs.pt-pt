---
title: Usando a importação/exportação do Azure para transferir dados para BLOBs do Azure | Microsoft Docs
description: Saiba como criar trabalhos de importação e exportação no portal do Azure para transferir dados de e para BLOBs do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/06/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 723cd78b1c7325300513664b64f7ca77ac71bcdd
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978533"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Usar o serviço de importação/exportação do Azure para importar dados para o armazenamento de BLOBs do Azure

Este artigo fornece instruções passo a passo sobre como usar o serviço de importação/exportação do Azure para importar com segurança grandes quantidades de dados para o armazenamento de BLOBs do Azure. Para importar dados para BLOBs do Azure, o serviço exige que você envie unidades de disco criptografadas contendo seus dados para um datacenter do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um trabalho de importação para transferir dados para o armazenamento de BLOBs do Azure, Examine cuidadosamente e conclua a lista de pré-requisitos a seguir para esse serviço.
Você deve:

- Ter uma assinatura ativa do Azure que possa ser usada para o serviço de importação/exportação.
- Ter pelo menos uma conta de armazenamento do Azure com um contêiner de armazenamento. Consulte a lista de [contas de armazenamento e tipos de armazenamento com suporte para o serviço de importação/exportação](storage-import-export-requirements.md).
    - Para obter informações sobre como criar uma nova conta de armazenamento, consulte [como criar uma conta de armazenamento](storage-account-create.md). 
    - Para obter informações sobre o contêiner de armazenamento, vá para [criar um contêiner de armazenamento](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Ter um número adequado de discos de [tipos com suporte](storage-import-export-requirements.md#supported-disks).
- Ter um sistema Windows executando uma [versão de sistema operacional com suporte](storage-import-export-requirements.md#supported-operating-systems).
- Habilite o BitLocker no sistema Windows. Consulte [como habilitar o BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Baixe a versão 1 do WAImportExport](https://www.microsoft.com/download/details.aspx?id=42659) no sistema do Windows. Descompacte para a pasta padrão `waimportexportv1`. Por exemplo, `C:\WaImportExportV1`.
- Ter uma conta do FedEx/DHL. Se você quiser usar uma operadora diferente de FedEx/DHL, contate a equipe de operações Azure Data Box em `adbops@microsoft.com`.  
    - A conta deve ser válida, deve ter um saldo e deve ter recursos de envio de retorno.
    - Gerar um número de rastreamento para o trabalho de exportação.
    - Cada trabalho deve ter um número de controle separado. Não há suporte para vários trabalhos com o mesmo número de acompanhamento.
    - Se você não tiver uma conta da operadora, vá para:
        - [Criar uma conta do FedEx](https://www.fedex.com/en-us/create-account.html)ou
        - [Crie uma conta da DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Etapa 1: preparar as unidades

Esta etapa gera um arquivo de diário. O arquivo de diário armazena informações básicas, como o número de série da unidade, a chave de criptografia e os detalhes da conta de armazenamento.

Execute as etapas a seguir para preparar as unidades.

1.  Conecte as unidades de disco ao sistema do Windows por meio de conectores SATA.
1.  Crie um único volume NTFS em cada unidade. Atribua uma letra de unidade ao volume. Não use montagem.
2.  Habilite a criptografia BitLocker no volume NTFS. Se estiver usando um sistema Windows Server, use as instruções em [como habilitar o BitLocker no Windows server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Copie os dados para o volume criptografado. Use o recurso arrastar e soltar ou Robocopy ou qualquer ferramenta de cópia desse tipo.
4.  Abra uma janela de linha de comando ou PowerShell com privilégios administrativos. Para alterar o diretório para a pasta descompactada, execute o seguinte comando:

    `cd C:\WaImportExportV1`
5.  Para obter a chave do BitLocker da unidade, execute o seguinte comando:

    `manage-bde -protectors -get <DriveLetter>:`
6.  Para preparar o disco, execute o comando a seguir. **Dependendo do tamanho dos dados, isso pode levar várias horas a dias.**

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```
    Um arquivo de diário é criado na mesma pasta em que você executou a ferramenta. Dois outros arquivos também são criados-um arquivo *. xml* (pasta em que você executa a ferramenta) e um arquivo *drive-manifest. xml* (pasta onde os dados residem).

    Os parâmetros usados são descritos na tabela a seguir:

    |Opção  |Descrição  |
    |---------|---------|
    |/j:     |O nome do arquivo de diário, com a extensão. JRN. Um arquivo de diário é gerado por unidade. Recomendamos que você use o número de série do disco como o nome do arquivo de diário.         |
    |/ID     |A ID da sessão. Use um número de sessão exclusivo para cada instância do comando.      |
    |/t:     |A letra da unidade do disco a ser enviado. Por exemplo, `D`de unidade.         |
    |/bk:     |A chave do BitLocker para a unidade. Sua senha numérica da saída de `manage-bde -protectors -get D:`      |
    |/srcdir:     |A letra da unidade do disco a ser enviado seguido por `:\`. Por exemplo, `D:\`.         |
    |/dstdir:     |O nome do contêiner de destino no armazenamento do Azure.         |
    |/blobtype:     |Esta opção especifica o tipo de BLOBs para os quais você deseja importar os dados. Para BLOBs de blocos, isso é `BlockBlob` e para BLOBs de páginas, é `PageBlob`.         |
    |/skipwrite:     |A opção que especifica que não há novos dados necessários para serem copiados e os dados existentes no disco devem ser preparados.          |
    |/enablecontentmd5:     |A opção quando habilitada, garante que o MD5 seja computado e definido como `Content-md5` Propriedade em cada blob. Use esta opção somente se desejar usar o campo `Content-md5` depois que os dados forem carregados no Azure. <br> Essa opção não afeta a verificação de integridade de dados (que ocorre por padrão). A configuração aumenta o tempo necessário para carregar dados na nuvem.          |
7. Repita a etapa anterior para cada disco que precisa ser enviado. Um arquivo de diário com o nome fornecido é criado para cada execução da linha de comando.

    > [!IMPORTANT]
    > - Junto com o arquivo de diário, um arquivo de `<Journal file name>_DriveInfo_<Drive serial ID>.xml` também é criado na mesma pasta em que a ferramenta reside. O arquivo. xml é usado no lugar do arquivo de diário ao criar um trabalho se o arquivo de diário for muito grande.

## <a name="step-2-create-an-import-job"></a>Etapa 2: criar um trabalho de importação

Execute as etapas a seguir para criar um trabalho de importação no portal do Azure.

1. Faça logon no https://portal.azure.com/.
2. Vá para **todos os serviços > armazenamento > trabalhos de importação/exportação**.

    ![Ir para trabalhos de importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Clique em **criar trabalho de importação/exportação**.

    ![Clique em criar trabalho de importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Em **noções básicas**:

   - Selecione **importar no Azure**.
   - Insira um nome descritivo para o trabalho de importação. Use o nome para acompanhar o progresso de seus trabalhos.
       - O nome pode conter apenas letras minúsculas, números e hifens.
       - O nome deve começar com uma letra e não pode conter espaços.
   - Selecionar uma subscrição.
   - Insira ou selecione um grupo de recursos.  

     ![Criar trabalho de importação – etapa 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. Em **detalhes do trabalho**:

    - Carregue os arquivos de diário da unidade que você obteve durante a etapa de preparação da unidade. Se `waimportexport.exe version1` foi usado, carregue um arquivo para cada unidade que você preparou. Se o tamanho do arquivo de diário exceder 2 MB, você poderá usar o `<Journal file name>_DriveInfo_<Drive serial ID>.xml` também criado com o arquivo de diário.
    - Selecione a conta de armazenamento de destino na qual os dados residirão.
    - O local chegada é preenchido automaticamente com base na região da conta de armazenamento selecionada.

   ![Criar trabalho de importação – etapa 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. Em **informações de envio de retorno**:

   - Selecione a transportadora na lista suspensa. Se você quiser usar uma operadora diferente de FedEx/DHL, escolha uma opção existente na lista suspensa. Contate a equipe de operações de Azure Data Box em `adbops@microsoft.com` com as informações sobre a transportadora que você planeja usar.
   - Insira um número de conta da transportadora válido que você criou com essa portadora. A Microsoft usa essa conta para enviar as unidades de volta para você quando o trabalho de importação for concluído. Se você não tiver um número de conta, crie uma conta de Carrier [FedEx](https://www.fedex.com/us/oadr/) ou [DHL](https://www.dhl.com/) .
   - Forneça um nome de contato completo e válido, telefone, email, endereço, cidade, CEP, estado/província e país/região.

       > [!TIP]
       > Em vez de especificar um endereço de email para um único usuário, forneça um email do grupo. Isso garante que você receba notificações mesmo que um administrador saia.

     ![Criar trabalho de importação – etapa 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

5. No **Resumo**:

   - Examine as informações do trabalho fornecidas no resumo. Anote o nome do trabalho e o endereço de envio do datacenter do Azure para enviar discos de volta para o Azure. Essas informações são usadas posteriormente no rótulo de remessa.
   - Clique em **OK** para criar o trabalho de importação.

     ![Criar trabalho de importação – etapa 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>Etapa 3: enviar as unidades

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Etapa 4: atualizar o trabalho com informações de rastreamento

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Etapa 5: verificar o carregamento de dados no Azure

Acompanhe a conclusão do trabalho. Quando o trabalho for concluído, verifique se seus dados foram carregados no Azure. Exclua os dados locais somente depois de verificar se o upload foi bem-sucedido.

## <a name="next-steps"></a>Passos seguintes

* [Exibir o status do trabalho e da unidade](storage-import-export-view-drive-status.md)
* [Examinar os requisitos de importação/exportação](storage-import-export-requirements.md)
