---
title: Configure as chaves geridas pelo cliente para a Azure API para fHIR
description: Traga a sua própria funcionalidade de chave suportada na Azure API para FHIR através da Cosmos DB
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 05c208ba3c9005d38b8924037748764f8d112e3a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398186"
---
# <a name="configure-customer-managed-keys"></a>Configurar chaves geridas pelo cliente

Quando cria uma nova API Azure para conta FHIR, os seus dados são encriptados utilizando por padrão as teclas geridas pela Microsoft. Agora, pode adicionar uma segunda camada de encriptação para os dados usando a sua própria chave que você escolhe e gere a si mesmo.

Em Azure, isto é normalmente realizado usando uma chave de encriptação no Cofre de Chaves Azure (AKV) do cliente. Azure SQL, Azure Storage e Cosmos DB são alguns exemplos que fornecem esta capacidade hoje em dia. A Azure API para fHIR aproveita este apoio da Cosmos DB. Quando criar uma conta, terá a opção de especificar uma chave AKV URI. Passaremos esta chave para cosmos DB quando a conta DB for abastado. Quando um pedido de FHIR é feito, cosmos DB pega na sua chave e usa-a para encriptar/desencriptar os dados. Para começar, pode consultar os seguintes links:

- [Registe o fornecedor de recursos DB da Azure Cosmos para a sua subscrição Azure](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Configure a sua instância AKV](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
-  [Adicione uma política de acesso à sua instância AKV](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Gerar uma chave em AKV](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

Depois de criar a sua API Azure para conta FHIR no portal Azure, pode ver uma opção de configuração de "Encriptação de Dados" no separador "Definições de base" no separador "Definições Adicionais". Por predefinição, a opção chave gerida pelo serviço será escolhida. Pode especificar a sua chave AKV aqui selecionando a opção "chave gerida pelo cliente". Pode introduzir aqui a chave copiada URI.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Criar Azure API para FHIR":::

Ou, pode escolher a sua chave no KeyPicker:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="KeyPicker":::

Para as contas FHIR existentes, pode ver a escolha de encriptação chave (chave gerida pelo serviço ou gerida pelo cliente) na lâmina "Database" como abaixo. A opção de configuração não pode ser modificada uma vez escolhida. No entanto, pode modificar e atualizar a sua chave.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Base de dados":::

Além disso, pode criar uma nova versão da chave especificada, após a qual os seus dados serão encriptados com a nova versão sem qualquer interrupção de serviço. Também pode remover o acesso à chave para remover o acesso aos dados.