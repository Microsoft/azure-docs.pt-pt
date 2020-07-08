---
title: Instale & implementar o agente Linux C
description: Saiba como instalar o Centro de Segurança Azure para agente IoT em Linux de 32 e 64 bits.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: d9f9602a19a266c70b17422e90566f72de2978f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311194"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Implementar um agente de segurança baseado em C do Centro de Segurança do Azure para IoT para o Linux

Este guia explica como instalar e implantar o Centro de Segurança Azure para o agente de segurança baseado em IoT C no Linux.

Neste guia, ficará a saber como:

> [!div class="checklist"]
> * Instalar
> * Verificar a implementação
> * Desinstalar o agente
> * Resolução de problemas

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e sabores de agente, consulte [Escolha o agente de segurança certo.](how-to-deploy-agent.md)

1. Para implantar o agente de segurança, são necessários direitos de administração locais na máquina que pretende instalar (sudo).

1. [Crie um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação

Para instalar e implantar o agente de segurança, utilize o seguinte fluxo de trabalho:

1. Descarregue a versão mais recente para a sua máquina a partir do [GitHub.](https://aka.ms/iot-security-github-c)

1. Extrair o conteúdo da embalagem e navegar para a pasta _/src/instalação._

1. Adicione permissões de execução ao **script InstallSecurityAgent** executando o seguinte comando:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Em seguida, corra:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Consulte [como configurar a autenticação](concept-security-agent-authentication-methods.md) para obter mais informações sobre parâmetros de autenticação.

Este script executa a seguinte função:

1. Instala pré-requisitos.

1. Adiciona um utilizador de serviço (com sinal interativo em desativado).

1. Instala o agente como Um **Daemon** - assume que o dispositivo utiliza **sistema para** gestão de serviços.

1. Configura o agente com os parâmetros de autenticação fornecidos.

Para obter ajuda adicional, execute o script com o parâmetro de ajuda:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com o parâmetro -desinstalar:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Resolução de problemas

Verifique o estado de implantação executando:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Próximos passos

- Leia o Centro de Segurança Azure para o serviço IoT [Visão geral](overview.md)
- Saiba mais sobre o Azure Security Center for IoT [Architecture](architecture.md)
- Ativar o [serviço](quickstart-onboard-iot-hub.md)
- Leia as [FAQ](resources-frequently-asked-questions.md)
- Compreender [alertas de segurança](concept-security-alerts.md)
