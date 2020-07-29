---
title: Porta de dados no local para serviços de análise da Azure Microsoft Docs
description: Um gateway no local é necessário se o seu servidor de Serviços de Análise em Azure ligar-se a fontes de dados no local.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 648646b6f973762245c344cd2629a874a219b170
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76310157"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Ligação a fontes de dados no local com gateway de dados no local

O gateway de dados no local fornece transferência segura de dados entre fontes de dados no local e os servidores dos Serviços de Análise Azure na nuvem. Além de trabalhar com vários servidores Azure Analysis Services na mesma região, a versão mais recente do gateway também funciona com Azure Logic Apps, Power BI, Power Apps e Power Automamate. Embora o gateway que instala seja o mesmo em todos estes serviços, os Serviços de Análise Azure e as Aplicações Lógicas têm alguns passos adicionais.

As informações fornecidas aqui são específicas de como os Serviços de Análise Azure funcionam com o Gateway de Dados no local. Para saber mais sobre a porta de entrada em geral e como funciona com outros [serviços, veja o que é um portal de dados no local?](/data-integration/gateway/service-gateway-onprem)

Para os Serviços de Análise Azure, a configuração com o gateway pela primeira vez é um processo em quatro partes:

- **Configuração de descarregamento e execução** - Este passo instala um serviço de gateway num computador da sua organização. Você também se inscreve no Azure usando uma conta no Ad Azure do seu [inquilino.](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) As contas Azure B2B (hóspede) não são suportadas.

- **Registe o seu gateway** - Neste passo, especifique um nome e chave de recuperação para o seu gateway e selecione uma região, registando o seu gateway com o Gateway Cloud Service. O seu recurso gateway pode ser registado em qualquer região, mas recomenda-se que seja na mesma região que os seus servidores de Serviços de Análise. 

- **Criar um recurso de gateway em Azure** - Neste passo, cria-se um recurso de gateway em Azure.

- **Conecte os seus servidores ao seu recurso gateway** - Uma vez que tenha um recurso de gateway, pode começar a ligar servidores ao mesmo. Pode ligar vários servidores e outros recursos desde que estejam na mesma região.



## <a name="how-it-works"></a><a name="how-it-works"> </a>Como funciona
O portal de entrada que instala num computador na sua organização funciona como um serviço Windows, **gateway de dados no local**. Este serviço local é registado com o Serviço Cloud do Gateway através do Azure Service Bus. Em seguida, cria um recurso de gateway de dados no local para a sua subscrição Azure. Os seus servidores Azure Analysis Services estão então ligados ao seu recurso de gateway Azure. Quando os modelos do seu servidor precisam de se ligar às suas fontes de dados no local para consultas ou processamento, uma consulta e fluxo de dados atravessa o recurso gateway, o Azure Service Bus, o serviço de gateway de dados local no local e as suas fontes de dados. 

![Como funciona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Fluxo de dados e consultas:

1. Uma consulta é criada pelo serviço cloud com as credenciais encriptadas para a origem de dados no local. Em seguida, é enviada para uma fila, de modo a ser processada pelo gateway.
2. O serviço de nuvem gateway analisa a consulta e empurra o pedido para o [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. O gateway de dados no local consulta o Azure Service Bus quanto a pedidos pendentes.
4. O gateway obtém a consulta, desencripta as credenciais e liga à ou às origens de dados com essas credenciais.
5. O gateway envia a consulta para a origem de dados para execução.
6. Os resultados são enviados da origem de dados de volta para o gateway e, em seguida, para o serviço cloud e o seu servidor.

## <a name="installing"></a>Instalar o

Ao instalar um ambiente de Serviços de Análise Azure, é importante que siga os passos descritos na [Instalação e configuure no local o portal de dados para os Serviços de Análise Azure](analysis-services-gateway-install.md). Este artigo é específico dos Serviços de Análise Azure. Inclui medidas adicionais necessárias para configurar um recurso de gateway de dados no local em Azure e ligar o servidor Azure Analysis Services ao recurso.

## <a name="ports-and-communication-settings"></a>Portas e configurações de comunicação

O gateway cria uma ligação de saída ao Azure Service Bus. Comunica com as portas de saída: TCP 443 (predefinida), 5671, 5672, 9350 a 9354.  O gateway não precisa de portas de entrada.

Poderá ter de incluir endereços IP para a sua região de dados na sua firewall. Pode transferir a [lista de IPs do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=56519). Esta lista é atualizada semanalmente. Os Endereços IP listados na lista de IPs do Azure Datacenter estão em notação CIDR. Para saber mais, consulte [o Roteamento Inter-Domínio Sem Classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Seguem-se os nomes de domínio totalmente qualificados utilizados pelo gateway.

| Nomes de domínio | Portas de saída | Descrição |
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
| *.msftncsi.com |443 |Utilizado para testar a conectividade à Internet se o serviço Power BI não conseguir aceder ao gateway. |
| *.microsoftonline-p.com |443 |Utilizado para autenticação, consoante a configuração. |
| dc.services.visualstudio.com  |443 |Usado pela AppInsights para recolher telemetria. |

### <a name="forcing-https-communication-with-azure-service-bus"></a><a name="force-https"></a>Forçar a comunicação HTTPS com o Azure Service Bus

Pode forçar a porta de entrada a comunicar com a Azure Service Bus utilizando HTTPS em vez de TCP direto; no entanto, fazê-lo pode reduzir consideravelmente o desempenho. Pode modificar o ficheiro *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* alterando o valor de `AutoDetect` `Https` . Este ficheiro está tipicamente localizado em *C:\Program Files\On-in-ins data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Próximos passos 

Os seguintes artigos estão incluídos no conteúdo geral do portal de dados on-ins que se aplica a todos os serviços que o gateway suporta:

* [FAQ do gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Utilizar a aplicação de gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Administração ao nível do inquilino](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Configurar configurações de procuração](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Ajustar as definições de comunicação](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Configurar ficheiros de registo](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Resolução de problemas](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorizar e otimizar o desempenho de gateways](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
