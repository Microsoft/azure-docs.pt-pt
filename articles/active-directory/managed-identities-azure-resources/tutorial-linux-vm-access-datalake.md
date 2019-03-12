---
title: Utilizar a identidade gerida atribuída pelo sistema da VM do Linux para aceder ao Azure Data Lake Store
description: Um tutorial que lhe mostra como utilizar uma identidade gerida atribuída pelo sistema da VM do Linux para aceder ao Azure Data Lake Store.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1d0540ccfb9b4aac03b750b5c0002055bdd536a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546743"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Tutorial: Utilizar a identidade gerida atribuída pelo sistema da VM do Linux para aceder ao Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como utilizar uma identidade gerida atribuído de sistema para uma máquina virtual (VM) do Linux para aceder ao Azure Data Lake Store. Saiba como: 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conceder acesso à VM ao Azure Data Lake Store.
> * Obter um token de acesso com a identidade gerida atribuída pelo sistema da VM e utilizá-lo para aceder ao Azure Data Lake Store.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Conceder acesso à VM ao Azure Data Lake Store

Agora pode conceder acesso à VM a ficheiros e pastas no Azure Data Lake Store. Neste passo, pode utilizar uma instância existente do Data Lake Store ou criar uma nova. Para criar uma instância do Data Lake Store com o portal do Azure, siga o [Início rápido do Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Também existem inícios rápidos que utilizam a CLI do Azure e o Azure PowerShell na [Documentação do Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).

No Data Lake Store, crie uma nova pasta e conceda à identidade gerida atribuída pelo sistema da VM do Linux permissão para ler, escrever e executar ficheiros nessa pasta:

1. No portal do Azure, selecione **Data Lake Store** no painel esquerdo.
2. Selecione a instância do Data Lake Store que pretende utilizar.
3. Selecione **Data Explorer** na barra de comandos.
4. A pasta raiz da instância do Data Lake Store fica selecionada. Selecione **Acesso** na barra de comandos.
5. Selecione **Adicionar**.  Na caixa **Selecionar**, introduza o nome da sua VM – por exemplo, **DevTestVM**. Selecione a sua VM nos resultados da pesquisa e clique em **Selecionar**.
6. Clique em **Selecionar Permissões**.  Selecione **Ler** e **Executar**, adicione a **Esta pasta**e adicione como **Apenas uma permissão de acesso**. Selecione **Ok**.  A permissão deve ser adicionada com êxito.
7. Feche o painel **Acesso**.
8. Neste tutorial, vai criar uma nova pasta. Selecione **Nova Pasta** na barra de comandos e dê um nome à pasta, por exemplo **TestFolder**.  Selecione **Ok**.
9. Selecione a pasta que criou e, em seguida, selecione **Acesso** na barra de comandos.
10. Como no passo 5, selecione **Adicionar**. Na caixa **Selecionar**, introduza o nome da sua VM. Selecione a sua VM nos resultados da pesquisa e clique em **Selecionar**.
11. Como no passo 6, selecione **Selecionar Permissões**. Selecione **Ler**, **Escrever** e **Executar**, adicione a **Esta pasta**e adicione como **Uma entrada de permissão de acesso e uma entrada de permissão predefinida**. Selecione **Ok**.  A permissão deve ser adicionada com êxito.

As identidades geridas para recursos do Azure podem agora realizar todas as operações nos ficheiros da pasta que criou. Para obter mais informações sobre como gerir o acesso ao Data Lake Store, veja [Controlo de Acesso no Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>Obter um token de acesso e chamar o sistema de ficheiros do Data Lake Store

O Azure Data Lake Store suporta nativamente a autenticação do Azure AD, para que possa aceitar diretamente tokens de acesso obtidos através de identidades geridas para recursos do Azure. Para autenticar para o sistema de ficheiros do Data Lake Store, envie um token de acesso emitido pelo Azure AD para o ponto final do sistema de ficheiros do Data Lake Store. O token de acesso está num cabeçalho de autorização no formato "Bearer \<ACCESS_TOKEN_VALUE\>".  Para saber mais sobre o suporte do Data Lake Store para a autenticação do Azure AD, veja [Autenticação com o Data Lake Store através do Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

Neste tutorial, vai autenticar para a API REST do sistema de ficheiros do Data Lake Store com o cURL, para fazer pedidos REST.

> [!NOTE]
> Os SDKs cliente para o sistema de ficheiros do Data Lake Store ainda não suportam identidades geridas para recursos do Azure.

Para concluir estes passos, precisa de um cliente SSH. Se estiver a utilizar o Windows, pode utilizar o cliente SSH no [Subsistema Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Se precisar de ajuda para configurar as chaves do seu cliente SSH, veja [Como utilizar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md), ou [Como criar e utilizar um par de chaves SSH públicas e privadas para VMs do Linux no Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal, navegue para a VM do Linux. Em **Descrição Geral**, selecione **Ligar**.  
2. Ligue à VM através do cliente SSH que escolher. 
3. Na janela de terminal, com o cURL, faça um pedido às identidades geridas locais do Axure para o ponto final dos recursos do Azure obter um token de acesso para o sistema de ficheiros do Data Lake Store. O identificador de recurso do Data Lake Store é "https://datalake.azure.net/".  É importante incluir a barra à direita no identificador de recurso.
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   Uma resposta com êxito devolve o token de acesso que utiliza para autenticar para o Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Com o cURL, faça um pedido ao ponto final REST do sistema de ficheiros do Data Lake Store para listar as pastas na pasta raiz. Esta é uma forma simples de verificar se tudo está configurado corretamente. Copie o valor do token de acesso do passo anterior. É importante que a cadeia "Bearer" no cabeçalho de autorização possua um "B" maiúsculo. Pode encontrar o nome da sua instância do Data Lake Store na secção **Descrição Geral** do painel **Data Lake Store** no portal do Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Uma resposta com êxito tem o seguinte aspeto:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Agora, pode tentar carregar um ficheiro para a sua instância do Data Lake Store. Primeiro, crie um ficheiro para carregar.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Com o cURL, faça um pedido ao ponto final REST do sistema de ficheiros do Data Lake Store para carregar o ficheiro para a pasta que criou anteriormente. O carregamento envolve um redirecionamento, e o cURL segue o redirecionamento automaticamente. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Uma resposta com êxito tem o seguinte aspeto:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Ao utilizar outras APIs do sistema de ficheiros do Data Lake Store pode anexar a ficheiros, transferir ficheiros, etc.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial aprendeu a utilizar uma identidade gerida atribuída pelo sistema da VM do Linux para aceder a um Azure Data Lake Store. Para saber mais sobre o Azure Data Lake Store, veja:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](/azure/data-lake-store/data-lake-store-overview)
