---
title: Obtenha assinatura de acesso partilhado URI para a sua imagem VM baseada no Microsoft Azure [ Mercado Azure
description: Explica como obter a assinatura de acesso partilhado (SAS) URI para a sua imagem VM.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 6fe15fb18d8865911363a4696e44dd7fe1d90c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277808"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Obtenha assinatura de acesso partilhado URI para a sua imagem VM

Durante o processo de publicação, deve fornecer um identificador de recursos uniforme (URI) para cada disco rígido virtual (VHD) associado ao seu SKUs. A Microsoft precisa de ter acesso a estes VHDs durante o processo de certificação. Este artigo explica como gerar uma assinatura de acesso partilhado (SAS) URI para cada VHD. Você introduzirá este URI no separador **SKUs** no Portal do Parceiro cloud.

Ao gerar URIs SAS para os seus VHDs, adere aos seguintes requisitos:

- Apenas VHDs não geridos são suportados.
- `List`e `Read` permissões são suficientes. *Não* forneça `Write` `Delete` nem aceda.
- A duração do acesso (data de*validade)* deve ser mínima de três semanas a contar da criação do SAS URI.
- Para salvaguardar as variações de tempo utc, detete a data de início para um dia antes da data atual. Por exemplo, se a data atual for 6 de outubro de 2014, selecione 10/5/2014.

## <a name="generate-the-sas-url"></a>Gerar o URL SAS

O URL SAS pode ser gerado de duas formas comuns utilizando as seguintes ferramentas:

- Microsoft Storage Explorer - Ferramenta gráfica disponível para Windows, macOS e Linux
- Microsoft Azure CLI - Recomendado para OSs não Windows e ambientes de integração automatizada ou contínua

### <a name="azure-cli"></a>CLI do Azure

Utilize os seguintes passos para gerar um SAS URI com O CLI Azure.

1. Descarregue e instale o [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). As versões estão disponíveis para Windows, macOS e vários distros do Linux.
2. Crie um ficheiro`.ps1` PowerShell (extensão de ficheiro), copie no seguinte código e, em seguida, guarde-o localmente.

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```

3. Editar o ficheiro para fornecer os seguintes valores de parâmetro.  As datas devem ser fornecidas no `2016-10-25T00:00:00Z`formato data-data UTC, por exemplo.
   - `<account-name>`- O nome da sua conta de armazenamento Azure
   - `<account-key>`- A chave da sua conta de armazenamento Azure
   - `<vhd-name>`- O seu nome VHD
   - `<start-date>`- Data de início de autorização para acesso vHD. Forneça uma data um dia antes da data atual.
   - `<expiry-date>`- Data de validade da autorização para acesso vHD.  Forneça uma data pelo menos três semanas além da data atual.

   O exemplo seguinte mostra valores de parâmetros adequados (no momento desta escrita).

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```

4. Guarde as alterações neste script PowerShell.
5. Executar este script, usando privilégios administrativos, para gerar uma cadeia de *ligação SAS* para acesso ao nível do contentor.  Pode utilizar duas abordagens básicas:
   - Executa o guião a partir da consola.  Por exemplo, no Windows, clique no script e selecione **Executar como administrador**.
   - Execute o script a partir de um editor de scripts PowerShell, como o [Windows PowerShell ISE,](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)usando privilégios administrativos.
     O seguinte demonstra que uma cadeia de ligação SAS está a ser gerada dentro deste editor.

     ![Geração SAS URI no PowerShell ISE](./media/publishvm_032.png)

6. Copie a cadeia de ligação SAS resultante e guarde-a para um ficheiro de texto num local seguro.  Irá editar esta cadeia para adicionar as informações de localização VHD associadas para criar o SAS URI final.
7. No portal Azure, navegue até ao armazenamento blob que contém o VHD associado ao URI recém-gerado.
8. Copie o valor URL do ponto final do **serviço Blob,** como mostrado abaixo.

    ![Ponto final do serviço blob no portal Azure](./media/publishvm_033.png)

9. Editar o ficheiro de texto com a linha de ligação SAS a partir do passo 6.  Irá construir o SAS URI completo utilizando o seguinte formato:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Por exemplo, se o nome do `TestRGVM2.vhd`VDH for, então o SAS URI resultante seria:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita estes passos para cada VHD nas SKUs que pretende publicar.

### <a name="microsoft-storage-explorer"></a>Microsoft Storage Explorer

Utilize os seguintes passos para gerar um SAS URI com o Microsoft Azure Storage Explorer.

1. Transferir e instalar o [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Abra o explorador e, na barra de menus à esquerda, clique no ícone **'Conta Adicionar'.**  A caixa de diálogo **Connect to Azure Storage** é apresentada.
3. Selecione **Adicionar uma Conta Azure** e clicar **em Iniciar sessão**.  Continue os passos necessários para assinar na sua conta Azure.
4. No **painel** explorer esquerdo, navegue para as suas Contas de **Armazenamento** e expanda este nó.
5. Clique à direita no seu VHD e selecione **Obter Assinatura** de Acesso a Partilha a partir do menu de contexto.

    ![Obtenha item SAS no Azure Explorer](./media/publishvm_034.png)

6. O diálogo **De Acesso Partilhado Signature** é apresentado. Introduza valores para os seguintes campos:
   - **Hora de início** - Data de início da permissão para acesso VHD. Forneça uma data que seja um dia antes da data atual.
   - **Tempo de validade** - Data de validade da permissão para acesso VHD.  Forneça uma data pelo menos três semanas além da data atual.
   - **Permissões** - `Read` `List` Selecione as permissões e permissões.

     ![Diálogo SAS no Explorador Azure](./media/publishvm_035.png)

7. Clique em **Criar** para criar o SAS URI associado para este VHD.  O diálogo apresenta agora detalhes sobre esta operação.
8. Copie o valor **do URL** e guarde-o para um ficheiro de texto num local seguro.

    ![SAS URI cria no Azure Explorer](./media/publishvm_036.png)

    Este URL SAS gerado destina-se ao acesso ao nível do contentor.  Para torná-lo específico, o nome VHD associado deve ser adicionado ao mesmo.

9. Editar o ficheiro de texto. Insira o seu `vhds` nome VHD após a cadeia no URL SAS (inclua uma barra dianteira dianteira).  O SAS URI final deve ser do formato:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Por exemplo, se o nome do `TestRGVM2.vhd`VDH for, então o SAS URI resultante seria:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita estes passos para cada VHD nas SKUs que pretende publicar.

## <a name="verify-the-sas-uri"></a>Verifique o SAS URI

Reveja e verifique cada SAS URI gerado utilizando a seguinte lista de verificação.  Verifique se:

- O URI é da `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + forma:`<sas-connection-string>`
- O URI contém o nome de ficheiro de imagem VHD, incluindo a extensão do nome de ficheiro ".vhd".
- No meio do URI, `sp=rl` aparece. Esta cadeia `Read` indica `List` que e o acesso é especificado.
- Depois disso, `sr=c` também aparece. Esta cadeia indica que o acesso ao nível do recipiente é especificado.
- Copie e cole o URI num browser para começar a descarregar a bolha associada.  (Pode cancelar a operação antes de o download terminar.)

## <a name="next-steps"></a>Passos seguintes

Se está com dificuldades em gerar um SAS URI, consulte os problemas de [URL Da SAS Comuns](./cpp-common-sas-url-issues.md).  Caso contrário, guarde os SAS URI(s) para um local seguro para posterior utilização. Será necessário publicar a [sua oferta VM](./cpp-publish-offer.md) no Portal do Parceiro cloud.
