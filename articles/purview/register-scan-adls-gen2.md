---
title: Registar e digitalizar Azure Data Lake Storage (ADLS) Gen2
description: Este tutorial descreve como digitalizar a Azure Data Lake Storage Gen2.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 4b7f71b5405708cc1988fafa5ca9c4628fe0d80b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882404"
---
# <a name="register-and-scan-azure-data-lake-storage-gen2"></a>Registar e digitalizar Azure Data Lake Storage Gen2

Este artigo descreve como registar a Azure Data Lake Storage Gen2 como fonte de dados em Azure Purview e configurar uma varredura.

## <a name="supported-capabilities"></a>Capacidades suportadas

A fonte de dados Azure Data Lake Storage Gen2 suporta a seguinte funcionalidade:

- **Digitalizações completas e incrementais** para capturar metadados e classificação na Azure Data Lake Storage Gen2

- **Linhagem** entre ativos de dados para atividades de cópia/fluxo de dados da ADF

## <a name="prerequisites"></a>Pré-requisitos

Antes de registar fontes de dados, crie uma conta Azure Purview. Para obter mais informações sobre a criação de uma conta Desemis, consulte [Quickstart: Criar uma conta Azure Purview](create-catalog-portal.md).

### <a name="setting-up-authentication-for-a-scan"></a>Configuração da autenticação para uma varredura

Os seguintes métodos de autenticação são suportados para a Azure Data Lake Storage Gen2:

- Identidade Gerida
- Service principal (Principal de serviço)
- Chave de conta

#### <a name="managed-identity-recommended"></a>Identidade gerida (Recomendada)

Ao escolher **Identidade Gerida,** para configurar a ligação, tem primeiro de dar à sua conta Purview a permissão para digitalizar a fonte de dados:

1. Navegue para a sua conta de armazenamento ADLS Gen2.
1. Selecione **Access Control (IAM)** do menu de navegação à esquerda. 
1. Selecione **+ Adicionar**.
1. Descreva a **Função** para **Storage Blob Data Reader** e introduza o nome da sua conta Azure Purview na caixa de entrada **Select.** Em seguida, **selecione Guardar** para dar esta atribuição de função à sua conta Purview.

> [!Note]
> Para mais detalhes, consulte os passos no [acesso autorizado a bolhas e filas usando o Azure Ative Directory](../storage/common/storage-auth-aad.md)

#### <a name="account-key"></a>Chave de conta

Quando o método de autenticação selecionado é **a Chave de Conta,** tem de obter a chave de acesso e armazenar no cofre da chave:

1. Navegue para a sua conta de armazenamento ADLS Gne2
1. Selecione **Definições > Teclas de acesso**
1. Copie a sua *chave* e guarde-a em algum lugar para os próximos passos
1. Navegue até ao cofre da chave
1. Selecione **Definições > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** e **Valor** como *chave* da sua conta de armazenamento
1. Selecione **Criar** para completar
1. Se o cofre da chave ainda não estiver ligado ao Purview, terá de [criar uma nova ligação](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) ao cofre de chaves
1. Finalmente, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando a chave para configurar o seu scan

#### <a name="service-principal"></a>Service principal (Principal de serviço)

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

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>Conceder ao Service Principal acesso à sua conta ADLS gen2

1. Navegue até à sua conta de armazenamento.
1. Selecione **Access Control (IAM)** do menu de navegação à esquerda. 
1. Selecione **+ Adicionar**.
1. Descreva a **Função** para **Storage Blob Data Reader** e introduza o nome principal do seu serviço ou iD do objeto na caixa de entrada **Select.** Em seguida, **selecione Save** para dar esta atribuição de função ao seu principal de serviço.
### <a name="firewall-settings"></a>Definições de firewall

> [!NOTE]
> Se tiver firewall ativada para a conta de armazenamento, deve utilizar o método de autenticação **de identidade gerida** ao configurar uma verificação.

1. Entre na sua conta de armazenamento ASLS Gen2 no [portal Azure](https://portal.azure.com)
1. Navegue para **Definições > Networking** e
1. Escolha **redes selecionadas** em **Permitir o acesso a partir de**
1. Na secção **Exceções,** selecione **Permitir que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento** e acerte o **Save**

:::image type="content" source="./media/register-scan-adls-gen2/firewall-setting.png" alt-text="Screenshot mostrando a definição de firewall":::

## <a name="register-azure-data-lake-storage-gen2-data-source"></a>Registar fonte de dados da Azure Data Lake Storage Gen2

Para registar uma nova conta ADLS Gen2 no seu catálogo de dados, faça o seguinte:

1. Navegue para a sua conta Desemis
2. Selecione **Fontes** na navegação à esquerda
3. Selecione **Registar-se**
4. Nas **fontes de registo**, selecione **Azure Data Lake Storage Gen2**
5. Selecione **Continuar**

No ecrã **'Azure Data Lake Storage Gen2' (Azure Data Lake Storage Gen2),** faça o seguinte:

1. Introduza um **Nome** que a fonte de dados será listada no Catálogo.
2. Escolha a sua subscrição para filtrar as contas de armazenamento
3. Selecione uma conta de armazenamento
4. Selecione uma coleção ou crie uma nova (Opcional)
5. **Termine** para registar a fonte de dados.

:::image type="content" source="media/register-scan-adls-gen2/register-sources.png" alt-text="registar opções de fontes" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Próximos passos

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)