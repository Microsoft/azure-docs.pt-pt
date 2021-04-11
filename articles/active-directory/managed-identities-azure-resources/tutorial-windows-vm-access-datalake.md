---
title: Tutorial `:` Utilize uma identidade gerida para aceder à Azure Data Lake Store - Windows - Azure AD
description: Um tutorial que lhe mostra como utilizar uma identidade gerida atribuída pelo sistema da VM do Windows para aceder ao Azure Data Lake Store.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd28ee509dc76ea0b2aca9264c591a7176ae2661
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093807"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Tutorial: como utilizar a identidade gerida atribuída pelo sistema da VM do Windows para aceder ao Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como utilizar uma identidade gerida de sistema atribuída para que uma máquina virtual (VM) do Windows aceda ao Azure Data Lake Store. As Identidades de Serviço Geridas são geridas automaticamente pelo Azure e permitem-lhe fazer a autenticação em serviços que suportam a autenticação do Azure AD, sem ser necessário inserir as credenciais no seu código. Saiba como:

> [!div class="checklist"]
> * Conceder o acesso à sua VM ao Azure Data Lake Store
> * Obter um token de acesso com a identidade da VM e utilizá-lo para aceder ao Azure Data Lake Store

## <a name="prerequisites"></a>Pré-requisitos

- Uma compreensão das identidades geridas. Se não estiver familiarizado com a funcionalidade das identidades geridas para os recursos do Azure, veja esta [descrição geral](overview.md). 
- Uma conta Azure, [inscreva-se para uma conta gratuita.](https://azure.microsoft.com/free/)
- Permissões "Proprietário" no âmbito apropriado (a sua subscrição ou grupo de recursos) para executar as etapas necessárias de criação de recursos e gestão de funções. Se precisar de assistência com a atribuição de funções, consulte [as funções de Assign Azure para gerir o acesso aos seus recursos de subscrição Azure](../../role-based-access-control/role-assignments-portal.md).
- Também precisa de uma máquina Virtual do Windows que tenha o sistema atribuído a identidades geridas ativadas.
  - Se precisar de criar uma máquina virtual para este tutorial, pode seguir o artigo intitulado [Criar uma máquina virtual com identidade atribuída ao sistema ativada](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)




## <a name="enable"></a>Ativar

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Conceder acesso

Agora, pode conceder o acesso à VM a ficheiros e pastas no Azure Data Lake Store.  Para este passo, pode utilizar um Data Lake Store existente ou criar um novo.  Para criar um novo Data Lake Store com o portal do Azure, siga este [Guia de início rápido do Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md). Também existem inícios rápidos que utilizam a CLI do Azure e o Azure PowerShell na [Documentação do Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md).

No seu Data Lake Store, crie uma nova pasta e conceda à identidade atribuída pelo sistema da VM permissão para ler, escrever e executar ficheiros nessa pasta:

1. No portal do Azure, clique em **Data Lake Store** no painel de navegação esquerdo.
2. Clique no Data Lake Store que pretende utilizar neste tutorial.
3. Clique em **Data Explorer** na barra de comandos.
4. A pasta raiz do Data Lake Store fica selecionada.  Clique em **Acesso** na barra de comandos.
5. Clique em **Adicionar**.  No campo **Selecionar**, introduza o nome da sua VM, por exemplo **DevTestVM**.  Clique para selecionar a sua VM nos resultados da pesquisa e clique em **Selecionar**.
6. Clique **em Selecionar Permissões**.  Selecione **Ler** e **Executar**, adicione a **Esta pasta** e adicione como **Apenas uma permissão de acesso**.  Clique em **OK**.  A permissão deve ser adicionada com êxito.
7. Feche o painel **Acesso**.
8. Neste tutorial, vai criar uma nova pasta.  Clique em **Nova Pasta** na barra de comandos e dê um nome à pasta, por exemplo **TestFolder**.  Clique em **OK**.
9. Clique na pasta que criou e clique em **Acesso** na barra de comandos.
10. Como no passo 5, clique em **Adicionar**, no campo **Selecionar** introduza o nome da sua VM, selecione-a e clique em **Selecionar**.
11. Como no passo 6, clique em **Selecionar Permissões**, selecione **Ler**, **Escrever** e **Executar**, adicione a **Esta pasta** e adicione como **Uma entrada de permissão de acesso e uma entrada de permissão predefinida**.  Clique em **OK**.  A permissão deve ser adicionada com êxito.

A identidade gerida atribuída pelo sistema da VM pode agora realizar todas as operações nos ficheiros da pasta que criou.  Para obter mais informações sobre como gerir o acesso ao Data Lake Store, leia este artigo sobre [Controlo de Acesso no Data Lake Store](../../data-lake-store/data-lake-store-access-control.md).

## <a name="access-data"></a>Aceder a dados

O Azure Data Lake Store suporta nativamente a autenticação do Azure AD, para que possa aceitar diretamente tokens de acesso obtidos através de identidades geridas para recursos do Azure.  Para autenticar para o sistema de ficheiros do Data Lake Store, envia um token de acesso emitido pelo Azure AD para o ponto final do sistema de ficheiros do Data Lake Store, num cabeçalho de autorização no formato "Bearer <ACCESS_TOKEN_VALUE>".  Para saber mais sobre o suporte do Data Lake Store para a autenticação do Azure AD, leia [Autenticação com o Data Lake Store através do Azure Active Directory](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

> [!NOTE]
> Os SDKs de cliente de sistema de ficheiros do Data Lake Store ainda não suportam identidades geridas para recursos do Azure.  Este tutorial será atualizado quando for adicionado suporte para o SDK.

Neste tutorial, vai autenticar para a API REST do sistema de ficheiros do Data Lake Store com o PowerShell, para fazer pedidos REST. Para utilizar a identidade gerida atribuída pelo sistema da VM para a autenticação, terá de fazer os pedidos a partir da VM.

1. No portal, navegue para **Máquinas Virtuais**, aceda à sua VM do Windows e, na **Descrição Geral** clique em **Ligar**.
2. Introduza o seu **Nome de Utilizador** e a **Palavra-passe** que adicionou quando criou a VM do Windows. 
3. Agora que criou uma **Ligação de Ambiente de Trabalho Remoto** com a máquina virtual, abra o **PowerShell** na sessão remota. 
4. Através de `Invoke-WebRequest` do PowerShell, envie um pedido às identidades geridas locais para o ponto final dos recursos do Azure obter um token de acesso para o Azure Data Lake Store.  O identificador de recursos da Data Lake Store é `https://datalake.azure.net/` .  O Data Lake faz uma correspondência exata no identificador de recurso e a barra à direita é importante.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   Converta a resposta de um objeto JSON num objeto do PowerShell. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extraia o token de acesso da resposta.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Através de “Invoke-WebRequest” do PowerShell, faça um pedido ao ponto final REST do Data Lake Store para listar as pastas na pasta raiz.  Esta é uma forma simples de verificar se tudo está configurado corretamente.  É importante que a cadeia "Bearer" no cabeçalho de autorização possua um "B" maiúsculo.  Pode encontrar o nome do seu Data Lake Store na secção **Descrição Geral** do painel Data Lake Store no portal do Azure.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Uma resposta com êxito é semelhante à seguinte:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Agora, pode tentar carregar um ficheiro para o Data Lake Store.  Primeiro, crie um ficheiro para carregar.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Através de `Invoke-WebRequest` do PowerShell, faça um pedido ao ponto final REST do Data Lake Store para carregar o ficheiro para a pasta que criou anteriormente.  Este pedido é feito em dois passos.  No primeiro passo, faz o pedido e obtém um redirecionamento para onde o ficheiro deve ser carregado.  No segundo, carrega o ficheiro.  Não se esqueça de definir o nome da pasta e do ficheiro adequadamente, se tiver utilizado valores diferentes aos deste tutorial. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Se inspecionar o valor de `$HdfsRedirectResponse`, deve ser semelhante à seguinte resposta:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Conclua o carregamento, enviando um pedido para o ponto final de redirecionamento:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Uma resposta com êxito é semelhante à seguinte:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

Ao utilizar outras APIs do sistema de ficheiros do Data Lake Store pode anexar a ficheiros, transferir ficheiros, etc.


## <a name="disable"></a>Desativar

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar uma identidade gerida atribuída pelo sistema para uma máquina virtual do Windows aceder ao Azure Data Lake Store. Para saber mais sobre o Azure Data Lake Store, veja:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)