---
title: Como digitalizar a bolha de armazenamento Azure
description: Saiba como digitalizar o armazenamento de blob Azure no seu catálogo de dados Azure Purview.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: b27b46c68d018d2ddf79d284b20cc05b51640891
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880646"
---
# <a name="register-and-scan-azure-blob-storage"></a>Registar e analisar o Armazenamento de Blobs do Azure

Este artigo descreve como registar uma conta de Armazenamento Azure Blob em Purview e configurar uma digitalização.

## <a name="supported-capabilities"></a>Capacidades suportadas

O Azure Blob Storage suporta digitalizações completas e incrementais para capturar os metadados e o esquema. Também classifica os dados automaticamente com base nas regras de classificação do sistema e personalizados.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registar fontes de dados, crie uma conta Azure Purview. Para obter mais informações sobre a criação de uma conta Desemis, consulte [Quickstart: Criar uma conta Azure Purview](create-catalog-portal.md).
- Você precisa ser um Azure Purview Data Source Admin

## <a name="setting-up-authentication-for-a-scan"></a>Configuração da autenticação para uma varredura

Existem três formas de configurar a autenticação para o armazenamento de bolhas Azure:

- Identidade Gerida
- Chave de conta
- Principal de Serviço

### <a name="managed-identity-recommended"></a>Identidade gerida (Recomendada)

Ao escolher **Identidade Gerida,** para configurar a ligação, tem primeiro de dar à sua conta Purview a permissão para digitalizar a fonte de dados:

1. Navegue até à sua conta de armazenamento.
1. Selecione **Access Control (IAM)** do menu de navegação à esquerda. 
1. Selecione **+ Adicionar**.
1. Descreva a **Função** para **Storage Blob Data Reader** e introduza o nome da sua conta Azure Purview na caixa de entrada **Select.** Em seguida, **selecione Guardar** para dar esta atribuição de função à sua conta Purview.

> [!Note]
> Para mais detalhes, consulte os passos no [acesso autorizado a bolhas e filas usando o Azure Ative Directory](../storage/common/storage-auth-aad.md)

### <a name="account-key"></a>Chave de conta

Quando o método de autenticação selecionado é **a Chave de Conta,** tem de obter a chave de acesso e armazenar no cofre da chave:

1. Navegue para a sua conta de armazenamento
1. Selecione **Definições > Teclas de acesso**
1. Copie a sua *chave* e guarde-a em algum lugar para os próximos passos
1. Navegue até ao cofre da chave
1. Selecione **Definições > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** e **Valor** como *chave* da sua conta de armazenamento
1. Selecione **Criar** para completar
1. Se o cofre da chave ainda não estiver ligado ao Purview, terá de [criar uma nova ligação](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) ao cofre de chaves
1. Finalmente, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando a chave para configurar o seu scan

### <a name="service-principal"></a>Service principal (Principal de serviço)

Para utilizar um principal de serviço, pode utilizar um existente ou criar um novo. 

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

#### <a name="granting-the-service-principal-access-to-your-blob-storage"></a>Concedendo ao Chefe de Serviço acesso ao seu armazenamento de bolhas

1. Navegue até à sua conta de armazenamento.
1. Selecione **Access Control (IAM)** do menu de navegação à esquerda. 
1. Selecione **+ Adicionar**.
1. Descreva a **Função** para **Storage Blob Data Reader** e introduza o nome principal do seu serviço ou iD do objeto na caixa de entrada **Select.** Em seguida, **selecione Save** para dar esta atribuição de função ao seu principal de serviço.

## <a name="firewall-settings"></a>Definições de firewall

> [!NOTE]
> Se tiver firewall ativada para a conta de armazenamento, deve utilizar o método de autenticação **de identidade gerida** ao configurar uma verificação.

1. Entre na sua conta de armazenamento no [portal Azure](https://portal.azure.com)
1. Navegue para **Definições > Networking** e
1. Escolha **redes selecionadas** em **Permitir o acesso a partir de**
1. Na secção **Firewall,** selecione **Permitir que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento** e acerte o **Save**

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="Screenshot mostrando a definição de firewall":::

## <a name="register-an-azure-blob-storage-account"></a>Registar uma conta de armazenamento Azure Blob

Para registar uma nova conta blob no seu catálogo de dados, faça o seguinte:

1. Navegue para a sua conta Desemis
1. Selecione **Fontes** na navegação à esquerda
1. Selecione **Registar-se**
1. Nas **fontes de registo**, selecione **Azure Blob Storage**
1. Selecione **Continuar**

No ecrã **'Registar' (Azure Blob Storage),** faça o seguinte:

1. Introduza um **Nome** que a fonte de dados será listada no Catálogo. 
1. Escolha a sua subscrição para filtrar as contas de armazenamento
1. Selecione uma conta de armazenamento
1. Selecione uma coleção ou crie uma nova (Opcional)
1. **Termine** para registar a fonte de dados.

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="registar opções de fontes" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passos seguintes

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)