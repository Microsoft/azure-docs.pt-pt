---
title: Abrir e guardar ficheiros com pacotes SSIS implantados no Azure
description: Saiba como abrir e guardar ficheiros nas instalações e no Azure quando levantar e transferir pacotes SSIS que utilizam sistemas de ficheiros locais para o SSIS em Azure
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 630a8ead409ad60b9146838f062210a3301b43b5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760202"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Abrir e guardar ficheiros nas instalações e em Azure com pacotes SSIS implantados no Azure

Este artigo descreve como abrir e guardar ficheiros nas instalações e no Azure quando levanta e transfere pacotes SSIS que utilizam sistemas de ficheiros locais para o SSIS em Azure.

## <a name="save-temporary-files"></a>Guardar ficheiros temporários

Se necessitar de armazenar e processar ficheiros temporários durante uma única execução de pacote, os pacotes podem utilizar o atual diretório de trabalho (`.`) ou pasta temporária ()`%TEMP%`dos seus nós de funcionamento de integração Azure-SSIS.

## <a name="use-on-premises-file-shares"></a>Utilizar ações de ficheiros no local

Para continuar a utilizar **as partilhas de ficheiros no local** quando levantar e transferir pacotes que utilizam sistemas de ficheiros locais para o SSIS em Azure, faça as seguintes coisas:

1. Transferir ficheiros dos sistemas de ficheiros locais para as ações de ficheiros no local.

2. Junte-se às ações de arquivo no local para uma rede virtual Azure.

3. Junte-se ao seu IR Azure-SSIS na mesma rede virtual. Para mais informações, consulte Adere a um tempo de execução de [integração Azure-SSIS para uma rede virtual.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

4. Ligue o seu IR Azure-SSIS às partilhas de ficheiros no local dentro da mesma rede virtual, configurando credenciais de acesso que utilizam a autenticação do Windows. Para mais informações, consulte Connect a dados e partilhas de [ficheiros com a Autenticação do Windows](ssis-azure-connect-with-windows-auth.md).

5. Atualize os caminhos de ficheiros locais nos seus pacotes para caminhos da UNC que apontam para partilhas de ficheiros no local. Por exemplo, `C:\abc.txt` `\\<on-prem-server-name>\<share-name>\abc.txt`atualizar para .

## <a name="use-azure-file-shares"></a>Use ações de ficheiros Azure

Para utilizar **ficheiros Azure** quando levantar e transferir pacotes que utilizam sistemas de ficheiros locais para o SSIS em Azure, faça as seguintes coisas:

1. Transferir ficheiros dos sistemas de ficheiros locais para ficheiros Azure. Para mais informações, consulte [Ficheiros Azure](https://azure.microsoft.com/services/storage/files/).

2. Ligue o seu IR Azure-SSIS a Ficheiros Azure, configurando credenciais de acesso que utilizam a autenticação do Windows. Para mais informações, consulte Connect a dados e partilhas de [ficheiros com a Autenticação do Windows](ssis-azure-connect-with-windows-auth.md).

3. Atualize os caminhos de ficheiros locais nos seus pacotes para caminhos unc apontando para Ficheiros Azure. Por exemplo, `C:\abc.txt` `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt`atualizar para .

## <a name="next-steps"></a>Passos seguintes

- Coloque os seus pacotes. Para mais informações, consulte [Implementar um projeto SSIS para Azure com SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Executem os vossos pacotes. Para mais informações, consulte [os pacotes Run SSIS em Azure com SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Agende os seus pacotes. Para mais informações, consulte os [pacotes Schedule SSIS em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).
