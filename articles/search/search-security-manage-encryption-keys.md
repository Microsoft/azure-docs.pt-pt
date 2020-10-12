---
title: Encriptação em repouso utilizando chaves geridas pelo cliente
titleSuffix: Azure Cognitive Search
description: Complemente a encriptação do lado do servidor sobre índices e mapas de sinónimo na Pesquisa Cognitiva Azure utilizando as teclas que cria e gere no Cofre da Chave Azure.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: 2dc7458dd905ff84455927c81b4ea93765d4f5cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88928824"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Configure as chaves geridas pelo cliente para encriptação de dados na Pesquisa Cognitiva Azure

A Azure Cognitive Search encripta automaticamente o conteúdo indexado em repouso com [as teclas geridas pelo serviço](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Se for necessária mais proteção, pode complementar a encriptação padrão com uma camada de encriptação adicional utilizando as teclas que cria e gere no Cofre da Chave Azure. Este artigo acompanha-o através dos passos da configuração da encriptação CMK.

A encriptação CMK depende do [Cofre da Chave Azure](../key-vault/general/overview.md). Pode criar as suas próprias chaves de encriptação e armazená-las num cofre de chaves, ou pode utilizar os APIs do Azure Key Vault para gerar chaves de encriptação. Com o Azure Key Vault, também pode auditar a utilização da chave se [ativar o registo](../key-vault/general/logging.md).  

A encriptação com teclas geridas pelo cliente é aplicada a índices individuais ou mapas de sinónimo quando esses objetos são criados, e não é especificado no próprio nível de serviço de pesquisa. Apenas novos objetos podem ser encriptados. Não é possível encriptar conteúdo que já existe.

As chaves não precisam de estar todas no mesmo cofre. Um único serviço de pesquisa pode hospedar vários índices encriptados ou mapas de sinónimo, cada um encriptado com as suas próprias chaves de encriptação geridas pelo cliente, armazenados em diferentes cofres-chave. Também pode ter índices e mapas de sinónimo no mesmo serviço que não são encriptados usando chaves geridas pelo cliente. 

## <a name="double-encryption"></a>Encriptação dupla

Para os serviços criados após 1 de agosto de 2020 e em regiões específicas, o âmbito da encriptação CMK inclui discos temporários, alcançando [a dupla encriptação completa,](search-security-overview.md#double-encryption)atualmente disponível nestas regiões: 

+ E.U.A. Oeste 2
+ E.U.A. Leste
+ E.U.A. Centro-Sul
+ US Gov - Virginia
+ US Gov - Arizona

Se estiver a utilizar uma região diferente, ou um serviço criado antes de 1 de agosto, então a encriptação CMK está limitada apenas ao disco de dados, excluindo os discos temporários utilizados pelo serviço.

## <a name="prerequisites"></a>Pré-requisitos

Neste exemplo são utilizados os seguintes serviços e serviços. 

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

+ [Crie um recurso Azure Key Vault](../key-vault/secrets/quick-create-portal.md#create-a-vault) ou encontre um cofre existente na mesma subscrição que a Azure Cognitive Search. Esta funcionalidade tem um requisito de subscrição igual.

+ [Azure PowerShell](/powershell/azure/) ou [Azure CLI](/cli/azure/install-azure-cli) são utilizados para tarefas de configuração.

+ [Postman](search-get-started-postman.md), [Azure PowerShell](./search-get-started-powershell.md) e [.NET SDK podem](https://aka.ms/search-sdk-preview) ser usados para chamar a API REST que cria índices e mapas de sinónimo que incluem um parâmetro chave de encriptação. Não existe suporte ao portal para adicionar uma chave para índices ou mapas de sinónimo neste momento.

>[!Note]
> Devido à natureza da encriptação com chaves geridas pelo cliente, a Azure Cognitive Search não será capaz de recuperar os seus dados se a chave do cofre da Chave Azure for eliminada. Para evitar a perda de dados causada por supressões acidentais das chaves do Cofre, deve ser ativada a proteção contra a eliminação e purga no cofre da chave. A eliminação suave é ativada por padrão, pelo que só encontrará problemas se o desativar propositadamente. A proteção de purga não é ativada por padrão, mas é necessária para encriptação CMK de pesquisa cognitiva Azure. Para obter mais informações, consulte [as vistas](../key-vault/general/soft-delete-overview.md) gerais da proteção para eliminar e [purgar.](../key-vault/general/soft-delete-overview.md#purge-protection)

## <a name="1---enable-key-recovery"></a>1 - Permitir a recuperação das chaves

O cofre da chave deve ter **proteção de eliminação** e **purga** ativada. Pode definir essas funcionalidades utilizando o portal ou os seguintes comandos PowerShell ou Azure CLI.

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

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - Criar uma nova chave

Se estiver a utilizar uma chave existente para encriptar o conteúdo da Pesquisa Cognitiva do Azure, ignore este passo.

1. [Inscreva-se no portal Azure](https://portal.azure.com) e abra a página geral do cofre da chave.

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

 Em geral, uma identidade gerida permite que o seu serviço de pesquisa autente para a Azure Key Vault sem armazenar credenciais em código. O ciclo de vida deste tipo de identidade gerida está ligado ao ciclo de vida do seu serviço de pesquisa, que só pode ter uma identidade gerida. [Saiba mais sobre identidades geridas.](../active-directory/managed-identities-azure-resources/overview.md)

1. [Inscreva-se no portal Azure](https://portal.azure.com) e abra a página geral do seu serviço de pesquisa. 

1. Clique em **Identidade** no painel de navegação esquerdo, altere o seu estado para **On**e clique em **Guardar**.

![Ativar uma identidade gerida](./media/search-enable-msi/enable-identity-portal.png "Ativar uma identidade manged")

## <a name="4---grant-key-access-permissions"></a>4 - Conceder permissões de acesso à chave

Para ativar o seu serviço de pesquisa para usar a sua chave Key Vault, terá de conceder ao seu serviço de pesquisa determinadas permissões de acesso.

As permissões de acesso podem ser revogadas a qualquer momento. Uma vez revogado, qualquer índice de serviço de pesquisa ou mapa de sinónimo que utilize o cofre chave tornar-se-á inutilizável. Restaurar as permissões de acesso ao cofre do cofre de chaves mais tarde irá restaurar o acesso ao mapa index\synonym. Para obter mais informações, consulte [acesso seguro a um cofre de chaves.](../key-vault/general/secure-your-key-vault.md)

1. [Inscreva-se no portal Azure](https://portal.azure.com) e abra a página geral do cofre da chave. 

1. Selecione as **políticas de Acesso** a partir do painel de navegação à esquerda e clique + Adicionar **novo**.

   ![Adicione nova política de acesso ao cofre de chaves](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Adicione nova política de acesso ao cofre de chaves")

1. Clique **em Selecionar o principal** e selecione o seu serviço de Pesquisa Cognitiva Azure. Pode pesquisar pelo nome ou pelo ID do objeto que foi apresentado depois de permitir a identidade gerida.

   ![Selecione o principal da política de acesso ao cofre](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Selecione o principal da política de acesso ao cofre")

1. Clique em **permissões chave** e selecione *Get*, *Desembrulhar a chave* e embrulhar *a tecla*. Pode utilizar o modelo *de armazenamento do Lago de Dados Azure ou Azure Para* selecionar rapidamente as permissões necessárias.

   A Azure Cognitive Search deve ser concedida com as [seguintes permissões de acesso:](../key-vault/keys/about-keys.md#key-operations)

   * *Get* - permite que o seu serviço de pesquisa recupere as partes públicas da sua chave num Cofre de Chaves
   * *Wrap Key* - permite que o seu serviço de pesquisa utilize a sua chave para proteger a chave de encriptação interna
   * *Desembrulhar-se -* permite que o seu serviço de pesquisa utilize a sua chave para desembrulhar a chave de encriptação interna

   ![Selecione permissões de chave de acesso ao cofre](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Selecione permissões de chave de acesso ao cofre")

1. Para **permissões secretas**, selecione *Get*.

1. Para **permissões de certificados**, selecione *Obter*.

1. Clique **em OK** e **guarde** as alterações da política de acesso.

> [!Important]
> O conteúdo encriptado em Azure Cognitive Search está configurado para utilizar uma chave Azure Key Vault específica com uma **versão**específica . Se alterar a chave ou versão, o mapa de índice ou sinónimo deve ser atualizado para utilizar a nova versão\chave **antes** de eliminar a chave anterior\versão. Se não o fizer, o índice ou o mapa de sinónimos não serão utilizáveis, pois não conseguirá desencriptar o conteúdo assim que o acesso à chave for perdido.   

## <a name="5---encrypt-content"></a>5 - Encriptar conteúdo

Para adicionar uma chave gerida pelo cliente num mapa de índice ou sinónimo, deve utilizar a [API search REST](/rest/api/searchservice/) ou um SDK. O portal não expõe mapas de sinónimo ou propriedades de encriptação. Quando utiliza uma API válida, tanto os índices como os mapas de sinónimo suportam uma propriedade **encriptação** de nível superior. 

Utilizando o **cofre-chave Uri,** **o nome chave** e a versão **chave** da chave do cofre key, crie uma definição **encriptaçãoKey** da seguinte forma:

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
Os detalhes da criação de um novo índice através da API REST podem ser encontrados no [Create Index (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index), onde a única diferença aqui é especificar os detalhes chave de encriptação como parte da definição de índice: 

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

Os detalhes da criação de um novo mapa de sinónimos através da API REST podem ser encontrados no [Create Synonym Map (Azure Cognitive Search REST API),](/rest/api/searchservice/create-synonym-map)onde a única diferença aqui é especificar os detalhes chave de encriptação como parte da definição do mapa de sinónimo: 

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

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a> Avançado: Utilize uma aplicação de Diretório Ativo Azure gerida externamente

Quando uma identidade gerida não é possível, pode criar uma aplicação Azure Ative Directory com um diretor de segurança para o seu serviço de Pesquisa Cognitiva Azure. Especificamente, uma identidade gerida não é viável nestas condições:

* Não é possível conceder diretamente ao seu serviço de pesquisa permissões de acesso ao cofre Key (por exemplo, se o serviço de pesquisa estiver num inquilino ative diferente do cofre da chave Azure).

* Um único serviço de pesquisa é necessário para hospedar vários mapas de índices encriptados\synonym, cada um usando uma chave diferente de um cofre chave diferente, onde cada cofre-chave deve usar **uma identidade diferente** para a autenticação. Se usar uma identidade diferente para gerir diferentes cofres chave não é um requisito, considere usar a opção de identidade gerida acima.  

Para acomodar tais topologias, a Azure Cognitive Search suporta a utilização de aplicações Azure Ative Directory (AAD) para autenticação entre o seu serviço de pesquisa e o Key Vault.    
Para criar uma aplicação AAD no portal:

1. [Crie uma aplicação no Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md).

1. [Obtenha o ID da aplicação e a chave de autenticação,](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) uma vez que serão necessários para criar um índice encriptado. Os valores que terá de fornecer incluem **iD de aplicação** e **chave de autenticação.**

>[!Important]
> Ao decidir utilizar uma aplicação de autenticação AAD em vez de uma identidade gerida, considere o facto de que a Azure Cognitive Search não está autorizada a gerir a sua aplicação AAD em seu nome, e cabe-lhe a si gerir a sua aplicação AAD, como a rotação periódica da chave de autenticação da aplicação.
> Ao alterar uma aplicação AAD ou a sua chave de autenticação, qualquer índice de Pesquisa Cognitiva Azure ou mapa de sinónimo que utilize essa aplicação deve ser primeiro atualizado para utilizar a nova iD\chave da aplicação **antes de** eliminar a aplicação anterior ou a sua chave de autorização, e antes de revogar o acesso do Cofre-Chave à sua.
> Se não o fizer, o mapa do índice ou do sinónimo não será utilizável, uma vez que não será capaz de desencriptar o conteúdo assim que o acesso à chave for perdido.

## <a name="work-with-encrypted-content"></a>Trabalhar com conteúdo encriptado

Com a encriptação CMK, irá notar latência tanto para indexar como para consultas devido ao trabalho extra de encriptação/desencriptação. A Azure Cognitive Search não regista a atividade de encriptação, mas pode monitorizar o acesso à chave através do registo do cofre de chaves. Recomendamos que [ative o registo como](../key-vault/general/logging.md) parte da instalação do cofre de chaves.

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