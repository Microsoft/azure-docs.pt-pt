---
title: Utilize o Windows PowerShell para ligar e gerir o dispositivo Azure Data Box Gateway
description: Descreve como ligar e, em seguida, gerir o Data Box Gateway através da interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: c071d372ba90d29806fd8a44909e2c803a8d3fa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84339285"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Gerir um dispositivo Azure Data Box Gateway via Windows PowerShell

A solução Azure Data Box Gateway permite-lhe enviar dados sobre a rede para o Azure. Este artigo descreve algumas das tarefas de configuração e gestão do seu dispositivo Data Box Gateway. Pode utilizar o portal Azure, a web UI local ou a interface Windows PowerShell para gerir o seu dispositivo.

Este artigo centra-se nas tarefas que faz utilizando a interface PowerShell.

Este artigo inclui os seguintes procedimentos:

- Ligue-se à interface do PowerShell
- Criar um pacote de suporte
- Carregar certificado
- Arranque em ambiente não-DHCP
- Ver informações do dispositivo

## <a name="connect-to-the-powershell-interface"></a>Ligue-se à interface do PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Arranque em ambiente não-DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Ver informações do dispositivo

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Passos seguintes

- Implemente o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.
