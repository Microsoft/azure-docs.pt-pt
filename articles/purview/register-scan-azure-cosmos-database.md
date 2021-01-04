---
title: Como digitalizar a Base de Dados Azure Cosmos (SQL API)
description: Esta forma de orientar descreve detalhes de como digitalizar a Base de Dados Azure Cosmos (SQL API).
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 1aaeed1973ebd15af312b722ab61938aa4271947
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696251"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Registar e digitalizar a Base de Dados Azure Cosmos (SQL API)

Este artigo descreve como registar uma conta Azure Cosmos Database (SQL API) em Azure Purview e configurar uma digitalização.

## <a name="supported-capabilities"></a>Capacidades suportadas

A Azure Cosmos Database (SQL API) suporta digitalizações completas e incrementais para capturar os metadados e esquemas. As análises também classificam os dados automaticamente com base nas regras de classificação do sistema e personalizados.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registar fontes de dados, crie uma conta Azure Purview. Para obter mais informações sobre a criação de uma conta Desemis, consulte [Quickstart: Criar uma conta Azure Purview](create-catalog-portal.md).
- Você precisa ser um Azure Purview Data Source Admin

## <a name="setting-up-authentication-for-a-scan"></a>Configuração da autenticação para uma varredura

Só há uma forma de configurar a autenticação para a Base de Dados Azure Cosmos (SQL API):

- Chave da conta
 
### <a name="account-key"></a>Chave da conta

Quando o método de autenticação selecionado é **a Chave de Conta,** tem de obter a chave de acesso e armazenar no cofre da chave:

1. Navegue para a sua conta Cosmos DB no portal Azure 
1. Selecione **Chaves de Definições**  >   
1. Copie a sua *chave* e guarde-a em algum lugar para os próximos passos
1. Navegue até ao cofre da chave
1. Selecione **Definições > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** e **Valor** como *chave* da sua conta de armazenamento
1. Selecione **Criar** para completar
1. Se o cofre da chave ainda não estiver ligado ao Purview, terá de [criar uma nova ligação](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) ao cofre de chaves
1. Finalmente, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando a chave para configurar o seu scan

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Registe uma conta Azure Cosmos Database (SQL API)

Para registar uma nova conta Azure Cosmos Database (SQL API) no seu catálogo de dados, faça o seguinte:

1. Navegue para a sua conta Desemis
1. Selecione **Fontes** na navegação à esquerda
1. Selecione **Registar-se**
1. Nas **fontes de registo**, selecione **Azure Cosmos DB (SQL API)**
1. Selecione **Continuar**

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="registar nova fonte de dados" border="true":::

No ecrã **'Azure Cosmos DB') (Azure Cosmos DB),** faça o seguinte:

1. Introduza um **Nome** que a fonte de dados será listada no Catálogo.
1. Escolha como pretende apontar para a sua conta de armazenamento desejada:
   1. **Selecione A partir da subscrição Azure**, selecione a subscrição apropriada da caixa de entrega de **assinaturas Azure** e a conta cosmosDB apropriada da caixa de entrega do **nome da conta Cosmos DB.**
   1. Ou, pode selecionar **Entrar manualmente** e introduzir um ponto final de serviço (URL).
1. **Termine** para registar a fonte de dados.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="registar opções de fontes" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passos seguintes

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)
