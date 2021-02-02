---
title: Configure as chaves geridas pelo cliente para a Azure API para fHIR
description: Traga a sua própria funcionalidade de chave suportada na Azure API para FHIR através da Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: ae78aa80594e46b02d77adcafed961e801780d4f
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430264"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Configure as chaves geridas pelo cliente em repouso

Quando cria uma nova API Azure para conta FHIR, os seus dados são encriptados utilizando por padrão as teclas geridas pela Microsoft. Agora, pode adicionar uma segunda camada de encriptação para os dados usando a sua própria chave que você escolhe e gere a si mesmo.

Em Azure, isto é normalmente realizado usando uma chave de encriptação no Cofre de Chaves Azure do cliente. Azure SQL, Azure Storage e Cosmos DB são alguns exemplos que fornecem esta capacidade hoje em dia. A Azure API para fHIR aproveita este apoio da Cosmos DB. Ao criar uma conta, terá a opção de especificar uma chave URI do Cofre de Chaves Azure. Esta chave será transmitida à Cosmos DB quando a conta DB for abastado. Quando um pedido de FHIR é feito, cosmos DB pega na sua chave e usa-a para encriptar/desencriptar os dados. Para começar, pode consultar os seguintes links:

- [Registe o fornecedor de recursos DB da Azure Cosmos para a sua subscrição Azure](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Configure o seu exemplo de Cofre de Chave Azure](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Adicione uma política de acesso ao seu Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Gere uma chave no Cofre da Chave Azure](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="specify-the-azure-key-vault-key"></a>Especificar a chave Azure Key Vault

Ao criar a sua API Azure para conta FHIR no portal Azure, pode ver uma opção de configuração de "Encriptação de Dados" no separador "Definições de Base de Dados" no separador "Definições Adicionais". Por predefinição, a opção chave gerida pelo serviço será escolhida. 

Pode escolher a sua chave no KeyPicker:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="KeyPicker":::

Ou pode especificar a sua chave Azure Key Vault aqui, selecionando a opção "chave gerida pelo cliente". Pode introduzir a chave URI aqui:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Criar Azure API para FHIR":::

Para as contas FHIR existentes, pode ver a escolha de encriptação chave (chave gerida pelo serviço ou gerida pelo cliente) na lâmina "Database" como abaixo. A opção de configuração não pode ser modificada uma vez escolhida. No entanto, pode modificar e atualizar a sua chave.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Base de dados":::

Além disso, pode criar uma nova versão da chave especificada, após a qual os seus dados serão encriptados com a nova versão sem qualquer interrupção de serviço. Também pode remover o acesso à chave para remover o acesso aos dados. Quando a chave estiver desativada, as consultas resultarão num erro. Se a chave for re-activada, as consultas voltarão a ter sucesso.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar chaves geridas pelo cliente em repouso. Em seguida, pode conferir mais uma vez na secção Azure Cosmos DB FAQ: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: como configurar a CMK](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#frequently-asked-questions)
