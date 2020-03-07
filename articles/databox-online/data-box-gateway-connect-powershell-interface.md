---
title: Utilize o Windows PowerShell para ligar e gerir o dispositivo Gateway da Caixa de Dados Azure
description: Descreve como se conectar e, em seguida, gerir o Portal da Caixa de Dados através da interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 6c9f3455a07001a8d1b9b8a1e84f2af3392b5690
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384596"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Gerir um dispositivo De Gateway de Caixa de Dados Azure via Windows PowerShell

A solução Azure Data Box Gateway permite-lhe enviar dados pela rede para o Azure. Este artigo descreve algumas das tarefas de configuração e gestão do seu dispositivo Data Box Gateway. Pode utilizar o portal Azure, a Web UI local ou a interface Windows PowerShell para gerir o seu dispositivo.

Este artigo centra-se nas tarefas que faz utilizando a interface PowerShell.

Este artigo inclui os seguintes procedimentos:

- Ligue-se à interface PowerShell
- Criar um pacote de apoio
- Carregar certificado
- Arranque em ambiente não DHCP
- Ver informações do dispositivo

## <a name="connect-to-the-powershell-interface"></a>Ligue-se à interface PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de apoio

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Arranque em ambiente não DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Ver informações do dispositivo

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Passos seguintes

- Implemente o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.
