---
title: Conectar dados do syslog à visualização do Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do syslog ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: dad74410562aa54aeb61675e7dc1c0adccc44797
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679214"
---
# <a name="connect-your-external-solution-using-syslog"></a>Conectar sua solução externa usando o syslog

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode conectar qualquer dispositivo local que dê suporte a syslog para o Azure Sentinel. Isso é feito usando um agente baseado em um computador Linux entre o dispositivo e o Azure Sentinel. Se seu computador Linux estiver no Azure, você poderá transmitir os logs de seu dispositivo ou aplicativo para um espaço de trabalho dedicado criado no Azure e conectá-lo. Se o seu computador Linux não estiver no Azure, você poderá transmitir os logs de seu dispositivo para uma VM local dedicada ou computador no qual você instala o agente para Linux. 

> [!NOTE]
> Se o seu dispositivo der suporte ao syslog CEF, a conexão será mais completa e você deverá escolher essa opção e seguir as instruções em [conectando dados do CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Como funciona

Syslog é um protocolo de registo de eventos que é comum para Linux. Aplicativos enviará as mensagens que podem ser armazenadas no computador local ou entregues a um recoletor de Syslog. Quando o agente de Log Analytics para Linux é instalado, ele configura o daemon do syslog local para encaminhar mensagens para o agente. Em seguida, o agente envia a mensagem para Azure Monitor onde um registro correspondente é criado.

Para obter mais informações, consulte [syslog Data Sources in Azure monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> O agente pode coletar logs de várias fontes, mas deve ser instalado no computador proxy dedicado.

## <a name="connect-your-syslog-appliance"></a>Conectar seu dispositivo syslog

1. No portal do Azure Sentinel, selecione **conectores de dados** e selecione a linha **syslog** na tabela e, no painel syslog à direita, clique em **abrir a página conector**.
2. Se seu computador Linux estiver no Azure, selecione **baixar e instalar o agente na máquina virtual Linux do Azure**. Na janela máquinas virtuais, selecione os computadores nos quais você deseja instalar o agente e clique em **conectar** na parte superior.
1. Se seu computador Linux não estiver no Azure, selecione **baixar e instalar o agente no computador Linux não Azure**. Na janela **agente direto** , copie o comando em **agente de download e integrado para Linux** e execute-o em seu computador. 
   > [!NOTE]
   > Certifique-se de configurar a segurança da máquina de acordo com a política de segurança da sua organização. Por exemplo, você pode configurar sua rede para se alinhar com sua política de segurança de rede corporativa e alterar as portas e protocolos no daemon para se alinhar com seus requisitos. 

1. Em **configurar os logs a serem conectados** na janela instalação do conector syslog, siga as instruções:
    1. Clique no link para **abrir a configuração de configurações avançadas do espaço de trabalho**. 
    1. Selecione **dados**, seguido pelo **syslog**.
    1. Em seguida, na tabela, defina os recursos que você deseja que o syslog colete. Você deve adicionar ou selecionar os recursos que seu dispositivo de syslog inclui em seus cabeçalhos de log. Você pode ver essa configuração em seu dispositivo de syslog no syslog-d na pasta:/etc/rsyslog.d/Security-config-omsagent.conf e em r-syslog em/etc/syslog-ng/Security-config-omsagent.conf. 
       > [!NOTE]
       > Se você marcar a caixa de seleção para **aplicar a configuração abaixo a meus computadores**, essa configuração será aplicada a todos os computadores Linux conectados a esse espaço de trabalho. Você pode ver essa configuração em seu computador syslog em 
1. Clique em **pressionar aqui para abrir a folha configuração**.
1. Selecione **dados** e **syslog**.
   - Verifique se cada recurso que você está enviando pelo syslog está na tabela. Para cada recurso, você vai monitorar, definir uma severidade. Clique em **Aplicar**.
1. Em seu computador syslog, certifique-se de que você está enviando essas instalações. 

1. Para usar o esquema relevante no Log Analytics para os logs de syslog, pesquise por **syslog**.
1. Você pode usar a função Kusto descrita em [usando funções em Azure monitor consultas de log](../azure-monitor/log-query/functions.md) para analisar suas mensagens de syslog e salvá-las como uma nova função de log Analytics e, em seguida, usar a função como um novo tipo de dados.




## <a name="next-steps"></a>Passos Seguintes
Neste documento, você aprendeu a conectar os dispositivos locais do syslog ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
