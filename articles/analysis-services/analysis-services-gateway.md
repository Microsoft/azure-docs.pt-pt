---
title: Porta de dados no local para os Serviços de Análise Azure | Microsoft Docs
description: Um gateway no local é necessário se o seu servidor de Serviços de Análise em Azure ligar-se a fontes de dados no local.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6ca96f76287482a445d8a9a1cdc441333b36efbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97739608"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Ligação a fontes de dados no local com gateway de dados no local

O gateway de dados no local fornece transferência segura de dados entre fontes de dados no local e os servidores dos Serviços de Análise Azure na nuvem. Além de trabalhar com vários servidores Azure Analysis Services na mesma região, a versão mais recente do gateway também funciona com Azure Logic Apps, Power BI, Power Apps e Power Automamate. Embora o gateway que instala seja o mesmo em todos estes serviços, os Serviços de Análise Azure e as Aplicações Lógicas têm alguns passos adicionais.

As informações fornecidas aqui são específicas de como os Serviços de Análise Azure funcionam com o Gateway de Dados no local. Para saber mais sobre a porta de entrada em geral e como funciona com outros [serviços, veja o que é um portal de dados no local?](/data-integration/gateway/service-gateway-onprem)

Para os Serviços de Análise Azure, a configuração com o gateway pela primeira vez é um processo em quatro partes:

- **Configuração de descarregamento e execução** - Este passo instala um serviço de gateway num computador da sua organização. Você também se inscreve no Azure usando uma conta no Ad Azure do seu [inquilino.](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) As contas Azure B2B (hóspede) não são suportadas.

- **Registe o seu gateway** - Neste passo, especifique um nome e chave de recuperação para o seu gateway e selecione uma região, registando o seu gateway com o Gateway Cloud Service. O seu recurso gateway pode ser registado em qualquer região, mas recomenda-se que seja na mesma região que os seus servidores de Serviços de Análise. 

- **Criar um recurso de gateway em Azure** - Neste passo, cria-se um recurso de gateway em Azure.

- **Conecte o recurso gateway aos servidores** - Uma vez que tenha um recurso de gateway, pode começar a ligar os servidores ao mesmo. Pode ligar vários servidores e outros recursos desde que estejam na mesma região.

## <a name="installing"></a>Instalação

Ao instalar um ambiente de Serviços de Análise Azure, é importante que siga os passos descritos na [Instalação e configuure no local o portal de dados para os Serviços de Análise Azure](analysis-services-gateway-install.md). Este artigo é específico dos Serviços de Análise Azure. Inclui medidas adicionais necessárias para configurar um recurso de gateway de dados no local em Azure e ligar o servidor Azure Analysis Services ao recurso.

## <a name="connecting-to-a-gateway-resource-in-a-different-subscription"></a>Conectar-se a um recurso de gateway numa subscrição diferente

Recomenda-se que crie o seu recurso Azure Gateway na mesma subscrição que o seu servidor. No entanto, pode configurar os seus servidores para se conectarem a um recurso de gateway noutra subscrição. A ligação a um recurso de gateway noutra subscrição não é suportada ao configurar as definições do servidor existente ou criar um novo servidor no portal, mas pode ser configurado utilizando o PowerShell. Para saber mais, consulte [o recurso De gateway connect para o servidor.](analysis-services-gateway-install.md#connect-gateway-resource-to-server)

## <a name="ports-and-communication-settings"></a>Portas e configurações de comunicação

O gateway cria uma ligação de saída ao Azure Service Bus. Comunica com as portas de saída: TCP 443 (predefinida), 5671, 5672, 9350 a 9354.  O gateway não precisa de portas de entrada.

Poderá ter de incluir endereços IP para a sua região de dados na sua firewall. Pode transferir a [lista de IPs do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=56519). Esta lista é atualizada semanalmente. Os Endereços IP listados na lista de IPs do Azure Datacenter estão em notação CIDR. Para saber mais, consulte [o Roteamento Inter-Domain Classless](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Seguem-se os nomes de domínio totalmente qualificados utilizados pelo gateway.

| Nomes de domínio | Portas de saída | Description |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP utilizado para transferir o instalador. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Avançadas Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Ouvintes no Reencaminhamento do Service Bus por TCP (precisa de 443 para aquisição do token de Controlo de Acesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |80 |Utilizado para testar a conectividade à Internet se o serviço Power BI não conseguir aceder ao gateway. |
| *.microsoftonline-p.com |443 |Utilizado para autenticação, consoante a configuração. |
| dc.services.visualstudio.com    |443 |Usado pela AppInsights para recolher telemetria. |

## <a name="next-steps"></a>Passos seguintes 

Os seguintes artigos estão incluídos no conteúdo geral do portal de dados on-ins que se aplica a todos os serviços que o gateway suporta:

* [FAQ do gateway de dados no local](/data-integration/gateway/service-gateway-onprem-faq)   
* [Utilizar a aplicação de gateway de dados no local](/data-integration/gateway/service-gateway-app)   
* [Administração ao nível do inquilino](/data-integration/gateway/service-gateway-tenant-level-admin)
* [Configurar configurações de procuração](/data-integration/gateway/service-gateway-proxy)   
* [Ajustar as definições de comunicação](/data-integration/gateway/service-gateway-communication)   
* [Configurar ficheiros de registo](/data-integration/gateway/service-gateway-log-files)   
* [Resolução de problemas](/data-integration/gateway/service-gateway-tshoot)
* [Monitorizar e otimizar o desempenho de gateways](/data-integration/gateway/service-gateway-performance)
