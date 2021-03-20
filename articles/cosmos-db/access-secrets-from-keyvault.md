---
title: Use o Cofre-Chave para armazenar e aceder às teclas DB do Azure Cosmos
description: Utilize o Cofre da Chave Azure para armazenar e aceder à cadeia de ligação DB Azure Cosmos, chaves, pontos finais.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: a9bea0664f99a21ac734de666c802e9875ff00b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359330"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Proteger as chaves do Azure Cosmos com o Azure Key Vault 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

>[!IMPORTANT]
> A solução recomendada para aceder às teclas DB do Azure Cosmos é utilizar uma [identidade gerida atribuída pelo sistema](managed-identity-based-authentication.md). Se o seu serviço não puder tirar partido das identidades geridas, utilize a [solução baseada em cert](certificate-based-authentication.md). Se a solução de identidade gerida e a solução baseada em cert não satisfaçam as suas necessidades, utilize a solução de cofre de tecla abaixo.

Ao utilizar o Azure Cosmos DB para as suas aplicações, pode aceder à base de dados, coleções, documentos utilizando o ponto final e a chave dentro do ficheiro de configuração da aplicação.  No entanto, não é seguro colocar chaves e URL diretamente no código de aplicação, uma vez que estão disponíveis em formato de texto claro para todos os utilizadores. Vai querer garantir que o ponto final e as chaves estão disponíveis, mas através de um mecanismo protegido. É aqui que o Azure Key Vault o pode ajudar a armazenar e gerir, de forma segura, os segredos das aplicações.

São necessários os seguintes passos para armazenar e ler as teclas de acesso DB da Azure Cosmos do Key Vault:

* Criar um Key Vault  
* Adicione chaves de acesso DB Azure Cosmos ao Cofre de Chaves  
* Criar uma aplicação web Azure  
* Registe o pedido & conceder permissões para ler o Cofre-Chave  


## <a name="create-a-key-vault"></a>Criar um Key Vault

1. Inscreva-se no [portal Azure](https://portal.azure.com/).  
2. Selecione **Criar um > de segurança > cofre de chaves**.  
3. Na secção **Criar cofre de chaves**, forneça as seguintes informações:  
   * **Nome:** Forneça um nome único para o seu Cofre de Chaves.  
   * **Assinatura:** Escolha a subscrição que vai utilizar.  
   * Em **Grupo de Recursos**, selecione **Criar novo** e introduza um nome de grupo de recursos.  
   * No menu pendente Localização, selecione uma localização.  
   * Deixe outras opções para os seus defeitos.  
4. Depois de fornecer as informações acima, selecione **Criar**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Adicione as chaves de acesso DB Azure Cosmos ao Cofre de Chaves.
1. Navegue para o Cofre chave que criou no passo anterior, abra o **separador Segredos.**  
2. Selecione **+Gerar/Importar,** 

   * Selecione **Manual** para **as opções de upload**.
   * Forneça um **nome** para o seu segredo
   * Forneça a cadeia de ligação da sua conta Cosmos DB no campo **Valor.** Em seguida, selecione **Criar**.

   :::image type="content" source="./media/access-secrets-from-keyvault/create-a-secret.png" alt-text="Criar um segredo":::

4. Após a criação do segredo, abra-o e copie o Identificador Secreto que está no seguinte formato. Usará este identificador na próxima secção. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Criar uma aplicação web Azure

1. Crie uma aplicação web Azure ou pode descarregar a aplicação a partir do [repositório GitHub.](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/Demo/keyvaultdemo) É uma aplicação MVC simples.  

2. Desaperte a aplicação descarregada e abra o ficheiro **homeController.cs.** Atualize o ID secreto na seguinte linha:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Guarde** o ficheiro, **construa** a solução.  
4. Em seguida, implemente a aplicação para Azure. Clique no projeto e escolha **publicar.** Crie um novo perfil de serviço de aplicações (pode nomear a aplicação WebAppKeyVault1) e selecione **Publish**.   

5. Uma vez que a aplicação é implementada. A partir do portal Azure, navegue para a web app que implementou, e ligue a identidade de **serviço gerido** desta aplicação.  

   :::image type="content" source="./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png" alt-text="Identidade de serviço gerida":::

Se executar a aplicação agora, verá o seguinte erro, uma vez que não deu qualquer permissão a esta aplicação no Key Vault.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-without-access.png" alt-text="App implementada sem acesso":::

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Registe o pedido & conceder permissões para ler o Cofre-Chave

Nesta secção, registe o pedido com o Azure Ative Directory e dá permissões para o pedido de leitura do Cofre-Chave. 

1. Navegue até ao portal Azure, abra o **Cofre-Chave** que criou na secção anterior.  

2. Políticas **de Acesso Aberto**, selecione **+Add New** find the web app que implementou, selecione permissões e selecione **OK**.  

   :::image type="content" source="./media/access-secrets-from-keyvault/add-access-policy.png" alt-text="Adicionar política de acesso":::

Agora, se executar o formulário, pode ler o segredo de Key Vault.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-with-access.png" alt-text="App implementada com segredo":::
 
Da mesma forma, pode adicionar um utilizador para aceder ao cofre da chave. Tem de se adicionar ao Cofre-Chave selecionando **As Políticas** de Acesso e, em seguida, conceder todas as permissões necessárias para executar a aplicação a partir do estúdio Visual. Quando esta aplicação está a funcionar a partir do seu ambiente de trabalho, retira a sua identidade.

## <a name="next-steps"></a>Passos seguintes

* Para configurar uma firewall para Azure Cosmos DB ver artigo [de suporte de firewall.](how-to-configure-firewall.md)
* Para configurar o ponto final do serviço de rede virtual, consulte o acesso seguro utilizando o artigo [de ponto final do serviço VNet.](how-to-configure-vnet-service-endpoint.md)
