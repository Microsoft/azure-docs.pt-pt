---
title: Credenciais encriptadas na Azure Data Factory
description: Saiba como encriptar e armazenar credenciais para as suas lojas de dados no local numa máquina com tempo de integração auto-hospedado.
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
ms.openlocfilehash: cd775c5a3bf367600a4537a9409a9bb8f902f588
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82628985"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Credenciais encriptadas para lojas de dados no local na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pode encriptar e armazenar credenciais para as suas lojas de dados no local (serviços ligados com informações sensíveis) numa máquina com tempo de integração auto-hospedado. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você passa um arquivo de definição JSON com credenciais para o <br/>[**Novo-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) cmdlet para produzir um ficheiro de definição JSON de saída com as credenciais encriptadas. Em seguida, utilize a definição JSON atualizada para criar os serviços ligados.

## <a name="author-sql-server-linked-service"></a>Serviço ligado ao Servidor SQL do autor
Crie um ficheiro JSON nomeado **SqlServerLinkedService.js** em qualquer pasta com o seguinte conteúdo:  

Substitua `<servername>` , , e por `<databasename>` `<username>` `<password>` valores para o seu SQL Server antes de guardar o ficheiro. E, `<integration runtime name>` substitua-o pelo nome do seu tempo de integração. 

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
Para encriptar os dados sensíveis da carga útil JSON num tempo de integração auto-hospedado no local, executar **New-AzDataFactoryV2LinkedServiceEncryptedCredential**e passar a carga útil JSON. Este cmdlet garante que as credenciais são encriptadas usando DPAPI e armazenadas no nó de tempo de execução de integração auto-hospedado localmente. A carga útil de saída que contém a referência encriptada à credencial pode ser redirecionada para outro ficheiro JSON (neste caso , 'encryptedLinkedService.jsligado').

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Use o JSON com credenciais encriptadas
Agora, utilize o ficheiro JSON de saída do comando anterior contendo a credencial encriptada para configurar o **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre considerações de segurança para o movimento de [dados, consulte considerações de segurança do movimento de dados](data-movement-security-considerations.md).

