---
title: Implementar o Centro de segurança do Azure para o módulo do IoT Edge | Documentos da Microsoft
description: Saiba mais sobre como implementar o Centro de segurança do Azure para o agente de segurança de IoT no IoT Edge.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 2a201fe649d52ad9604c7ac6675b26d60e7f2dd1
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754767"
---
# <a name="deploy-security-module-on-your-iot-edge-device"></a>Implementar o módulo de segurança no seu dispositivo IoT Edge

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Centro de segurança do Azure (ASC) do IoT **azureiotsecurity** módulo fornece uma solução de segurança abrangente para o seu dispositivo IoT Edge.
Módulo de segurança recolhe, agrega e analisa os dados de segurança bruto do seu sistema de sistema operativo e um contentor em alertas e recomendações de segurança acionáveis.
Para obter mais informações, consulte [módulo de segurança para o IoT Edge](security-edge-architecture.md).

Neste guia, irá aprender a implementar um módulo de segurança no seu dispositivo IoT Edge.

## <a name="deploy-security-module"></a>Implementar o módulo de segurança

Utilize os seguintes passos para implementar um ASC para o módulo de segurança de IoT para o IoT Edge.

### <a name="prerequisites"></a>Pré-requisitos

1. Certifique-se de que o dispositivo está [registado como um dispositivo IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device-portal).

1. O ASC para o módulo do IoT Edge requer o [AuditD framework](https://linux.die.net/man/8/auditd) instalada no dispositivo do Edge.

   Instale o framework executando o seguinte comando no seu dispositivo Edge:
   
   `apt-get install auditd audispd-plugins`

### <a name="deployment-using-azure-portal"></a>Implementação com o portal do Azure

1. Open **Marketplace** no portal do Azure.

1. Procure **segurança de iot do azure** e clique em **segurança de IoT do Azure**.

   ![](media/howto/edge_onboarding_7.png)

1. Clique em **Criar**.

1. Escolha sua **subscrição**, **IoT Hub** e **nome de dispositivo do IoT Edge**, em seguida, clique em **criar**.

1. Clique em **próxima** duas vezes para **revisão implementação**.

1. Certifique-se **edgeHub.settings.createOptions** está configurada da seguinte forma:

   `"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"`

1. Clique em **submeter** para concluir a implementação.


## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as opções de configuração, avance para o guia de procedimentos de configuração do módulo. 
> [!div class="nextstepaction"]
> [Configuração do módulo como guia](./how-to-agent-configuration.md)
