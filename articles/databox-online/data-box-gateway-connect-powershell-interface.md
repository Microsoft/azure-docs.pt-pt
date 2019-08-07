---
title: Conectar e gerenciar Microsoft Azure dispositivo de Gateway do Data Box por meio da interface do Windows PowerShell | Microsoft Docs
description: Descreve como se conectar ao e gerenciar o Gateway do Data Box por meio da interface do Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 650777d849e172686e8b46502a84db8c519174e7
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775201"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Gerenciar um dispositivo Gateway do Azure Data Box por meio do Windows PowerShell

Gateway do Azure Data Box solução permite que você envie dados pela rede para o Azure. Este artigo descreve algumas das tarefas de configuração e gerenciamento para seu dispositivo Gateway do Data Box. Você pode usar o portal do Azure, a interface do usuário da Web local ou a interface do Windows PowerShell para gerenciar seu dispositivo.

Este artigo se concentra nas tarefas que você faz usando a interface do PowerShell.

Este artigo inclui os seguintes procedimentos:

- Conectar-se à interface do PowerShell
- Criar um pacote de suporte
- Carregar certificado
- Inicializar em um ambiente não-DHCP
- Exibir informações do dispositivo

## <a name="connect-to-the-powershell-interface"></a>Conectar-se à interface do PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Inicializar em um ambiente não-DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Exibir informações do dispositivo

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Passos Seguintes

- Implemente o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.
