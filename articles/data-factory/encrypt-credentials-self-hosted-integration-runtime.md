---
title: Criptografe credenciais na Fábrica de Dados Azure
description: Aprenda a encriptar e armazenar credenciais para as suas lojas de dados no local numa máquina com tempo de funcionação de integração auto-hospedado.
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
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628985"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Criptografe credenciais para lojas de dados no local na Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pode encriptar e armazenar credenciais para as suas lojas de dados no local (serviços ligados com informações sensíveis) numa máquina com tempo de execução de integração auto-hospedada. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você passa um arquivo de definição JSON com credenciais para o <br/>[**Novo AzDataFactoryV2LinkedServiceEncriptadoCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) cmdlet para produzir um ficheiro de definição JSON de saída com as credenciais encriptadas. Em seguida, utilize a definição jSON atualizada para criar os serviços ligados.

## <a name="author-sql-server-linked-service"></a>Serviço ligado ao servidor Autor SQL
Crie um ficheiro JSON chamado **SqlServerLinkedService.json** em qualquer pasta com o seguinte conteúdo:  

`<servername>`Substitua, `<databasename>` `<username>`, `<password>` e com valores para o seu Servidor SQL antes de guardar o ficheiro. E, `<integration runtime name>` substitua-o pelo nome do seu tempo de integração. 

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
Para encriptar os dados sensíveis da carga útil JSON num tempo de execução de integração auto-hospedado no local, executar **New-AzDataFactoryV2LinkedServiceLinkedLinkedLinkedCredential**, e passar a carga útil JSON. Este cmdlet garante que as credenciais são encriptadas usando DPAPI e armazenadas no nó de tempo de execução de integração auto-hospedado localmente. A carga útil de saída que contém a referência encriptada à credencial pode ser redirecionada para outro ficheiro JSON (neste caso 'encryptedLinkedService.json').

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Use o JSON com credenciais encriptadas
Agora, utilize o ficheiro JSON de saída do comando anterior contendo a credencial encriptada para configurar o **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre considerações de segurança para o movimento de dados, consulte [considerações](data-movement-security-considerations.md)de segurança do movimento de dados .

