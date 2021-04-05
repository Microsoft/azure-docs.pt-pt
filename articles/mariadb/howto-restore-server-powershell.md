---
title: Backup e restauro - Azure PowerShell - Base de Dados Azure para MariaDB
description: Saiba como fazer backup e restaurar um servidor na Base de Dados Azure para MariaDB utilizando o Azure PowerShell.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 05/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0207be2c983fd986d5852403e36462d2d7d2cdda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664856"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-mariadb-server-using-powershell"></a>Como fazer o back up e restaurar uma Base de Dados Azure para o servidor MariaDB usando o PowerShell

A base de dados Azure para servidores MariaDB é monitorizada periodicamente para permitir a restauração de funcionalidades. Utilizando esta funcionalidade, poderá restaurar o servidor e todas as suas bases de dados num ponto-a-tempo anterior, num novo servidor.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

- O [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MariaDb PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Uma vez que o módulo Az.MariaDb PowerShell está geralmente disponível, torna-se parte de futuros lançamentos do módulo Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Configuração de backup de definição

Na criação do servidor, você faz a escolha entre configurar o seu servidor para backups locais redundantes ou geograficamente redundantes.

> [!NOTE]
> Depois de um servidor ser criado, o tipo de redundância que tem, geograficamente redundante vs localmente redundante, não pode ser alterado.

Ao criar um servidor através do `New-AzMariaDbServer` comando, o parâmetro **GeoRedundantBackup** decide a sua opção de redundância de backup. Se **Ativados,** são tomadas cópias de segurança geo redundantes. Ou se **deficientes,** os backups locais redundantes são levados.

O período de retenção de backup é definido pelo parâmetro **BackupRetentionDay.**

Para obter mais informações sobre a definição destes valores durante a criação do servidor, consulte [criar uma base de dados Azure para o servidor MariaDB utilizando o PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md).

O período de retenção de backup de um servidor pode ser alterado da seguinte forma:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

O exemplo anterior altera o período de retenção de backup do mydemoserver para 10 dias.

O período de retenção de backups regula até onde um ponto de restauração pode ser recuperado, uma vez que é baseado em cópias de segurança disponíveis. A restauração pontual é descrita mais na secção seguinte.

## <a name="server-point-in-time-restore"></a>Restauro ponto-a-tempo do servidor

Pode restaurar o servidor para um ponto-a-tempo anterior. Os dados restaurados são copiados para um novo servidor, e o servidor existente permanece inalterado. Por exemplo, se uma mesa for acidentalmente largada, pode restaurar a hora em que ocorreu a queda. Em seguida, pode recuperar a tabela e os dados em falta a partir da cópia restaurada do servidor.

Para restaurar o servidor, utilize o `Restore-AzMariaDbServer` cmdlet PowerShell.

### <a name="run-the-restore-command"></a>Executar o comando de restauro

Para restaurar o servidor, execute o seguinte exemplo da PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

O conjunto de **parâmetros PointInTimeRestore** do `Restore-AzMariaDbServer` cmdlet requer os seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  O grupo de recursos onde existe o servidor de origem.  |
| Name | mydemoserver-restored | O nome do novo servidor que é criado pelo comando restore. |
| RestaurarPointInTime | 2020-03-13T13:59:00Z | Selecione um ponto no tempo para restaurar. Esta data e hora têm de estar dentro do período de retenção de cópias de segurança do servidor de origem. Utilize o formato de data e hora ISO8601. Por exemplo, pode utilizar o seu próprio fuso horário local, como **2020-03-13T05:59:00-08:00**. Também pode utilizar o formato UTC Zulu, por exemplo, **2018-03-13T13:59:00Z**. |
| UsePointInTimeRestore | `<SwitchParameter>` | Utilize o modo ponto-a-tempo para restaurar. |

Quando restaura um servidor a um ponto-a-tempo anterior, é criado um novo servidor. O servidor original e as suas bases de dados a partir do ponto-a-tempo especificado são copiados para o novo servidor.

Os valores de localização e de nível de preços do servidor restaurado permanecem os mesmos que o servidor original.

Após o fim do processo de restauro, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de login de administrador do servidor e senha que era válido para o servidor existente no momento em que a restauração foi iniciada. A palavra-passe pode ser alterada a partir da página **geral** do novo servidor.

O novo servidor criado durante uma restauração não possui os pontos finais do serviço VNet que existiam no servidor original. Estas regras devem ser configuras separadamente para o novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="geo-restore"></a>Restauro geo

Se configurar o servidor para cópias de segurança geograficamente redundantes, um novo servidor pode ser criado a partir da cópia de segurança do servidor existente. Este novo servidor pode ser criado em qualquer região onde a Base de Dados Azure para MariaDB esteja disponível.

Para criar um servidor utilizando uma cópia de segurança geo redundante, utilize o `Restore-AzMariaDbServer` comando com o parâmetro **UseGeoRestore.**

> [!NOTE]
> Quando um servidor é criado pela primeira vez, pode não estar imediatamente disponível para restauro geo. Pode levar algumas horas para que os metadados necessários sejam povoados.

Para restaurar geo o servidor, executar o seguinte exemplo a partir de PowerShell:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Este exemplo cria um novo servidor chamado **mydemoserver-georestored** na região leste dos EUA que pertence ao **myresourcegroup**. É um servidor De Propósito Geral, Gen 5 com 8 vCores. O servidor é criado a partir da cópia de segurança geo-redundante do **mydemoserver,** também no grupo de recursos **myresourcegroup**.

Para criar o novo servidor num grupo de recursos diferente do servidor existente, especifique o nome do novo grupo de recursos utilizando o parâmetro **ResourceGroupName,** como mostrado no exemplo seguinte:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

O conjunto de **parâmetros GeoRestore** do `Restore-AzMariaDbServer` cmdlet requer os seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
|ResourceGroupName | myResourceGroup | O nome do grupo de recursos a que pertence o novo servidor pertence.|
|Name | mydemoserver-georestored | O nome do novo servidor. |
|Localização | eastus | A localização do novo servidor. |
|UseGeoRestore | `<SwitchParameter>` | Utilize o modo geo para restaurar. |

Ao criar um novo servidor utilizando a geo restauração, herda o mesmo tamanho de armazenamento e nível de preços que o servidor de origem, a menos que o parâmetro **Sku** seja especificado.

Após o fim do processo de restauro, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de login de administrador do servidor e senha que era válido para o servidor existente no momento em que a restauração foi iniciada. A palavra-passe pode ser alterada a partir da página **geral** do novo servidor.

O novo servidor criado durante uma restauração não possui os pontos finais do serviço VNet que existiam no servidor original. Estas regras devem ser configuras separadamente para este novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como gerar uma base de dados Azure para cadeia de ligação MariaDB com PowerShell](howto-connection-string-powershell.md)