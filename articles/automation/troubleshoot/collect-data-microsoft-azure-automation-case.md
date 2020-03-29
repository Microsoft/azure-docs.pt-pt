---
title: Dados para recolher quando abrir um caso para a Microsoft Azure Automation. Microsoft Docs
description: Este artigo descreve algumas das informações básicas que deve recolher antes de abrir um caso para a Azure Automation com o Microsoft Azure Support.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849382"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Dados a recolher quando abre um incidente da Automatização do Microsoft Azure

Este artigo descreve algumas das informações básicas que deve recolher antes de abrir um caso para a Azure Automation com o Microsoft Azure Support. Esta informação não é necessária para abrir o caso. No entanto, pode ajudar a Microsoft a resolver o seu problema mais rapidamente. Além disso, pode ser-lhe pedido estes dados pelo engenheiro de suporte depois de abrir o caso.

## <a name="collect-more-information"></a>Recolher mais informações

Antes de abrir um caso para o Suporte de Automação Microsoft Azure, recomendamos que recolha as seguintes informações.

### <a name="basic-data-collection"></a>Recolha de dados básicos

Recolher os dados descritos no seguinte artigo da Base de Conhecimento:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Como capturar diagnósticos escritos pela Automação Azure

## <a name="collect-data-depending-on-issue"></a>Recolher dados dependendo do problema
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Para questões que afetam a Gestão de Atualizações no Linux

1. Além dos itens listados no KB [4034605,](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)execute a seguinte ferramenta de recolha de registos:

   [OMS Linux Agent Log Collector](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Comprima o conteúdo da seguinte pasta e, em seguida, envie o ficheiro comprimido para o Suporte Azure:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Verifique se o ID do espaço de trabalho a que o Agente OMS Linux está a reportar, é o mesmo que o espaço de trabalho que está a ser monitorizado para atualizações.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Para problemas que afetam a Gestão de Atualizações no Windows

Para além dos itens listados no [4034605,](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)faça o seguinte:

1. Exportar os seguintes registos de eventos para o formato EVTX:

   * Sistema
   * Aplicação
   * Segurança
   * Operations Manager
   * Microsoft-SMA/Operacional

2. Verifique se o ID do espaço de trabalho a que o agente está a reportar é o mesmo que o espaço de trabalho que está a ser monitorizado pelas Atualizações do Windows.

### <a name="for-issues-that-affect-a-job"></a>Para questões que afetam um trabalho

Além dos itens listados no [4034605,](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)recolher as seguintes informações:

1. Recolher o número **JobID** (para o trabalho que está a passar por um problema):

   1. No portal Azure, vá à lâmina das Contas de **Automação.**
   2. Selecione a **Conta de Automação** que está a resolver problemas.
   3. Selecione **Jobs**.
   4. Selecione o trabalho que está a resolver.
   5. Under **Job Sumário,** procure um ID de **trabalho** (GUID).

   ![ID de trabalho dentro do Painel De Resumo de Emprego](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Recolher o nome da conta:

   1. No portal Azure, vá à lâmina das Contas de **Automação.**
   2. Arranja o nome da **Conta de Automação** que estás a resolver problemas.

3. Recolha a amostra do guião que está a executar.

4. Recolher os ficheiros de registo:

   1. No portal Azure, vá à lâmina das Contas de **Automação.**
   2. Selecione a **Conta de Automação** que está a resolver problemas.
   3. Selecione **Jobs**.
   4. Selecione o trabalho que está a resolver.
   5. Selecione **Todos os Registos**.
   6. Na lâmina resultante, recolha os dados.

   ![Dados listados em Todos os Registos](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>Para problemas que afetam módulos

Para além dos itens em "Recolha de dados básicos", recolher as seguintes informações:

* Os passos que seguiu para que o problema possa ser reproduzido.
* Uma imagem de qualquer mensagem de erro.
* Uma imagem dos módulos atuais e dos seus números de versão.

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda em algum ponto deste artigo, contacte os especialistas da Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

Em alternativa, apresente um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
