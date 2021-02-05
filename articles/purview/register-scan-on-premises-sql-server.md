---
title: Registar e digitalizar um servidor SQL no local
description: Este tutorial descreve como digitalizar o servidor SQL pré-pré-pré-pré-usando um IR auto-hospedado.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: b5f4218cfcd5f9ccfbe43efac46e2f70fdc30905
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574962"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>Registar e digitalizar um servidor SQL no local

Este artigo descreve como registar uma fonte de dados do servidor SQL em Purview e configurar uma verificação nele.

## <a name="supported-capabilities"></a>Capacidades suportadas

A fonte de dados do servidor SQL no local suporta a seguinte funcionalidade:

- **Varreduras completas e incrementais** para capturar metadados e classificação numa rede no local ou num servidor SQL instalado num VM Azure.

- **Linhagem** entre ativos de dados para atividades de cópia/fluxo de dados da ADF

Suportes de fonte de dados do servidor SQL no local:

- cada versão do SQL do sql servidor 2019 de volta ao servidor SQL 2000

- Método de autenticação: Autenticação SQL

### <a name="known-limitations"></a>Limitações conhecidas

O Azure Purview não suporta a digitalização de [visualizações](/sql/relational-databases/views/views) no SQL Server.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registar fontes de dados, crie uma conta Azure Purview. Para obter mais informações sobre a criação de uma conta Desemis, consulte [Quickstart: Criar uma conta Azure Purview](create-catalog-portal.md).

- Crie um [tempo de integração auto-hospedado](manage-integration-runtimes.md) para digitalizar a fonte de dados.

## <a name="setting-up-authentication-for-a-scan"></a>Configuração da autenticação para uma varredura

Só há uma maneira de configurar a autenticação para o servidor SQL no local:

- Autenticação SQL

### <a name="sql-authentication"></a>Autenticação do SQL

A conta SQL deve ter acesso à base **de dados principal.** Isto é porque `sys.databases` está na base de dados principal. O scanner de Purview precisa de enumerar `sys.databases` para encontrar todas as bases de dados SQL no servidor.

#### <a name="using-an-existing-server-administrator"></a>Usando um administrador de servidor existente

Se planeia utilizar um utilizador de administração (sa) de servidor existente para digitalizar o servidor SQL no local, certifique-se do seguinte:

1. `sa` não é uma conta de autenticação do Windows.

2. O início de sessão de nível de servidor que está a planear utilizar deve ter funções de servidor de público e sysadmin. Pode verificar isto ligando-o ao servidor, navegando no SQL Server Management Studio (SSMS), navegando para segurança, selecionando o login que planeia utilizar, clicando com o botão direito **de propriedades** e, em seguida, selecionando **funções do Servidor**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="Início de sessão de nível do servidor.":::

#### <a name="creating-a-new-login-and-user"></a>Criar um novo login e utilizador

Se pretender criar um novo login e o utilizador para poder digitalizar o seu servidor SQL, siga os passos abaixo:

> [!Note]
   > Todos os passos abaixo podem ser executados usando o código fornecido [aqui](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql)

1. Navegue para o SQL Server Management Studio (SSMS), ligue-se ao servidor, navegue para segurança, clique com o botão direito no login e crie novo login. Certifique-se de que seleciona a autenticação SQL.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Crie novo login e utilizador.":::

2. Selecione as funções do Servidor na navegação à esquerda e certifique-se de que o papel público é atribuído.

3. Selecione o mapeamento do Utilizador na navegação à esquerda, selecione todas as bases de dados no mapa e selecione a função Base de Dados: **db_datareader**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="mapeamento do utilizador.":::

4. Clique em OK para guardar.

5. Navegue novamente para o utilizador que criou, clicando e selecionando **propriedades.** Introduza uma nova senha e confirme-a. Selecione a palavra-passe 'Especificar a palavra-passe antiga' e introduza a senha antiga. **É necessário alterar a sua palavra-passe assim que criar um novo login.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="alterar a palavra-passe.":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Armazenar a sua senha de login SQL num cofre de chaves e criar uma credencial em Purview

1. Navegue até ao cofre chave no portal Azure1. Selecione **Definições > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** e **Valor** como *palavra-passe* do seu login do servidor SQL
1. Selecione **Criar** para completar
1. Se o cofre da chave ainda não estiver ligado ao Purview, terá de [criar uma nova ligação](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) ao cofre de chaves
1. Finalmente, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando o nome de **utilizador** e **senha** para configurar a sua digitalização

## <a name="register-a-sql-server-data-source"></a>Registar uma fonte de dados do servidor SQL

1. Navegue para a sua conta Desemis

1. Sob Fontes e digitalização na navegação à esquerda, selecione **Os tempos de integração**. Certifique-se de que é configurado um tempo de integração auto-hospedado. Se não for configurado, siga os passos [aqui](manage-integration-runtimes.md) mencionados para criar um tempo de integração auto-hospedado para digitalização em um local ou VM Azure que tenha acesso à sua rede no local.

1. Selecione **Fontes** na navegação à esquerda

1. Selecione **Registar-se**

1. Selecione **o servidor SQL** e, em seguida, **continue**

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Configurar a fonte de dados SQL.":::

5. Forneça um nome amigável e ponto final do servidor e, em seguida, **selecione Finish** para registar a fonte de dados. Se, por exemplo, o seu servidor SQL FQDN for **foobar.database.windows.net**, então *introduza o foobar* como ponto final do servidor.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passos seguintes

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)
