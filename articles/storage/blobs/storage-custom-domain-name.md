---
title: Mapear um domínio personalizado para um ponto final de armazenamento Azure Blob
titleSuffix: Azure Storage
description: Mapear um domínio personalizado para um Blob Storage ou web endpoint numa conta de armazenamento Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 903413b6ca00600e15ac3af0a93b98a8d67a1c28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88053631"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Mapear um domínio personalizado para um ponto final de armazenamento Azure Blob

Pode mapear um domínio personalizado para um ponto final de serviço blob ou um ponto final [estático do site.](storage-blob-static-website.md) 

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> Este mapeamento funciona apenas para subdomínios (por exemplo: `www.contoso.com` ). Se quiser que o seu ponto final da web esteja disponível no domínio raiz (por exemplo: `contoso.com` ), então terá de utilizar o Azure CDN. Para obter orientação, consulte o Mapa de um domínio personalizado com secção [via HTTPS](#enable-https) deste artigo. Porque a sua ida para aquela secção deste artigo para ativar o domínio raiz do seu domínio personalizado, o passo dentro dessa secção para permitir HTTPS é opcional. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Mapear um domínio personalizado com apenas HTTP ativado

Esta abordagem é mais fácil, mas permite apenas acesso HTTP. Se a conta de armazenamento estiver configurada para [exigir transferência segura](../common/storage-require-secure-transfer.md) sobre HTTPS, então deve ativar o acesso HTTPS para o seu domínio personalizado. 

Para ativar o acesso a HTTPS, consulte o Mapa de um domínio personalizado com a secção [via HTTPS](#enable-https) deste artigo. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Mapear um domínio personalizado

> [!IMPORTANT]
> O seu domínio personalizado ficará brevemente indisponível para os utilizadores enquanto completa a configuração. Se o seu domínio suporta atualmente uma aplicação com um acordo de nível de serviço (SLA) que requer zero tempo de inatividade, então siga os passos no Mapa um domínio personalizado com secção de tempo de [inatividade zero](#zero-down-time) deste artigo para garantir que os utilizadores possam aceder ao seu domínio enquanto o mapeamento DNS ocorre.

Se não estiver preocupado com o facto de o domínio estar brevemente indisponível para os seus utilizadores, siga estes passos.

:heavy_check_mark: Passo 1: Obtenha o nome de anfitrião do seu ponto final de armazenamento.

:heavy_check_mark: Passo 2: Crie um registo de nome canónico (CNAME) com o seu fornecedor de domínio.

:heavy_check_mark: Passo 3: Registe o domínio personalizado com o Azure. 

:heavy_check_mark: Passo 4: Teste o seu domínio personalizado.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Passo 1: Obtenha o nome de anfitrião do seu ponto final de armazenamento 

O nome do anfitrião é o URL do ponto final de armazenamento sem o identificador de protocolo e o corte de fuga. 

1. No [portal Azure,](https://portal.azure.com)aceda à sua conta de armazenamento.

2. No painel de menus, em **Definições,** selecione **Propriedades**.  

3. Copie o valor do Ponto final do Serviço primário de **Blob** ou do **ponto final do site estático primário** para um ficheiro de texto. 

4. Retire o identificador de protocolo *(por exemplo,* HTTPS) e o corte de fuga dessa cadeia. A tabela a seguir contém exemplos.

   | Tipo de ponto final |  endpoint | nome hospedeiro |
   |------------|-----------------|-------------------|
   |serviço blob  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |Web site estático  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Reserve este valor para mais tarde.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Passo 2: Crie um registo de nome canónico (CNAME) com o seu fornecedor de domínio

Crie um registo CNAME para apontar para o nome de anfitrião. Um registo CNAME é um tipo de registo DNS que mapeia um nome de domínio de origem para um nome de domínio de destino.

1. Inscreva-se no site do seu registo de domínio e, em seguida, vá à página para gerir a definição de DNS.

   Pode encontrar a página numa secção chamada **Nome de Domínio**, **DNS**ou **Gestão do Servidor de Nomes**.

2. Encontre a secção para gerir os registos da CNAME. 

   Você pode ter que ir a uma página de definições avançadas e procurar **CNAME,** **Alias,** ou **Subdomínios.**

3. Crie um registo CNAME. Como parte desse registo, forneça os seguintes itens: 

   - O pseudónimo do subdomínio, tais `www` `photos` como. O subdomínio é necessário, os domínios de raiz não são suportados. 
      
   - O nome de anfitrião que obteve no [Get the host name of your storage endpoint](#endpoint) section anteriormente neste artigo. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Passo 3: Registe o seu domínio personalizado com o Azure

1. No [portal Azure,](https://portal.azure.com)aceda à sua conta de armazenamento.

2. No painel de menus, em **Serviço Blob,** selecione **Domínio Personalizado**.  

   ![opção de domínio personalizado](./media/storage-custom-domain-name/custom-domain-button.png "domínio personalizado")

   O painel de **domínio personalizado** abre-se.

3. Na caixa de texto **do nome de domínio,** insira o nome do seu domínio personalizado, incluindo o subdomínio  
   
   Por exemplo, se o seu domínio for *contoso.com* e o seu pseudónimo subdomínio é *www.* `www.contoso.com` Se o seu subdomínio for *de fotos,* insira `photos.contoso.com` .

4. Para registar o domínio personalizado, escolha o botão **Guardar.**

   Após a propagação do registo CNAME através dos Servidores de Nome de Domínio (DNS), e se os seus utilizadores tiverem as permissões adequadas, podem ver dados blob utilizando o domínio personalizado.

#### <a name="step-4-test-your-custom-domain"></a>Passo 4: Teste o seu domínio personalizado

Para confirmar que o seu domínio personalizado está mapeado para o seu ponto final de serviço blob, crie uma bolha num recipiente público dentro da sua conta de armazenamento. Em seguida, num navegador web, aceda à bolha utilizando um URI no seguinte formato: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por exemplo, para aceder a um formulário web no recipiente *myforms* no subdomínio personalizado *photos.contoso.com,* poderá utilizar o seguinte URI: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Mapear um domínio personalizado com tempo de inatividade zero

> [!NOTE]
> Se não estiver preocupado com o facto de o domínio estar brevemente indisponível para os seus utilizadores, considere seguir os passos no Mapa uma secção de [domínio personalizada](#map-a-domain) deste artigo. É uma abordagem mais simples com menos passos.  

Se o seu domínio suporta atualmente uma aplicação com um acordo de nível de serviço (SLA) que requer zero tempo de inatividade, então siga estes passos para garantir que os utilizadores possam aceder ao seu domínio enquanto o mapeamento DNS ocorre. 

:heavy_check_mark: Passo 1: Obtenha o nome de anfitrião do seu ponto final de armazenamento.

:heavy_check_mark: Passo 2: Crie um registo de nome canónico intermediário (CNAME) com o seu fornecedor de domínio.

:heavy_check_mark: Passo 3: Pré-registo do domínio personalizado com a Azure.

:heavy_check_mark: Passo 4: Crie um registo CNAME com o seu fornecedor de domínio.

:heavy_check_mark: Passo 5: Teste o seu domínio personalizado.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Passo 1: Obtenha o nome de anfitrião do seu ponto final de armazenamento 

O nome do anfitrião é o URL do ponto final de armazenamento sem o identificador de protocolo e o corte de fuga. 

1. No [portal Azure,](https://portal.azure.com)aceda à sua conta de armazenamento.

2. No painel de menus, em **Definições,** selecione **Propriedades**.  

3. Copie o valor do Ponto final do Serviço primário de **Blob** ou do **ponto final do site estático primário** para um ficheiro de texto. 

4. Retire o identificador de protocolo *(por exemplo,* HTTPS) e o corte de fuga dessa cadeia. A tabela a seguir contém exemplos.

   | Tipo de ponto final |  endpoint | nome hospedeiro |
   |------------|-----------------|-------------------|
   |serviço blob  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |Web site estático  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Reserve este valor para mais tarde.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Passo 2: Crie um registo de nome canónico intermediário (CNAME) com o seu fornecedor de domínios

Crie um registo CNAME temporário para apontar para o nome de anfitrião. Um registo CNAME é um tipo de registo DNS que mapeia um nome de domínio de origem para um nome de domínio de destino.

1. Inscreva-se no site do seu registo de domínio e, em seguida, vá à página para gerir a definição de DNS.

   Pode encontrar a página numa secção chamada **Nome de Domínio**, **DNS**ou **Gestão do Servidor de Nomes**.

2. Encontre a secção para gerir os registos da CNAME. 

   Você pode ter que ir a uma página de definições avançadas e procurar **CNAME,** **Alias,** ou **Subdomínios.**

3. Crie um registo CNAME. Como parte desse registo, forneça os seguintes itens: 

   - O pseudónimo do subdomínio, tais `www` `photos` como. O subdomínio é necessário, os domínios de raiz não são suportados.

     Adicione o `asverify` subdomínio ao pseudónimo. Por exemplo: `asverify.www` ou `asverify.photos` . .
       
   - O nome de anfitrião que obteve no [Get the host name of your storage endpoint](#endpoint) section anteriormente neste artigo. 

     Adicione o subdomínio `asverify` ao nome do anfitrião. Por exemplo: `asverify.mystorageaccount.blob.core.windows.net`.

4. Para registar o domínio personalizado, escolha o botão **Guardar.**

   Se o registo for bem sucedido, o portal notifica-o de que a sua conta de armazenamento foi atualizada com sucesso. O seu domínio personalizado foi verificado pelo Azure, mas o tráfego para o seu domínio ainda não está a ser encaminhado para a sua conta de armazenamento.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Passo 3: Pré-registrar o seu domínio personalizado com Azure

Quando regista o seu domínio personalizado com o Azure, permite que o Azure reconheça o seu domínio personalizado sem ter de modificar o registo DNS para o domínio. Assim, quando modificar o registo DNS para o domínio, será mapeado para o ponto final da bolha sem tempo de inatividade.

1. No [portal Azure,](https://portal.azure.com)aceda à sua conta de armazenamento.

2. No painel de menus, em **Serviço Blob,** selecione **Domínio Personalizado**.  

   ![opção de domínio personalizado](./media/storage-custom-domain-name/custom-domain-button.png "domínio personalizado")

   O painel de **domínio personalizado** abre-se.

3. Na caixa de texto **do nome de domínio,** insira o nome do seu domínio personalizado, incluindo o subdomínio  
   
   Por exemplo, se o seu domínio for *contoso.com* e o seu pseudónimo subdomínio é *www.* `www.contoso.com` Se o seu subdomínio for *de fotos,* insira `photos.contoso.com` .

4. Selecione a caixa **de verificação de validação CNAME indireta.**

5. Para registar o domínio personalizado, escolha o botão **Guardar.**
  
   Após a propagação do registo CNAME através dos Servidores de Nome de Domínio (DNS), e se os seus utilizadores tiverem as permissões adequadas, podem ver dados blob utilizando o domínio personalizado.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Passo 4: Crie um registo CNAME com o seu fornecedor de domínios

Crie um registo CNAME temporário para apontar para o nome de anfitrião.

1. Inscreva-se no site do seu registo de domínio e, em seguida, vá à página para gerir a definição de DNS.

   Pode encontrar a página numa secção chamada **Nome de Domínio**, **DNS**ou **Gestão do Servidor de Nomes**.

2. Encontre a secção para gerir os registos da CNAME. 

   Você pode ter que ir a uma página de definições avançadas e procurar **CNAME,** **Alias,** ou **Subdomínios.**

3. Crie um registo CNAME. Como parte desse registo, forneça os seguintes itens: 

   - O pseudónimo do subdomínio, tais `www` `photos` como. O subdomínio é necessário, os domínios de raiz não são suportados.
      
   - O nome de anfitrião que obteve no [Get the host name of your storage endpoint](#endpoint-2) section anteriormente neste artigo. 

#### <a name="step-5-test-your-custom-domain"></a>Passo 5: Teste o seu domínio personalizado

Para confirmar que o seu domínio personalizado está mapeado para o seu ponto final de serviço blob, crie uma bolha num recipiente público dentro da sua conta de armazenamento. Em seguida, num navegador web, aceda à bolha utilizando um URI no seguinte formato: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por exemplo, para aceder a um formulário web no recipiente *myforms* no subdomínio personalizado *photos.contoso.com,* poderá utilizar o seguinte URI: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Remova um mapeamento de domínio personalizado

Para remover um mapeamento de domínio personalizado, desregistar o domínio personalizado. Utilize um dos seguintes procedimentos.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Para remover a definição de domínio personalizado, faça o seguinte:

1. No [portal Azure,](https://portal.azure.com)aceda à sua conta de armazenamento.

2. No painel de menus, em **Serviço Blob,** selecione **Domínio Personalizado**.  
   O painel de **domínio personalizado** abre-se.

3. Limpe o conteúdo da caixa de texto que contém o seu nome de domínio personalizado.

4. Selecione o botão **Guardar**.

Depois de o domínio personalizado ter sido removido com sucesso, verá uma notificação do portal de que a sua conta de armazenamento foi atualizada com sucesso

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para remover um registo de domínio personalizado, utilize o comando CLI [da conta de armazenamento az](https://docs.microsoft.com/cli/azure/storage/account) e, em seguida, especifique uma cadeia vazia `""` () para o valor do `--custom-domain` argumento.

* Formato de comando:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Exemplo de comando:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para remover um registo de domínio personalizado, utilize o [cmdlet Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell e, em seguida, especifique uma corda vazia `""` () para o valor do `-CustomDomainName` argumento.

* Formato de comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Exemplo de comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Mapear um domínio personalizado com HTTPS ativado

Esta abordagem envolve mais passos, mas permite o acesso https. 

Se não precisar que os utilizadores acedam ao seu conteúdo blob ou web utilizando HTTPS, consulte o [Mapa de um domínio personalizado apenas com](#enable-http) secção via HTTP deste artigo. 

Para mapear um domínio personalizado e permitir o acesso a HTTPS, faça o seguinte:

1. Ativar [o Azure CDN](../../cdn/cdn-overview.md) no seu ponto final de bolha ou web. 

   Para obter um ponto final de armazenamento blob, consulte [integrar uma conta de armazenamento Azure com a Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   Para obter um ponto final estático do site, consulte [integrar um website estático com a Azure CDN](static-website-content-delivery-network.md).

2. [Mapear o conteúdo do CDN do Azure para um domínio personalizado.](../../cdn/cdn-map-content-to-custom-domain.md)

3. [Ativar HTTPS num domínio personalizado Azure CDN](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Quando atualizar o seu website estático, certifique-se de limpar o conteúdo em cache nos servidores de borda CDN purgando o ponto final do CDN. Para obter mais informações, consulte [Remover um ponto final do Azure CDN](../../cdn/cdn-purge-endpoint.md).

4. (Opcional) Reveja as seguintes orientações:

   * [Fichas de assinatura de acesso partilhado (SAS) com CDN Azure](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures).

   * [Reorientação HTTP-to-HTTPS com Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

   * [Preços e faturação ao utilizar o Blob Storage com Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o alojamento estático do site no armazenamento do Azure Blob](storage-blob-static-website.md)
