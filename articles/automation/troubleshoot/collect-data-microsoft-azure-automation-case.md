---
title: Dados a recolher ao abrir um caso para a Microsoft Azure Automation Microsoft Docs
description: Este artigo descreve as informações a recolher antes de abrir um caso para a Azure Automation com o Microsoft Azure Support.
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
ms.openlocfilehash: d2f97d0b889186324aef9613847e3eddbfe1eb02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83684839"
---
# <a name="data-to-collect-when-opening-a-case-for-microsoft-azure-automation"></a>Dados a recolher quando abre um incidente da Automatização do Microsoft Azure

Este artigo descreve algumas das informações que deve recolher antes de abrir um caso para a Azure Automation com o Microsoft Azure Support. Esta informação não é necessária para abrir o caso. No entanto, pode ajudar a Microsoft a resolver o seu problema mais rapidamente. Além disso, pode ser-lhe pedido estes dados pelo engenheiro de suporte depois de abrir o caso.

## <a name="basic-data"></a>Dados básicos

Recolher os dados básicos descritos no artigo [4034605 da Base de Conhecimento - Como capturar diagnósticos scripted Azure Automation](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Dados para problemas de gestão de atualização no Linux

1. Para além dos itens listados no KB [4034605,](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)execute a seguinte ferramenta de recolha de registos:

   [Colecionador de registo de agente OMS Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Comprima o conteúdo da pasta **/var/opt/microsoft/omsagent/run/automationr/pasta,** em seguida, envie o ficheiro comprimido para suporte Azure.
 
3. Verifique se o ID para o espaço de trabalho que o agente Log Analytics do Linux reporta é o mesmo que o ID para o espaço de trabalho que está a ser monitorizado para atualizações.

## <a name="data-for-update-management-issues-on-windows"></a>Dados para problemas de Gestão de Atualização no Windows

1. Recolher dados para os itens listados em [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Exporte os seguintes registos de eventos para o formato EVTX:

   * Sistema
   * Aplicação
   * Segurança
   * Operations Manager
   * Microsoft-SMA/Operacional

3. Verifique se o ID do espaço de trabalho a que o agente reporta é o mesmo que o ID para o espaço de trabalho que está a ser monitorizado pelo Windows Updates.

## <a name="data-for-job-issues"></a>Dados para questões de emprego

1. Recolher dados para os itens listados em [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Recolha a iD do trabalho para o trabalho que tem um problema:

   1. No portal Azure, aceda às **Contas de Automação.**
   2. Selecione a conta Automation que está a resolver problemas e tome nota do nome.
   3. Selecione **Jobs**.
   4. Escolha o trabalho que está a resolver problemas.
   5. No painel de resumo de trabalho, procure o valor GUID em **Job ID**.

   ![ID de trabalho dentro do painel de resumo de trabalho](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Recolha uma amostra do guião que está a executar.

4. Recolher os ficheiros de registo:

   1. No portal Azure, aceda às **Contas de Automação.**
   2. Selecione a conta Automation que está a resolver problemas.
   3. Selecione **Jobs**.
   4. Escolha o trabalho que está a resolver problemas.
   5. Selecione **Todos os Registos**.
   6. No painel resultante, recolha os dados.

   ![Dados listados em Todos os Registos](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Dados para problemas com módulos

Para além dos [dados básicos,](#basic-data)reúna as seguintes informações:

* Os passos que seguiu, para que o problema possa ser reproduzido.
* Imagens de quaisquer mensagens de erro.
* Screenshots dos módulos atuais e os seus números de versão.

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda:

* Obtenha respostas de especialistas da Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se [@AzureSupport](https://twitter.com/azuresupport) com, a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Arquive um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.
