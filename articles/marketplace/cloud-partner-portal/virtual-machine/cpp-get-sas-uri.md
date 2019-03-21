---
title: Obter o URI de assinatura de acesso partilhado para a sua imagem VM baseada no Azure da Microsoft | Documentos da Microsoft
description: Explica como obter a assinatura de acesso partilhado (SAS) URI para a imagem de VM.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c21fa3cf819f48dcda46f2d444ed52bc2eb9ae3d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113525"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Obter o URI de assinatura de acesso partilhado para a imagem de VM

Durante o processo de publicação, tem de fornecer um identificador de recurso uniforme (URI) para cada disco rígido virtual (VHD) associado com os SKUs. A Microsoft precisa de aceder a estes VHDs durante o processo de certificação. Este artigo explica como gerar uma assinatura de acesso partilhado (SAS) URI para cada VHD. Irá introduzir este URI no **SKUs** separador no Portal de parceiros de nuvem. 

Durante a geração dos URIs de SAS para os VHDs, de cumprir os seguintes requisitos:

- Apenas os VHDs não geridos são suportados.
- `List` e `Read` permissões são suficientes. Fazer *não* fornecer `Write` ou `Delete` acesso.
- A duração do acesso (*data de expiração*) deve ser um mínimo de três semanas desde quando o URI de SAS é criado.
- Para salvaguardar contra variações de hora UTC, defina a data de início para um dia antes da data atual. Por exemplo, se a data atual é de 6 de Outubro de 2014, selecione 10/5/2014.

## <a name="generate-the-sas-url"></a>Gerar o URL de SAS

O URL de SAS podem ser gerado de duas formas comuns de utilizar as ferramentas abaixo:

-   Microsoft Storage Explorer - ferramenta gráfica disponível para Windows, macOS e Linux
-   CLI do Microsoft Azure - recomendado para não - Windows sistemas operacionais e ambientes de integração automatizada ou contínua


### <a name="azure-cli"></a>CLI do Azure

Utilize os seguintes passos para gerar um URI de SAS com a CLI do Azure.

1. Transferir e instalar o [da CLI do Azure do Microsoft](https://azure.microsoft.com/documentation/articles/xplat-cli-install/).  Versões estão disponíveis para várias distribuições do Linux, macOS e Windows. 
2. Crie um ficheiro de PowerShell (`.ps1` extensão de ficheiro), copie o código a seguir, em seguida, guarde-o localmente.

   ``` powershell
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```
    
3. Edite o ficheiro para fornecer os seguintes valores de parâmetro.  Datas devem ser fornecidas no formato de datetime UTC, por exemplo `10-25-2016T00:00:00Z`.
   - `<account-name>` -O nome da conta de armazenamento do Azure
   - `<account-key>` -A sua chave de conta de armazenamento do Azure
   - `<vhd-name>` -O nome do VHD
   - `<start-date>` -Data para o acesso VHD de início de permissão. Forneça uma data de um dia antes da data atual. 
   - `<expiry-date>` -Data de expiração de permissão de acesso VHD.  Forneça uma data de, pelo menos, três semanas para além da data atual. 
 
   O exemplo seguinte mostra os valores de parâmetros adequada (no momento da redação deste artigo).

   ``` powershell
       az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```
 
4. Guarde as alterações para este script do PowerShell.
5. Execute este script, usando os privilégios administrativos, para gerar um *cadeia de ligação SAS* para acesso de nível de contêiner.  Pode usar duas abordagens básicas:
   - Execute o script a partir da consola.  Por exemplo, no Windows, escrita-clique em de script e selecione **executar como administrador**.
   - Execute o script a partir de um editor de script do PowerShell, como o [ISE do Windows PowerShell](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise), com privilégios administrativos. 
     O seguinte exemplo demonstra uma cadeia de ligação de SAS que está a ser gerada dentro deste editor. 

     ![Geração de URI de SAS no ISE do PowerShell](./media/publishvm_032.png)

6. Copie a cadeia de ligação SAS resultante e guarde-o num arquivo de texto numa localização segura.  Será necessário editar essa cadeia de caracteres para adicionar as informações de localização do VHD associadas a ele para criar o URI de SAS final. 
7. No portal do Azure, navegue para o armazenamento de BLOBs que contém o VHD associado com o URI gerado recentemente.
8. Copie o valor de URL dos **ponto final de serviço Blob**, conforme mostrado abaixo.

    ![Ponto final de serviço de BLOBs no portal do Azure](./media/publishvm_033.png)

9. Edite o ficheiro de texto com a cadeia de ligação de SAS do passo 6.  Será a construir o URI de SAS completo com o seguinte formato:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Por exemplo, se o nome do VDH é `TestRGVM2.vhd`, em seguida, o URI de SAS resultante será:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita estes passos para cada VHD em SKUs que pretende publicar.


### <a name="microsoft-storage-explorer"></a>Explorador de armazenamento da Microsoft

Utilize os seguintes passos para gerar um URI de SAS com o Explorador de armazenamento do Microsoft Azure.

1. Transferir e instalar o [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Abra o explorer e, na barra de menu à esquerda, clique nas **adicionar conta** ícone.  O **ligar ao armazenamento do Azure** é apresentada a caixa de diálogo.
3. Selecione **Adicionar uma Conta do Azure** e clique em **Iniciar sessão**.  Continue os passos necessários para iniciar sessão na sua conta do Azure.
4. O lado esquerdo no **Explorer** painel, navegue até à sua **contas de armazenamento** e expandir este nó.
5. Com o botão direito no seu VHD e selecione **obter assinatura de acesso de partilha** no menu de contexto. 

    ![Obter o item SAS no Explorador do Azure](./media/publishvm_034.png)

6. O **assinatura de acesso partilhado** é apresentada a caixa de diálogo. Introduza valores para os seguintes campos:
   - **Hora de início** -data para o acesso VHD de início de permissão. Forneça uma data que é um dia antes da data atual.
   - **Hora de expiração** -data de expiração da permissão de acesso VHD.  Forneça uma data de, pelo menos, três semanas para além da data atual.
   - **Permissões** - selecione o `Read` e `List` permissões. 

     ![Caixa de diálogo SAS no Explorador do Azure](./media/publishvm_035.png)

7. Clique em **criar** para criar o URI de SAS associado para este VHD.  A caixa de diálogo agora apresenta detalhes sobre esta operação. 
8. Copiar o **URL** valor e guardá-lo num arquivo de texto numa localização segura. 

    ![URI de SAS criar no Explorador do Azure](./media/publishvm_036.png)

    Este URL de SAS gerado é para acesso de nível de contêiner.  Para que seja específico, o nome do VHD associado tem de ser adicionado a ele.

9. Edite o ficheiro de texto. Inserir o nome do VHD após a `vhds` o URL de SAS de cadeia de caracteres (incluir uma barra à esquerda).  O URI de SAS final deve estar no formato:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Por exemplo, se o nome do VDH é `TestRGVM2.vhd`, em seguida, o URI de SAS resultante será:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita estes passos para cada VHD em SKUs que pretende publicar.


## <a name="verify-the-sas-uri"></a>Verifique se o URI de SAS

Reveja e verifique se que cada gerado o URI de SAS, utilizando a lista de verificação seguinte.  Certifique-se de que:
- É o URI do formulário:       `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- O URI contém seu filename de imagem VHD, incluindo a extensão de nome de ficheiro ". vhd".
- Em direção a meio do URI, `sp=rl` aparece. Esta cadeia indica que `Read` e `List` acesso está especificado.
- Após esse ponto, `sr=c` também é apresentada. Esta cadeia indica que o acesso de nível de contêiner é especificado.
- Copie e cole o URI num browser para começar a transferir o blob associado.  (Pode cancelar a operação antes da conclusão da transferência.)


## <a name="next-steps"></a>Passos Seguintes

Se estiver a ter dificuldades a gerar um URI de SAS, veja [URL de SAS comum emite](./cpp-common-sas-url-issues.md).  Caso contrário, guarde o URI(s) de SAS para uma localização segura para utilização posterior. É necessário [publicar a sua oferta VM](./cpp-publish-offer.md) no Portal de parceiros de nuvem.
