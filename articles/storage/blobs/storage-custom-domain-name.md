---
title: Mapear um domínio personalizado para um ponto final de armazenamento de Blob Azure
titleSuffix: Azure Storage
description: Mapeie um domínio personalizado para um Armazenamento Blob ou ponto final web numa conta de armazenamento Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 9d05677ec47851557594ef47499da653accad141
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370479"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Mapear um domínio personalizado para um ponto final de armazenamento de Blob Azure

Você pode mapear um domínio personalizado para um ponto final de serviço blob ou um ponto final [de site estático.](storage-blob-static-website.md) 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> Este mapeamento funciona apenas para subdomínios (por exemplo: `www.contoso.com`). Se quiser que o seu ponto final da web esteja disponível no domínio raiz (por exemplo: `contoso.com`), então terá de utilizar o Azure CDN. Para obter orientação, consulte o Mapa um domínio personalizado com secção [ativada](#enable-https) por HTTPS deste artigo. Porque se vai a essa secção deste artigo para ativar o domínio raiz do seu domínio personalizado, o passo dentro dessa secção para ativar https é opcional. 

<a id="enable-http" />

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Mapear um domínio personalizado com apenas HTTP habilitado

Esta abordagem é mais fácil, mas permite apenas o acesso http. Se a conta de armazenamento estiver configurada para [exigir transferência segura](../common/storage-require-secure-transfer.md) em HTTPS, então deve ativar o acesso HTTPS para o seu domínio personalizado. 

Para permitir o acesso HTTPS, consulte o Mapa um domínio personalizado com secção [https ativada](#enable-https) deste artigo. 

<a id="map-a-domain" />

### <a name="map-a-custom-domain"></a>Mapear um domínio personalizado

> [!IMPORTANT]
> O seu domínio personalizado não será brevemente disponível para os utilizadores enquanto completa a configuração. Se o seu domínio suporta atualmente uma aplicação com um acordo de nível de serviço (SLA) que requer zero tempo de inatividade, siga os passos no Mapa um domínio personalizado com zero secção de tempo de [inatividade](#zero-down-time) deste artigo para garantir que os utilizadores podem aceder ao seu domínio enquanto o mapeamento DNS ocorre.

Se não estiver preocupado que o domínio não esteja brevemente disponível para os seus utilizadores, siga estes passos.

:heavy_check_mark: Passo 1: Obtenha o nome do anfitrião do seu ponto final de armazenamento.

:heavy_check_mark: Passo 2: Crie um registo de nome canónico (CNAME) com o seu fornecedor de domínio.

:heavy_check_mark: Passo 3: Registe o domínio personalizado com o Azure. 

:heavy_check_mark: Passo 4: Teste o seu domínio personalizado.

<a id="endpoint" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Passo 1: Obtenha o nome do anfitrião do seu ponto final de armazenamento 

O nome do hospedeiro é o URL final de ponto de armazenamento sem o identificador de protocolo e o corte de rasto. 

1. No [portal Azure,](https://portal.azure.com)vá à sua conta de armazenamento.

2. No painel do menu, em **Definições,** selecione **Propriedades**.  

3. Copie o valor do Ponto final do **Serviço De Blob Primário** ou do ponto final do site **estático primário** para um ficheiro de texto. 

4. Retire o identificador de protocolo *(por exemplo,* HTTPS) e o corte de rasto dessa corda. A tabela que se segue contém exemplos.

   | Tipo de ponto final |  endpoint | nome anfitrião |
   |------------|-----------------|-------------------|
   |serviço blob  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |site estática  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Reserve este valor para mais tarde.

<a id="create-cname-record" />

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Passo 2: Criar um registo de nome canónico (CNAME) com o seu fornecedor de domínio

Crie um disco CNAME para apontar para o seu nome de anfitrião. Um registo CNAME é um tipo de registo DNS que mapeia um nome de domínio de origem para um nome de domínio de destino.

1. Inscreva-se no site do seu registo de domínio e, em seguida, vá para a página para gerir a definição de DNS.

   Pode encontrar a página numa secção chamada **Nome de Domínio,** **DNS**ou **Gestão de Servidores**de Nome .

2. Encontre a secção para gerir os registos CNAME. 

   Você pode ter que ir a uma **CNAME**página **Alias**de configurações avançada si mesmo si mesmo si mesmo si **mesmo**si.

3. Crie um disco CNAME. Como parte desse registo, forneça os seguintes itens: 

   - O pseudónimo de subdomínio, como `www` ou `photos`. O subdomínio é necessário, os domínios radiculares não são suportados. 
      
   - O nome de anfitrião que obteve na secção [Get the host name of your storage endpoint](#endpoint) no início deste artigo. 

<a id="register" />

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Passo 3: Registe o seu domínio personalizado com o Azure

1. No [portal Azure,](https://portal.azure.com)vá à sua conta de armazenamento.

2. No painel de menus, em **Serviço Blob,** selecione **domínio Personalizado**.  

   ![opção de domínio personalizado](./media/storage-custom-domain-name/custom-domain-button.png "domínio personalizado")

   O painel de **domínio personalizado** abre.

3. Na caixa de **texto de nome domínio,** introduza o nome do seu domínio personalizado, incluindo o subdomínio  
   
   Por exemplo, se o seu domínio for *contoso.com* e o seu pseudónimo de subdomínio for *www,* insira `www.contoso.com`. Se o seu subdomínio for *em fotos,* introduza `photos.contoso.com`.

4. Para registar o domínio personalizado, escolha o botão **Guardar.**

   Depois de o registo CNAME ter procedido através dos Servidores de Nome de Domínio (DNS), e se os seus utilizadores tiverem as permissões adequadas, podem ver os dados blob utilizando o domínio personalizado.

#### <a name="step-4-test-your-custom-domain"></a>Passo 4: Teste o seu domínio personalizado

Para confirmar que o seu domínio personalizado está mapeado para o seu ponto final de serviço blob, crie uma bolha num contentor público dentro da sua conta de armazenamento. Em seguida, num navegador web, aceda à bolha utilizando um URI no seguinte formato: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por exemplo, para aceder a um formulário web no recipiente *myforms* no subdomínio *personalizado photos.contoso.com,* pode utilizar o seguinte URI: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time" />

### <a name="map-a-custom-domain-with-zero-downtime"></a>Mapear um domínio personalizado com zero tempo de inatividade

> [!NOTE]
> Se não estiver preocupado que o domínio não esteja brevemente disponível para os seus utilizadores, considere seguir os passos no Mapa uma secção de [domínio personalizado](#map-a-domain) deste artigo. É uma abordagem mais simples com menos passos.  

Se o seu domínio suporta atualmente uma aplicação com um acordo de nível de serviço (SLA) que requer zero tempo de inatividade, siga estas medidas para garantir que os utilizadores podem aceder ao seu domínio enquanto o mapeamento DNS ocorre. 

:heavy_check_mark: Passo 1: Obtenha o nome do anfitrião do seu ponto final de armazenamento.

:heavy_check_mark: Passo 2: Crie um registo de nome canónico intermediário (CNAME) com o seu fornecedor de domínio.

:heavy_check_mark: Passo 3: Pré-registar o domínio personalizado com o Azure.

:heavy_check_mark: Passo 4: Crie um registo CNAME com o seu fornecedor de domínio.

:heavy_check_mark: Passo 5: Teste o seu domínio personalizado.

<a id="endpoint-2" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Passo 1: Obtenha o nome do anfitrião do seu ponto final de armazenamento 

O nome do hospedeiro é o URL final de ponto de armazenamento sem o identificador de protocolo e o corte de rasto. 

1. No [portal Azure,](https://portal.azure.com)vá à sua conta de armazenamento.

2. No painel do menu, em **Definições,** selecione **Propriedades**.  

3. Copie o valor do Ponto final do **Serviço De Blob Primário** ou do ponto final do site **estático primário** para um ficheiro de texto. 

4. Retire o identificador de protocolo *(por exemplo,* HTTPS) e o corte de rasto dessa corda. A tabela que se segue contém exemplos.

   | Tipo de ponto final |  endpoint | nome anfitrião |
   |------------|-----------------|-------------------|
   |serviço blob  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |site estática  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Reserve este valor para mais tarde.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Passo 2: Criar um registo de nome canónico intermediário (CNAME) com o seu fornecedor de domínio

Crie um registo cname temporário para apontar para o seu nome de anfitrião. Um registo CNAME é um tipo de registo DNS que mapeia um nome de domínio de origem para um nome de domínio de destino.

1. Inscreva-se no site do seu registo de domínio e, em seguida, vá para a página para gerir a definição de DNS.

   Pode encontrar a página numa secção chamada **Nome de Domínio,** **DNS**ou **Gestão de Servidores**de Nome .

2. Encontre a secção para gerir os registos CNAME. 

   Você pode ter que ir a uma **CNAME**página **Alias**de configurações avançada si mesmo si mesmo si mesmo si **mesmo**si.

3. Crie um disco CNAME. Como parte desse registo, forneça os seguintes itens: 

   - O pseudónimo de subdomínio, como `www` ou `photos`. O subdomínio é necessário, os domínios radiculares não são suportados.

     Adicione o subdomínio `asverify` ao pseudónimo. Por exemplo: `asverify.www` ou `asverify.photos`.
       
   - O nome de anfitrião que obteve na secção [Get the host name of your storage endpoint](#endpoint) no início deste artigo. 

     Adicione o subdomínio `asverify` ao nome do anfitrião. Por exemplo: `asverify.mystorageaccount.blob.core.windows.net`.

4. Para registar o domínio personalizado, escolha o botão **Guardar.**

   Se o registo for bem sucedido, o portal notifica que a sua conta de armazenamento foi atualizada com sucesso. O seu domínio personalizado foi verificado pelo Azure, mas o tráfego para o seu domínio ainda não está a ser encaminhado para a sua conta de armazenamento.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Passo 3: Pré-registe o seu domínio personalizado com o Azure

Ao registar o seu domínio personalizado com o Azure, permite que o Azure reconheça o seu domínio personalizado sem ter de modificar o registo DNS para o domínio. Desta forma, quando modificar o registo DNS para o domínio, será mapeado para o ponto final blob sem tempo de inatividade.

1. No [portal Azure,](https://portal.azure.com)vá à sua conta de armazenamento.

2. No painel de menus, em **Serviço Blob,** selecione **domínio Personalizado**.  

   ![opção de domínio personalizado](./media/storage-custom-domain-name/custom-domain-button.png "domínio personalizado")

   O painel de **domínio personalizado** abre.

3. Na caixa de **texto de nome domínio,** introduza o nome do seu domínio personalizado, incluindo o subdomínio  
   
   Por exemplo, se o seu domínio for *contoso.com* e o seu pseudónimo de subdomínio for *www,* insira `www.contoso.com`. Se o seu subdomínio for *em fotos,* introduza `photos.contoso.com`.

4. Selecione a caixa de **verificação de validação Indireta CNAME.**

5. Para registar o domínio personalizado, escolha o botão **Guardar.**
  
   Depois de o registo CNAME ter procedido através dos Servidores de Nome de Domínio (DNS), e se os seus utilizadores tiverem as permissões adequadas, podem ver os dados blob utilizando o domínio personalizado.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Passo 4: Criar um registo CNAME com o seu fornecedor de domínio

Crie um registo cname temporário para apontar para o seu nome de anfitrião.

1. Inscreva-se no site do seu registo de domínio e, em seguida, vá para a página para gerir a definição de DNS.

   Pode encontrar a página numa secção chamada **Nome de Domínio,** **DNS**ou **Gestão de Servidores**de Nome .

2. Encontre a secção para gerir os registos CNAME. 

   Você pode ter que ir a uma **CNAME**página **Alias**de configurações avançada si mesmo si mesmo si mesmo si **mesmo**si.

3. Crie um disco CNAME. Como parte desse registo, forneça os seguintes itens: 

   - O pseudónimo de subdomínio, como `www` ou `photos`. O subdomínio é necessário, os domínios radiculares não são suportados.
      
   - O nome de anfitrião que obteve na secção [Get the host name of your storage endpoint](#endpoint-2) no início deste artigo. 

#### <a name="step-5-test-your-custom-domain"></a>Passo 5: Teste o seu domínio personalizado

Para confirmar que o seu domínio personalizado está mapeado para o seu ponto final de serviço blob, crie uma bolha num contentor público dentro da sua conta de armazenamento. Em seguida, num navegador web, aceda à bolha utilizando um URI no seguinte formato: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por exemplo, para aceder a um formulário web no recipiente *myforms* no subdomínio *personalizado photos.contoso.com,* pode utilizar o seguinte URI: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Remova um mapeamento de domínio personalizado

Para remover um mapeamento de domínio personalizado, desregilhe o domínio personalizado. Utilize um dos seguintes procedimentos.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Para remover a definição de domínio personalizado, faça o seguinte:

1. No [portal Azure,](https://portal.azure.com)vá à sua conta de armazenamento.

2. No painel de menus, em **Serviço Blob,** selecione **domínio Personalizado**.  
   O painel de **domínio personalizado** abre.

3. Limpe o conteúdo da caixa de texto que contém o seu nome de domínio personalizado.

4. Selecione o botão **Guardar**.

Depois de o domínio personalizado ter sido removido com sucesso, verá uma notificação do portal de que a sua conta de armazenamento foi atualizada com sucesso

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para remover um registo de domínio personalizado, utilize o comando CLI de atualização da conta de [armazenamento az](https://docs.microsoft.com/cli/azure/storage/account) e, em seguida, especifique uma cadeia vazia (`""`) para o valor de argumento `--custom-domain`.

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

Para remover um registo de domínio personalizado, utilize o cmdlet [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell e, em seguida, especifique uma cadeia vazia (`""`) para o valor de argumento `-CustomDomainName`.

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

<a id="enable-https" />

## <a name="map-a-custom-domain-with-https-enabled"></a>Mapear um domínio personalizado com HTTPS habilitado

Esta abordagem envolve mais passos, mas permite o acesso HTTPS. 

Se não precisar de utilizadores para aceder ao seu conteúdo blob ou web utilizando HTTPS, consulte o [Mapa um domínio personalizado apenas com](#enable-http) secção ativada por HTTP deste artigo. 

Para mapear um domínio personalizado e ativar o acesso HTTPS, faça o seguinte:

1. Ative [o Azure CDN](../../cdn/cdn-overview.md) no seu ponto final ou bolha. 

   Para um ponto final de armazenamento blob, consulte Integrar uma conta de [armazenamento Azure com O Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   Para um ponto final de site estático, consulte [Integrar um site estático com O CDN Azure](static-website-content-delivery-network.md).

2. [Map Azure CDN conteúdo para um domínio personalizado](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Ativar HTTPS num domínio personalizado Azure CDN](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Quando atualizar o seu website estático, certifique-se de limpar o conteúdo em cache nos servidores de borda CDN purgando o ponto final do CDN. Para obter mais informações, consulte [Remover um ponto final do Azure CDN](../../cdn/cdn-purge-endpoint.md).

4. (Opcional) Reveja as seguintes orientações:

   * Fichas de assinatura de [acesso partilhado (SAS) com Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures).

   * [Reorientação HTTP-to-HTTPS com Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

   * [Preços e faturação ao utilizar o Blob Storage com o Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre hospedagem de site estático no armazenamento de Azure Blob](storage-blob-static-website.md)
