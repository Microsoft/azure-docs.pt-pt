---
title: Ligação privada Azure para Azure Data Factory
description: Saiba mais sobre a Azure Private Link funciona na Azure Data Factory.
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
ms.openlocfilehash: 164a1005d9382711874b644e14b23d2154d613a0
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596027"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Ligação privada Azure para Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

O Private Link permite-lhe ligar-se a vários serviços PaaS em Azure através de um ponto final privado. Para obter uma lista de serviços PaaS que suportam a funcionalidade Private Link, aceda à [página de Documentação de Link Privado](https://docs.microsoft.com/azure/private-link/). Um ponto final privado é um endereço IP privado dentro de uma rede virtual específica e sub-rede.

## <a name="secure-communication-between-customer-network-and-azure-data-factory-service"></a>Comunicação segura entre a rede de clientes e o serviço Azure Data Factory
Para proteger os seus recursos Azure de ataques em rede pública ou deixá-los comunicar de forma segura entre si, pode configurar uma Rede Virtual Azure como uma representação lógica da sua rede na nuvem. Também pode ligar uma rede no local à sua rede virtual, configurando IPSec VPN (site-to-site) ou ExpressRoute (peering privado). O Tempo de Execução de Integração Auto-hospedado pode ser instalado numa máquina no local ou numa máquina virtual em Rede Virtual para executar atividades de cópia entre uma loja de dados em nuvem e uma loja de dados numa rede privada ou despachar atividades de transformação contra recursos computacionais numa rede de informação ou numa rede virtual Azure. 

Existem vários canais de comunicação necessários entre a Data Factory e a rede virtual do cliente.


| **Domínio** | **Porta** | **Descrição** |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Avião de controlo. Requerido pela data factory de autoria e monitorização. |
| `*.{region}.datafactory.azure.net` | 443 | Requerido pelo tempo de funcionação de integração auto-hospedado para ligar ao serviço data factory. |
| `*.servicebus.windows.net` | 443 | Requerido pelo Tempo de Integração Auto-hospedado para Autoria Interativa. |
| `download.microsoft.com` | 443 | Requerido pelo Prazo de Integração Auto-hospedado para descarregar as atualizações. |


Com o apoio do Azure Private Link for Azure Data Factory, pode criar um Ponto Final Privado (PE) na sua rede virtual e ativar a ligação privada à Fábrica de Dados Azure específica. As comunicações ao serviço Azure Data Factory passam pela Azure Private Link que proporciona uma conectividade privada segura. E não precisa de configurar acima do domínio e do porto na rede virtual ou na sua firewall corporativa que forneça uma forma mais segura de proteger os seus recursos.  

![Azure Data Factory Private Link Architecture](./media/data-factory-private-link/private-link-architecture.png)

Aqui estão os benefícios para permitir o Serviço de Ligação Privada para cada um dos canais de comunicação acima descritos:
- (Apoiado) Pode fazer a autoria e monitorização da Azure Data Factory na sua rede virtual, mesmo bloqueando todas as comunicações de saída.
- (Apoiado) As comunicações de comando entre o tempo de execução de integração auto-hospedado e o serviço Azure Data Factory podem ser executados de forma segura num ambiente de rede privada. O tráfego entre o tempo de execução de integração auto-hospedado e o serviço Azure Data Factory passa por Private Link. 
- (Não suportado atualmente) A autoria interativa utilizando o tempo de execução de integração auto-hospedado passa por Private Link, como a ligação de teste, a lista de pastas de navegação e a lista de tabelas, obter esquemas e dados de pré-visualização.
- (Não suportado atualmente) A nova versão do Self-hosted Integration Runtime pode ser automaticamente descarregada a partir do centro de descarregamento se ativar a atualização automática.

> [!NOTE]
> Para funcionalidades que não são suportadas atualmente, ainda precisa de configurar acima do domínio e da porta na rede virtual ou na firewall corporativa. 

> [!WARNING]
> Quando criar o Linked Service, certifique-se de que a credencial está armazenada no Cofre da Chave Azure. Caso contrário, não funciona quando ativa o Serviço de Ligação Privada na Azure Data Factory.

## <a name="how-to-set-up-private-link-for-azure-data-factory"></a>Como configurar o Link Privado para a Azure Data Factory
Os pontos finais privados podem ser criados utilizando o portal Azure, PowerShell ou o Azure CLI:

[Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)


Também pode navegar para a sua Fábrica de Dados Azure no portal Azure e criar Private Endpoint (PE):

![Criar Ponto Final Privado](./media/data-factory-private-link/create-private-endpoint.png)


Se pretender bloquear o acesso público a esta Azure Data Factory e apenas permitir o acesso através do Private Link, pode desativar o acesso à rede da Azure Data Factory no portal Azure:

![Criar Ponto Final Privado](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> O desativação do acesso à rede pública só é aplicável ao tempo de execução de integração auto-hospedado, não ao tempo de execução da integração do Azure e ao tempo de execução da integração SSIS.

> [!NOTE]
> Os utilizadores ainda podem aceder ao Portal da Fábrica de Dados do Azure através da rede pública depois de desativar o acesso à rede pública.

## <a name="next-steps"></a>Próximos passos

- [Criar uma fábrica de dados utilizando a UI da Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Introdução ao Azure Data Factory](introduction.md)

- [Autoria visual na Azure Data Factory](author-visually.md)

