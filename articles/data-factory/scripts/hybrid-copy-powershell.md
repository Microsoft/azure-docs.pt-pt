---
title: Copiar dados do local para o Azure usando o PowerShell
description: Este script PowerShell copia dados de uma base de dados do SQL Server no local para outro armazenamento de Blob Azure.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: jingwang
author: linda33wj
manager: shwang
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 10555defc4888af66bb88d19190b6543aa8ae0c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75974697"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Use a PowerShell para criar um oleoduto de fábrica de dados para copiar dados do local para o Azure

Esta amostra de script PowerShell cria um pipeline na Azure Data Factory que copia dados de uma base de dados do SQL Server no local para um Armazenamento De Blob Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- **Servidor SQL**. Utiliza uma base de dados do SQL Server no local como uma loja de dados **de origem** nesta amostra.
- **Conta de Armazenamento Azure.** Utiliza o armazenamento de blob Azure como uma loja de dados de **destino/pia** nesta amostra. se não tiver uma conta de armazenamento Azure, consulte o artigo de [conta de armazenamento Criar uma conta](../../storage/common/storage-account-create.md) de armazenamento para passos para criar uma.
- **Tempo de execução de integração auto-hospedado.** Descarregue o ficheiro MSI do centro de [descarregamento](https://www.microsoft.com/download/details.aspx?id=39717) e execute-o para instalar um tempo de funcionação de integração auto-hospedado na sua máquina.  

### <a name="create-sample-database-in-sql-server"></a>Criar base de dados de amostras no Servidor SQL
1. Na base de dados do SQL Server no local, crie uma tabela chamada **emp** utilizando o seguinte script SQL:

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Insira alguns dados da amostra na tabela:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Script de exemplo

> [!IMPORTANT]
> Este script cria ficheiros JSON que definem entidades da Data Factory (serviço ligado, conjunto de dados e pipeline) no seu disco rígido no c:\ pasta.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script da amostra, pode utilizar o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Para remover a fábrica de dados do grupo de recursos, executar o seguinte comando:

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Criar uma fábrica de dados. |
| [Novo AzDataFactoryV2LinkedServiceLinkedLinkedCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Encripta credenciais num serviço ligado e gera uma nova definição de serviço ligada com a credencial encriptada.
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Cria um serviço ligado na fábrica de dados. Um serviço ligado liga uma loja de dados ou uma computação a uma fábrica de dados. |
| [Conjunto-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Cria um conjunto de dados na fábrica de dados. Um conjunto de dados representa a entrada/saída para uma atividade num gasoduto. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Cria um oleoduto na fábrica de dados. Um oleoduto contém uma ou mais atividades que realizam uma determinada operação. Neste pipeline, uma atividade de cópia copia dados de um local para outro local num Armazém Azure Blob. |
| [Invocar-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Cria uma corrida para o oleoduto. Por outras palavras, corre o oleoduto. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Obtém detalhes sobre o execução da atividade (execução de atividade) no oleoduto.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de script da Fábrica de Dados Azure PowerShell podem ser encontradas nas [amostras de PowerShell da Fábrica](../samples-powershell.md)de Dados Azure .
