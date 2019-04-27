---
title: Carregar em pré-visualização do Azure Sentinel | Documentos da Microsoft
description: Saiba como recolher dados no Azure sentinela.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 5c6dfa359a85b5330e70a2618d59ffab15cf24f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60804848"
---
# <a name="on-board-azure-sentinel-preview"></a>Pré-visualização de acumular Sentinel do Azure

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia de introdução irá aprender como integrar sentinela de Azure. 

Para integrar sentinela do Azure, tem primeiro de ativar o Azure sentinela e, em seguida, ligar as origens de dados. Sentinel do Azure é fornecido com uma série de conectores para soluções da Microsoft, disponíveis para ser usado na caixa e o fornecimento de integração em tempo real, incluindo soluções de proteção de ameaças da Microsoft, origens de Microsoft 365, incluindo o Office 365, Azure AD, Azure ATP, e Microsoft Cloud App Security e muito mais. Além disso, existem conectores incorporados para o mais amplo ecossistema de segurança para soluções de terceiros. Também pode utilizar o formato de evento comum, Syslog ou REST API para ligar as suas origens de dados com o Azure sentinela.  

Depois de ligar as origens de dados, escolha a partir de uma galeria de criado com dashboards que surgir insights com base nos seus dados. Estes dashboards podem ser facilmente personalizados para as suas necessidades.


## <a name="global-prerequisites"></a>Pré-requisitos globais

- Subscrição do Azure Active Directory, se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Inicie a sessão de área de trabalho de análise. Saiba como [criar uma área de trabalho do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md)

-  Para ativar o Azure sentinela, terá de permissões de contribuinte à subscrição na qual reside a área de trabalho de sentinela do Azure. 
- Para utilizar o Azure sentinela, precisa de permissões de Contribuidor ou visualizador no grupo de recursos que pertence a área de trabalho
- Permissões adicionais que poderão ser necessária para ligar a origens de dados específicos
 
## Ativar Sentinel do Azure <a name="enable"></a>

1. Ir para o portal do Azure.
2. Certifique-se de que a subscrição na qual o Azure sentinela é criado, está selecionado. 
3. Procure Sentinel do Azure. 
   ![search](./media/quickstart-onboard/search-product.png)

1. Clique em **+ adicionar**.
1. Selecione a área de trabalho que pretende utilizar ou criar um novo. É possível executar o Azure sentinela em mais do que uma área de trabalho, mas os dados são isolados para um único espaço de trabalho.

   ![procurar](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - **Localização da área de trabalho** é importante compreender que todos os dados que os transmitir para o Azure sentinela é armazenado na localização geográfica da área de trabalho que selecionou.  
   > - Áreas de trabalho predefinida criadas pelo centro de segurança do Azure não irão aparecer na lista; Não é possível instalar o Azure sentinela nos mesmos.
   > - Sentinel do Azure pode ser executado em áreas de trabalho que são implementadas em qualquer uma das seguintes regiões:  Sudeste da Austrália, Canadá Central, Índia Central, E.U.A. leste, E.U.A. Leste 2 EUAP (Canárias), leste do Japão, Sudeste asiático, do Reino Unido Sul, Europa Ocidental, oeste dos E.U.A. 2.

6. Clique em **adicionar Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Ligar a origens de dados

Sentinel do Azure cria a ligação a serviços e aplicações ao ligar ao serviço e reencaminhar os eventos e registos para sentinela do Azure. Para computadores e máquinas virtuais, pode instalar o agente de Azure sentinela de mensagens em fila que recolhe os registos e reencaminha-os para sentinela do Azure. Para Firewalls e proxies, sentinela do Azure utiliza um servidor do Linux Syslog. O agente está instalado no mesmo e de que o agente recolhe o registo de ficheiros e reencaminha-os para sentinela do Azure. 
 
1. Clique em **recolha de dados**.
2. Há um mosaico para cada origem de dados, que pode ligar.<br>
Por exemplo, clique em **do Azure Active Directory**. Se ligar esta origem de dados, transmite todos os registos do Azure AD para o Azure sentinela. Pode selecionar o tipo de registos de wan para obter - início de sessão registos e/ou registos de auditoria. <br>
Na parte inferior, Azure sentinela fornece recomendações para os dashboards, deve instalar para cada conector para que pode imediatamente obter interessante informações em seus dados. <br> Siga as instruções de instalação ou [consulte o guia de ligação relevantes](connect-data-sources.md) para obter mais informações. Para obter informações sobre conectores de dados, consulte [serviços do Connect Microsoft](connect-data-sources.md).

Após sua estão ligadas a origens de dados, os seus dados começa a transmissão em fluxo para o Azure sentinela e estão prontos para que possa começar a trabalhar com. Pode ver os registos no [painéis internos](quickstart-get-visibility.md) e comece a criar consultas no Log Analytics para [investigar os dados](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar a origens de dados para sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).
- Dados a partir de Stream [aparelhos de formato de erro comuns](connect-common-event-format.md) em sentinela do Azure.
