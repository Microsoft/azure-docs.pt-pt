---
title: Abra e guarde ficheiros com pacotes SSIS implantados no Azure
description: Aprenda a abrir e guardar ficheiros nas instalações e no Azure quando levantar e transferir pacotes SSIS que utilizam sistemas de ficheiros locais para SSIS em Azure
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: a2b95e0de642dc17865643ec569e7220fe5581b2
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637501"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Abra e guarde ficheiros nas instalações e no Azure com pacotes SSIS implantados no Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como abrir e guardar ficheiros nas instalações e no Azure quando levanta e transfere pacotes SSIS que utilizam sistemas de ficheiros locais para SSIS em Azure.

## <a name="save-temporary-files"></a>Guardar ficheiros temporários

Se necessitar de armazenar e processar ficheiros temporários durante uma única execução de pacotes, as embalagens podem utilizar o diretório de trabalho atual `.` () ou pasta temporária `%TEMP%` () dos seus nós de runtime de integração Azure-SSIS.

## <a name="use-on-premises-file-shares"></a>Utilizar ações de ficheiros no local

Para continuar a utilizar **ações de ficheiros no local** quando levantar e transferir pacotes que utilizam sistemas de ficheiros locais para SSIS em Azure, faça as seguintes coisas:

1. Transfira ficheiros de sistemas de ficheiros locais para ações de ficheiros no local.

2. Junte-se às ações de ficheiros no local para uma rede virtual Azure.

3. Junte-se ao seu Azure-SSIS IR na mesma rede virtual. Para obter mais informações, consulte [Junte-se a um tempo de integração Azure-SSIS a uma rede virtual.](./join-azure-ssis-integration-runtime-virtual-network.md)

4. Ligue o seu Azure-SSIS IR às partilhas de ficheiros no local dentro da mesma rede virtual, criando credenciais de acesso que utilizam a autenticação do Windows. Para obter mais informações, consulte [Ligar aos dados e partilhar ficheiros com a Autenticação do Windows.](ssis-azure-connect-with-windows-auth.md)

5. Atualize os caminhos de arquivo locais nos seus pacotes para caminhos unc apontando para ações de ficheiros no local. Por exemplo, atualizar `C:\abc.txt` para `\\<on-prem-server-name>\<share-name>\abc.txt` .

## <a name="use-azure-file-shares"></a>Use ações de ficheiros Azure

Para utilizar **ficheiros Azure** quando levantar e transferir pacotes que utilizam sistemas de ficheiros locais para SSIS em Azure, faça as seguintes coisas:

1. Transfira ficheiros dos sistemas de ficheiros locais para ficheiros Azure. Para mais informações, consulte [os Ficheiros Azure.](https://azure.microsoft.com/services/storage/files/)

2. Ligue o seu Azure-SSIS IR aos Ficheiros Azure, configurando credenciais de acesso que utilizam a autenticação do Windows. Para obter mais informações, consulte [Ligar aos dados e partilhar ficheiros com a Autenticação do Windows.](ssis-azure-connect-with-windows-auth.md)

3. Atualize os caminhos de ficheiros locais nos seus pacotes para caminhos unc apontando para ficheiros Azure. Por exemplo, atualizar `C:\abc.txt` para `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt` .

## <a name="next-steps"></a>Passos seguintes

- Desloquem os vossos pacotes. Para mais informações, consulte [implementar um projeto SSIS para Azure com SSMS.](/sql/integration-services/ssis-quickstart-deploy-ssms)
- Coloquem os seus pacotes. Para mais informações, consulte [os pacotes Run SSIS em Azure com SSMS](/sql/integration-services/ssis-quickstart-run-ssms).
- Agende os seus pacotes. Para mais informações, consulte [os pacotes Schedule SSIS em Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).