---
title: Backup e restauro - Azure PowerShell - Base de Dados Azure para MariaDB
description: Aprenda a fazer backup e restaurar um servidor na Base de Dados Azure para O D' MariaDB utilizando o Azure PowerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: c16c18a7056b2d23b64caeca4a88905b3dd2557c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041946"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-mariadb-server-using-powershell"></a>Como fazer cópias e restaurar uma Base de Dados Azure para o servidor MariaDB utilizando o PowerShell

A Base de Dados Azure para servidores MariaDB é apoiada periodicamente para permitir a restauração de funcionalidades. Utilizando esta funcionalidade, poderá restaurar o servidor e todas as suas bases de dados num momento anterior, num novo servidor.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia de como orientar, precisa de:

- O [módulo Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [Base de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MariaDb PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Uma vez que o módulo Az.MariaDb PowerShell esteja geralmente disponível, torna-se parte das futuras versões do módulo Az PowerShell e disponível de forma nativa a partir de dentro da Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Definir configuração de backup

Na criação do servidor, você faz a escolha entre configurar o seu servidor para backups locais redundantes ou geograficamente redundantes.

> [!NOTE]
> Depois de criado um servidor, o tipo de redundância que tem, geograficamente redundante vs localmente redundante, não pode ser alterado.

Ao criar um servidor através do `New-AzMariaDbServer` comando, o parâmetro **GeoRedundantBackup** decide a sua opção de redundância de reserva. Se **ativado,** são tomadas cópias de segurança georedundantes. Ou se os **deficientes,** cópias de segurança redundantes localmente são tomadas.

O período de retenção de reserva é definido pelo parâmetro **BackupRetentionDay.**

Para obter mais informações sobre a definição destes valores durante a criação do servidor, consulte [Criar uma Base de Dados Azure para o servidor MariaDB utilizando o PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md).

O período de retenção de cópia de segurança de um servidor pode ser alterado da seguinte forma:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

O exemplo anterior altera o período de retenção de cópia de segurança do mydemoserver para 10 dias.

O período de retenção de reserva rege até onde um restauro pontual pode ser recuperado, uma vez que é baseado em cópias de segurança disponíveis. A restauração ponto-a-tempo é descrita mais na secção seguinte.

## <a name="server-point-in-time-restore"></a>Restauro ponto-a-tempo do servidor

Pode restaurar o servidor num ponto-a-tempo anterior. Os dados restaurados são copiados para um novo servidor, e o servidor existente é deixado inalterado. Por exemplo, se uma mesa for acidentalmente largada, pode restaurar até ao momento em que agotou aquecência. Em seguida, pode recuperar a tabela em falta e os dados da cópia restaurada do servidor.

Para restaurar o servidor, utilize o `Restore-AzMariaDbServer` cmdlet PowerShell.

### <a name="run-the-restore-command"></a>Executar o comando de restauro

Para restaurar o servidor, execute o seguinte exemplo a partir do PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

O conjunto de parâmetros **PointInTimeRestore** do `Restore-AzMariaDbServer` cmdlet requer os seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  O grupo de recursos onde o servidor de origem existe.  |
| Name | mydemoserver-restored | O nome do novo servidor que é criado pelo comando restore. |
| RestorePointInTime | 2020-03-13T13:59:00Z | Selecione um ponto a tempo para restaurar. Esta data e hora têm de estar dentro do período de retenção de cópias de segurança do servidor de origem. Utilize o formato de data e hora ISO8601. Por exemplo, pode utilizar o seu próprio fuso horário local, como **2020-03-13T05:59:00-08:00**. Também pode utilizar o formato UTC Zulu, por exemplo, **2018-03-13T13:59:00Z**. |
| UtilizaçãoPointInTimeRestore | `<SwitchParameter>` | Utilize o modo ponto-a-tempo para restaurar. |

Quando restaura um servidor para um momento anterior, é criado um novo servidor. O servidor original e as suas bases de dados do ponto de vista especificado são copiados para o novo servidor.

Os valores de nível de localização e de preços para o servidor restaurado permanecem os mesmos que o servidor original.

Após o final do processo de restauro, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de login de servidor e senha que era válido para o servidor existente no momento em que o restauro foi iniciado. A palavra-passe pode ser alterada a partir da página **de visão geral** do novo servidor.

O novo servidor criado durante uma restauração não tem os pontos finais do serviço VNet que existiam no servidor original. Estas regras devem ser configuradas separadamente para o novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="geo-restore"></a>Geo restaurar

Se configurar o seu servidor para backups geograficamente redundantes, um novo servidor pode ser criado a partir da cópia de segurança do servidor existente. Este novo servidor pode ser criado em qualquer região que o Azure Database para MariaDB esteja disponível.

Para criar um servidor utilizando uma cópia de segurança georedundante, utilize o `Restore-AzMariaDbServer` comando com o parâmetro **UseGeoRestore.**

> [!NOTE]
> Quando um servidor é criado pela primeira vez, pode não estar imediatamente disponível para restauro geo. Pode levar algumas horas para que os metadados necessários sejam povoados.

Para restaurar o servidor, execute o seguinte exemplo a partir do PowerShell:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Este exemplo cria um novo servidor chamado **mydemoserver-georestored** na região leste dos EUA que pertence ao **myresourcegroup**. É um General Purpose, servidor Gen 5 com 8 vCores. O servidor é criado a partir da cópia de segurança georedundantdo do **mydemoserver**, também no grupo de recursos **myresourcegroup**.

Para criar o novo servidor num grupo de recursos diferente do servidor existente, especifique o novo nome do grupo de recursos utilizando o parâmetro **ResourceGroupName,** como mostra o seguinte exemplo:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

O conjunto de **parâmetros GeoRestore** do `Restore-AzMariaDbServer` cmdlet requer os seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
|ResourceGroupName | myResourceGroup | O nome do grupo de recursos a que o novo servidor pertence.|
|Name | mydemoserver-georestauro | O nome do novo servidor. |
|Localização | eastus | A localização do novo servidor. |
|UseGeoRestore | `<SwitchParameter>` | Utilize o modo geopara restaurar. |

Ao criar um novo servidor utilizando georestauro, herda o mesmo tamanho de armazenamento e nível de preços que o servidor de origem, a menos que o parâmetro **Sku** seja especificado.

Após o final do processo de restauro, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de login de servidor e senha que era válido para o servidor existente no momento em que o restauro foi iniciado. A palavra-passe pode ser alterada a partir da página **de visão geral** do novo servidor.

O novo servidor criado durante uma restauração não tem os pontos finais do serviço VNet que existiam no servidor original. Estas regras devem ser configuradas separadamente para este novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Personalize a Base de Dados Azure para parâmetros do servidor MariaDB utilizando o PowerShell](howto-configure-server-parameters-using-powershell.md)
