---
title: Transfira o Azure SDK para PHP
description: Saiba como transferir e instalar o Azure SDK para PHP.
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
ms.author: allclark;yaqiyang
ms.openlocfilehash: f6b21f288b94e06414fe66ff775ebb264368c0b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411573"
---
# <a name="download-the-azure-sdk-for-php"></a>Transfira o Azure SDK para PHP

## <a name="overview"></a>Descrição geral

O Azure SDK para PHP inclui componentes que permitem desenvolver, implementar e gerir aplicações do PHP para o Azure. Especificamente, o Azure SDK para PHP inclui o seguinte:

* **As bibliotecas de cliente PHP para Azure**. Estas bibliotecas de classe fornecem uma interface para aceder a funcionalidades do Azure, como os serviços de gestão de dados e serviços em nuvem.
* **A Interface de linha de comandos do Azure para Mac, Linux e Windows (CLI do Azure)** . Este é um conjunto de comandos para implementar e gerir serviços do Azure, como Web sites do Azure e máquinas virtuais do Azure. O trabalho de CLI do Azure em qualquer plataforma, incluindo a Mac, Linux e Windows.
* **O Azure PowerShell (apenas Windows)** . Este é um conjunto de cmdlets do PowerShell para implementar e gerir serviços do Azure, como serviços Cloud e máquinas virtuais.
* **Os emuladores do Azure (apenas Windows)** . Os emuladores de computação e armazenamento são emuladores locais de serviços cloud e serviços de gestão de dados que lhe permitem testar uma aplicação localmente. Os emuladores do Azure executados apenas no Windows.

As secções abaixo descrevem como transferir e instalar os componentes descritos acima.

As instruções neste tópico partem do princípio de que tenha [PHP] [ install-php] instalado.

> [!NOTE]
> Tem de ter PHP 5.5 ou superior para utilizar as bibliotecas de cliente PHP para o Azure.
>
>

## <a name="php-client-libraries-for-azure"></a>Bibliotecas de cliente PHP para Azure

As bibliotecas de cliente PHP para Azure fornecem uma interface para aceder a funcionalidades do Azure, como os serviços de gestão de dados e serviços em nuvem, de qualquer sistema operativo. Essas bibliotecas podem ser instaladas através do compositor.

Para obter informações sobre como utilizar as bibliotecas de cliente PHP para o Azure, consulte [como utilizar o serviço Blob][blob-service], [como utilizar o serviço de tabelas] [ table-service]e [como utilizar o serviço de fila][queue-service].

### <a name="install-via-composer"></a>Instalar através do compositor

1. [Instalar o Git][install-git]. No Windows, também terá de adicionar o Git executável a variável de ambiente PATH.

2. Crie um ficheiro denominado **Composer. JSON** na raiz do seu projeto e adicione o seguinte código ao mesmo:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Baixe **[composer.phar] [ composer-phar]** na raiz do projeto.

4. Abra um prompt de comando e executá-lo na raiz do projeto

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>O Azure PowerShell e emuladores do Azure

O Azure PowerShell é um conjunto de cmdlets do PowerShell para implementar e gerir serviços do Azure (por exemplo, serviços Cloud e máquinas virtuais). Os emuladores do Azure são emuladores de serviços cloud e serviços de gestão de dados que lhe permitem testar uma aplicação localmente. Esses componentes são suportados apenas o Windows.

A forma recomendada para instalar o Azure PowerShell e os emuladores do Azure está a utilizar o [Microsoft Web Platform Installer][download-wpi]. Tenha em atenção que também pode optar por instalar outros componentes de desenvolvimento, como PHP, SQL Server, os Drivers Microsoft para o SQL Server para o PHP e o WebMatrix.

Para obter informações sobre como utilizar o Azure PowerShell, consulte [como utilizar o Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>CLI do Azure

A CLI do Azure é um conjunto de comandos para implementar e gerir serviços do Azure, como Web sites do Azure e máquinas virtuais do Azure. Para obter informações sobre como instalar a CLI do Azure, consulte [instalar a CLI do Azure](cli-install-nodejs.md).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte a [Centro de programadores PHP](https://azure.microsoft.com/develop/php/).

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
