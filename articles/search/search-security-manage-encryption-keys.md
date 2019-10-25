---
title: Criptografia em repouso usando chaves gerenciadas pelo cliente no Azure Key Vault (versão prévia)
titleSuffix: Azure Cognitive Search
description: Complemente a criptografia do lado do servidor sobre índices e mapas de sinônimos no Azure Pesquisa Cognitiva por meio de chaves que você cria e gerencia no Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 94c9d94edb9a9ca3f6117bd43ab9cefe1dad52a3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794361"
---
# <a name="content-encryption-of-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Criptografia de conteúdo do Azure Pesquisa Cognitiva usando chaves gerenciadas pelo cliente no Azure Key Vault

> [!Note]
> A criptografia com chaves gerenciadas pelo cliente está em versão prévia e não se destina ao uso em produção. A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Você também pode usar o SDK do .NET versão 8,0-Preview.
>
> Esse recurso não está disponível para serviços gratuitos. Você deve usar um serviço de pesquisa Faturável criado em ou após 2019-01-01. Não há suporte ao portal no momento.

Por padrão, o Azure Pesquisa Cognitiva criptografa o conteúdo do usuário em repouso com [chaves gerenciadas pelo serviço](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models). Você pode complementar a criptografia padrão com uma camada de criptografia adicional usando as chaves que você cria e gerencia no Azure Key Vault. Este artigo orienta você pelas etapas.

A criptografia do lado do servidor tem suporte por meio da integração com o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Você pode criar suas próprias chaves de criptografia e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves de criptografia. Com Azure Key Vault, você também pode auditar o uso da chave. 

A criptografia com chaves gerenciadas pelo cliente é configurada no nível de mapa de índice ou sinônimo quando esses objetos são criados e não no nível de serviço de pesquisa. Você não pode criptografar o conteúdo que já existe. 

Você pode usar chaves diferentes de diferentes cofres de chaves. Isso significa que um único serviço de pesquisa pode hospedar vários mapas indexes\synonym criptografados, cada um criptografado potencialmente usando uma chave gerenciada pelo cliente diferente, juntamente com os mapas do indexes\synonym que não são criptografados usando chaves gerenciadas pelo cliente. 

## <a name="prerequisites"></a>Pré-requisitos

Os serviços a seguir são usados neste exemplo. 

+ [Crie um serviço de pesquisa cognitiva do Azure](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este tutorial.

+ [Crie um recurso Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) ou localize um cofre existente em sua assinatura.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) é usado para tarefas de configuração.

+ O [Azure PowerShell](search-create-index-rest-api.md) e o [SDK do Azure pesquisa cognitiva](https://aka.ms/search-sdk-preview) podem ser usados para chamar a API [REST de visualização](search-get-started-postman.md). No momento, não há suporte para Portal ou SDK do .NET para criptografia gerenciada pelo cliente.

## <a name="1---enable-key-recovery"></a>1-Habilitar recuperação de chave

Esta etapa é opcional, mas altamente recomendada. Depois de criar o recurso de Azure Key Vault, habilite a **exclusão reversível** e **Limpe a proteção** no cofre de chaves selecionado executando os seguintes comandos do PowerShell ou do CLI do Azure:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

>[!Note]
> Devido à natureza da criptografia com o recurso de chaves gerenciadas pelo cliente, o Azure Pesquisa Cognitiva não poderá recuperar seus dados se a chave do cofre de chaves do Azure for excluída. Para evitar a perda de dados causada por exclusões de chave Key Vault acidentais, é altamente recomendável habilitar a exclusão reversível e limpar a proteção no cofre de chaves selecionado. Para obter mais informações, consulte [Azure Key Vault reexclusão reversível](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="2---create-a-new-key"></a>2-criar uma nova chave

Se você estiver usando uma chave existente para criptografar o conteúdo de Pesquisa Cognitiva do Azure, ignore esta etapa.

1. [Entre no portal do Azure](https://portal.azure.com) e navegue até o painel do Key Vault.

1. Selecione a configuração **chaves** no painel de navegação à esquerda e clique em **+ gerar/importar**.

1. No painel **criar uma chave** , na lista de **Opções**, escolha o método que você deseja usar para criar uma chave. Você pode **gerar** uma nova chave, **carregar** uma chave existente ou usar **restore backup** para selecionar um backup de uma chave.

1. Insira um **nome** para a chave e, opcionalmente, selecione outras propriedades de chave.

1. Clique no botão **criar** para iniciar a implantação.

Anote o identificador de chave – isso é composto do **URI do valor da chave**, do nome da **chave**e da **versão da chave**. Você precisará delas para definir um índice criptografado no Azure Pesquisa Cognitiva.
 
![Criar uma nova chave do Key Vault](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Criar uma nova chave do Key Vault")

## <a name="3---create-a-service-identity"></a>3-criar uma identidade de serviço

Atribuir uma identidade ao serviço de pesquisa permite que você conceda permissões de acesso de Key Vault ao serviço de pesquisa. Seu serviço de pesquisa usará sua identidade para autenticar com o cofre de chaves do Azure.

O Azure Pesquisa Cognitiva dá suporte a duas maneiras de atribuir a identidade: uma identidade gerenciada ou um aplicativo de Azure Active Directory gerenciado externamente. 

Se possível, use uma identidade gerenciada. É a maneira mais simples de atribuir uma identidade ao serviço de pesquisa e deve funcionar na maioria dos cenários. Se você estiver usando várias chaves para índices e mapas de sinônimos, ou se sua solução estiver em uma arquitetura distribuída que desqualifica a autenticação baseada em identidade, use a [abordagem avançada de Azure Active Directory gerenciada externamente](#aad-app) descrita no final deste artigo.

 Em geral, uma identidade gerenciada permite que o serviço de pesquisa se autentique em Azure Key Vault sem armazenar credenciais no código. O ciclo de vida desse tipo de identidade gerenciada está vinculado ao ciclo de vida do seu serviço de pesquisa, que só pode ter uma identidade gerenciada. [Saiba mais sobre identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Para criar uma identidade gerenciada, [entre no portal do Azure](https://portal.azure.com) e abra o painel do serviço de pesquisa. 

1. Clique em **identidade** no painel de navegação esquerdo, altere seu status para **ativado**e clique em **salvar**.

![Habilitar uma identidade gerenciada](./media/search-enable-msi/enable-identity-portal.png "Habilitar uma identidade gerenciada")

## <a name="4---grant-key-access-permissions"></a>4-conceder permissões de acesso à chave

Para permitir que o serviço de pesquisa Use sua chave de Key Vault, você precisará conceder permissões de acesso específicas a seu serviço de pesquisa.

As permissões de acesso podem ser revogadas em um determinado momento. Depois de revogado, qualquer índice de serviço de pesquisa ou mapa de sinônimos que usa esse cofre de chaves ficará inutilizável. Restaurar as permissões de acesso do cofre de chaves posteriormente irá restaurar o acesso ao mapa do index\synonym. Para obter mais informações, consulte [proteger o acesso a um cofre de chaves](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Entre no portal do Azure](https://portal.azure.com) e abra a página Visão geral do cofre de chaves. 

1. Selecione a configuração **políticas de acesso** no painel de navegação à esquerda e clique em **+ Adicionar nova**.

   ![Adicionar nova política de acesso do cofre de chaves](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Adicionar nova política de acesso do cofre de chaves")

1. Clique em **selecionar entidade de segurança** e selecione o serviço de pesquisa cognitiva do Azure. Você pode procurá-lo pelo nome ou pela ID de objeto que foi exibida depois de habilitar a identidade gerenciada.

   ![Selecionar entidade de segurança de acesso do cofre de chaves](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Selecionar entidade de segurança de acesso do cofre de chaves")

1. Clique em **permissões de chave** e *selecione obter*, *desencapsular chave* e *encapsular chave*. Você pode usar o *Azure data Lake Storage ou o modelo de armazenamento do Azure* para selecionar rapidamente as permissões necessárias.

   O Azure Pesquisa Cognitiva deve ser concedido com as seguintes [permissões de acesso](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Get* – permite que o serviço de pesquisa recupere as partes públicas da sua chave em um Key Vault
   * *Chave de encapsulamento* – permite que o serviço de pesquisa Use sua chave para proteger a chave de criptografia interna
   * *Desencapsular chave* – permite que o serviço de pesquisa Use sua chave para desencapsular a chave de criptografia interna

   ![Selecionar permissões de chave de política de acesso do cofre de chaves](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Selecionar permissões de chave de política de acesso do cofre de chaves")

1. Clique em **OK** e **salve** as alterações da política de acesso.

> [!Important]
> O conteúdo criptografado no Azure Pesquisa Cognitiva está configurado para usar uma chave de Azure Key Vault específica com uma **versão**específica. Se você alterar a chave ou a versão, o mapa de índice ou sinônimo deverá ser atualizado para usar o novo key\version **antes** de excluir o key\version. anterior A falha ao fazer isso tornará o mapa de índice ou sinônimo inutilizável, em você não poderá descriptografar o conteúdo depois que o acesso à chave for perdido.   

## <a name="5---encrypt-content"></a>5-criptografar conteúdo

A criação de um índice ou mapa de sinônimos criptografados com a chave gerenciada pelo cliente ainda não é possível usando portal do Azure. Use a API REST do Azure Pesquisa Cognitiva para criar um mapa de índice ou sinônimo.

O mapa de índice e sinônimo dá suporte a uma nova propriedade **encryptionKey** de nível superior usada para especificar a chave. 

Usando o **URI do Key Vault**, o **nome da chave** e a **versão da chave** da chave do cofre de chaves, podemos criar uma definição de **encryptionKey** :

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
> Nenhum desses detalhes do cofre de chaves são considerados secretos e podem ser recuperados facilmente navegando até a página de chave Azure Key Vault relevante em portal do Azure.

Se você estiver usando um aplicativo do AAD para Key Vault autenticação em vez de usar uma identidade gerenciada, adicione as **credenciais de acesso** do aplicativo do AAD à sua chave de criptografia: 
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

## <a name="example-index-encryption"></a>Exemplo: index Encryption
Os detalhes da criação de um novo índice por meio da API REST podem ser encontrados em [criar índice (API REST do Azure pesquisa cognitiva)](https://docs.microsoft.com/rest/api/searchservice/create-index), em que a única diferença aqui é especificar os detalhes da chave de criptografia como parte da definição do índice: 

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
Agora você pode enviar a solicitação de criação de índice e começar a usar o índice normalmente.

## <a name="example-synonym-map-encryption"></a>Exemplo: criptografia de mapa de sinônimos

Os detalhes da criação de um novo mapa de sinônimos por meio da API REST podem ser encontrados em [criar mapa de sinônimos (API REST do Azure pesquisa cognitiva)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), em que a única diferença aqui é especificar os detalhes da chave de criptografia como parte da definição do mapa de sinônimos: 

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
Agora você pode enviar a solicitação de criação de mapa de sinônimo e começar a usá-la normalmente.

>[!Important] 
> Embora **encryptionKey** não possa ser adicionado a índices de pesquisa cognitiva do Azure existentes ou mapas de sinônimos, ele pode ser atualizado fornecendo valores diferentes para qualquer um dos três detalhes do Key Vault (por exemplo, atualizar a versão da chave). Ao mudar para uma nova chave de Key Vault ou uma nova versão de chave, qualquer índice de Pesquisa Cognitiva do Azure ou mapa de sinônimos que usa a chave deve primeiro ser atualizado para usar o novo key\version **antes** de excluir o key\version. anterior A falha ao fazer isso tornará o mapa de índice ou sinônimo inutilizável, pois ele não poderá descriptografar o conteúdo depois que o acesso à chave for perdido.   
> Restaurar as permissões de acesso do cofre de chaves posteriormente irá restaurar o acesso ao conteúdo.

## <a name="aad-app"></a>Avançado: usar um aplicativo de Azure Active Directory gerenciado externamente

Quando uma identidade gerenciada não é possível, você pode criar um aplicativo Azure Active Directory com uma entidade de segurança para o serviço de Pesquisa Cognitiva do Azure. Especificamente, uma identidade gerenciada não é viável sob estas condições:

* Você não pode conceder permissões de acesso de serviço de pesquisa diretamente para o cofre de chaves (por exemplo, se o serviço de pesquisa estiver em um locatário de Active Directory diferente do Azure Key Vault).

* Um único serviço de pesquisa é necessário para hospedar vários mapas indexes\synonym criptografados, cada um usando uma chave diferente de um cofre de chaves diferente, em que cada cofre de chaves deve usar **uma identidade diferente** para autenticação. Se usar uma identidade diferente para gerenciar diferentes cofres de chaves não for um requisito, considere o uso da opção de identidade gerenciada acima.  

Para acomodar essas topologias, o Azure Pesquisa Cognitiva dá suporte ao uso de aplicativos Azure Active Directory (AAD) para autenticação entre o serviço de pesquisa e o Key Vault.    
Para criar um aplicativo do AAD no Portal:

1. [Crie uma aplicação no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Obtenha a ID do aplicativo e a chave de autenticação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) , pois elas serão necessárias para criar um índice criptografado. Os valores que você precisará fornecer incluem a **ID do aplicativo** e a **chave de autenticação**.

>[!Important]
> Ao decidir usar um aplicativo AAD de autenticação em vez de uma identidade gerenciada, considere o fato de que o Azure Pesquisa Cognitiva não está autorizado a gerenciar seu aplicativo AAD em seu nome, e cabe a você gerenciar seu aplicativo AAD, como periodicamente rotação da chave de autenticação do aplicativo.
> Ao alterar um aplicativo do AAD ou sua chave de autenticação, qualquer índice de Pesquisa Cognitiva do Azure ou mapa de sinônimos que usa esse aplicativo deve primeiro ser atualizado para usar o novo aplicativo ID\key **antes** de excluir o aplicativo anterior ou sua autorização e antes de revogar seu Key Vault acesso a ele.
> A falha ao fazer isso tornará o mapa de índice ou sinônimo inutilizável, pois ele não poderá descriptografar o conteúdo depois que o acesso à chave for perdido.   

## <a name="next-steps"></a>Passos seguintes

Se você não estiver familiarizado com a arquitetura de segurança do Azure, examine a [documentação de segurança do Azure](https://docs.microsoft.com/azure/security/)e, em particular, este artigo:

> [!div class="nextstepaction"]
> [Encriptação inativa de dados](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
