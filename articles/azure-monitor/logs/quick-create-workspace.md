---
title: Crie um espaço de trabalho log Analytics no portal Azure | Microsoft Docs
description: Saiba como criar um espaço de trabalho Log Analytics para permitir soluções de gestão e recolha de dados a partir dos ambientes da sua nuvem e ambientes no portal Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/18/2021
ms.openlocfilehash: 27eac9cefe645087cae43c34cb6503b562fb7c07
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656319"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Criar uma área de trabalho do Log Analytics no portal do Azure
Utilize o menu **de espaços de trabalho Log Analytics** para criar um espaço de trabalho Log Analytics utilizando o portal Azure. Um espaço de trabalho Log Analytics é um ambiente único para os dados de registo do Azure Monitor. Cada espaço de trabalho tem o seu próprio repositório de dados e configuração, e as fontes de dados e soluções são configuradas para armazenar os seus dados num determinado espaço de trabalho. Necessita de um espaço de trabalho Log Analytics se pretender recolher dados a partir das seguintes fontes:

* Recursos Azure na sua subscrição
* Computadores no local monitorizados pelo Gestor de Operações do Centro de Sistema
* Coleções de dispositivos do Gestor de Configuração 
* Diagnósticos ou dados de registo do armazenamento da Azure

Para outras fontes, tais como VMs Azure e Windows ou VMs Linux no seu ambiente, consulte os seguintes tópicos:

*  [Recolher dados de máquinas virtuais Azure](../vm/quick-collect-azurevm.md) 
*  [Recolher dados do computador híbrido Linux](../vm/quick-collect-linux-computer.md)
*  [Recolher dados do computador híbrido Windows](../vm/quick-collect-windows-computer.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar uma área de trabalho
No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log analytics espaços de trabalho**.

![Portal do Azure](media/quick-create-workspace/azure-portal-01.png)
  
Clique **em Adicionar** e, em seguida, forneça valores para as seguintes opções:

   * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   * Para **o Grupo de Recursos,** opte por utilizar um grupo de recursos já existente configurado ou crie um novo.  
   * Indique um nome para a nova **Área de trabalho do Log Analytics**, como *DefaultLAWorkspace*. Este nome deve ser globalmente único em todas as subscrições do Azure Monitor.
   * Selecione uma **Região** disponível.  Para obter mais informações, consulte em que [regiões o Log Analytics está disponível](https://azure.microsoft.com/regions/services/) e procure o Azure Monitor a partir do campo de **pesquisa de um** produto.  


        ![Criar lâmina de recurso Log Analytics](media/quick-create-workspace/create-workspace.png)  


Clique **em Rever + criar** para rever as definições e, em seguida, **criar** para criar o espaço de trabalho. Isto irá selecionar um nível de preços predefinido de Pay-as-you-go que não incorrerá em quaisquer alterações até que comece a recolher uma quantidade suficiente de dados. Para obter mais informações sobre outros níveis de preços, consulte [Detalhes de preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).



## <a name="troubleshooting"></a>Resolução de problemas
Quando cria um espaço de trabalho que foi eliminado nos últimos 14 dias e em [estado de eliminação suave,](../logs/delete-workspace.md#soft-delete-behavior)a operação pode ter resultados diferentes dependendo da configuração do seu espaço de trabalho:
1. Se fornecer o mesmo nome do espaço de trabalho, grupo de recursos, subscrição e região como no espaço de trabalho eliminado, o seu espaço de trabalho será recuperado, incluindo os seus dados, configuração e agentes conectados.
2. Se utilizar o mesmo nome do espaço de trabalho, mas diferentes grupos de recursos, subscrição ou região, obterá um erro *Este nome do espaço de trabalho já está em uso. Por favor, experimente outro.* Para anular o soft-delete e eliminar permanentemente o seu espaço de trabalho e criar um novo espaço de trabalho com o mesmo nome, siga estes passos para recuperar primeiro o espaço de trabalho e realizar a eliminação permanente:
   - [Recuperar](../logs/delete-workspace.md#recover-workspace) a área de trabalho
   - [Eliminar de forma permanente](../logs/delete-workspace.md#permanent-workspace-delete) a área de trabalho
   - Criar um novo espaço de trabalho usando o mesmo nome do espaço de trabalho

## <a name="next-steps"></a>Passos seguintes
Agora que tem um espaço de trabalho disponível, pode configurar a recolha de telemetria de monitorização, executar pesquisas de registo para analisar esses dados e adicionar uma solução de gestão para fornecer dados adicionais e insights analíticos. 

* Consulte [a saúde do Monitor do log analytics workspace no Azure Monitor](../logs/monitor-workspace.md) crie regras de alerta para monitorizar a saúde do seu espaço de trabalho. 
* Consulte [os registos e métricas de serviço Da Recolha Azure para utilização no Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace) para permitir a recolha de dados a partir de recursos Azure com Azure Diagnostics ou armazenamento Azure.
