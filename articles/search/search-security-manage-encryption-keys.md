---
title: Encriptação em repouso com chaves geridas pelo cliente no Azure Key Vault - Azure Search
description: Encriptação do lado do servidor de suplemento ao longo de índices e mapas de sinónimos no Azure Search através de chaves que criar e gerir no Azure Key Vault.
author: NatiNimni
manager: jlembicz
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: 987b56a9571fd50f605dbe6fb4112ef857021530
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029179"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Encriptação de pesquisa do Azure com chaves geridas pelo cliente no Azure Key Vault

Por predefinição, o Azure Search encripta conteúdo de utilizador em repouso com [chaves geridas pelo serviço](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest#data-encryption-models). Pode complementar a encriptação de padrão com uma camada de encriptação adicionais com chaves que criar e gerir no Azure Key Vault. Este artigo orienta-o através dos passos.

A encriptação do lado do servidor é suportada através da integração com [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Pode criar suas próprias chaves de encriptação e armazená-las num cofre de chaves, ou pode usar APIs do Azure Key Vault para gerar chaves de encriptação. Com o Azure Key Vault, também é possível auditar a utilização da chave. 

Encriptação com chaves geridas pelo cliente é configurada no nível de mapa índice ou sinónimo quando esses objetos são criados e não no nível de serviço de pesquisa. Não é possível encriptar o conteúdo que já existe. 

Pode utilizar chaves diferentes de cofres de chave diferentes. Isso significa que um serviço de pesquisa único pode alojar vários mapas de indexes\synonym encriptados, cada encriptados potencialmente com uma chave diferente gerida pelo cliente, juntamente com os mapas de indexes\synonym que não estão encriptados utilizando chaves geridas pelo cliente. 

>[!Note]
> **Disponibilidade da funcionalidade**: Encriptação com chaves geridas pelo cliente é uma funcionalidade de pré-visualização que não está disponível para serviços gratuitos. Em serviços pagos, só está disponível para os serviços de pesquisa criados em ou após 2019-01-01, usando a api-version mais recente pré-visualização (api-version = 2019-05-06-pré-visualização). Atualmente, não há nenhum suporte do portal para esta funcionalidade.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços são utilizados neste exemplo. 

[Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este tutorial.

[Criar um recurso do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) ou encontrar um cofre existente na sua subscrição.

[O Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) é utilizado para tarefas de configuração.

[Postman](search-fiddler.md), [Azure PowerShell](search-create-index-rest-api.md) e [Azure Search SDK](https://aka.ms/search-sdk-preview) podem ser utilizados para chamar a REST API de pré-visualização. Não existe nenhum portal ou o suporte do SDK de .NET para a encriptação gerida pelo cliente neste momento.

## <a name="1---enable-key-recovery"></a>1 - ativar a recuperação de chaves

Este passo é opcional mas altamente recomendado. Depois de criar o recurso do Azure Key Vault, ative **eliminação de forma recuperável** e **remover proteção** no Cofre de chaves selecionado executando os seguintes comandos do PowerShell ou CLI do Azure:   

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
> Devido à natureza da criptografia com recurso de chaves geridas pelo cliente, a Azure Search não será capaz de obter os seus dados se a sua chave de Cofre de chave do Azure é eliminada. Para evitar a perda de dados causada por acidentais eliminações de chave de Cofre de chaves, é altamente recomendável que ativar a eliminação de forma recuperável e remover proteção no Cofre de chaves selecionado. Para obter mais informações, consulte [eliminação de forma recuperável do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="2---create-a-new-key"></a>2 - criar uma nova chave

Se estiver a utilizar uma chave existente para encriptar o conteúdo do Azure Search, ignore este passo.

1. [Inicie sessão no portal do Azure](https://portal.azure.com) e navegue para o dashboard do Cofre de chaves.

1. Selecione o **chaves** definição no painel de navegação esquerdo e clique em **+ gerar/importar**.

1. Na **crie uma chave** painel, na lista de **opções**, escolha o método que pretende utilizar para criar uma chave. Pode **gerar** uma nova chave **carregar** existente de chaves ou utilizar **restaurar cópia de segurança** para selecionar uma cópia de segurança de uma chave.

1. Introduza um **nome** para a sua chave e, opcionalmente, selecione as outras propriedades chave.

1. Clique nas **criar** botão para iniciar a implementação.

Anote o identificador de chave – isto é composto pela **valor Uri da chave**, o **nome da chave**e o **versão da chave**. Estes são necessários para definir um índice encriptado no Azure Search.
 
![Criar uma nova chave de Cofre de chaves](./media/search-manage-encryption-keys/create-new-key-vault-key.png "criar uma nova chave de Cofre de chaves")

## <a name="3---create-a-service-identity"></a>3 - criar uma identidade de serviço

Atribuir uma identidade para o serviço de pesquisa permite-lhe conceder permissões de acesso do Key Vault para o serviço de pesquisa. O serviço de pesquisa irá utilizar a sua identidade para autenticar com o Azure Key vault.

O Azure Search suporta duas formas para atribuir identidade: uma identidade gerida ou uma aplicação do Azure Active Directory gerenciado externamente. 

Se possível, utilize uma identidade gerida. É a forma mais simples de atribuição de uma identidade para o serviço de pesquisa e deve funcionar na maioria dos cenários. Se estiver a utilizar várias chaves para índices e mapas de sinónimos, ou se a sua solução está numa arquitetura distribuída que disqualifies autenticação baseada em identidades, utilizar a avançada [gerenciado externamente abordagem do Azure Active Directory](#aad-app)descrito no final deste artigo.

 Em geral, uma identidade gerida permite que o serviço de pesquisa autenticar para o Azure Key Vault sem armazenar credenciais no código. O ciclo de vida desse tipo de identidade gerida está associado ao ciclo de vida do seu serviço de pesquisa, que só pode ter uma identidade gerida. [Saiba mais sobre as identidades de gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Para criar uma identidade gerida, [iniciar sessão no portal de toAzure](https://portal.azure.com) e abra o dashboard de serviço de pesquisa. 

1. Clique em **identidade** no painel de navegação à esquerda, alterar seu status para **no**e clique em **guardar**.

![Ativar uma identidade gerida](./media/search-enable-msi/enable-identity-portal.png "ativar uma identidade gerida")

## <a name="4---grant-key-access-permissions"></a>4 - conceder permissões de acesso a chaves

Para ativar o serviço de pesquisa utilizar a sua chave de Cofre de chaves, terá de conceder à pesquisa determinadas permissões de acesso de serviço.

Permissões de acesso podem ser revogadas a qualquer momento. Depois de revogado, qualquer mapa de índice ou sinónimo de serviço de pesquisa que utiliza o Cofre de chaves ficará inutilizável. Restaurar as permissões de acesso do Cofre de chave numa altura posterior irá restaurar o acesso de mapa de index\synonym. Para obter mais informações, consulte [proteger o acesso a um cofre de chaves](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Inicie sessão no portal do Azure](https://portal.azure.com) e abra sua página de descrição geral do Cofre de chaves. 

1. Selecione o **políticas de acesso** definição no painel de navegação esquerdo e clique em **+ Adicionar nova**.

   ![Adicionar nova política de acesso do Cofre de chaves](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Adicionar nova política de acesso do Cofre de chaves")

1. Clique em **principal selecione** e selecione o serviço Azure Search. Pode procurá-lo por nome ou o ID de objeto que foi apresentado depois de ativar a identidade gerida.

   ![Principal de política de acesso do Cofre de chaves selecione](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "principal de política de acesso do Cofre de chaves selecione")

1. Clique em **permissões da chave** e selecione *obter*, *anular moldagem de chave* e *moldar chave*. Pode utilizar o *armazenamento do Azure Data Lake ou de armazenamento do Azure* modelo para selecionar rapidamente as permissões necessárias.

   O Azure search tem de ser concedido com o seguinte procedimento [permissões de acesso](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Obter* -permite que o serviço de pesquisa recuperar as partes públicas da sua chave no Cofre de chaves
   * *Moldar chave* -permite que o serviço de pesquisa utilizar a sua chave para proteger a chave de encriptação interno
   * *Anular a moldagem de chave* -permite que o serviço de pesquisa utilizar a sua chave para anular a moldagem da chave de encriptação interno

   ![Selecione as permissões de chave de política de acesso de Cofre de chaves](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "selecionar permissões de chave de política de acesso de Cofre de chaves")

1. Clique em **OK** e **guardar** as alterações de política de acesso.

> [!Important]
> Conteúdos encriptados no Azure search está configurado para utilizar uma chave específica do Azure Key Vault com uma específica **versão**. Se alterar a chave ou a versão, o mapa de índice ou de sinónimos tem de ser atualizado para utilizar o novo key\version **antes de** a eliminar o key\version anterior. Caso contrário, irá renderizar o índice ou sinónimo mapear inutilizável, em que não será capaz de descriptografar o conteúdo depois do acesso de chave é perdido.   

## <a name="5---encrypt-content"></a>5 - encriptar o conteúdo

Criar um mapa de índice ou sinónimo encriptado com chaves geridas pelo cliente ainda não foi possível através do portal do Azure. Utilize as API de REST do Azure Search para criar tal um mapa de índice ou sinónimos.

Suporte uma nova em nível superior de mapa de índice e sinónimos **encryptionKey** utilizada para especificar a chave de propriedade. 

Utilizar o **Cofre de chaves Uri**, **nome da chave** e o **versão da chave** da sua chave de Cofre de chave, podemos criar um **encryptionKey** definição:

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
> Nenhum desses detalhes do Cofre de chaves são considerados segredo e poderia ser facilmente obtido ao navegar para a página de chave de Cofre de chaves do Azure relevante no portal do Azure.

Se estiver a utilizar uma aplicação AAD para a autenticação do Key Vault em vez de utilizar uma identidade gerida, adicionar a aplicação do AAD **aceder a credenciais** à sua chave de encriptação: 
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

## <a name="example-index-encryption"></a>Exemplo: Encriptação de índice
Os detalhes da criação de um novo índice através da API REST podem ser encontrados em [criar índice (API do REST de serviço de pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/create-index), em que a única diferença aqui é especificar os detalhes da chave de encriptação como parte da definição de índice: 

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
Pode agora enviar o pedido de criação do índice e, em seguida, começar a utilizar o índice normalmente.

## <a name="example-synonym-map-encryption"></a>Exemplo: Encriptação de mapa de sinónimos

Os detalhes da criação de um novo mapa de sinónimos através da API REST que podem ser encontrados em [mapa de sinónimos de criar (API do REST de serviço de pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), em que a única diferença aqui é especificar os detalhes da chave de encriptação como parte da definição de mapa de sinónimos: 

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
Pode agora enviar o pedido de criação de mapa de sinónimos e, em seguida, começar a usá-lo normalmente.

>[!Important] 
> Embora **encryptionKey** não é possível adicionar a índices de pesquisa do Azure existentes ou mapeia sinónimo, ele pode ser atualizado, fornecendo valores diferentes para qualquer um dos três detalhes do Cofre de chaves (por exemplo, a atualizar a versão da chave). Quando mudar para uma nova chave do Key Vault ou uma versão nova, qualquer mapa de índice ou sinónimos do Azure Search que usa a chave tem primeiro de ser atualizado para utilizar o novo key\version **antes de** a eliminar o key\version anterior. Caso contrário, irá renderizar o índice ou mapa de sinónimos inutilizável, pois não será capaz de descriptografar o acesso ao conteúdo de uma vez chave é perdido.   
> Restaurar as permissões de acesso do Cofre de chave numa altura posterior irá restaurar o acesso ao conteúdo.

## <a name="aad-app"></a> Avançado: Utilize uma aplicação externamente gerida do Azure Active Directory

Quando uma identidade gerida não for possível, que pode criar uma aplicação do Azure Active Directory com uma segurança principal para o serviço Azure Search. Especificamente, uma identidade gerida não é viável sob estas condições:

* Diretamente não é possível conceder a pesquisa as permissões de acesso de serviço para o Cofre de chaves (por exemplo, se o serviço de pesquisa está no inquilino do Active Directory diferente que o Azure Key Vault).

* É necessário para hospedar vários mapas de indexes\synonym encriptados, cada um com uma chave diferente de um cofre de chaves diferente, onde cada Cofre de chaves tem de utilizar um serviço de pesquisa único **uma identidade diferente** para autenticação. Se utilizar uma identidade diferente para gerir cofres de chave diferentes não é um requisito, considere utilizar a opção de identidade gerida acima.  

Para acomodar tais topologias, o Azure pesquise suporta a utilização de aplicações do Azure Active Directory (AAD) para a autenticação entre o serviço de pesquisa e o Key Vault.    
Para criar uma aplicação do AAD no portal do:

1. [Crie uma aplicação no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Obter a chave de autenticação e o ID de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) como esses serão necessários para criar um índice encriptado. Terá de fornecer os valores incluem **ID da aplicação** e **chave de autenticação**.

>[!Important]
> Ao decidir utilizar uma aplicação do AAD de autenticação em vez de uma identidade gerida, considere o fato de que o Azure Search não está autorizado a gerir a sua aplicação do AAD em seu nome, e cabe-lhe gerir a sua aplicação do AAD, por exemplo, rotação periódica de a chave de autenticação da aplicação.
> Ao alterar uma aplicação do AAD ou a chave de autenticação, qualquer mapa de índice ou sinónimos do Azure Search que usa esse aplicativo tem primeiro de ser atualizado para utilizar a nova aplicação ID\key **antes de** eliminar a aplicação anterior ou seu autorização de chave e antes de revogar o acesso do Cofre de chaves ao mesmo.
> Caso contrário, irá renderizar o índice ou mapa de sinónimos inutilizável, pois não será capaz de descriptografar o acesso ao conteúdo de uma vez chave é perdido.   

## <a name="next-steps"></a>Passos Seguintes

Se não estiver familiarizado com a arquitetura de segurança do Azure, reveja os [documentação de segurança do Azure](https://docs.microsoft.com/azure/security/)e, em particular, este artigo:

> [!div class="nextstepaction"]
> [Encriptação inativa de dados](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
