---
title: Obter o URI da assinatura de acesso compartilhado para sua imagem VM baseada em Microsoft Azure | Azure Marketplace
description: Explica como obter o URI de assinatura de acesso compartilhado (SAS) para sua imagem de VM.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: c242fbcd19187abb608ca80a49d04dae195bd7c6
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374377"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Obter URI de assinatura de acesso compartilhado para sua imagem de VM

Durante o processo de publicação, você deve fornecer um URI (Uniform Resource Identifier) para cada VHD (disco rígido virtual) associado às suas SKUs. A Microsoft precisa de aceder a estes VHDs durante o processo de certificação. Este artigo explica como gerar um URI de assinatura de acesso compartilhado (SAS) para cada VHD. Você digitará esse URI na guia **SKUs** na portal do Cloud Partner. 

Ao gerar URIs SAS para seus VHDs, siga os seguintes requisitos:

- Somente VHDs não gerenciados têm suporte.
- as permissões `List` e `Read` são suficientes. *Não* forneça acesso `Write` ou `Delete`.
- A duração do acesso (*data de expiração*) deve ser no mínimo três semanas a partir da criação do URI de SAS.
- Para proteger contra variações de horário UTC, defina a data de início como um dia antes da data atual. Por exemplo, se a data atual for 6 de outubro de 2014, selecione 10/5/2014.

## <a name="generate-the-sas-url"></a>Gerar a URL SAS

A URL da SAS pode ser gerada de duas maneiras comuns usando as seguintes ferramentas:

-   Microsoft Gerenciador de Armazenamento-ferramenta gráfica disponível para Windows, macOS e Linux
-   Microsoft Azure CLI-recomendado para ambientes de integração de sistemas operacionais não Windows e automatizados ou contínuos


### <a name="azure-cli"></a>CLI do Azure

Use as etapas a seguir para gerar um URI de SAS com CLI do Azure.

1. Baixe e instale a [CLI do Microsoft Azure](https://azure.microsoft.com/documentation/articles/xplat-cli-install/).  As versões estão disponíveis para Windows, macOS e vários distribuições do Linux. 
2. Crie um arquivo do PowerShell (extensão de arquivo `.ps1`), copie o código a seguir e salve-o localmente.

   ``` powershell
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```
    
3. Edite o arquivo para fornecer os valores de parâmetro a seguir.  As datas devem ser fornecidas no formato UTC DateTime, por exemplo `2016-10-25T00:00:00Z`.
   - `<account-name>`-seu nome de conta de armazenamento do Azure
   - `<account-key>`-sua chave de conta de armazenamento do Azure
   - `<vhd-name>`-o nome do VHD
   - `<start-date>`-Data de início da permissão para acesso VHD. Forneça uma data um dia antes da data atual. 
   - `<expiry-date>`-Data de expiração da permissão para acesso VHD.  Forneça uma data pelo menos três semanas além da data atual. 
 
   O exemplo a seguir mostra os valores de parâmetro adequados (no momento da redação deste artigo).

   ``` powershell
       az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```
 
4. Salve as alterações neste script do PowerShell.
5. Execute esse script, usando privilégios administrativos, para gerar uma *cadeia de conexão SAS* para acesso no nível do contêiner.  Você pode usar duas abordagens básicas:
   - Execute o script no console do.  Por exemplo, no Windows, clique em gravar no script e selecione **Executar como administrador**.
   - Execute o script de um editor de script do PowerShell, como o [ISE do Windows PowerShell](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise), usando privilégios administrativos. 
     O seguinte demonstra uma cadeia de conexão SAS que está sendo gerada nesse editor. 

     ![Geração de URI SAS no ISE do PowerShell](./media/publishvm_032.png)

6. Copie a cadeia de conexão SAS resultante e salve-a em um arquivo de texto em um local seguro.  Você editará essa cadeia de caracteres para adicionar as informações do local do VHD associado a ela para criar o URI de SAS final. 
7. No portal do Azure, navegue até o armazenamento de BLOBs que contém o VHD associado ao URI gerado recentemente.
8. Copie o valor da URL do **ponto de extremidade do serviço blob**, conforme mostrado abaixo.

    ![Ponto de extremidade do serviço blob no portal do Azure](./media/publishvm_033.png)

9. Edite o arquivo de texto com a cadeia de conexão SAS da etapa 6.  Você criará o URI de SAS completo usando o seguinte formato:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Por exemplo, se o nome do VDH for `TestRGVM2.vhd`, o URI de SAS resultante será:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita essas etapas para cada VHD nas SKUs que você planeja publicar.


### <a name="microsoft-storage-explorer"></a>Gerenciador de Armazenamento da Microsoft

Use as etapas a seguir para gerar um URI de SAS com o Gerenciador de Armazenamento do Microsoft Azure.

1. Transferir e instalar o [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Abra o Explorer e, na barra de menus à esquerda, clique no ícone **adicionar conta** .  A caixa de diálogo **conectar ao armazenamento do Azure** é exibida.
3. Selecione **Adicionar uma Conta do Azure** e clique em **Iniciar sessão**.  Continue as etapas necessárias para entrar em sua conta do Azure.
4. No painel do **Gerenciador** esquerdo, navegue até suas contas de **armazenamento** e expanda este nó.
5. Clique com o botão direito do mouse em seu VHD e selecione **obter assinatura de acesso de compartilhamento** no menu de contexto. 

    ![Obter item SAS no Azure Explorer](./media/publishvm_034.png)

6. A caixa de diálogo **assinatura de acesso compartilhado** é exibida. Insira valores para os seguintes campos:
   - **Hora de início** -data de início da permissão para acesso VHD. Forneça uma data que seja um dia antes da data atual.
   - **Hora de expiração** -data de expiração da permissão para acesso do VHD.  Forneça uma data pelo menos três semanas além da data atual.
   - **Permissões** -selecione as permissões `Read` e `List`. 

     ![Caixa de diálogo SAS no Azure Explorer](./media/publishvm_035.png)

7. Clique em **criar** para criar o URI de SAS associado para este VHD.  A caixa de diálogo agora exibe detalhes sobre esta operação. 
8. Copie o valor da **URL** e salve-o em um arquivo de texto em um local seguro. 

    ![Criação de URI de SAS no Azure Explorer](./media/publishvm_036.png)

    Essa URL SAS gerada é para o acesso em nível de contêiner.  Para torná-lo específico, o nome do VHD associado deve ser adicionado a ele.

9. Edite o arquivo de texto. Insira o nome do VHD após a cadeia de caracteres `vhds` na URL SAS (inclua uma barra à esquerda).  O URI de SAS final deve ser do formato:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Por exemplo, se o nome do VDH for `TestRGVM2.vhd`, o URI de SAS resultante será:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita essas etapas para cada VHD nas SKUs que você planeja publicar.


## <a name="verify-the-sas-uri"></a>Verificar o URI de SAS

Examine e verifique cada URI de SAS gerado usando a seguinte lista de verificação.  Verifique se:
- O URI está no formato: `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- O URI contém o nome de arquivo da imagem VHD, incluindo a extensão de nome de arquivo ". vhd".
- Em direção ao meio do URI, `sp=rl` é exibido. Essa cadeia de caracteres indica que `Read` e o acesso `List` são especificados.
- Depois desse ponto, `sr=c` também será exibido. Essa cadeia de caracteres indica que o acesso no nível de contêiner foi especificado.
- Copie e cole o URI em um navegador para começar a baixar o blob associado.  (Você pode cancelar a operação antes que o download seja concluído.)


## <a name="next-steps"></a>Passos seguintes

Se você estiver tendo dificuldades para gerar um URI de SAS, consulte [problemas comuns de URL de SAS](./cpp-common-sas-url-issues.md).  Caso contrário, salve os URI (s) SAS em um local seguro para uso posterior. Será necessário [publicar sua oferta de VM](./cpp-publish-offer.md) no portal do Cloud Partner.
