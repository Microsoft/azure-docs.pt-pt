---
title: Implementar consola de gestão no Azure Defender para IoT
description: Saiba como implementar a consola de gestão no Azure Defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: 6af943951b2dba0c4a11b08ad1bcd961cf1c0fc2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094536"
---
# <a name="deploy-the-management-console"></a>Implementar a consola de gestão
Este artigo descreve como implementar o Azure Defender para a consola de gestão IoT no local.

## <a name="the-on-premises-management-console"></a>A consola de gestão no local

A consola de gestão no local proporciona uma visão consolidada de todos os ativos da rede e oferece uma visão em tempo real dos principais indicadores e alertas de risco IoT e OT em todas as suas instalações. Fortemente integrado com os seus fluxos de trabalho SOC e livros de execução, permite uma priorização fácil de atividades de mitigação e correlação de ameaças entre locais.

- Holistic - reduza a complexidade com uma única plataforma unificada para a gestão de ativos, gestão de riscos e vulnerabilidades, bem como monitorização de ameaças com resposta a incidentes.

- Agregação e correlação – mostrar, agregar e analisar dados e alertas recolhidos de todos os sites.

- Controlar todos os sensores – configurar e monitorizar todos os sensores a partir de um único local.

   ![Azure Defender para a visão de IoT Site Management](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>Implementar o aparelho de consola de gestão no local

Este processo requer a aquisição do seu próprio hardware e a instalação de software. A solução funciona em aparelhos certificados. Consulte o guia de especificações de [hardware IoT do Azure Defender](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) como referência na compra do seu aparelho certificado.

Consulte o [Azure Defender para obter](https://aka.ms/AzureDefenderforIoTInstallSensorISO) informações sobre o descarregamento da imagem ISO e a instalação do software do sensor.

**Para implantar a consola de gestão no local:**

1. Navegue para o Microsoft Azure Defender para IoT.

2. Selecione **consola de gestão no local.**

   ![Azure Defender para a visão da consola de gestão de IoT on-premises](media/updates/image15.png)

3. Selecione a versão 3.1 do menu **Select Version.**

4. Selecione **Baixar** e guardar o ficheiro.

5. Depois de instalado o software, efetua tarefas de configuração de rede. Consulte o [Azure Defender para o Guia de Configuração da Rede IoT](https://aka.ms/AzureDefenderForIoTNetworkSetup) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as opções de configuração, continue a guiar como fazer para a configuração do módulo.
> [!div class="nextstepaction"]
> [Configuração do módulo como guiar](./how-to-agent-configuration.md)
