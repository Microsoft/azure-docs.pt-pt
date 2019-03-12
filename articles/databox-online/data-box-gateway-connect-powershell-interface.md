---
title: Ligar e gerir o dispositivo de Gateway de caixa de dados do Microsoft Azure através da interface do Windows PowerShell | Documentos da Microsoft
description: Descreve como ligar a e, em seguida, gerir o Gateway de caixa de dados através da interface do Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 0ca570235ac2a87b62c5d0fcebbd24dc5186e37d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555106"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Gerir um dispositivo de Gateway de caixa de dados do Azure através do Windows PowerShell

Solução de Gateway de caixa de dados do Azure permite-lhe enviar dados através da rede para o Azure. Este artigo descreve algumas das tarefas de configuração e gestão para o seu dispositivo de Gateway de caixa de dados. Pode utilizar o portal do Azure, da IU web local ou a interface do Windows PowerShell para gerir o seu dispositivo.

Este artigo se concentra nas tarefas que fizer através da interface do PowerShell.

Este artigo inclui os seguintes procedimentos:

- Ligar a interface do PowerShell
- Iniciar uma sessão de suporte
- Criar um pacote de suporte
- Carregar certificado
- Efetuar o arranque no ambiente de não-DHCP
- Ver informações de dispositivo

> [!IMPORTANT]
> O Gateway de caixa de dados do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connect-to-the-powershell-interface"></a>Ligar a interface do PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="start-a-support-session"></a>Iniciar uma sessão de suporte

[!INCLUDE [Connect to support runspace](../../includes/data-box-edge-gateway-connect-support.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Efetuar o arranque no ambiente de não-DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Ver informações de dispositivo

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Passos Seguintes

- Implemente o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.
