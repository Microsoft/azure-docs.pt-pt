---
title: Configure Azure Attestation para o seu servidor lógico Azure SQL
description: Configure Azure Attestation para Sempre Encriptado com enclaves seguros na Base de Dados Azure SQL.
keywords: encriptar dados, encriptação sql, encriptação de base de dados, dados confidenciais, Sempre Criptografados, enclaves seguros, SGX, atestado
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: fb42a0428f0439053375027481d38977b068e356
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122583"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>Configure Azure Attestation para o seu servidor lógico Azure SQL

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Sempre encriptado com enclaves seguros para Azure SQL Database está atualmente em **pré-visualização pública**.

[O Microsoft Azure Attestation](../../attestation/overview.md) é uma solução para atestar ambientes de execução fidedigna (TEEs), incluindo enclaves de extensões de guarda de software intel (Intel SGX). 

Para utilizar o Azure Attestation para atestar enclaves Intel SGX utilizados para [sempre encriptados com enclaves seguros](/sql/relational-databases/security/encryption/always-encrypted-enclaves) na Base de Dados Azure SQL, precisa de:

1. Crie um [fornecedor de atestado e](../../attestation/basic-concepts.md#attestation-provider) configuure-o com a política de atestação recomendada.

2. Conceda ao seu servidor lógico Azure SQL acesso ao seu fornecedor de atestado.

> [!NOTE]
> A configuração do atestado é da responsabilidade do administrador da atestado. Ver [Papéis e responsabilidades ao configurar enclaves EGX e atestado](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

## <a name="requirements"></a>Requisitos

O servidor lógico Azure SQL e o fornecedor de atestado devem pertencer ao mesmo inquilino do Azure Ative Directory. Interações entre inquilinos não são apoiadas. 

O servidor lógico Azure SQL deve ter uma identidade AD AZure atribuída a ele. Como administrador de atestado, precisa de obter a identidade AD AD do servidor do administrador de base de dados Azure SQL para esse servidor. Utilizará a identidade para conceder ao servidor acesso ao fornecedor de atestado. 

Para obter instruções sobre como criar um servidor com uma identidade ou atribuir uma identidade a um servidor existente utilizando o PowerShell e o Azure CLI, consulte [atribuir uma identidade AD Azure ao seu servidor](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server).

## <a name="create-and-configure-an-attestation-provider"></a>Criar e configurar um provedor de atestado

Um [fornecedor de atestado](../../attestation/basic-concepts.md#attestation-provider) é um recurso no Azure Attestation que avalia [os pedidos de atestado](../../attestation/basic-concepts.md#attestation-request) contra políticas de [atestado](../../attestation/basic-concepts.md#attestation-request) e fichas [de atestado .](../../attestation/basic-concepts.md#attestation-token) 

As políticas de atestado são especificadas utilizando a [gramática da regra de reivindicação](../../attestation/claim-rule-grammar.md).

A Microsoft recomenda a seguinte política para atestar enclaves Intel SGX utilizados para Sempre Encriptados na Base de Dados Azure SQL:

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

A política acima verificada verifica:

- O enclave dentro da Base de Dados Azure SQL não suporta a depuração. 
  > Os enclaves podem ser carregados com depuração desativada ou ativada. O suporte de depuração foi concebido para permitir que os desenvolvedores resolvam problemas com o código que está a ser em execução num enclave. Num sistema de produção, a depuração poderia permitir que um administrador examinasse o conteúdo do enclave, o que reduziria o nível de proteção que o enclave proporciona. A política recomendada desativa a depuração para garantir que, se um administrador malicioso tentar ligar o suporte de depuração assumindo a máquina do enclave, o atestado falhará. 
- A identificação do produto do enclave corresponde ao ID do produto atribuído a Sempre Encriptado com enclaves seguros.
  > Cada enclave tem um iD de produto único que diferencia o enclave de outros enclaves. O ID do produto atribuído ao enclave sempre encriptado é 4639. 
- O número da versão de segurança (SVN) da biblioteca é superior a 0.
  > O SVN permite que a Microsoft responda a potenciais bugs de segurança identificados no código enclave. Caso um problema de segurança seja ditado e corrigido, a Microsoft implementará uma nova versão do enclave com um novo SVN (incrementado). A política acima recomendada será atualizada para refletir o novo SVN. Ao atualizar a sua política para corresponder à política recomendada, pode garantir que se um administrador malicioso tentar carregar um enclave mais antigo e inseguro, o atestado falhará.
- A biblioteca do enclave foi assinada com a chave de assinatura da Microsoft (o valor da alegação x-ms-sgx-mrsigner é o haxixe da chave de assinatura).
  > Um dos principais objetivos do atestado é convencer os clientes de que o binário que corre no enclave é o binário que é suposto funcionar. As políticas de atestação fornecem dois mecanismos para este fim. Uma delas é a alegação **de mrenclave** que é o haxixe do binário que supostamente funciona num enclave. O problema com o **mrenclave** é que o haxixe binário muda mesmo com alterações triviais ao código, o que dificulta a reveir o código que está a decorrer no enclave. Por isso, recomendamos a utilização do **mrsigner**, que é um haxixe de uma chave que é usada para assinar o binário enclave. Quando a Microsoft revê o enclave, o **mrsigner** permanece o mesmo desde que a chave de assinatura não mude. Desta forma, torna-se viável implementar binários atualizados sem quebrar as aplicações dos clientes. 

> [!IMPORTANT]
> Um fornecedor de atestado é criado com a política padrão para os enclaves da Intel SGX, que não valida o código que corre dentro do enclave. A Microsoft aconselha-o a definir a política acima recomendada e a não utilizar a política padrão, para Sempre Encriptado com enclaves seguros.

Para instruções sobre como criar um fornecedor de atestado e configurar com uma política de atestado utilizando:

- [Quickstart: Configurar a Azure Attestation com o portal Azure](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > Quando configurar a sua política de atestado com o portal Azure, desafie o Attestation Type para `SGX-IntelSDK` .
- [Quickstart: Configurar a Azure Attestation com a Azure PowerShell](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > Quando configurar a sua política de atestado com a Azure PowerShell, desafie o `Tee` parâmetro para `SgxEnclave` .
- [Quickstart: Instale a Azure Attestation com o Azure CLI](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > Quando configurar a sua política de atestado com o Azure CLI, desafie o `attestation-type` parâmetro para `SGX-IntelSDK` .

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>Determine o URL de atestado para a sua política de atestado

Depois de configurar uma política de atestado, precisa de partilhar o URL da estação de atestado, referindo-se à política, administradores de aplicações que usam Sempre Encriptados com enclaves seguros na Base de Dados Azure SQL. Os administradores de aplicações ou/e os utilizadores de aplicações terão de configurar as suas aplicações com o URL da estação de atestado, para que possam executar declarações que utilizem enclaves seguros.

### <a name="use-powershell-to-determine-the-attestation-url"></a>Use powerShell para determinar o URL da estação de atestação

Utilize o seguinte script para determinar o seu URL de atestado:

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Use o portal Azure para determinar o URL de atestado

1. No painel de visão geral para o seu fornecedor de atestado, copie o valor da propriedade Attest URI para a área de transferência. Um Attest URI deve ser assim: `https://MyAttestationProvider.us.attest.azure.net` .

2. Apecimos o seguinte ao Attest URI: `/attest/SgxEnclave` . 

O URL de atestação resultante deve ser assim: `https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>Conceda ao seu servidor lógico Azure SQL acesso ao seu fornecedor de atestado

Durante o fluxo de trabalho do atestado, o servidor lógico Azure SQL que contém a sua base de dados liga para o provedor de atestado para apresentar um pedido de atestado. Para que o servidor lógico Azure SQL possa submeter pedidos de atestado, o servidor deve ter uma permissão para a `Microsoft.Attestation/attestationProviders/attestation/read` ação no fornecedor de atestado. A forma recomendada de conceder a permissão é que o administrador do prestador de atestado atribua a identidade AD do servidor ao papel de Attestation Reader para o fornecedor de atestado, ou o seu grupo de recursos contendo.

### <a name="use-azure-portal-to-assign-permission"></a>Use o portal Azure para atribuir permissão

Para atribuir a identidade de um servidor Azure SQL à função Attestation Reader para um provedor de atestado, siga as instruções gerais nas [funções De Atribuição Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md). Quando estiver no painel de atribuição de **funções Add:**

1. No **drop-down role,** selecione o papel **Attestation Reader.**
1. No campo **Selecione,** insira o nome do seu servidor Azure SQL para o procurar.

Veja a imagem abaixo para um exemplo.

![atribuição de papel de leitor attestation](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> Para que um servidor apareça no painel de atribuição de **funções Add,** o servidor deve ter uma identidade AD AZure atribuída - ver [Requisitos](#requirements).

### <a name="use-powershell-to-assign-permission"></a>Use o PowerShell para atribuir permissão

1. Encontre o seu servidor lógico Azure SQL.

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName
```
 
2. Atribua o servidor à função Attestation Reader para o grupo de recursos que contém o seu fornecedor de atestado.

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

Para obter mais informações, consulte [atribuir as funções Azure utilizando a Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md#assign-role-examples).

## <a name="next-steps"></a>Passos Seguintes

- [Gerir chaves através do Always Encrypted com enclaves protegidos](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>Ver também

- [Tutorial: Começar com Sempre Encriptado com enclaves seguros na Base de Dados Azure SQL](always-encrypted-enclaves-getting-started.md)
