---
title: Como digitalizar o Azure Data Explorer
description: Esta forma de orientar descreve detalhes de como digitalizar o Azure Data Explorer.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 7adc7f568fb82692f2c96f610575076e397bd99c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896117"
---
# <a name="register-and-scan-azure-data-explorer"></a>Registar e digitalizar Azure Data Explorer

Este artigo descreve como registar uma conta Azure Data Explorer em Azure Purview e configurar uma digitalização.

## <a name="supported-capabilities"></a>Capacidades suportadas

O Azure Data Explorer suporta digitalizações completas e incrementais para capturar os metadados e o esquema. As análises também classificam os dados automaticamente com base nas regras de classificação do sistema e personalizados.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registar fontes de dados, crie uma conta Azure Purview. Para obter mais informações sobre a criação de uma conta Desemis, consulte [Quickstart: Criar uma conta Azure Purview](create-catalog-portal.md).
- Você precisa ser um Azure Purview Data Source Admin

## <a name="setting-up-authentication-for-a-scan"></a>Configuração da autenticação para uma varredura

Só há uma forma de configurar a autenticação para o explorador de dados Azure:

- Principal de Serviço

### <a name="service-principal"></a>Service principal (Principal de serviço)

Para utilizar a autenticação principal do serviço para digitalizações, pode utilizar uma existente ou criar uma nova. 

> [!Note]
> Se tiver de criar um novo Diretor de Serviços, siga estes passos:
> 1. Navegue até ao [portal Azure.](https://portal.azure.com)
> 1. Selecione **Azure Ative Directory** a partir do menu do lado esquerdo.
> 1. Selecione **Registos de aplicações**.
> 1. Selecione **+ Novo registo de inscrição.**
> 1. Introduza um nome para a **inscrição** (nome principal do serviço).
> 1. Selecione **Contas apenas neste diretório organizacional**.
> 1. Para redirecionar uri selecione **Web** e introduza qualquer URL que pretenda; não tem que ser real ou trabalho.
> 1. Em seguida, selecione **Registar**.

É necessário obter a iD de aplicação do diretor de serviço e segredo:

1. Navegue para o seu Diretor de Serviço no [portal Azure](https://portal.azure.com)
1. Copie os valores que o ID da **Aplicação (cliente)** do **Resumo** e **do Segredo** do Cliente a partir de **Certificados & segredos.**
1. Navegue até ao cofre da chave
1. Selecione **Definições > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** da sua escolha e **Valor** como segredo do **Cliente** do seu Diretor de Serviço
1. Selecione **Criar** para completar
1. Se o cofre da chave ainda não estiver ligado ao Purview, terá de [criar uma nova ligação](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) ao cofre de chaves
1. Finalmente, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando o Principal de Serviço para configurar a sua digitalização

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>Conceder ao Service Principal acesso à sua instância de explorador de dados Azure

1. Navegue para o portal do Azure. Em seguida, navegue para o seu exemplo de explorador de dados Azure.

1. Adicione o principal de serviço à função **AllDatabasesViewer** no separador **Permissões,** como mostrado na imagem seguinte.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="Screenshot para adicionar principal de serviço em permissões" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Registar uma conta Azure Data Explorer

Para registar uma nova conta Azure Data Explorer (Kusto) no seu catálogo de dados, faça o seguinte:

1. Navegue para a sua conta Desemis
1. Selecione **Fontes** na navegação à esquerda
1. Selecione **Registar-se**
1. Nas **fontes de registo**, selecione **Azure Data Explorer**
1. Selecione **Continuar**

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="registar nova fonte de dados" border="true":::

No ecrã **'Registar' (Azure Data Explorer(Kusto),,** faça o seguinte:

1. Introduza um **Nome** que a fonte de dados será listada no Catálogo.
1. Escolha como pretende apontar para a sua conta de armazenamento desejada:
   1. **Selecione A partir da subscrição Azure**, selecione a subscrição apropriada da caixa de entrega de assinatura **Azure** e o cluster apropriado da caixa de entrega do **Cluster.**
   1. Ou, pode selecionar **Entrar manualmente** e introduzir um ponto final de serviço (URL).
1. **Termine** para registar a fonte de dados.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="registar opções de fontes" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans-azure-data-explorer.md)]

## <a name="next-steps"></a>Passos seguintes

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)
