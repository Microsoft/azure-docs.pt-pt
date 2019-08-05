---
title: Conectar fontes de dados à visualização do Azure Sentinel? | Microsoft Docs
description: Saiba como conectar fontes de dados ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a3b63cfa-b5fe-4aff-b105-b22b424c418a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 4928657aa9052b50faf1f326cc09797c5aaf69bb
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780504"
---
# <a name="connect-data-sources"></a>Ligar a origens de dados

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Para o Azure Sentinel integrado, primeiro você precisa se conectar às suas fontes de dados. O Azure Sentinel vem com vários conectores para soluções da Microsoft, disponíveis prontos para uso e fornecimento de integração em tempo real, incluindo soluções de proteção contra ameaças da Microsoft e fontes de Microsoft 365, incluindo o Office 365, o Azure AD, o Azure ATP e o Microsoft Cloud App Security e muito mais. Além disso, há conectores internos para o ecossistema de segurança mais amplo para soluções que não são da Microsoft. Você também pode usar o formato de evento comum, syslog ou REST-API para conectar suas fontes de dados com o Azure Sentinel também.  

1. No menu, selecione conectores de **dados**. Esta página permite que você veja a lista completa de conectores que o Azure Sentinel fornece e seu status. Selecione o conector que você deseja conectar e selecione a **página abrir conector**. 

   ![Coletores de dados](./media/collect-data/collect-data-page.png)

1. Na página conector específico, certifique-se de que você atendeu a todos os pré-requisitos e siga as instruções para conectar os dados ao Azure Sentinel. Pode levar algum tempo para que os logs comecem a sincronizar com o Azure Sentinel. Depois de se conectar, você verá um resumo dos dados no grafo **dados recebidos** e o status de conectividade dos tipos de dados.

   ![Conectar coletores](./media/collect-data/opened-connector-page.png)
  
1. Clique na guia **próximas etapas** para obter uma lista de conteúdo pronto para uso que o Azure Sentinel fornece para o tipo de dados específico.

   ![Coletores de dados](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Métodos de conexão de dados

Os seguintes métodos de conexão de dados têm suporte do Azure Sentinel:

- **Serviços da Microsoft**:<br> Os serviços da Microsoft são conectados nativamente, aproveitando a base do Azure para integração integrada, as seguintes soluções podem ser conectadas com alguns cliques:
    - [Office 365](connect-office-365.md)
    - [Logs e entradas de auditoria do Azure AD](connect-azure-active-directory.md)
    - [Atividade do Azure](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Centro de Segurança do Azure](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Proteção avançada contra ameaças do Azure](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Eventos de segurança do Windows](connect-windows-security-events.md) 
    - [Firewall do Windows](connect-windows-firewall.md)

- **Soluções externas via API**: Algumas fontes de dados são conectadas usando APIs que são fornecidas pela fonte de dados conectada. Normalmente, a maioria das tecnologias de segurança fornece um conjunto de APIs por meio dos quais os logs de eventos podem ser recuperados. As APIs se conectam ao Azure Sentinel e coletam tipos de dados específicos e as enviam para o Azure Log Analytics. Os dispositivos conectados via API incluem:
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
- **Soluções externas por meio do agente**: O Azure Sentinel pode ser conectado a todas as outras fontes de dados que podem executar o streaming de log em tempo real usando o protocolo syslog, por meio de um agente. <br>A maioria dos dispositivos usa o protocolo syslog para enviar mensagens de evento que incluem o próprio log e os dados sobre o log. O formato dos logs varia, mas a maioria dos dispositivos oferece suporte ao padrão de formato de evento comum (CEF). <br>O agente do Azure Sentinel, que se baseia na Microsoft Monitoring Agent, converte logs formatados por CEF em um formato que pode ser ingerido pelo Log Analytics. Dependendo do tipo de dispositivo, o agente é instalado diretamente no dispositivo ou em um servidor Linux dedicado. O agente para Linux recebe eventos do daemon do syslog sobre UDP, mas, se um computador Linux for esperado para coletar um alto volume de eventos de syslog, eles serão enviados por TCP do daemon do syslog para o agente e daí para Log Analytics.
    - Firewalls, proxies e pontos de extremidade:
        - [F5](connect-f5.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Outros dispositivos CEF](connect-common-event-format.md)
        - [Outros dispositivos de syslog](connect-syslog.md)
    - Soluções DLP
    - [Provedores de inteligência contra ameaças](connect-threat-intelligence.md)
    - [Computadores DNS](connect-dns.md) -agente instalado diretamente no computador DNS
    - Servidores Linux
    - Outras nuvens
    
## Opções de conexão do agente<a name="agent-options"></a>

Para conectar seu dispositivo externo ao Azure Sentinel, o agente deve ser implantado em um computador dedicado (VM ou local) para dar suporte à comunicação entre o dispositivo e o Azure Sentinel. Você pode implantar o agente automaticamente ou manualmente. A implantação automática só estará disponível se seu computador dedicado for uma nova VM que você está criando no Azure. 


![CEF no Azure](./media/connect-cef/cef-syslog-azure.png)

Como alternativa, você pode implantar o agente manualmente em uma VM do Azure existente, em uma VM em outra nuvem ou em um computador local.

![CEF local](./media/connect-cef/cef-syslog-onprem.png)


## <a name="next-steps"></a>Passos Seguintes

- Para começar a usar o Azure Sentinel, você precisa de uma assinatura para Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como integrar [seus dados ao Azure Sentinel](quickstart-onboard.md)e [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
