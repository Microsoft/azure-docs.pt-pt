---
title: 'Quickstart: A bordo em Azure Sentinel'
description: Saiba como recolher dados em Azure Sentinel seguindo este documento Quickstart.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: a07ba26128d1d4689e820ec8e300ca9420c3c2d2
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85367092"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Quickstart: A bordo Azure Sentinel

Neste arranque rápido, aprenda a embarcar Azure Sentinel. 

Para embarcar no Azure Sentinel, primeiro tem de ativar o Azure Sentinel e, em seguida, ligar as suas fontes de dados. O Azure Sentinel vem com uma série de conectores para soluções Microsoft, disponíveis fora da caixa e fornecendo integração em tempo real, incluindo soluções microsoft Threat Protection, fontes Microsoft 365 (incluindo Office 365), Azure AD, Azure ATP, Microsoft Cloud App Security, entre outras. Além disso, existem conectores incorporados no ecossistema de segurança mais amplo para soluções não Microsoft. Também pode utilizar o Formato Comum de Eventos (CEF), Syslog ou REST-API para ligar as suas fontes de dados ao Azure Sentinel.  

Depois de ligar as suas fontes de dados, escolha entre uma galeria de livros criados por especialistas que superem insights com base nos seus dados. Estes livros de trabalho podem ser facilmente personalizados às suas necessidades.

>[!IMPORTANT] 
> Para obter informações sobre as acusações incorridos ao utilizar o Azure Sentinel, consulte [os preços do Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
  

## <a name="global-prerequisites"></a>Pré-requisitos globais

- Subscrição Ative Azure, se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Log Analytics espaço de trabalho. Saiba como [criar um espaço de trabalho Log Analytics.](../log-analytics/log-analytics-quick-create-workspace.md) Para obter mais informações sobre os espaços de trabalho do Log Analytics, consulte a conceção da sua implementação de [Registos de Monitores Azure.](../azure-monitor/platform/design-logs-deployment.md)

- Para ativar o Azure Sentinel, precisa de permissões de colaboradores para a subscrição em que reside o espaço de trabalho Azure Sentinel. 
- Para utilizar o Azure Sentinel, precisa de permissões de colaborador ou leitor no grupo de recursos a que pertence o espaço de trabalho.
- Podem ser necessárias permissões adicionais para ligar fontes de dados específicas.
- Azure Sentinel é um serviço pago. Para obter informações sobre preços consulte [Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## <a name="enable-azure-sentinel"></a>Ativar sentinela Azure<a name="enable"></a>

1. Inicie sessão no Portal do Azure. Certifique-se de que a subscrição na qual o Azure Sentinel é criado está selecionada.

1. Procure e selecione **Azure Sentinel.**

   ![pesquisar](./media/quickstart-onboard/search-product.png)

1. Selecione **Adicionar**.

1. Selecione o espaço de trabalho que pretende utilizar ou crie um novo. Você pode executar Azure Sentinel em mais de um espaço de trabalho, mas os dados são isolados para um único espaço de trabalho.

   ![pesquisar](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Os espaços de trabalho predefinidos criados pelo Azure Security Center não aparecerão na lista; não se pode instalar o Azure Sentinel neles.
   > - O Azure Sentinel pode funcionar em espaços de trabalho em qualquer [região de GA de Log Analytics,](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) exceto as regiões da China e Alemanha (Soberana). Os dados gerados pelo Azure Sentinel (como incidentes, marcadores e regras de alerta, que podem conter alguns dados dos clientes provenientes destes espaços de trabalho) são guardados na Europa (para espaços de trabalho localizados na Europa), na Austrália (para espaços de trabalho localizados na Austrália), ou nos EUA orientais (para espaços de trabalho localizados em qualquer outra região).

   >[!IMPORTANT]
   > - Uma vez que o Azure Sentinel tenha sido ativado num espaço de trabalho, a mudança desse espaço de trabalho para outros grupos de recursos ou subscrições não é suportada. Se precisar de deslocar o espaço de trabalho, contacte [o Azure Sentinel](mailto:AzureSentinel@microsoft.com).

1. **Selecione Add Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Ligar a origens de dados

O Azure Sentinel cria a ligação a serviços e aplicações ligando-se ao serviço e reencaminhando os eventos e registos para o Azure Sentinel. Para máquinas e máquinas virtuais, pode instalar o agente Azure Sentinel que recolhe os registos e os encaminha para a Azure Sentinel. Para Firewalls e proxies, a Azure Sentinel utiliza um servidor Linux Syslog. O agente está instalado nele e a partir do qual o agente recolhe os ficheiros de registo e os encaminha para a Azure Sentinel. 
 
1. Clique nos **conectores de dados.**
1. Há um azulejo para cada fonte de dados que pode ligar.<br>
Por exemplo, clique em **Azure Ative Directory**. Se ligar esta fonte de dados, transmite todos os registos do Azure AD para o Azure Sentinel. Pode selecionar que tipo de registos quer obter - registos de inscrição e/ou registos de auditoria. <br>
Na parte inferior, o Azure Sentinel fornece recomendações para as quais os livros de trabalho que deve instalar para cada conector para que possa obter imediatamente informações interessantes sobre os seus dados. <br> Siga as instruções de instalação ou [consulte o guia de ligação relevante](connect-data-sources.md) para obter mais informações. Para obter informações sobre os conectores de dados, consulte [os serviços da Connect Microsoft](connect-data-sources.md).

Depois de as suas fontes de dados estarem ligadas, os seus dados começam a fluir para o Azure Sentinel e estão prontos para começar a trabalhar. Pode ver os registos nos [dashboards incorporados](quickstart-get-visibility.md) e começar a criar consultas no Log Analytics para [investigar os dados.](tutorial-investigate-cases.md)



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar fontes de dados ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- Transmita os dados dos [aparelhos common Event Format](connect-common-event-format.md) para o Azure Sentinel.
