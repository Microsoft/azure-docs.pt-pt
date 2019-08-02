---
title: Transfira o Azure SDK para PHP
description: Saiba como baixar e instalar o SDK do Azure para PHP.
documentationcenter: php
services: app-service\web
author: allclark
manager: douge
editor: ''
ms.assetid: bac355ac-4c25-42f4-8273-c5112eafa8d4
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/01/2016
ms.author: yaya
ms.openlocfilehash: 9dd336ef973b603108aad62c90e4dab385d20317
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "67873886"
---
# <a name="download-the-azure-sdk-for-php"></a>Transfira o Azure SDK para PHP

## <a name="overview"></a>Descrição geral

O SDK do Azure para PHP inclui componentes que permitem que você desenvolva, implante e gerencie aplicativos PHP para o Azure. Especificamente, o SDK do Azure para PHP inclui o seguinte:

* **As bibliotecas de cliente php para Azure**. Essas bibliotecas de classes fornecem uma interface para acessar recursos do Azure, como serviços de gerenciamento de dados e serviços de nuvem.
* **A interface de linha de comando do Azure para Mac, Linux e Windows (CLI do Azure)** . Este é um conjunto de comandos para implantar e gerenciar serviços do Azure, como sites do Azure e máquinas virtuais do Azure. O CLI do Azure funciona em qualquer plataforma, incluindo Mac, Linux e Windows.
* **Azure PowerShell (somente Windows)** . Este é um conjunto de cmdlets do PowerShell para implantar e gerenciar serviços do Azure, como serviços de nuvem e máquinas virtuais.
* **Os emuladores do Azure (somente Windows)** . Os emuladores de computação e armazenamento são emuladores locais de serviços de nuvem e serviços de gerenciamento de dados que permitem testar um aplicativo localmente. Os emuladores do Azure são executados somente no Windows.

As seções a seguir descrevem como baixar e instalar os componentes descritos acima.

As instruções neste tópico pressupõem que você tenha o [php][install-php] instalado.

> [!NOTE]
> Você deve ter o PHP 5,5 ou superior para usar as bibliotecas de cliente PHP para o Azure.
>
>

## <a name="php-client-libraries-for-azure"></a>Bibliotecas de cliente PHP para Azure

As bibliotecas de cliente PHP para Azure fornecem uma interface para acessar recursos do Azure, como serviços de gerenciamento de dados e serviços de nuvem, de qualquer sistema operacional. Essas bibliotecas podem ser instaladas por meio do Composer.

Para obter informações sobre como usar as bibliotecas de cliente PHP para o Azure, consulte [como usar o serviço blob][blob-service], [como usar o serviço tabela][table-service] e [como usar o serviço fila][queue-service].

### <a name="install-via-composer"></a>Instalar por meio do Composer

1. [Instalar o Git][install-git]. No Windows, também será necessário adicionar o Git executável à variável de ambiente PATH.

2. Crie um arquivo chamado **Composer. JSON** na raiz do seu projeto e adicione o seguinte código a ele:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Baixe o **[Composer. Phar][composer-phar]** na raiz do projeto.

4. Abra um prompt de comando e execute-o na raiz do projeto

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell e emuladores do Azure

Azure PowerShell é um conjunto de cmdlets do PowerShell para implantar e gerenciar serviços do Azure (como serviços de nuvem e máquinas virtuais). Os emuladores do Azure são emuladores de serviços de nuvem e de gerenciamento de dados que permitem testar um aplicativo localmente. Esses componentes são somente Windows com suporte.

A maneira recomendada para instalar Azure PowerShell e os emuladores do Azure é usar o [Microsoft Web Platform Installer][download-wpi]. Observe que você também pode optar por instalar outros componentes de desenvolvimento, como PHP, SQL Server, os drivers da Microsoft para SQL Server para PHP e WebMatrix.

Para obter informações sobre como usar Azure PowerShell, consulte [como usar Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>CLI do Azure

O CLI do Azure é um conjunto de comandos para implantar e gerenciar serviços do Azure, como sites do Azure e máquinas virtuais do Azure. Para obter informações sobre como instalar o CLI do Azure, consulte [instalar o CLI do Azure](cli-install-nodejs.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte a [central de desenvolvedores do PHP](https://azure.microsoft.com/develop/php/).

[install-php]: https://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: https://getcomposer.org/composer.phar
[nodejs-org]: https://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: https://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: https://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: https://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: https://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: https://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: https://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: https://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: https://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
