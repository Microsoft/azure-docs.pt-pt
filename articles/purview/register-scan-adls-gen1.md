---
title: Registar e digitalizar Azure Data Lake Storage (ADLS) Gen1
description: Este tutorial descreve como digitalizar dados do Azure Data Lake Storage Gen1 para a Azure Purview.
author: shsandeep123
ms.author: sandeepshah
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: a831681f892de9f6aae50fa9a2fcf71e883fe6ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693715"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Registar e digitalizar Azure Data Lake Storage Gen1

Este artigo descreve como registar a Azure Data Lake Storage Gen1 como fonte de dados em Azure Purview e configurar uma verificação nele.

> [!Note]
> O Azure Data Lake Storage Gen2 já está em disponibilidade geral. Recomendamos que comece a utilizar hoje. Para mais informações, consulte a página do [produto.](https://azure.microsoft.com/services/storage/data-lake-storage/)

## <a name="supported-capabilities"></a>Capacidades suportadas

A fonte de dados da Azure Data Lake Storage Gen1 suporta a seguinte funcionalidade:

- **Digitalizações completas e incrementais** para capturar metadados e classificação na Azure Data Lake Storage Gen1

- **Linhagem** entre ativos de dados para atividades de cópia/fluxo de dados da ADF

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registar fontes de dados, crie uma conta Azure Purview. Para obter mais informações sobre a criação de uma conta Desemis, consulte [Quickstart: Criar uma conta Azure Purview](create-catalog-portal.md).
- Você precisa ser um Azure Purview Data Source Admin

## <a name="setting-up-authentication-for-a-scan"></a>Configuração da autenticação para uma varredura

Os seguintes métodos de autenticação são suportados para a Azure Data Lake Storage Gen1:

- Identidade Gerida
- Service principal (Principal de serviço)

### <a name="managed-identity-recommended"></a>Identidade gerida (Recomendada)

Para facilitar e segurança, pode querer utilizar o PURview MSI para autenticar com a sua loja de dados.

Para configurar uma verificação utilizando o MSI do Catálogo de Dados, tem primeiro de dar à sua conta Purview a permissão para digitalizar a fonte de dados. Este passo tem de ser dado *antes* de configurar a sua tomografia ou a sua tomografia falhará.

#### <a name="adding-the-data-catalog-msi-to-an-azure-data-lake-storage-gen1-account"></a>Adicionar o Catálogo de Dados MSI a uma conta Gen1 de armazenamento de dados do Azure Data Lake

Pode adicionar o MSI do Catálogo ao nível de Subscrição, Grupo de Recursos ou Recursos, dependendo do que pretende que tenha permissões de digitalização.

> [!Note]
> Você precisa ser proprietário da subscrição para ser capaz de adicionar uma identidade gerida em um recurso Azure.

1. A partir do [portal Azure](https://portal.azure.com), encontre a subscrição, grupo de recursos ou recurso (por exemplo, uma conta de armazenamento Azure Data Lake Storage Gen1) que gostaria de permitir que o catálogo digitalize.

2. Clique em **Visão Geral** e, em seguida, selecione **Data Explorer**

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Escolher o controlo de acesso":::

3. Clique no **Acesso** na navegação superior

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Clique no Acesso":::

4. Clique em **Adicionar**. Adicione o **Catálogo DeVisão** na seleção de utilizador ou grupo Select. Selecione **permissões de Ler** e **Executar.** Certifique-se de escolher **Esta pasta e todas as crianças** na opção Adicionar à opção como mostrado na imagem abaixo e clique em detalhes de autenticação **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/managed-instance-authentication.png" alt-text="MSI":::

5. Se o cofre da chave ainda não estiver ligado ao Purview, terá de [criar uma nova ligação](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)ao cofre de chaves .

6. Finalmente, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando o Principal de Serviço para configurar a sua digitalização
> [!Note]
> Depois de ter adicionado o MSI do catálogo na fonte de dados, aguarde até 15 minutos para que as permissões se propaguem antes de configurar uma verificação.

Após cerca de 15 minutos, o catálogo deve ter as permissões adequadas para poder digitalizar os recursos.

### <a name="service-principal"></a>Service principal (Principal de serviço)

Para utilizar um principal de serviço, tem primeiro de criar um seguindo estes passos:

1. Navegue até ao [portal Azure.](https://portal.azure.com)

2. Selecione **Azure Ative Directory** a partir do menu do lado esquerdo.

3. Selecione **Registos de aplicações**.

4. Selecione **+ Novo registo de inscrição.**

5. Introduza um nome para a **inscrição** (nome principal do serviço).

6. Selecione **Contas apenas neste diretório organizacional**.

7. Para **redirecionar uri** selecione **Web** e introduza qualquer URL que pretenda; não tem que ser real ou trabalho.

8. Em seguida, selecione **Registar**.

9. Copie os valores tanto do nome do visor como do ID da aplicação.

#### <a name="adding-the-data-catalog-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Adicionar o principal do serviço de catálogo de dados a uma conta gen1 de armazenamento de dados Azure Data Lake
1. A partir do [portal Azure](https://portal.azure.com), encontre a subscrição, grupo de recursos ou recurso (por exemplo, uma conta de armazenamento Azure Data Lake Storage Gen1) que gostaria de permitir que o catálogo digitalize.

2. Clique em **Visão Geral** e, em seguida, selecione **Data Explorer**

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Escolher o controlo de acesso":::

3. Clique no **Acesso** na navegação superior

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Clique no Acesso":::

4. Clique em **Adicionar**. Adicione a **aplicação principal do Serviço** na seleção de utilizador ou grupo Select. Selecione **permissões de Ler** e **Executar.** Certifique-se de escolher **Esta pasta e todas as crianças** na opção Adicionar à opção como mostrado na imagem abaixo e clique em detalhes de  
    :::image type="content" source="./media/register-scan-adls-gen1/service-principal-authentication.png" alt-text="autenticação principal do serviço"::: OK

5. Se o cofre da chave ainda não estiver ligado ao Purview, terá de [criar uma nova ligação](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)ao cofre de chaves .

6. Por fim, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) utilizando o Diretor de Serviço para configurar a sua digitalização.

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Registar fonte de dados da Azure Data Lake Storage Gen1

Para registar uma nova conta ADLS Gen1 no seu catálogo de dados, faça o seguinte:

1. Navegue para o seu Catálogo de Dados de Purga.
2. Selecione **Fontes** na navegação à esquerda.
3. Selecione **Registar-se**
4. Nas **fontes do Registo**, selecione **Azure Data Lake Storage Gen1**. 
5. Selecione **Continuar**.

No ecrã 'Azure Data Lake Storage Gen1), faça o seguinte:

1. Introduza um **Nome** que a fonte de dados será listada no Catálogo.
2. Escolha a sua subscrição para filtrar as contas de armazenamento
3. Selecione uma conta de armazenamento
4. Selecione uma coleção ou crie uma nova (Opcional)
5. Termine para registar a fonte de dados.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passos seguintes

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)
