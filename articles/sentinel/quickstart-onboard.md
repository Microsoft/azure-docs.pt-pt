---
title: Carregar em pré-visualização do Azure Sentinel | Documentos da Microsoft
description: Saiba como recolher dados no Azure sentinela.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 9c5f4c73bb516172773f6aad5e5393db6d40b3d5
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992916"
---
# <a name="on-board-azure-sentinel-preview"></a>Pré-visualização de acumular Sentinel do Azure

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia de introdução irá aprender como integrar sentinela de Azure. 

Para integrar sentinela do Azure, primeiro tem de ligar às suas origens de dados. Sentinel do Azure é fornecido com uma série de conectores para soluções da Microsoft, disponíveis para ser usado na caixa e o fornecimento de integração em tempo real, incluindo soluções de proteção de ameaças da Microsoft, origens de Microsoft 365, incluindo o Office 365, Azure AD, Azure ATP, e Microsoft Cloud App Security e muito mais. Além disso, existem conectores incorporados para o mais amplo ecossistema de segurança para soluções de terceiros. Também pode utilizar o formato de evento comum, Syslog ou REST API para ligar as suas origens de dados com o Azure sentinela.  

Depois de ligar as origens de dados, escolha a partir de uma galeria de criado com dashboards que surgir insights com base nos seus dados. Estes dashboards podem ser facilmente personalizados para as suas necessidades.


## <a name="global-prerequisites"></a>Pré-requisitos globais

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Inicie a sessão de área de trabalho de análise. Saiba como [criar uma área de trabalho do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md)

- Permissões de Contribuidor para o seu inquilino para ativar o Azure sentinela

- Inquilino global ou permissões de administrador de segurança

## <a name="enable-azure-sentinel"></a>Ativar Sentinel do Azure

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
