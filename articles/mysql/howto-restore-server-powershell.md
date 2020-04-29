---
title: Backup e restauro - Azure PowerShell - Base de Dados Azure para MySQL
description: Aprenda a fazer backup e restaurar um servidor na Base de Dados Azure para o MySQL utilizando o Azure PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershel
ms.topic: conceptual
ms.date: 4/28/2020
ms.openlocfilehash: 1e63a6e57e1dc929c78e5278df6ef0e4ab2a17d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82230850"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-powershell"></a>Como fazer cópias e restaurar um servidor na Base de Dados Azure para o MySQL usando o PowerShell

A Base de Dados Azure para servidores MySQL é apoiada periodicamente para permitir a restauração de funcionalidades. Utilizando esta funcionalidade, poderá restaurar o servidor e todas as suas bases de dados num momento anterior, num novo servidor.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia de como orientar, precisa de:

- O [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MySql PowerShell estiver em pré-visualização, deve instalá-lo `Install-Module -Name Az.MySql -AllowPrerelease`separadamente do módulo Az PowerShell utilizando o seguinte comando: .
> Uma vez que o módulo Az.MySql PowerShell esteja geralmente disponível, torna-se parte das futuras versões do módulo Az PowerShell e disponível de forma nativa dentro da Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/Connect-AzAccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Definir configuração de backup

Na criação do servidor, você faz a escolha entre configurar o seu servidor para backups locais redundantes ou geograficamente redundantes.

> [!NOTE]
> Depois de criado um servidor, o tipo de redundância que tem, geograficamente redundante vs localmente redundante, não pode ser alterado.

Ao criar um `New-AzMySqlServer` servidor através do comando, o parâmetro **GeoRedundantBackup** decide a sua opção de redundância de reserva. Se **ativado,** são tomadas cópias de segurança georedundantes. Ou se os **deficientes,** cópias de segurança redundantes localmente são tomadas.

O período de retenção de reserva é definido pelo parâmetro **BackupRetentionDay.**

Para obter mais informações sobre a definição destes valores durante a criação do servidor, consulte [Criar uma Base de Dados Azure para o servidor MySQL utilizando o PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md).

O período de retenção de cópia de segurança de um servidor pode ser alterado da seguinte forma:

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

O exemplo anterior altera o período de retenção de cópia de segurança do mydemoserver para 10 dias.

O período de retenção de reserva rege até onde um restauro pontual pode ser recuperado, uma vez que é baseado em cópias de segurança disponíveis. A restauração ponto-a-tempo é descrita mais na secção seguinte.

## <a name="server-point-in-time-restore"></a>Restauro ponto-a-tempo do servidor

Pode restaurar o servidor num ponto-a-tempo anterior. Os dados restaurados são copiados para um novo servidor, e o servidor existente é deixado inalterado. Por exemplo, se uma mesa for acidentalmente largada, pode restaurar até ao momento em que agotou aquecência. Em seguida, pode recuperar a tabela em falta e os dados da cópia restaurada do servidor.

Para restaurar o servidor, utilize o `Restore-AzMySqlServer` cmdlet PowerShell.

### <a name="run-the-restore-command"></a>Executar o comando de restauro

Para restaurar o servidor, execute o seguinte exemplo a partir do PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

O conjunto de parâmetros **PointInTimeRestore** do `Restore-AzMySqlServer` cmdlet requer os seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  O grupo de recursos onde o servidor de origem existe.  |
| Nome | mydemoserver-restored | O nome do novo servidor que é criado pelo comando restore. |
| RestorePointInTime | 2020-03-13T13:59:00Z | Selecione um ponto a tempo para restaurar. Esta data e hora têm de estar dentro do período de retenção de cópias de segurança do servidor de origem. Utilize o formato de data e hora ISO8601. Por exemplo, pode utilizar o seu próprio fuso horário local, como **2020-03-13T05:59:00-08:00**. Também pode utilizar o formato UTC Zulu, por exemplo, **2018-03-13T13:59:00Z**. |
| UtilizaçãoPointInTimeRestore | `<SwitchParameter>` | Utilize o modo ponto-a-tempo para restaurar. |

Quando restaura um servidor para um momento anterior, é criado um novo servidor. O servidor original e as suas bases de dados do ponto de vista especificado são copiados para o novo servidor.

Os valores de nível de localização e de preços para o servidor restaurado permanecem os mesmos que o servidor original.

Após o final do processo de restauro, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de login de servidor e senha que era válido para o servidor existente no momento em que o restauro foi iniciado. A palavra-passe pode ser alterada a partir da página **de visão geral** do novo servidor.

O novo servidor criado durante uma restauração não tem os pontos finais do serviço VNet que existiam no servidor original. Estas regras devem ser configuradas separadamente para o novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="geo-restore"></a>Geo restaurar

Se configurar o seu servidor para backups geograficamente redundantes, um novo servidor pode ser criado a partir da cópia de segurança do servidor existente. Este novo servidor pode ser criado em qualquer região que o Azure Database para mySQL esteja disponível.

Para criar um servidor utilizando uma `Restore-AzMySqlServer` cópia de segurança georedundante, utilize o comando com o parâmetro **UseGeoRestore.**

> [!NOTE]
> Quando um servidor é criado pela primeira vez, pode não estar imediatamente disponível para restauro geo. Pode levar algumas horas para que os metadados necessários sejam povoados.

Para restaurar o servidor, execute o seguinte exemplo a partir do PowerShell:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Este exemplo cria um novo servidor chamado **mydemoserver-georestored** na região leste dos EUA que pertence ao **myresourcegroup**. É um General Purpose, servidor Gen 5 com 8 vCores. O servidor é criado a partir da cópia de segurança georedundantdo do **mydemoserver**, também no grupo de recursos **myresourcegroup**.

Para criar o novo servidor num grupo de recursos diferente do servidor existente, especifique o novo nome do grupo de recursos utilizando o parâmetro **ResourceGroupName,** como mostra o seguinte exemplo:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

O conjunto de **parâmetros GeoRestore** do `Restore-AzMySqlServer` cmdlet requer os seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
|ResourceGroupName | myResourceGroup | O nome do grupo de recursos a que o novo servidor pertence.|
|Nome | mydemoserver-georestauro | O nome do novo servidor. |
|Localização | eastus | A localização do novo servidor. |
|UseGeoRestore | `<SwitchParameter>` | Utilize o modo geopara restaurar. |

Ao criar um novo servidor utilizando georestauro, herda o mesmo tamanho de armazenamento e nível de preços que o servidor de origem, a menos que o parâmetro **Sku** seja especificado.

Após o final do processo de restauro, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de login de servidor e senha que era válido para o servidor existente no momento em que o restauro foi iniciado. A palavra-passe pode ser alterada a partir da página **de visão geral** do novo servidor.

O novo servidor criado durante uma restauração não tem os pontos finais do serviço VNet que existiam no servidor original. Estas regras devem ser configuradas separadamente para este novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [backups](concepts-backup.md) do serviço
- Saiba mais sobre [réplicas](concepts-read-replicas.md)
- Saiba mais sobre opções [de continuidade do negócio](concepts-business-continuity.md)
