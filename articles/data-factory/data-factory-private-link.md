---
title: Ligação privada Azure para Azure Data Factory
description: Saiba como funciona a Azure Private Link na Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: a6faac66edaaf047f5ba025e94a1522c2313f9ed
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546675"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Ligação privada Azure para Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Ao utilizar o Azure Private Link, pode ligar-se a várias plataformas como um serviço (PaaS) implementações em Azure através de um ponto final privado. Um ponto final privado é um endereço IP privado dentro de uma rede virtual específica e sub-rede. Para obter uma lista de implementações paaS que suportam a funcionalidade Private Link, consulte [a documentação private link](https://docs.microsoft.com/azure/private-link/). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Comunicação segura entre as redes de clientes e a Azure Data Factory 
Pode configurar uma rede virtual Azure como uma representação lógica da sua rede na nuvem. Ao fazê-lo, proporciona os seguintes benefícios:
* Ajudas a proteger os teus recursos Azure de ataques em redes públicas.
* Deixa as redes e a Data Factory comunicarem-se de forma segura entre si. 

Também pode ligar uma rede no local à sua rede virtual através da criação de uma ligação VPN (site-to-site) de segurança do Protocolo de Internet (site-to-site) ou uma ligação Azure ExpressRoute (peering privado). 

Também pode instalar um tempo de integração auto-hospedado numa máquina no local ou numa máquina virtual na rede virtual. Ao fazê-lo, permite-lhe:
* Executar atividades de cópia entre uma loja de dados em nuvem e uma loja de dados em uma rede privada.
* Despachar atividades de transformação contra recursos de computação numa rede no local ou numa rede virtual Azure. 

São necessários vários canais de comunicação entre a Azure Data Factory e a rede virtual do cliente, como mostra o seguinte quadro:

| Domínio | Porta | Descrição |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Um avião de controlo, exigido pela data factory de autoria e monitorização. |
| `*.{region}.datafactory.azure.net` | 443 | Requerido pelo tempo de integração auto-hospedado para ligar ao serviço Data Factory. |
| `*.servicebus.windows.net` | 443 | Exigido pelo tempo de integração auto-hospedado para a autoria interativa. |
| `download.microsoft.com` | 443 | Requerido pelo tempo de integração auto-hospedado para descarregar as atualizações. |

Com o apoio do Private Link for Azure Data Factory, pode:
* Crie um ponto final privado na sua rede virtual.
* Ativar a ligação privada a uma instância específica da fábrica de dados. 

As comunicações ao serviço Azure Data Factory passam pelo Private Link e ajudam a fornecer conectividade privada segura. Não precisa de configurar o domínio e a porta anteriores numa rede virtual ou na sua firewall corporativa para fornecer uma forma mais segura de proteger os seus recursos.  

![Diagrama de Ligação Privada para a arquitetura da Azure Data Factory.](./media/data-factory-private-link/private-link-architecture.png)

Ativar o serviço Private Link para cada um dos canais de comunicação anteriores oferece a seguinte funcionalidade:
- **Apoiado:**
   - Pode ser autor e monitorizar a fábrica de dados na sua rede virtual, mesmo que bloqueie todas as comunicações de saída.
   - As comunicações de comando entre o tempo de integração auto-hospedado e o serviço Azure Data Factory podem ser executados de forma segura num ambiente de rede privada. O tráfego entre o tempo de integração auto-hospedado e o serviço Azure Data Factory passa por Private Link. 
- **Não suportado atualmente:**
   - A autoria interativa que utiliza um tempo de integração auto-hospedado, como a ligação de teste, a lista de pastas de navegação e a lista de tabelas, obter esquemas e dados de pré-visualização, passa pelo Private Link.
   - A nova versão do tempo de funcionaamento de integração auto-hospedado pode ser automaticamente descarregada a partir do Microsoft Download Center se ativar o AutoUpdate.

   > [!NOTE]
   > Para funcionalidades que não são suportadas atualmente, ainda precisa configurar o domínio e a porta anteriormente mencionados na rede virtual ou na firewall corporativa. 

> [!WARNING]
> Quando criar um serviço ligado, certifique-se de que as suas credenciais estão guardadas num cofre de chaves Azure. Caso contrário, as credenciais não funcionarão quando ativar o Private Link na Azure Data Factory.

## <a name="set-up-private-link-for-azure-data-factory"></a>Configurar link privado para a fábrica de dados Azure
Pode criar pontos finais privados utilizando [o portal Azure](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).

Você também pode ir à sua fábrica de dados Azure no portal Azure e criar um ponto final privado, como mostrado aqui:

![Screenshot do painel "Ligações de ponto final privado" para criar um ponto final privado.](./media/data-factory-private-link/create-private-endpoint.png)


Se pretender bloquear o acesso público à fábrica de dados Azure e permitir o acesso apenas através do Private Link, desativar o acesso da rede à Azure Data Factory no portal Azure, como mostra aqui:

![Screenshot do painel de acesso à rede para criar um ponto final privado.](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> O desativação do acesso à rede pública é aplicável apenas ao tempo de funcionamento da integração auto-hospedada, e não ao tempo de execução da integração da integração do Azure Integration e aos Serviços de Integração de Servidores SQL (SSIS).

> [!NOTE]
> Ainda é possível aceder ao portal Azure Data Factory através de uma rede pública depois de desativar o acesso à rede pública.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma fábrica de dados utilizando a UI da Azure Data Factory](quickstart-create-data-factory-portal.md)
- [Introdução ao Azure Data Factory](introduction.md)
- [Autoria visual na Azure Data Factory](author-visually.md)

