---
title: Utilizar o Key Vault para armazenar e aceder a chaves do Azure Cosmos DB
description: Utilize o Azure Key Vault para armazenar e aceder a cadeia de ligação do Azure Cosmos DB, as chaves, pontos finais.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 157ccd284c25cb5c7275aa942823ade2a40795cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239859"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Chaves do Azure Cosmos seguras com o Azure Key Vault 

Ao utilizar o Azure Cosmos DB para as suas aplicações, pode acessar o banco de dados, coleções, documentos, utilizando o ponto final e a chave no ficheiro de configuração da aplicação.  No entanto, não é seguro colocar chaves e o URL diretamente no código da aplicação, porque eles estão disponíveis no formato de texto não encriptado para todos os utilizadores. Pretende certificar-se de que o ponto final e as chaves são disponíveis, mas através de um mecanismo seguro. Isso é que o Azure Key Vault pode ajudar-lhe armazenar e gerir segredos da aplicação de forma segura.

Os seguintes passos são necessários para armazenar e ler as chaves de acesso do Azure Cosmos DB a partir do Key Vault:

* Criar um Key Vault  
* Adicionar chaves de acesso do Azure Cosmos DB para o Key Vault  
* Criar uma aplicação web do Azure  
* Registar a aplicação & conceder permissões para ler o Key Vault  


## <a name="create-a-key-vault"></a>Criar um Key Vault

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).  
2. Selecione **criar um recurso > Segurança > Key Vault**.  
3. Na secção **Criar cofre de chaves**, forneça as seguintes informações:  
   * **Nome:** Forneça um nome exclusivo para o seu Cofre de chaves.  
   * **Subscrição:** Escolha a subscrição que irá utilizar.  
   * Em **Grupo de Recursos**, selecione **Criar novo** e introduza um nome de grupo de recursos.  
   * No menu pendente de localização, escolha uma localização.  
   * Deixe as outras opções com os valores predefinidos.  
4. Depois de fornecer as informações acima, selecione **Criar**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Adicione chaves de acesso do Azure Cosmos DB para o Key Vault.
1. Navegue para o Cofre de chaves que criou no passo anterior, abra a **segredos** separador.  
2. Selecione **+ gerar/importar**, 

   * Selecione **Manual** para **opções de carregamento**.
   * Fornecer um **nome** para seu segredo
   * Fornecer a cadeia de ligação da sua conta do Cosmos DB para o **valor** campo. E, em seguida, selecione **criar**.

   ![Criar um segredo](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Depois do segredo é criado, abra-o e copie o * * identificador de segredo que é o seguinte formato. Irá utilizar este identificador na próxima seção. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Criar uma aplicação web do Azure

1. Criar uma aplicação web do Azure ou pode transferir a aplicação a partir da [repositório do GitHub](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). É uma simple aplicação de MVC.  

2. Deszipe o aplicativo baixado e abra o **HomeController.cs** ficheiro. Atualize o ID secreto na linha a seguir:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Guarde** o arquivo **criar** a solução.  
4. Em seguida, implemente a aplicação no Azure. Clique com o botão direito do rato no projeto e escolha **publicar**. Criar um novo perfil de serviço de aplicações (pode nomear a aplicação WebAppKeyVault1) e selecione **publicar**.   

5. Assim que a aplicação é implementada. A partir do portal do Azure, navegue para a aplicação web que implementou e ativar a **identidade do serviço gerido** desta aplicação.  

   ![Identidade do serviço gerido](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Se quiser executar o aplicativo agora, verá o seguinte erro, como qualquer permissão para esta aplicação não tiver dado no Cofre de chaves.

![Aplicação implementada sem acesso](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Registar a aplicação & conceder permissões para ler o Key Vault

Nesta secção, registar a aplicação no Azure Active Directory e conceder permissões para a aplicação leia o Key Vault. 

1. Navegue para o portal do Azure, abra a **Key Vault** que criou na secção anterior.  

2. Open **políticas de acesso**, selecione **+ adicionar novo** encontrar a aplicação web que implementou, selecione as permissões e selecione **OK**.  

   ![Adicionar política de acesso](./media/access-secrets-from-keyvault/add-access-policy.png)

Agora, se executar o aplicativo, é possível ler o segredo do Key Vault.

![Aplicação implementada com o segredo](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Da mesma forma, pode adicionar um utilizador aceder à chave do cofre. Terá de adicionar a próprio para o Key Vault, selecionando **políticas de acesso** e, em seguida, conceder todas as permissões que precisa executar a aplicação a partir do Visual studio. Quando esta aplicação está em execução no ambiente de trabalho, que demora a sua identidade.

## <a name="next-steps"></a>Passos Seguintes

* Configurar uma firewall para Azure Cosmos DB, veja [suporte de firewall](firewall-support.md) artigo.
* Para configurar o ponto final de serviço de rede virtual, consulte [proteger o acesso ao utilizar o ponto final de serviço de VNet](vnet-service-endpoint.md) artigo.
