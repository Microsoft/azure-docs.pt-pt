---
title: 'Quickstart: A bordo em Azure Sentinel'
description: Saiba como recolher dados no Azure Sentinel seguindo este documento Quickstart.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: bcb6cd7393419fb8fc75decb00a8f4165d15668c
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025276"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Quickstart: A bordo do Azure Sentinel

Neste arranque rápido, aprenda a bordo do Azure Sentinel. 

Para embarcar no Azure Sentinel, primeiro é necessário ativar o Azure Sentinel e, em seguida, ligar as suas fontes de dados. O Azure Sentinel vem com uma série de conectores para soluções Microsoft, disponíveis fora da caixa e proporcionando integração em tempo real, incluindo soluções de Proteção de Ameaças microsoft, fontes microsoft 365, incluindo Office 365, Azure AD, Azure ATP e Microsoft Cloud App Security, e muito mais. Além disso, existem conectores incorporados para o ecossistema de segurança mais amplo para soluções não Microsoft. Também pode utilizar formato de evento comum, Syslog ou REST-API para ligar as suas fontes de dados ao Azure Sentinel.  

Depois de ligar as suas fontes de dados, escolha entre uma galeria de livros de trabalho habilmente criados que superem os conhecimentos com base nos seus dados. Estes livros de livros podem ser facilmente personalizados às suas necessidades.

>[!IMPORTANT] 
> Para obter informações sobre as acusações incorridas ao utilizar o Azure Sentinel, consulte [o preço do Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
  

## <a name="global-prerequisites"></a>Pré-requisitos globais

- Subscrição Ative Azure, se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Log Analytics espaço de trabalho. Aprenda a criar um espaço de [trabalho log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Para obter mais informações sobre os espaços de trabalho do Log Analytics, consulte a conceção da implementação dos registos do [Monitor Azure](../azure-monitor/platform/design-logs-deployment.md).

- Para ativar o Azure Sentinel, necessita de permissões contributivas para a subscrição em que reside o espaço de trabalho Azure Sentinel. 
- Para utilizar o Azure Sentinel, é necessário que seja a permissão do colaborador ou do leitor no grupo de recursos a que o espaço de trabalho pertence.
- Podem ser necessárias permissões adicionais para ligar fontes de dados específicas.
- Azure Sentinel é um serviço pago. Para obter informações sobre preços consulte [O Sentinel a ua.](https://go.microsoft.com/fwlink/?linkid=2104058)
 
## <a name="enable-azure-sentinel"></a>Ativar o Sentinela Azure<a name="enable"></a>

1. Inicie sessão no Portal do Azure. Certifique-se de que a subscrição em que o Azure Sentinel é criado é selecionada.

1. Procure e selecione **Azure Sentinel**.

   ![pesquisar](./media/quickstart-onboard/search-product.png)

1. Selecione **Adicionar**.

1. Selecione o espaço de trabalho que pretende utilizar ou crie um novo. Pode executar o Azure Sentinel em mais de um espaço de trabalho, mas os dados estão isolados num único espaço de trabalho.

   ![pesquisar](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Os espaços de trabalho por defeito criados pelo Azure Security Center não constam da lista; não se pode instalar o Azure Sentinel neles.
   > - O Azure Sentinel pode funcionar em espaços de trabalho em qualquer [região da AG de Log Analytics,](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) exceto nas regiões da China, Alemanha e Governo azure. Os dados gerados pelo Azure Sentinel (tais como incidentes, marcadores e regras de alerta, que podem conter alguns dados dos clientes provenientes destes espaços de trabalho) são guardados quer na Europa Ocidental (para espaços de trabalho localizados na Europa) quer nos EUA (para todos os espaços de trabalho baseados nos EUA, bem como em qualquer outra região, com exceção da Europa).

1. **Selecione Adicionar Sentinel a Azure**.
  

## <a name="connect-data-sources"></a>Ligar a origens de dados

O Azure Sentinel cria a ligação a serviços e aplicações ligando-se ao serviço e reencaminhando os eventos e registos para o Azure Sentinel. Para máquinas e máquinas virtuais, pode instalar o agente Azure Sentinel que recolhe os registos e os encaminha para o Azure Sentinel. Para firewalls e proxies, o Azure Sentinel utiliza um servidor Linux Syslog. O agente está instalado nele e a partir do qual o agente recolhe os ficheiros de registo e os encaminha para o Azure Sentinel. 
 
1. Clique em **Conectores de Dados**.
2. Há um azulejo para cada fonte de dados que pode ligar.<br>
Por exemplo, clique em **Azure Ative Directory**. Se ligar esta fonte de dados, transe todos os registos do Azure AD para o Azure Sentinel. Pode selecionar que tipo de registos se desloque para obter - registos de login e/ou registos de auditoria. <br>
Na parte inferior, o Azure Sentinel fornece recomendações para quais os livros de trabalho que deve instalar para cada conector para que possa obter imediatamente informações interessantes através dos seus dados. <br> Siga as instruções de instalação ou consulte o guia de [ligação relevante](connect-data-sources.md) para mais informações. Para obter informações sobre conectores de dados, consulte [os serviços Connect Microsoft](connect-data-sources.md).

Depois de as suas fontes de dados estarem ligadas, os seus dados começam a ser transmitidos para o Azure Sentinel e estão prontos para começar a trabalhar. Pode ver os registos nos [painéis incorporados](quickstart-get-visibility.md) e começar a construir consultas no Log Analytics para [investigar os dados](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar fontes de dados ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- Transmita dados dos [aparelhos de formato](connect-common-event-format.md) de evento comum para O Sentinel a D.I.
