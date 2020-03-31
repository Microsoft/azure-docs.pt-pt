---
title: Encriptação em repouso usando chaves geridas pelo cliente
titleSuffix: Azure Cognitive Search
description: Complemente a encriptação do lado do servidor sobre índices e mapas de sinónimo em Pesquisa Cognitiva Azure usando chaves que cria e gere no Cofre chave Azure.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899941"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Encriptação-no-resto de conteúdo em Pesquisa Cognitiva Azure usando chaves geridas pelo cliente em Cofre chave Azure

Por padrão, a Pesquisa Cognitiva Azure encripta o conteúdo indexado em repouso com [chaves geridas](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models)pelo serviço . Pode complementar a encriptação padrão com uma camada de encriptação adicional utilizando chaves que cria e gere no Cofre de Chaves Azure. Este artigo acompanha-o através dos degraus.

A encriptação do lado do servidor é suportada através da integração com [o Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Pode criar as suas próprias chaves de encriptação e armazená-las num cofre de chaves, ou pode usar as APIs do Azure Key Vault para gerar chaves de encriptação. Com o Azure Key Vault, também pode auditar o uso da chave. 

A encriptação com chaves geridas pelo cliente é configurada ao nível do mapa de índice ou sinónimo quando esses objetos são criados, e não no nível do serviço de pesquisa. Não é possível encriptar conteúdos que já existem. 

As chaves não precisam de estar todas no mesmo cofre. Um único serviço de pesquisa pode alojar vários índices encriptados ou mapas de sinónimo cada um encriptado com as suas próprias chaves de encriptação geridas pelo cliente armazenadas em diferentes Cofres chave.  Também pode ter índices e mapas de sinónimo no mesmo serviço que não são encriptados usando chaves geridas pelo cliente. 

> [!IMPORTANT] 
> Esta funcionalidade está disponível na [versão REST API 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) e [na versão 8.0 do SDK .NET .](search-dotnet-sdk-migration-version-9.md) Atualmente não existe suporte para configurar chaves de encriptação geridas pelo cliente no portal Azure. O serviço de pesquisa deve ser criado depois de janeiro de 2019 e não pode ser um serviço Gratuito (partilhado).

## <a name="prerequisites"></a>Pré-requisitos

Neste exemplo, são utilizados os seguintes serviços. 

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. 

+ [Crie um recurso Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) ou encontre um cofre existente sob a sua subscrição.

+ [O Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou [o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) são utilizados para tarefas de configuração.

+ [O Carteiro,](search-get-started-postman.md) [o Azure PowerShell](search-create-index-rest-api.md) e o [Azure Cognitive Search SDK](https://aka.ms/search-sdk-preview) podem ser usados para chamar a API REST. Não existe nenhum suporte de portal para encriptação gerida pelo cliente neste momento.

>[!Note]
> Devido à natureza da encriptação com a funcionalidade de chaves gerida pelo cliente, a Pesquisa Cognitiva Azure não será capaz de recuperar os seus dados se a chave do cofre da Chave Azure for eliminada. Para evitar a perda de dados causada por eliminações acidentais da chave do cofre de chaves, **deve** ativar a proteção soft delete e purga no cofre da chave antes de poder ser utilizada. Para mais informações, consulte [o Cofre chave Azure .](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)   

## <a name="1---enable-key-recovery"></a>1 - Permitir a recuperação da chave

Depois de criar o recurso Azure Key Vault, ative **a proteção soft delete** e **purga** no cofre chave selecionado executando os seguintes comandos PowerShell ou Azure CLI:   

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

Se estiver a utilizar uma chave existente para encriptar o conteúdo da Pesquisa Cognitiva Azure, ignore este passo.

1. [Inscreva-se no portal Azure](https://portal.azure.com) e navegue para o painel de instrumentos do cofre da chave.

1. Selecione a definição de **Teclas** a partir do painel de navegação esquerdo e clique **em + Gerar/Importar**.

1. No **Painel Criar um painel chave,** a partir da lista de **Opções,** escolha o método que pretende utilizar para criar uma chave. Pode **gerar** uma nova tecla, **carregar** uma chave existente ou utilizar a **Reserva de Apoio restaurar** para selecionar uma cópia de segurança de uma chave.

1. Introduza um **Nome** para a sua chave e selecione opcionalmente outras propriedades chave.

1. Clique no botão **Criar** para iniciar a implementação.

Tome nota do Identificador chave – este é composto pelo **valor-chave Uri,** o **nome chave**, e a **versão chave**. Você precisará destes para definir um índice encriptado em Pesquisa Cognitiva Azure.
 
![Crie uma nova chave do cofre](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Crie uma nova chave do cofre")

## <a name="3---create-a-service-identity"></a>3 - Criar uma identidade de serviço

Atribuir uma identidade ao seu serviço de pesquisa permite-lhe conceder permissões de acesso ao Key Vault ao seu serviço de pesquisa. O seu serviço de pesquisa usará a sua identidade para autenticar com cofre Azure Key.

A Azure Cognitive Search suporta duas formas de atribuir identidade: uma identidade gerida ou uma aplicação de Diretório Ativo Azure gerida externamente. 

Se possível, use uma identidade gerida. É a forma mais simples de atribuir uma identidade ao seu serviço de pesquisa e deve funcionar na maioria dos cenários. Se estiver a usar várias teclas para índices e mapas de sinónimo, ou se a sua solução estiver numa arquitetura distribuída que desqualifica a autenticação baseada na identidade, utilize a abordagem avançada do [Azure Ative Directory, gerida externamente,](#aad-app) descrita no final deste artigo.

 Em geral, uma identidade gerida permite ao seu serviço de pesquisa autenticar o Cofre chave Azure sem armazenar credenciais em código. O ciclo de vida deste tipo de identidade gerida está ligado ao ciclo de vida do seu serviço de pesquisa, que só pode ter uma identidade gerida. [Saiba mais sobre identidades geridas.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

1. Para criar uma identidade gerida, [inscreva-se no portal DoAzure](https://portal.azure.com) e abra o seu painel de serviço de pesquisa. 

1. Clique em **Identidade** no painel de navegação esquerdo, mude o seu estado para **On**, e clique em **Guardar**.

![Ativar uma identidade gerida](./media/search-enable-msi/enable-identity-portal.png "Permitir uma identidade manhosa")

## <a name="4---grant-key-access-permissions"></a>4 - Conceder permissões de acesso chave

Para permitir que o seu serviço de pesquisa utilize a sua chave Key Vault, terá de conceder ao seu serviço de pesquisa determinadas permissões de acesso.

As permissões de acesso podem ser revogadas a qualquer momento. Uma vez revogado, qualquer índice de serviço de pesquisa ou mapa de sinónimo que use o cofre chave tornar-se-á inutilizável. Restaurar as permissões de acesso ao cofre chave mais tarde irá restaurar o acesso ao mapa index\synonym. Para mais informações, consulte [O acesso seguro a um cofre chave](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Inscreva-se no portal Azure](https://portal.azure.com) e abra a sua página de visão geral do cofre. 

1. Selecione a definição das políticas de **Acesso** a partir do painel de navegação esquerdo e clique em **+Adicionar novo**.

   ![Adicione nova política de acesso ao cofre chave](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Adicione nova política de acesso ao cofre chave")

1. Clique em **Selecionar o principal** e selecione o seu serviço de Pesquisa Cognitiva Azure. Pode procurá-lo pelo nome ou pelo ID do objeto que foi apresentado após permitir a identidade gerida.

   ![Selecione principal da política de acesso ao cofre chave](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Selecione principal da política de acesso ao cofre chave")

1. Clique em **permissões chave** e selecione *Obter,* *desembrulhar a chave* e a chave de *embrulho*. Pode utilizar o modelo *azure Data Lake Storage ou Azure Storage* para selecionar rapidamente as permissões necessárias.

   A Pesquisa Cognitiva Azure deve ser concedida com as [seguintes permissões](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)de acesso:

   * *Get* - permite que o seu serviço de pesquisa recupere as partes públicas da sua chave num Cofre chave
   * *Chave de embrulho* - permite que o seu serviço de pesquisa use a sua chave para proteger a chave de encriptação interna
   * *Chave de desembrulhar* - permite que o seu serviço de pesquisa use a chave para desembrulhar a chave de encriptação interna

   ![Selecione permissões de chave de acesso ao cofre chave](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Selecione permissões de chave de acesso ao cofre chave")

1. Clique em **OK** e **Guarde** as alterações da política de acesso.

> [!Important]
> O conteúdo encriptado em Azure Cognitive Search está configurado para usar uma chave azure key vault específica com uma **versão**específica . Se alterar a chave ou versão, o mapa de indexação ou sinónimo deve ser atualizado para utilizar a nova versão **chave\antes** de apagar a versão chave anterior\. Se não o fizer, o mapa de indexação ou sinónimo será inutilizável, pois não será capaz de desencriptar o conteúdo assim que o acesso chave for perdido.   

## <a name="5---encrypt-content"></a>5 - Encriptar conteúdo

A criação de um mapa de indexação ou sinónimo encriptado com a chave gerida pelo cliente ainda não é possível utilizando o portal Azure. Utilize a API de pesquisa cognitiva azure para criar um mapa de tal índice ou sinónimo.

Tanto o mapa de indexação como o mapa de sinónimo suportam uma nova propriedade **de encriptação** de topoKey usada para especificar a chave. 

Utilizando o **cofre de chaves Uri**, nome **chave** e a **versão chave** da sua chave de cofre chave, podemos criar uma definição **de encriptaçãoChave:**

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
> Nenhum destes detalhes do cofre chave é considerado secreto e poderia ser facilmente recuperado navegando para a página chave do Cofre chave Azure relevante no portal Azure.

Se estiver a utilizar uma aplicação AAD para autenticação key vault em vez de utilizar uma identidade gerida, adicione as **credenciais** de acesso à aplicação AAD à sua chave de encriptação: 
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
Os detalhes da criação de um novo índice através da API REST podem ser encontrados no [Create Index (Azure Cognitive Search REST API),](https://docs.microsoft.com/rest/api/searchservice/create-index)onde a única diferença aqui é especificar os detalhes chave de encriptação como parte da definição de índice: 

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
Agora pode enviar o pedido de criação de índices e, em seguida, começar a usar o índice normalmente.

## <a name="example-synonym-map-encryption"></a>Exemplo: Encriptação do mapa do sinónimo

Os detalhes da criação de um novo mapa de sinónimo através da API REST podem ser encontrados no [Mapa Create Synonym (Azure Cognitive Search REST API),](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)onde a única diferença aqui é especificar os detalhes da chave de encriptação como parte da definição do mapa do sinónimo: 

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
Agora pode enviar o pedido de criação de mapas sinónimoe e, em seguida, começar a usá-lo normalmente.

>[!Important] 
> Embora a **encriptaçãoKey** não possa ser adicionada aos índices de pesquisa cognitiva do Azure existentes ou mapas de sinónimo, pode ser atualizado fornecendo valores diferentes para qualquer um dos três detalhes chave do cofre (por exemplo, atualizando a versão chave). Ao mudar para uma nova chave Key Vault ou uma nova versão chave, qualquer índice de pesquisa cognitiva azure ou mapa de sinónimo que utilize a chave deve primeiro ser atualizado para usar a nova versão **chave\antes** de apagar a versão chave anterior\. Se não o fizer, o mapa de indexação ou sinónimo será inutilizável, uma vez que não será capaz de desencriptar o conteúdo assim que o acesso chave for perdido.   
> Restaurar as permissões de acesso ao cofre chave mais tarde irá restaurar o acesso ao conteúdo.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>Avançado: Utilize uma aplicação de Diretório Ativo Azure gerida externamente

Quando uma identidade gerida não é possível, pode criar uma aplicação azure Ative Direy com um diretor de segurança para o seu serviço de Pesquisa Cognitiva Azure. Especificamente, uma identidade gerida não é viável nestas condições:

* Não é possível conceder diretamente permissões de acesso ao seu serviço de pesquisa para o cofre Key (por exemplo, se o serviço de pesquisa estiver num inquilino de Diretório Ativo diferente do Cofre chave Azure).

* Um único serviço de pesquisa é necessário para alojar vários índices encriptados\synonym maps, cada um usando uma chave diferente de um cofre chave diferente, onde cada cofre chave deve usar **uma identidade diferente** para autenticação. Se utilizar uma identidade diferente para gerir diferentes cofres chave não é um requisito, considere usar a opção de identidade gerida acima.  

Para acomodar tais topologas, a Azure Cognitive Search suporta a utilização de aplicações de Diretório Ativo Azure (AAD) para autenticação entre o seu serviço de pesquisa e o Key Vault.    
Para criar uma aplicação AAD no portal:

1. [Crie uma aplicação no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Obtenha o ID da aplicação e a chave de autenticação,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) uma vez que estes serão necessários para criar um índice encriptado. Os valores que terá de fornecer incluem **id de aplicação** e **chave de autenticação.**

>[!Important]
> Ao decidir utilizar uma aplicação AAD de autenticação em vez de uma identidade gerida, considere que a Pesquisa Cognitiva Azure não está autorizada a gerir a sua aplicação AAD em seu nome, e cabe-lhe a si gerir a sua aplicação AAD, como periódica rotação da chave de autenticação da aplicação.
> Ao alterar uma aplicação AAD ou a sua chave de autenticação, qualquer índice de pesquisa cognitiva Azure ou mapa de sinónimoque utilize essa aplicação deve primeiro ser atualizado para utilizar a nova chave ID\de aplicação **antes** de apagar a aplicação anterior ou a sua chave de autorização, e antes de revogar o seu acesso ao Cofre Chave.
> Se não o fizer, o mapa de indexação ou sinónimo será inutilizável, uma vez que não será capaz de desencriptar o conteúdo assim que o acesso chave for perdido.   

## <a name="next-steps"></a>Passos seguintes

Se não estiver familiarizado com a arquitetura de segurança Azure, reveja a documentação da [Segurança Azure,](https://docs.microsoft.com/azure/security/)e em particular, este artigo:

> [!div class="nextstepaction"]
> [Encriptação de dados em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
