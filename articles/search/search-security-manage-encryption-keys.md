---
title: Encriptação em repouso utilizando chaves geridas pelo cliente
titleSuffix: Azure Cognitive Search
description: Complemente a encriptação do lado do servidor sobre índices e mapas de sinónimo na Pesquisa Cognitiva Azure utilizando as teclas que cria e gere no Cofre da Chave Azure.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: b0871b6365d78129cd6fdaec82fee14e2b0a7a4b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693448"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Configure as chaves geridas pelo cliente para encriptação de dados na Pesquisa Cognitiva Azure

A Azure Cognitive Search encripta automaticamente o conteúdo indexado em repouso com [as teclas geridas pelo serviço](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Se for necessária mais proteção, pode complementar a encriptação padrão com uma camada de encriptação adicional utilizando as teclas que cria e gere no Cofre da Chave Azure. Este artigo acompanha-o através dos passos da configuração da encriptação CMK.

A encriptação CMK depende do [Cofre da Chave Azure](../key-vault/general/overview.md). Pode criar as suas próprias chaves de encriptação e armazená-las num cofre de chaves, ou pode utilizar os APIs do Azure Key Vault para gerar chaves de encriptação. Com o Azure Key Vault, também pode auditar a utilização da chave se [ativar o registo](../key-vault/general/logging.md).  

A encriptação com teclas geridas pelo cliente é aplicada a índices individuais ou mapas de sinónimo quando esses objetos são criados, e não é especificado no próprio nível de serviço de pesquisa. Apenas novos objetos podem ser encriptados. Não é possível encriptar conteúdo que já existe.

As chaves não precisam de estar todas no mesmo cofre. Um único serviço de pesquisa pode hospedar vários índices encriptados ou mapas de sinónimo, cada um encriptado com as suas próprias chaves de encriptação geridas pelo cliente, armazenados em diferentes cofres-chave. Também pode ter índices e mapas de sinónimo no mesmo serviço que não são encriptados usando chaves geridas pelo cliente.

>[!Important]
> Se implementar chaves geridas pelo cliente, certifique-se de seguir procedimentos rigorosos durante a rotação de rotina das chaves do cofre e dos segredos e registos da aplicação do Ative Directory. Atualize sempre todos os conteúdos encriptados para utilizar novos segredos e chaves antes de eliminar os antigos. Se perder este passo, o seu conteúdo não pode ser desencriptado.

## <a name="double-encryption"></a>Encriptação dupla

Para os serviços criados após 1 de agosto de 2020 e em regiões específicas, o âmbito da encriptação CMK inclui discos temporários, alcançando [a dupla encriptação completa,](search-security-overview.md#double-encryption)atualmente disponível nestas regiões: 

+ E.U.A. Oeste 2
+ E.U.A. Leste
+ E.U.A. Centro-Sul
+ US Gov - Virginia
+ US Gov - Arizona

Se estiver a utilizar uma região diferente, ou um serviço criado antes de 1 de agosto, então a encriptação CMK está limitada apenas ao disco de dados, excluindo os discos temporários utilizados pelo serviço.

## <a name="prerequisites"></a>Pré-requisitos

Neste cenário são utilizadas as seguintes ferramentas e serviços.

+ [Pesquisa Cognitiva Azure](search-create-service-portal.md) num [nível de faturação](search-sku-tier.md#tiers) (Básico ou superior, em qualquer região).
+ [Azure Key Vault](../key-vault/general/overview.md), pode criar um cofre de chaves utilizando [o portal Azure,](../key-vault//general/quick-create-portal.md) [Azure CLI](../key-vault//general/quick-create-cli.md)ou [Azure PowerShell](../key-vault//general/quick-create-powershell.md). na mesma subscrição que a Azure Cognitive Search. O cofre da chave deve ter **proteção de eliminação** e **purga** ativada.
+ [Diretório Ativo Azure](../active-directory/fundamentals/active-directory-whatis.md). Se não tiver um, [crie um novo inquilino.](../active-directory/develop/quickstart-create-new-tenant.md)

Deve ter uma aplicação de pesquisa que possa criar o objeto encriptado. Neste código, você vai fazer referência a uma chave de cofre e informações de registo do Ative Directory. Este código pode ser uma aplicação de funcionamento, ou código protótipo, como a amostra de [código C# DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK).

> [!TIP]
> Pode utilizar [o Código do Estúdio Visual ou Carteiro](search-get-started-rest.md), ou [Azure PowerShell,](./search-get-started-powershell.md)para chamar APIs REST que criam índices e mapas de sinónimo que incluem um parâmetro chave de encriptação. Não existe suporte ao portal para adicionar uma chave para índices ou mapas de sinónimo neste momento.

## <a name="1---enable-key-recovery"></a>1 - Permitir a recuperação das chaves

Devido à natureza da encriptação com chaves geridas pelo cliente, ninguém pode recuperar os seus dados se a chave do cofre da Chave Azure for eliminada. Para evitar a perda de dados causada por supressões acidentais das chaves do Cofre, deve ser ativada a proteção contra a eliminação e purga no cofre da chave. A eliminação suave é ativada por padrão, pelo que só encontrará problemas se o desativar propositadamente. A proteção de purga não é ativada por padrão, mas é necessária para encriptação CMK de pesquisa cognitiva Azure. Para obter mais informações, consulte [as vistas](../key-vault/general/soft-delete-overview.md) gerais da proteção para eliminar e [purgar.](../key-vault/general/soft-delete-overview.md#purge-protection)

Pode definir ambas as propriedades utilizando os comandos portal, PowerShell ou Azure CLI.

### <a name="using-azure-portal"></a>Com o Portal do Azure

1. [Inscreva-se no portal Azure](https://portal.azure.com) e abra a página geral do cofre da chave.

1. Na página **'Visão Geral'** no **âmbito do Essencial,** **ativar a proteção para eliminar** e **purgar.**

### <a name="using-powershell"></a>Com o PowerShell

1. Corre `Connect-AzAccount` para preparar as tuas credenciais do Azure.

1. Executar o seguinte comando para ligar ao seu cofre de chaves, substituindo `<vault_name>` por um nome válido:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. O Azure Key Vault é criado com soft-delete ativado. Se estiver desativado no seu cofre, executar o seguinte comando:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Permitir a proteção da purga:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Guarde as suas atualizações:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

+ Se tiver uma [instalação de Azure CLI,](https://docs.microsoft.com/cli/azure/install-azure-cli)pode executar o seguinte comando para ativar as propriedades necessárias.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 - Criar uma chave no Cofre de Chaves

Salte este passo se já tiver uma chave no Cofre da Chave Azure.

1. [Inscreva-se no portal Azure](https://portal.azure.com) e abra a página geral do cofre da chave.

1. Selecione **Teclas** à esquerda e, em seguida, **selecione + Gerar/Importar**.

1. No painel **de chaves Criar,** a partir da lista de **Opções,** escolha o método que pretende utilizar para criar uma chave. Pode **gerar** uma nova chave, **carregar** uma chave existente ou utilizar **o Restore Backup** para selecionar uma cópia de segurança de uma chave.

1. Introduza um **Nome** para a sua chave e selecione opcionalmente outras propriedades chave.

1. Selecione **Criar** para iniciar a implementação.

1. Tome nota do Identificador chave – é composto pelo **valor-chave Uri**, o **nome-chave** e a **versão chave**. Necessitará do identificador para definir um índice encriptado na Pesquisa Cognitiva Azure.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Criar uma nova chave de cofre":::

## <a name="3---register-an-app-in-active-directory"></a>3 - Registar uma aplicação no Ative Directory

1. No [portal Azure,](https://portal.azure.com)encontre o recurso Azure Ative Directory para a sua subscrição.

1. À esquerda, em **Manage,** selecione **registos de Aplicações** e, em seguida, selecione **Novo registo**.

1. Dê ao registo um nome, talvez um nome semelhante ao nome do pedido de pesquisa. Selecione **Registar**.

1. Assim que o registo da aplicação for criado, copie o ID da aplicação. Terá de fornecer este cordão à sua aplicação. 

   Se estiver a passar pelo [DotNetHowToEncryptionUsingCMK,](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)cole este valor no **appsettings.jsem** ficheiro.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="ID de aplicação na secção Essencial":::

1. Em seguida, **selecione Certificados & segredos** à esquerda.

1. Selecione **Novo segredo do cliente**. Dê ao segredo um nome de exibição e **selecione Adicionar**.

1. Copie o segredo da aplicação. Se estiver a passar pela amostra, cole este valor na **appsettings.jsficheiro.**

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Segredo da aplicação":::

## <a name="4---grant-key-access-permissions"></a>4 - Conceder permissões de acesso à chave

Neste passo, irá criar uma política de acesso no Key Vault. Esta política dá à aplicação que registou com o Ative Directory permissão para utilizar a sua chave gerida pelo cliente.

As permissões de acesso podem ser revogadas a qualquer momento. Uma vez revogado, qualquer índice de serviço de pesquisa ou mapa de sinónimo que utilize o cofre chave tornar-se-á inutilizável. Restaurar as permissões de acesso ao cofre do cofre de chaves mais tarde irá restaurar o acesso ao mapa index\synonym. Para obter mais informações, consulte [acesso seguro a um cofre de chaves.](../key-vault/general/secure-your-key-vault.md)

1. Ainda no portal Azure, abra a página **geral do** cofre da chave. 

1. Selecione as **políticas de Acesso** à esquerda e selecione + Adicionar Política de **Acesso**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Adicione nova política de acesso ao cofre de chaves":::

1. Escolha **Selecionar o principal** e selecione a aplicação que registou no Ative Directory. Pode procurá-la pelo nome.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Selecione o principal da política de acesso ao cofre":::

1. Nas **permissões chave**, escolha *Obter,* *Desembrulhar a chave* e a chave de *embrulho*.

1. Em **Permissões Secretas**, selecione *Get*.

1. Nas **Permissões de Certificados**, selecione *Get*.

1. **Selecione Adicionar** e, em seguida, **Guardar**.

> [!Important]
> O conteúdo encriptado em Azure Cognitive Search está configurado para utilizar uma chave Azure Key Vault específica com uma **versão** específica . Se alterar a chave ou versão, o mapa de índice ou sinónimo deve ser atualizado para utilizar a nova versão\chave **antes** de eliminar a chave anterior\versão. Se não o fizer, o índice ou o mapa de sinónimos não serão utilizáveis, pois não conseguirá desencriptar o conteúdo assim que o acesso à chave for perdido.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5 - Encriptar conteúdo

Para adicionar uma chave gerida pelo cliente num mapa de índice, fonte de dados, skillset, indexante ou sinónimo, deve utilizar a [API search REST](https://docs.microsoft.com/rest/api/searchservice/) ou um SDK. O portal não expõe mapas de sinónimo ou propriedades de encriptação. Quando utiliza índices de API válidos, fontes de dados, skillsets, indexadores e mapas de sinónimo suportam uma propriedade encriptação de alto **nívelKey.**

Este exemplo utiliza a API REST, com valores para Azure Key Vault e Azure Ative Directory:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> Nenhum destes detalhes chave do cofre é considerado secreto e poderia ser facilmente recuperado navegando para a página chave do Azure Key Vault relevante no portal Azure.

## <a name="example-index-encryption"></a>Exemplo: Encriptação do índice

Criar um índice encriptado utilizando a [API de Pesquisa Cognitiva Do Índice Azure.](https://docs.microsoft.com/rest/api/searchservice/create-index) Utilize a `encryptionKey` propriedade para especificar qual a chave de encriptação a utilizar.
> [!Note]
> Nenhum destes detalhes chave do cofre é considerado secreto e poderia ser facilmente recuperado navegando para a página chave do Azure Key Vault relevante no portal Azure.

## <a name="rest-examples"></a>Exemplos DE REPOUSO

Esta secção mostra o JSON completo para um mapa de índice e sinónimo encriptado

### <a name="index-encryption"></a>Encriptação de índice

Os detalhes da criação de um novo índice através da API REST podem ser encontrados no [Create Index (REST API)](/rest/api/searchservice/create-index), onde a única diferença aqui é especificar os detalhes chave de encriptação como parte da definição de índice:

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Agora pode enviar o pedido de criação de índice e, em seguida, começar a usar o índice normalmente.

### <a name="synonym-map-encryption"></a>Encriptação do mapa do synonym

Crie um mapa de sinónimo encriptado utilizando o [Create Synonym Map Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map). Utilize a `encryptionKey` propriedade para especificar qual a chave de encriptação a utilizar.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Agora pode enviar o pedido de criação de mapa sinónimo e, em seguida, começar a usá-lo normalmente.

## <a name="example-data-source-encryption"></a>Exemplo: Encriptação de fonte de dados

Criar uma fonte de dados encriptada utilizando a [Create Data Source (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Utilize a `encryptionKey` propriedade para especificar qual a chave de encriptação a utilizar.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Pode agora enviar o pedido de criação de fonte de dados e, em seguida, começar a usá-lo normalmente.

## <a name="example-skillset-encryption"></a>Exemplo: Encriptação skillset

Crie um skillset encriptado utilizando a [API de Pesquisa Cognitiva Create Skillset Azure](https://docs.microsoft.com/rest/api/searchservice/create-skillset). Utilize a `encryptionKey` propriedade para especificar qual a chave de encriptação a utilizar.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Agora pode enviar o pedido de criação de skillset e, em seguida, começar a usá-lo normalmente.

## <a name="example-indexer-encryption"></a>Exemplo: Encriptação do Indexante

Crie um indexante encriptado utilizando a [API de Pesquisa Cognitiva Do Indexer Azure](https://docs.microsoft.com/rest/api/searchservice/create-indexer). Utilize a `encryptionKey` propriedade para especificar qual a chave de encriptação a utilizar.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Pode agora enviar o pedido de criação do indexante e, em seguida, começar a usá-lo normalmente.

>[!Important]
> Embora `encryptionKey` não possa ser adicionado aos índices de pesquisa existentes ou mapas de sinónimo, pode ser atualizado fornecendo valores diferentes para qualquer um dos três detalhes chave do cofre (por exemplo, atualizar a versão chave). Ao mudar para uma nova chave Key Vault ou uma nova versão chave, qualquer índice de pesquisa ou mapa de sinónimo que utilize a chave deve ser atualizado primeiro para usar a nova versão **key\antes** de eliminar a versão\versão da chave anterior. Se não o fizer, o mapa do índice ou do sinónimo não será utilizável, uma vez que não será capaz de desencriptar o conteúdo assim que o acesso à chave for perdido. Embora restaurar as permissões de acesso ao cofre do cofre chave mais tarde, irá restaurar o acesso ao conteúdo.

## <a name="simpler-alternative-trusted-service"></a>Alternativa mais simples: Serviço de confiança

Dependendo da configuração do inquilino e dos requisitos de autenticação, poderá implementar uma abordagem mais simples para aceder a uma chave de cofre. Em vez de criar e utilizar uma aplicação Ative Directory, pode fazer de um serviço de pesquisa um serviço de confiança, permitindo uma identidade gerida pelo sistema para o mesmo. Em seguida, usaria o serviço de pesquisa fidedigno como um princípio de segurança, em vez de uma aplicação registada em AD, para aceder à chave do cofre.

Esta abordagem permite-lhe omitir os passos para registo de aplicações e segredos de aplicação, e simplifica uma definição chave de encriptação apenas para os componentes chave do cofre (URI, nome do cofre, versão chave).

Em geral, uma identidade gerida permite que o seu serviço de pesquisa autente para a Azure Key Vault sem armazenar credenciais (ApplicationID ou ApplicationSecret) em código. O ciclo de vida deste tipo de identidade gerida está ligado ao ciclo de vida do seu serviço de pesquisa, que só pode ter uma identidade gerida. Para obter mais informações sobre como funcionam as identidades geridas, consulte [o que são identidades geridas para os recursos da Azure.](../active-directory/managed-identities-azure-resources/overview.md)

1. Faça do seu serviço de pesquisa um serviço de confiança.
   ![Ligue o sistema atribuído identidade gerida](./media/search-managed-identities/turn-on-system-assigned-identity.png "Ligue o sistema atribuído identidade gerida")

1. Ao estabelecer uma política de acesso no Azure Key Vault, escolha o serviço de pesquisa fidedigno como o princípio (em vez da aplicação registada em AD). Atribua as mesmas permissões (várias GETs, WRAP, UNWRAP) como instruído na etapa de permissões de acesso ao subsídio.

1. Utilize uma construção simplificada do `encryptionKey` que omite as propriedades do Ative Directory.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

As condições que o impedirão de adotar esta abordagem simplificada incluem:

+ Não é possível conceder diretamente ao seu serviço de pesquisa permissões de acesso ao cofre Key (por exemplo, se o serviço de pesquisa estiver num inquilino ative diferente do cofre da chave Azure).

+ Um único serviço de pesquisa é necessário para hospedar vários mapas de índices encriptados\synonym, cada um usando uma chave diferente de um cofre chave diferente, onde cada cofre-chave deve usar **uma identidade diferente** para a autenticação. Como um serviço de pesquisa só pode ter uma identidade gerida, um requisito para identidades múltiplas desqualifica a abordagem simplificada para o seu cenário.  

## <a name="work-with-encrypted-content"></a>Trabalhar com conteúdo encriptado

Com a encriptação CMK, irá notar latência tanto para indexar como para consultas devido ao trabalho extra de encriptação/desencriptação. A Azure Cognitive Search não regista a atividade de encriptação, mas pode monitorizar o acesso à chave através do registo do cofre de chaves. Recomendamos que [ative o registo](../key-vault/general/logging.md) como parte da configuração do cofre de chaves.

Espera-se que a rotação da chave ocorra ao longo do tempo. Sempre que roda as teclas, é importante seguir esta sequência:

1. [Determine a chave utilizada por um mapa de índice ou sinónimo](search-security-get-encryption-keys.md).
1. [Crie uma nova chave no cofre da chave,](../key-vault/keys/quick-create-portal.md)mas deixe a chave original disponível.
1. [Atualize as propriedades de encriptaçãoSa num](/rest/api/searchservice/update-index) mapa de índice ou sinónimo para utilizar os novos valores. Apenas objetos que foram originalmente criados com esta propriedade podem ser atualizados para usar um valor diferente.
1. Desative ou elimine a chave anterior no cofre da chave. Monitorize o acesso da chave para verificar se a nova chave está a ser utilizada.

Por razões de desempenho, o serviço de pesquisa caches a chave por até várias horas. Se desativar ou eliminar a chave sem fornecer uma nova, as consultas continuarão a funcionar temporariamente até que a cache expire. No entanto, uma vez que o serviço de pesquisa não consegue desencriptar o conteúdo, receberá esta mensagem: "Acesso proibido. A chave de consulta usada poderia ter sido revogada - por favor, redaçar." 

## <a name="next-steps"></a>Passos seguintes

Se não está familiarizado com a arquitetura de segurança da Azure, reveja a documentação da [Azure Security,](../security/index.yml)e em particular, este artigo:

> [!div class="nextstepaction"]
> [Encriptação inativa de dados](../security/fundamentals/encryption-atrest.md)
