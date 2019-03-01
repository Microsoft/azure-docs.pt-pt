---
title: Ligar a origens de dados para a pré-visualização de sentinela do Azure? | Documentos da Microsoft
description: Saiba como ligar a origens de dados ao sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: a3b63cfa-b5fe-4aff-b105-b22b424c418a
ms.service: sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2ef41cba4fc63ec2dee42f08f66fd9fdabb3ef0c
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992814"
---
# <a name="connect-data-sources"></a>Ligar a origens de dados

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Para integrar sentinela do Azure, primeiro tem de ligar às suas origens de dados. Sentinel do Azure é fornecido com uma série de conectores para soluções da Microsoft, disponíveis para ser usado na caixa e o fornecimento de integração em tempo real, incluindo soluções de proteção de ameaças da Microsoft e do Microsoft 365 origens, incluindo o Office 365, Azure AD, Azure ATP, e Microsoft Cloud App Security e muito mais. Além disso, existem conectores incorporados para o mais amplo ecossistema de segurança para soluções de terceiros. Também pode utilizar o formato de evento comum, Syslog ou REST API para ligar as origens de dados com o Azure sentinela também.  

![Recoletores de dados](./media/collect-data/collect-data-page.png)

## <a name="data-collection-methods"></a>Métodos de recolha de dados

Os seguintes métodos de recolha de dados são suportados pelo Azure sentinela:

- **Serviços da Microsoft**:<br> Serviços da Microsoft estão ligados nativamente, aproveitando a base do Azure para integração de fora da caixa, as seguintes soluções podem ser ligadas em poucos cliques:
    - [Office 365](connect-office-365.md)
    - [Inícios de sessão e registos de auditoria do Azure AD](connect-azure-active-directory.md)
    - [Atividades do Azure](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Centro de Segurança do Azure](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Proteção contra ameaças avançada do Azure](connect-azure-atp.md)
    - [O cloud App Security](connect-cloud-app-security.md)
    - [Eventos de segurança do Windows](connect-windows-security-events.md) 
    - [Firewall do Windows](connect-windows-firewall.md)

- **Soluções externas através da API**: Algumas origens de dados estão ligadas através de APIs que são fornecidas pela origem de dados ligada. Normalmente, a maioria das tecnologias de segurança fornecem um conjunto de APIs, através do qual os registos de eventos pode ser obtidos. As APIs de ligar ao Azure sentinela e recolher os tipos de dados específicos e enviá-los para o Azure Log Analytics. Aplicações ligadas através da API incluem:
    - [Barracuda](connect-barracuda.md)
    - Symantec
- **Soluções externas através de agente**: Sentinel do Azure pode ser ligada a todas as outras origens de dados que podem realizar a transmissão de registos em tempo real utilizando o protocolo de Syslog, através de um agente. <br>A maioria das aplicações utilizam o protocolo de Syslog para enviar mensagens de eventos que incluem o registo em si e os dados sobre o registo. O formato dos registos varia, mas a maioria das aplicações suportam o padrão de Common Event Format (CEF). <br>O agente de Azure sentinela, que se baseia o agente do OMS, converte CEF formatado registos num formato que podem ser ingeridos pelo Log Analytics. Dependendo do tipo de aplicação, o agente é instalado diretamente na aplicação da ou num servidor Linux dedicado. O agente para Linux recebe eventos a partir do Syslog daemon através do UDP, mas em casos em que uma máquina Linux é esperada para recolher um grande volume de eventos do Syslog, enviados por TCP partir do Syslog daemon para o agente e daí para o Log Analytics.
    - Firewalls, proxies e pontos de extremidade:
        - [F5](connect-f5.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Outras aplicações de CEF](connect-common-event-format.md)
        - [Outras aplicações de Syslog](connect-syslog.md)
    - Soluções DLP
    - [Fornecedores de inteligência de ameaças](connect-threat-intelligence.md)
    - [Máquinas DNS](connect-dns.md) -agente instalado diretamente na máquina de DNS
    - Servidores Linux
    - Outras clouds
    
## Opções de ligação do agente<a name="agent-options"></a>

Para ligar a sua aplicação da externa ao sentinela do Azure, o agente tem de ser implementado numa máquina dedicada (VM ou no local) para suportar a comunicação entre a aplicação e, em seguida, sentinela do Azure. Pode deploly o agente manualmente ou automaticamente. Implementação automática só está disponível se a sua máquina dedicada é uma nova VM que está a criar no Azure. 


![CEF no Azure](./media/connect-cef/cef-syslog-azure.png)

Em alternativa, pode implementar o agente manualmente numa VM do Azure existente, numa VM noutra cloud ou numa máquina no local.

![CEF no local](./media/connect-cef/cef-syslog-onprem.png)


## <a name="next-steps"></a>Passos Seguintes

- Para começar a utilizar com o Azure sentinela, terá de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como [carregar os dados para o Azure sentinela](quickstart-onboard.md), e [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
