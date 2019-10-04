---
title: Dados a serem coletados quando você abre um caso para Microsoft Azure automação | Microsoft Docs
description: Este artigo descreve algumas das informações básicas que você deve coletar antes de abrir um caso para a automação do Azure com suporte Microsoft Azure.
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
ms.openlocfilehash: 83ee78d369af7fe99de8e7236fe1eb0bc63a942f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846683"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Dados a serem coletados quando você abre um caso para Microsoft Azure automação

Este artigo descreve algumas das informações básicas que você deve coletar antes de abrir um caso para a automação do Azure com suporte Microsoft Azure. Essas informações não são necessárias para abrir o caso. No entanto, ele pode ajudar a Microsoft a resolver seu problema mais rapidamente. Além disso, você pode ser solicitado a fornecer esses dados pelo engenheiro de suporte depois de abrir o caso.

## <a name="collect-more-information"></a>Coletar mais informações

Antes de abrir um caso para Microsoft Azure suporte à automação, recomendamos que você colete as informações a seguir.

### <a name="basic-data-collection"></a>Coleta de dados básica

Colete os dados descritos no seguinte artigo da base de dados de conhecimento:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) como capturar a automação do Azure-diagnóstico com script

## <a name="collect-data-depending-on-issue"></a>Coletar dados dependendo do problema
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Para problemas que afetam o Gerenciamento de Atualizações no Linux

1. Além dos itens listados em KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), execute a seguinte ferramenta de coleta de logs:

   [Coletor de logs do agente do OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Compacte o conteúdo da seguinte pasta e, em seguida, envie o arquivo compactado para o suporte do Azure:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Verifique se a ID do espaço de trabalho para a qual o agente do OMS Linux está se reportando é a mesma do espaço de trabalho que está sendo monitorado para atualizações.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Para problemas que afetam Gerenciamento de Atualizações no Windows

Além dos itens listados em [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), faça o seguinte:

1. Exporte os seguintes logs de eventos para o formato EVTX:

   * Sistema
   * Aplicação
   * Segurança
   * Operations Manager
   * Microsoft-SMA/operacional

2. Verifique se a ID do espaço de trabalho que o agente está relatando é igual ao espaço de trabalho que está sendo monitorado pelas atualizações do Windows.

### <a name="for-issues-that-affect-a-job"></a>Para problemas que afetam um trabalho

Além dos itens listados em [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), colete as seguintes informações:

1. Colete o número **JobID** (para o trabalho que está enfrentando um problema):

   1. Na portal do Azure, vá para a folha **contas de automação** .
   2. Selecione a **conta de automação** para a qual você está Solucionando problemas.
   3. Selecione **trabalhos**.
   4. Selecione o trabalho que você está Solucionando problemas.
   5. Em **Resumo do trabalho**, procure uma **ID do trabalho** (GUID).

   ![ID do trabalho no painel Resumo do trabalho](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Colete o nome da conta:

   1. Na portal do Azure, vá para a folha **contas de automação** .
   2. Obtenha o nome da **conta de automação** que está Solucionando problemas.

3. Colete uma amostra do script que você está executando.

4. Coletar os arquivos de log:

   1. Na portal do Azure, vá para a folha **contas de automação** .
   2. Selecione a **conta de automação** para a qual você está Solucionando problemas.
   3. Selecione **trabalhos**.
   4. Selecione o trabalho que você está Solucionando problemas.
   5. Selecione **todos os logs**.
   6. Na folha resultante, colete os dados.

   ![Dados listados em todos os logs](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>Para problemas que afetam os módulos

Além dos itens em "coleta de dados básica", reúna as seguintes informações:

* As etapas que você seguiu para que o problema possa ser reproduzida.
* Uma captura de tela de qualquer mensagem de erro.
* Uma captura de tela dos módulos atuais e seus números de versão.

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/).

Como alternativa, arquivo de um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.