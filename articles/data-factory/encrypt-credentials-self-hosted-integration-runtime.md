---
title: Criptografar credenciais no Azure Data Factory
description: Saiba como criptografar e armazenar credenciais para seus armazenamentos de dados locais em um computador com o Integration Runtime de hospedagem interna.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 67ba2fadd5376997b528af4fcd2c5a666bb134a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443990"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Criptografar credenciais para armazenamentos de dados locais no Azure Data Factory
Você pode criptografar e armazenar credenciais para seus armazenamentos de dados locais (serviços vinculados com informações confidenciais) em um computador com o Integration Runtime de hospedagem interna. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você passa um arquivo de definição JSON com credenciais para o <br/>Cmdlet [**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) para produzir um arquivo de definição JSON de saída com as credenciais criptografadas. Em seguida, use a definição de JSON atualizada para criar os serviços vinculados.

## <a name="author-sql-server-linked-service"></a>Criar SQL Server serviço vinculado
Crie um arquivo JSON chamado **SqlServerLinkedService. JSON** em qualquer pasta com o seguinte conteúdo:  

Substitua `<servername>`, `<databasename>`, `<username>`e `<password>` pelos valores de seu SQL Server antes de salvar o arquivo. E substitua `<integration runtime name>` pelo nome do seu tempo de execução de integração. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Encriptar credenciais
Para criptografar os dados confidenciais do conteúdo JSON em um tempo de execução de integração autohospedado local, execute **New-AzDataFactoryV2LinkedServiceEncryptedCredential**e passe o conteúdo JSON. Esse cmdlet garante que as credenciais sejam criptografadas usando DPAPI e armazenadas no nó de tempo de execução de integração auto-hospedado localmente. A carga de saída que contém a referência criptografada para a credencial pode ser redirecionada para outro arquivo JSON (neste caso, ' encryptedLinkedService. JSON ').

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Usar o JSON com credenciais criptografadas
Agora, use o arquivo JSON de saída do comando anterior que contém a credencial criptografada para configurar o **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre considerações de segurança para movimentação de dados, consulte [considerações de segurança de movimentação de dados](data-movement-security-considerations.md).

