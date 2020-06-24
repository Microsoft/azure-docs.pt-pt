---
title: Encriptação em repouso utilizando chaves geridas pelo cliente
titleSuffix: Azure Cognitive Search
description: Complemente a encriptação do lado do servidor sobre índices e mapas de sinónimo na Pesquisa Cognitiva Azure utilizando as teclas que cria e gere no Cofre da Chave Azure.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 548ebf5b0d5836383b74326516fbe47b1b25f60f
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85080197"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Encriptação no resto do conteúdo na Pesquisa Cognitiva Azure utilizando chaves geridas pelo cliente no Cofre da Chave Azure

Por predefinição, a Azure Cognitive Search encripta o conteúdo indexado em repouso com [as teclas geridas pelo serviço](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models). Pode complementar a encriptação padrão com uma camada de encriptação adicional utilizando as teclas que cria e gere no Cofre da Chave Azure. Este artigo leva-o pelos degraus.

A encriptação do lado do servidor é suportada através da integração com [o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Pode criar as suas próprias chaves de encriptação e armazená-las num cofre de chaves, ou pode utilizar os APIs do Azure Key Vault para gerar chaves de encriptação. Com o Azure Key Vault, também pode auditar o uso da chave. 

A encriptação com as teclas geridas pelo cliente é configurada ao nível do mapa de índice ou de sinónimo quando esses objetos são criados, e não no nível de serviço de pesquisa. Não é possível encriptar conteúdo que já existe. 

As chaves não precisam de estar todas no mesmo Cofre de Chaves. Um único serviço de pesquisa pode hospedar vários índices encriptados ou mapas de sinónimos cada um encriptado com as suas próprias chaves de encriptação geridas pelo cliente armazenadas em diferentes Cofres-Chave.  Também pode ter índices e mapas de sinónimo no mesmo serviço que não são encriptados usando chaves geridas pelo cliente. 

> [!IMPORTANT] 
> Esta funcionalidade está disponível na [versão REST API 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) e [na versão .NET SDK 8.0-preview](search-dotnet-sdk-migration-version-9.md). Atualmente não existe suporte para configurar chaves de encriptação geridas pelo cliente no portal Azure. O serviço de pesquisa deve ser criado após janeiro de 2019 e não pode ser um serviço gratuito (partilhado).

## <a name="prerequisites"></a>Pré-requisitos

Neste exemplo são utilizados os seguintes serviços. 

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. 

+ [Crie um recurso Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) ou encontre um cofre existente sob a sua subscrição.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) são utilizados para tarefas de configuração.

+ [Postman](search-get-started-postman.md), [Azure PowerShell](search-create-index-rest-api.md) e [.NET SDK pré-visualização](https://aka.ms/search-sdk-preview) podem ser usados para chamar a API REST. Não existe suporte ao portal para encriptação gerida pelo cliente neste momento.

>[!Note]
> Devido à natureza da encriptação com a funcionalidade de chaves geridas pelo cliente, a Azure Cognitive Search não será capaz de recuperar os seus dados se a sua chave de cofre Azure Key for eliminada. Para evitar a perda de dados causada por eliminações acidentais das chaves do Cofre da Chave, **deve** ativar a proteção de eliminação e purga suave no cofre de chaves antes de poder ser utilizada. Para obter mais informações, consulte [a Azure Key Vault soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="1---enable-key-recovery"></a>1 - Permitir a recuperação das chaves

Depois de criar o recurso Azure Key Vault, ative a **Proteção** de **Eliminação e** Purga Suave no cofre de chave selecionado executando os seguintes comandos PowerShell ou Azure CLI:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - Criar uma nova chave

Se estiver a utilizar uma chave existente para encriptar o conteúdo da Pesquisa Cognitiva do Azure, ignore este passo.

1. [Inscreva-se no portal Azure](https://portal.azure.com) e navegue para o painel de porta-chaves do cofre.

1. Selecione a definição de **Chaves** a partir do painel de navegação esquerdo e clique **em + Gerar/Importar**.

1. No painel **de chaves Criar,** a partir da lista de **Opções,** escolha o método que pretende utilizar para criar uma chave. Pode **gerar** uma nova chave, **carregar** uma chave existente ou utilizar **o Restore Backup** para selecionar uma cópia de segurança de uma chave.

1. Introduza um **Nome** para a sua chave e selecione opcionalmente outras propriedades chave.

1. Clique no botão **Criar** para iniciar a implementação.

Tome nota do Identificador chave – este é composto a partir do **valor-chave Uri,** o **nome-chave**, e a **versão chave**. Você precisará destes para definir um índice encriptado na Pesquisa Cognitiva Azure.
 
![Criar uma nova chave de cofre](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Criar uma nova chave de cofre")

## <a name="3---create-a-service-identity"></a>3 - Criar uma identidade de serviço

A atribuição de uma identidade ao seu serviço de pesquisa permite-lhe conceder permissões de acesso ao Key Vault ao seu serviço de pesquisa. O seu serviço de pesquisa utilizará a sua identidade para autenticar com o cofre da Chave Azure.

A Azure Cognitive Search suporta duas formas de atribuir identidade: uma identidade gerida ou uma aplicação de Diretório Ativo Azure gerida externamente. 

Se possível, utilize uma identidade gerida. É a forma mais simples de atribuir uma identidade ao seu serviço de pesquisa e deve funcionar na maioria dos cenários. Se estiver a utilizar várias teclas para índices e mapas de sinónimos, ou se a sua solução estiver numa arquitetura distribuída que desqualifique a autenticação baseada na identidade, utilize a abordagem avançada [do Azure Ative Directory,](#aad-app) descrita no final deste artigo.

 Em geral, uma identidade gerida permite que o seu serviço de pesquisa autente para a Azure Key Vault sem armazenar credenciais em código. O ciclo de vida deste tipo de identidade gerida está ligado ao ciclo de vida do seu serviço de pesquisa, que só pode ter uma identidade gerida. [Saiba mais sobre identidades geridas.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

1. Para criar uma identidade gerida, [inscreva-se no portal Deaure](https://portal.azure.com) e abra o painel de instrumentos de serviço de pesquisa. 

1. Clique em **Identidade** no painel de navegação esquerdo, altere o seu estado para **On**e clique em **Guardar**.

![Ativar uma identidade gerida](./media/search-enable-msi/enable-identity-portal.png "Ativar uma identidade manged")

## <a name="4---grant-key-access-permissions"></a>4 - Conceder permissões de acesso à chave

Para ativar o seu serviço de pesquisa para usar a sua chave Key Vault, terá de conceder ao seu serviço de pesquisa determinadas permissões de acesso.

As permissões de acesso podem ser revogadas a qualquer momento. Uma vez revogado, qualquer índice de serviço de pesquisa ou mapa de sinónimo que utilize o cofre chave tornar-se-á inutilizável. Restaurar as permissões de acesso ao cofre do cofre de chaves mais tarde irá restaurar o acesso ao mapa index\synonym. Para obter mais informações, consulte [acesso seguro a um cofre de chaves.](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)

1. [Inscreva-se no portal Azure](https://portal.azure.com) e abra a página geral do cofre da chave. 

1. Selecione as **políticas de Acesso** a partir do painel de navegação à esquerda e clique em **+Adicionar novo**.

   ![Adicione nova política de acesso ao cofre de chaves](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Adicione nova política de acesso ao cofre de chaves")

1. Clique **em Selecionar o principal** e selecione o seu serviço de Pesquisa Cognitiva Azure. Pode pesquisar pelo nome ou pelo ID do objeto que foi apresentado depois de permitir a identidade gerida.

   ![Selecione o principal da política de acesso ao cofre](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Selecione o principal da política de acesso ao cofre")

1. Clique em **permissões chave** e selecione *Get*, *Desembrulhar a chave* e embrulhar *a tecla*. Pode utilizar o modelo *de armazenamento do Lago de Dados Azure ou Azure Para* selecionar rapidamente as permissões necessárias.

   A Azure Cognitive Search deve ser concedida com as [seguintes permissões de acesso:](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)

   * *Get* - permite que o seu serviço de pesquisa recupere as partes públicas da sua chave num Cofre de Chaves
   * *Wrap Key* - permite que o seu serviço de pesquisa utilize a sua chave para proteger a chave de encriptação interna
   * *Desembrulhar-se -* permite que o seu serviço de pesquisa utilize a sua chave para desembrulhar a chave de encriptação interna

   ![Selecione permissões de chave de acesso ao cofre](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Selecione permissões de chave de acesso ao cofre")

1. Clique **em OK** e **guarde** as alterações da política de acesso.

> [!Important]
> O conteúdo encriptado em Azure Cognitive Search está configurado para utilizar uma chave Azure Key Vault específica com uma **versão**específica . Se alterar a chave ou versão, o mapa de índice ou sinónimo deve ser atualizado para utilizar a nova versão\chave **antes** de eliminar a chave anterior\versão. Se não o fizer, o índice ou o mapa de sinónimos não serão utilizáveis, pois não conseguirá desencriptar o conteúdo assim que o acesso à chave for perdido.   

## <a name="5---encrypt-content"></a>5 - Encriptar conteúdo

A criação de um mapa de índice ou sinónimo encriptado com chave gerida pelo cliente ainda não é possível utilizando o portal Azure. Utilize a Azure Cognitive Search REST API para criar um mapa de índice ou sinónimo.

Tanto o mapa de índice como o mapa de sinónimo suportam uma nova propriedade encriptação de nível **superior,** usada para especificar a chave. 

Utilizando o **cofre-chave Uri,** **nome chave** e a **versão chave** da chave do cofre chave, podemos criar uma definição **encriptaçãoKey:**

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Nenhum destes detalhes chave do cofre é considerado secreto e poderia ser facilmente recuperado navegando para a página chave do Azure Key Vault relevante no portal Azure.

Se estiver a utilizar uma aplicação AAD para autenticação do Key Vault em vez de utilizar uma identidade gerida, adicione **as credenciais** de acesso à aplicação AAD à sua chave de encriptação: 
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

## <a name="example-index-encryption"></a>Exemplo: Encriptação do índice
Os detalhes da criação de um novo índice através da API REST podem ser encontrados no [Create Index (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index), onde a única diferença aqui é especificar os detalhes chave de encriptação como parte da definição de índice: 

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
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
Agora pode enviar o pedido de criação de índice e, em seguida, começar a usar o índice normalmente.

## <a name="example-synonym-map-encryption"></a>Exemplo: Encriptação do mapa do synonym

Os detalhes da criação de um novo mapa de sinónimos através da API REST podem ser encontrados no [Create Synonym Map (Azure Cognitive Search REST API),](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)onde a única diferença aqui é especificar os detalhes chave de encriptação como parte da definição do mapa de sinónimo: 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
Agora pode enviar o pedido de criação de mapa sinónimo e, em seguida, começar a usá-lo normalmente.

>[!Important] 
> Embora a **encriptaçãoKey** não possa ser adicionada aos índices de Pesquisa Cognitiva do Azure ou aos mapas de sinónimos existentes, pode ser atualizado fornecendo valores diferentes para qualquer um dos três detalhes chave do cofre (por exemplo, atualizar a versão chave). Ao mudar para uma nova chave key Vault ou uma nova versão chave, qualquer índice de pesquisa cognitiva ou mapa de sinónimos Azure que utilize a chave deve ser atualizado primeiro para usar a nova versão\chave **antes de** eliminar a versão\chave anterior. Se não o fizer, o mapa do índice ou do sinónimo não será utilizável, uma vez que não será capaz de desencriptar o conteúdo assim que o acesso à chave for perdido.   
> Restaurar as permissões de acesso ao cofre do cofre da Chave mais tarde irá restaurar o acesso ao conteúdo.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>Avançado: Utilize uma aplicação de Diretório Ativo Azure gerida externamente

Quando uma identidade gerida não é possível, pode criar uma aplicação Azure Ative Directory com um diretor de segurança para o seu serviço de Pesquisa Cognitiva Azure. Especificamente, uma identidade gerida não é viável nestas condições:

* Não é possível conceder diretamente ao seu serviço de pesquisa permissões de acesso ao cofre Key (por exemplo, se o serviço de pesquisa estiver num inquilino ative diferente do cofre da chave Azure).

* Um único serviço de pesquisa é necessário para hospedar vários mapas de índices encriptados\synonym, cada um usando uma chave diferente de um cofre chave diferente, onde cada cofre-chave deve usar **uma identidade diferente** para a autenticação. Se usar uma identidade diferente para gerir diferentes cofres chave não é um requisito, considere usar a opção de identidade gerida acima.  

Para acomodar tais topologias, a Azure Cognitive Search suporta a utilização de aplicações Azure Ative Directory (AAD) para autenticação entre o seu serviço de pesquisa e o Key Vault.    
Para criar uma aplicação AAD no portal:

1. [Crie uma aplicação no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Obtenha o ID da aplicação e a chave de autenticação,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) uma vez que serão necessários para criar um índice encriptado. Os valores que terá de fornecer incluem **iD de aplicação** e **chave de autenticação.**

>[!Important]
> Ao decidir utilizar uma aplicação de autenticação AAD em vez de uma identidade gerida, considere o facto de que a Azure Cognitive Search não está autorizada a gerir a sua aplicação AAD em seu nome, e cabe-lhe a si gerir a sua aplicação AAD, como a rotação periódica da chave de autenticação da aplicação.
> Ao alterar uma aplicação AAD ou a sua chave de autenticação, qualquer índice de Pesquisa Cognitiva Azure ou mapa de sinónimo que utilize essa aplicação deve ser primeiro atualizado para utilizar a nova iD\chave da aplicação **antes de** eliminar a aplicação anterior ou a sua chave de autorização, e antes de revogar o acesso do Cofre-Chave à sua.
> Se não o fizer, o mapa do índice ou do sinónimo não será utilizável, uma vez que não será capaz de desencriptar o conteúdo assim que o acesso à chave for perdido.   

## <a name="next-steps"></a>Passos seguintes

Se não está familiarizado com a arquitetura de segurança da Azure, reveja a documentação da [Azure Security,](https://docs.microsoft.com/azure/security/)e em particular, este artigo:

> [!div class="nextstepaction"]
> [Encriptação inativa de dados](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
