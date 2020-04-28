---
title: Use o Cofre chave para armazenar e aceder às chaves Azure Cosmos DB
description: Utilize o Cofre chave Azure para armazenar e aceder à cadeia de ligação Azure Cosmos DB, chaves, pontos finais.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 225221635f978e3d70cec4ce7e9d78d6b100b4fd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80618763"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Proteger as chaves do Azure Cosmos com o Azure Key Vault 

>[!IMPORTANT]
> A solução recomendada para aceder às teclas Azure Cosmos DB é utilizar uma identidade gerida atribuída pelo [sistema.](managed-identity-based-authentication.md) Se o seu serviço não puder tirar partido das identidades geridas, utilize a [solução baseada em cert](certificate-based-authentication.md). Se tanto a solução de identidade gerida como a solução baseada em cert não satisfazem as suas necessidades, por favor, utilize a solução de cofre chave abaixo.

Ao utilizar o Azure Cosmos DB para as suas aplicações, pode aceder à base de dados, coleções, documentos utilizando o ponto final e a chave dentro do ficheiro de configuração da aplicação.  No entanto, não é seguro colocar chaves e URL diretamente no código de aplicação porque estão disponíveis em formato de texto claro para todos os utilizadores. Vai querer garantir que o ponto final e as chaves estão disponíveis, mas através de um mecanismo protegido. É aqui que o Azure Key Vault o pode ajudar a armazenar e gerir, de forma segura, os segredos das aplicações.

Os seguintes passos são necessários para armazenar e ler chaves de acesso Azure Cosmos DB a partir do Cofre Chave:

* Criar um Key Vault  
* Adicione as chaves de acesso Azure Cosmos DB ao Cofre chave  
* Criar uma aplicação web Azure  
* Registe o pedido & conceder permissões para ler o Cofre chave  


## <a name="create-a-key-vault"></a>Criar um Key Vault

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).  
2. Selecione **Criar um recurso > Cofre chave > de segurança**.  
3. Na secção **Criar cofre de chaves**, forneça as seguintes informações:  
   * **Nome:** Forneça um nome único para o seu Cofre chave.  
   * **Subscrição:** Escolha a subscrição que irá utilizar.  
   * Em **Grupo de Recursos**, selecione **Criar novo** e introduza um nome de grupo de recursos.  
   * No menu pendente Localização, selecione uma localização.  
   * Deixe outras opções para os seus incumprimentos.  
4. Depois de fornecer as informações acima, selecione **Criar**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Adicione as chaves de acesso Azure Cosmos DB ao Cofre chave.
1. Navegue para o Cofre chave que criou no passo anterior, abra o separador **Segredos.**  
2. Selecione **+Generate/Import,** 

   * Selecione **Manual** para **opções de upload**.
   * Forneça um **nome** para o seu segredo
   * Forneça a cadeia de ligação da sua conta Cosmos DB no campo **Valor.** E, em seguida, **selecione Criar**.

   ![Criar um segredo](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Depois de criado o segredo, abra-o e copie o Identificador Secreto que se encontra no seguinte formato. Utilizará este identificador na secção seguinte. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Criar uma aplicação web Azure

1. Crie uma aplicação web Azure ou pode descarregar a aplicação a partir do [repositório GitHub](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). É uma aplicação mvc simples.  

2. Desinserta a aplicação descarregada e abra o ficheiro **HomeController.cs.** Atualize o ID secreto na seguinte linha:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Guarde** o ficheiro, **construa** a solução.  
4. Em seguida, implemente a aplicação para Azure. Clique no projeto e escolha **publicar**. Crie um novo perfil de serviço de aplicações (pode nomear a aplicação WebAppKeyVault1) e selecionar **Publicar**.   

5. Uma vez que a aplicação é implantada. A partir do portal Azure, navegue até à aplicação web que implementou e ligue a identidade de **serviço gerida** desta aplicação.  

   ![Identidade de serviço gerida](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Se executar a aplicação agora, verá o seguinte erro, uma vez que não autorizou esta aplicação no Key Vault.

![App implantada sem acesso](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Registe o pedido & conceder permissões para ler o Cofre chave

Nesta secção, regista o pedido com o Diretório Ativo Azure e autoriza a aplicação para ler o Cofre chave. 

1. Navegue para o portal Azure, abra o **Cofre chave** que criou na secção anterior.  

2. **Políticas de Acesso**Aberto , selecione **+Adicionar Nova** encontrar a aplicação web que implementou, selecionar permissões e selecionar **OK**.  

   ![Adicionar política de acesso](./media/access-secrets-from-keyvault/add-access-policy.png)

Se executar a aplicação, pode ler o segredo do Cofre chave.

![App implantada com segredo](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Da mesma forma, pode adicionar um utilizador para aceder à chave Vault. Você precisa adicionar-se ao Key Vault selecionando Políticas de **Acesso** e, em seguida, conceder todas as permissões que você precisa para executar a aplicação a partir do estúdio Visual. Quando esta aplicação está a funcionar a partir do seu ambiente de trabalho, leva a sua identidade.

## <a name="next-steps"></a>Passos seguintes

* Para configurar uma firewall para Azure Cosmos DB consulte o artigo de suporte de [firewall.](firewall-support.md)
* Para configurar o ponto final do serviço de rede virtual, consulte o acesso seguro utilizando o artigo final do [serviço VNet.](vnet-service-endpoint.md)
