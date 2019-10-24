---
title: Usar Key Vault para armazenar e acessar chaves de Azure Cosmos DB
description: Use Azure Key Vault para armazenar e acessar Azure Cosmos DB cadeia de conexão, chaves, pontos de extremidade.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 55e6bbc338c1ac6f9ef935b4a3a05c32f2b5e9f5
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755214"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Proteger as chaves Cosmos do Azure usando Azure Key Vault 

Ao usar Azure Cosmos DB para seus aplicativos, você pode acessar o banco de dados, coleções, documentos usando o ponto de extremidade e a chave dentro do arquivo de configuração do aplicativo.  No entanto, não é seguro colocar as chaves e a URL diretamente no código do aplicativo porque elas estão disponíveis no formato de texto não criptografado para todos os usuários. Você deseja certificar-se de que o ponto de extremidade e as chaves estão disponíveis, mas por meio de um mecanismo protegido. É aí que Azure Key Vault pode ajudá-lo a armazenar e gerenciar segredos de aplicativo com segurança.

As etapas a seguir são necessárias para armazenar e ler chaves de acesso Azure Cosmos DB de Key Vault:

* Criar um Key Vault  
* Adicionar chaves de acesso de Azure Cosmos DB ao Key Vault  
* Criar um aplicativo Web do Azure  
* Registrar o aplicativo & conceder permissões para ler o Key Vault  


## <a name="create-a-key-vault"></a>Criar um Key Vault

1. Entre no [portal do Azure](https://portal.azure.com/).  
2. Selecione **criar um recurso > segurança > Key Vault**.  
3. Na secção **Criar cofre de chaves**, forneça as seguintes informações:  
   * **Nome:** Forneça um nome exclusivo para seu Key Vault.  
   * **Assinatura:** Escolha a assinatura que será usada.  
   * Em **Grupo de Recursos**, selecione **Criar novo** e introduza um nome de grupo de recursos.  
   * No menu suspenso local, escolha um local.  
   * Deixe outras opções para seus padrões.  
4. Depois de fornecer as informações acima, selecione **Criar**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Adicione chaves de acesso de Azure Cosmos DB à Key Vault.
1. Navegue até o Key Vault criado na etapa anterior, abra a guia **segredos** .  
2. Selecione **+ gerar/importar**, 

   * Selecione **manual** para **Opções de upload**.
   * Forneça um **nome** para seu segredo
   * Forneça a cadeia de conexão de sua conta de Cosmos DB para o campo **valor** . E, em seguida, selecione **criar**.

   ![Criar um segredo](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Depois que o segredo for criado, abra-o e copie o identificador de segredo * * que está no formato a seguir. Você usará esse identificador na próxima seção. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Criar um aplicativo Web do Azure

1. Crie um aplicativo Web do Azure ou baixe o aplicativo do [repositório GitHub](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). É um aplicativo MVC simples.  

2. Descompacte o aplicativo baixado e abra o arquivo **HomeController.cs** . Atualize a ID do segredo na seguinte linha:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Salve** o arquivo, **compile** a solução.  
4. Em seguida, implante o aplicativo no Azure. Clique com o botão direito do mouse em projeto e escolha **publicar**. Criar um novo perfil de serviço de aplicativo (você pode nomear o aplicativo WebAppKeyVault1) e selecionar **publicar**.   

5. Depois que o aplicativo for implantado. No portal do Azure, navegue até o aplicativo Web que você implantou e ative a **identidade de serviço gerenciada** desse aplicativo.  

   ![Identidade de serviço gerenciada](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Se você executar o aplicativo agora, verá o erro a seguir, pois não recebeu nenhuma permissão para esse aplicativo no Key Vault.

![Aplicativo implantado sem acesso](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Registrar o aplicativo & conceder permissões para ler o Key Vault

Nesta seção, você registra o aplicativo com Azure Active Directory e concede permissões para que o aplicativo Leia o Key Vault. 

1. Navegue até a portal do Azure, abra o **Key Vault** que você criou na seção anterior.  

2. Abra **as políticas de acesso**, selecione **+ Adicionar novo** localizar o aplicativo Web que você implantou, selecione permissões e selecione **OK**.  

   ![Adicionar política de acesso](./media/access-secrets-from-keyvault/add-access-policy.png)

Agora, se você executar o aplicativo, poderá ler o segredo em Key Vault.

![Aplicativo implantado com segredo](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Da mesma forma, você pode adicionar um usuário para acessar o cofre de chaves. Você precisa se adicionar à Key Vault selecionando políticas de **acesso** e, em seguida, conceder todas as permissões necessárias para executar o aplicativo do Visual Studio. Quando esse aplicativo é executado em sua área de trabalho, ele usa sua identidade.

## <a name="next-steps"></a>Passos seguintes

* Para configurar um firewall para Azure Cosmos DB consulte o artigo de [suporte do firewall](firewall-support.md) .
* Para configurar o ponto de extremidade de serviço de rede virtual, consulte o artigo [acesso seguro usando ponto de extremidade de serviço de VNet](vnet-service-endpoint.md) .
