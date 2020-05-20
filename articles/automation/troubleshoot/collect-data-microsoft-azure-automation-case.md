---
title: Dados a recolher ao abrir um caso para a Microsoft Azure Automation [ Automação Azul] Microsoft Docs
description: Este artigo descreve as informações a recolher antes de abrir um caso para a Automação Azure com o Suporte Microsoft Azure.
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
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684839"
---
# <a name="data-to-collect-when-opening-a-case-for-microsoft-azure-automation"></a>Dados a recolher ao abrir um caso para a Microsoft Azure Automation

Este artigo descreve algumas das informações que deve recolher antes de abrir um caso para a Azure Automation com o Microsoft Azure Support. Esta informação não é necessária para abrir o caso. No entanto, pode ajudar a Microsoft a resolver o seu problema mais rapidamente. Além disso, pode ser-lhe pedido estes dados pelo engenheiro de suporte depois de abrir o caso.

## <a name="basic-data"></a>Dados básicos

Recolher os dados básicos descritos no artigo [4034605 da](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)Base de Conhecimento - Como capturar diagnósticos escritos pela Automação Azure .

## <a name="data-for-update-management-issues-on-linux"></a>Dados para problemas de Gestão de Atualizações no Linux

1. Além dos itens listados no KB [4034605,](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)execute a seguinte ferramenta de recolha de registos:

   [OMS Linux Agent Log Collector](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Comprima o conteúdo da pasta **/var/opt/microsoft/omsagent/run/automationworker/** e, em seguida, envie o ficheiro comprimido para o Suporte Azure.
 
3. Verifique se o ID para o espaço de trabalho a que o agente Log Analytics para o Linux reporta é o mesmo que o ID para o espaço de trabalho que está a ser monitorizado para atualizações.

## <a name="data-for-update-management-issues-on-windows"></a>Dados para problemas de Gestão de Atualizações no Windows

1. Recolher dados relativos aos itens listados em [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Exportar os seguintes registos de eventos para o formato EVTX:

   * Sistema
   * Aplicação
   * Segurança
   * Operations Manager
   * Microsoft-SMA/Operacional

3. Verifique se a identificação do espaço de trabalho a que o agente reporta é a mesma que o ID para o espaço de trabalho que está a ser monitorizado pelas Atualizações do Windows.

## <a name="data-for-job-issues"></a>Dados relativos a questões de emprego

1. Recolher dados relativos aos itens listados em [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Recolha a identificação do trabalho para o trabalho que tem um problema:

   1. No portal Azure, vá para **Contas de Automação.**
   2. Selecione a conta Automation que está a resolver problemas e note o nome.
   3. Selecione **Jobs**.
   4. Escolha o trabalho que está a resolver.
   5. No painel de resumo de trabalho, procure o valor GUID no ID de **trabalho**.

   ![ID de trabalho dentro do Painel De Resumo de Emprego](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Recolha uma amostra do guião que está a executar.

4. Recolher os ficheiros de registo:

   1. No portal Azure, vá para **Contas de Automação.**
   2. Selecione a conta Automation que está a resolver problemas.
   3. Selecione **Jobs**.
   4. Escolha o trabalho que está a resolver.
   5. Selecione **Todos os Registos**.
   6. No painel resultante, recolha os dados.

   ![Dados listados em Todos os Registos](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Dados para problemas de módulos

Para além dos [dados básicos,](#basic-data)recolher as seguintes informações:

* Os passos que seguiu, para que o problema possa ser reproduzido.
* Imagens de quaisquer mensagens de erro.
* Imagens dos módulos atuais e dos seus números de versão.

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Arquiva um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
