---
title: Gateway de dados local para Azure Analysis Services | Microsoft Docs
description: Um gateway local será necessário se o servidor de Analysis Services no Azure se conectar a fontes de dados locais.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 35ffc7f3c97ca7ab14f94c3607560ffb6ea0b399
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146860"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Conectando-se a fontes de dados locais com o gateway de dados local

O gateway de dados local fornece transferência segura de dados entre fontes de dados locais e seus servidores de Azure Analysis Services na nuvem. Além de trabalhar com vários servidores Azure Analysis Services na mesma região, a versão mais recente do gateway também funciona com aplicativos lógicos do Azure, Power BI, Power apps e Microsoft Flow. Você pode associar vários serviços na mesma assinatura e na mesma região com um único gateway. Embora o gateway que você instalar seja o mesmo em todos esses serviços, Azure Analysis Services e aplicativos lógicos têm algumas etapas adicionais.

Por Azure Analysis Services, obter a configuração com o gateway na primeira vez é um processo de quatro partes:

- **Baixar e executar a instalação** – esta etapa instala um serviço de gateway em um computador em sua organização. Você também entra no Azure usando uma conta no Azure AD [do seu locatário](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) . Não há suporte para contas B2B (de convidado) do Azure.

- **Registrar seu gateway** -nesta etapa, você especifica um nome e uma chave de recuperação para seu gateway e seleciona uma região, registrando seu gateway com o serviço de nuvem do gateway. O recurso de gateway pode ser registrado em qualquer região, mas é recomendável que ele esteja na mesma região que seus servidores de Analysis Services. 

- **Criar um recurso de gateway no Azure** – nesta etapa, você cria um recurso de gateway em sua assinatura do Azure.

- **Conecte seus servidores ao recurso de gateway** -depois de ter um recurso de gateway em sua assinatura, você pode começar a conectar seus servidores a ele. Você pode conectar vários servidores e outros recursos, desde que eles estejam na mesma assinatura e na mesma região.

## <a name="how-it-works"> </a>Como funciona
O gateway que você instala em um computador da sua organização é executado como um serviço **do Windows, gateway de dados local**. Esse serviço local é registrado com o serviço de nuvem do gateway por meio do barramento de serviço do Azure. Em seguida, você cria um recurso de gateway de dados local para sua assinatura do Azure. Seus servidores de Azure Analysis Services são então conectados ao recurso de gateway do Azure. Quando os modelos em seu servidor precisam se conectar às suas fontes de dados locais para consultas ou processamentos, uma consulta e um fluxo de dados atravessam o recurso de gateway, o barramento de serviço do Azure, o serviço do gateway de dados local e suas fontes de dados. 

![Como funciona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Consultas e fluxo de dados:

1. Uma consulta é criada pelo serviço de nuvem com as credenciais criptografadas para a fonte de dados local. Em seguida, ele é enviado para uma fila para o gateway processar.
2. O serviço de nuvem do gateway analisa a consulta e envia a solicitação para o [barramento de serviço do Azure](https://azure.microsoft.com/documentation/services/service-bus/).
3. O gateway de dados local sonda o barramento de serviço do Azure para solicitações pendentes.
4. O gateway Obtém a consulta, descriptografa as credenciais e conecta-se às fontes de dados com essas credenciais.
5. O gateway envia a consulta à fonte de dados para execução.
6. Os resultados são enviados da fonte de dados, de volta ao gateway e, em seguida, para o serviço de nuvem e seu servidor.

## <a name="installing"></a>Instalado

Ao instalar o para um ambiente de Azure Analysis Services, é importante seguir as etapas descritas em [instalar e configurar o gateway de dados local para Azure Analysis Services](analysis-services-gateway-install.md). Este artigo é específico para Azure Analysis Services. Ele inclui etapas adicionais necessárias para configurar um recurso de gateway de dados local no Azure e conectar seu servidor de Azure Analysis Services ao recurso.

## <a name="ports-and-communication-settings"></a>Portas e configurações de comunicação

O gateway cria uma conexão de saída com o barramento de serviço do Azure. Ele se comunica nas portas de saída: TCP 443 (padrão), 5671, 5672, 9350 até 9354.  O gateway não requer portas de entrada.

Talvez seja necessário incluir endereços IP para sua região de dados em seu firewall. Você pode baixar a [lista de IP do Microsoft Azure datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Essa lista é atualizada semanalmente. Os endereços IP listados na lista de IP do datacenter do Azure estão na notação CIDR. Para saber mais, consulte [Roteamento entre domínios sem classificação](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Estes são os nomes de domínio totalmente qualificados usados pelo Gateway.

| Nomes de domínio | Portas de saída | Descrição |
| --- | --- | --- |
| *. powerbi.com |80 |HTTP usado para baixar o instalador. |
| *. powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Ouvintes na retransmissão do barramento de serviço sobre TCP (requer 443 para aquisição de token de controle de acesso) |
| *. frontend.clouddatahub.net |443 |HTTPS |
| *. core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *. msftncsi.com |443 |Usado para testar a conectividade com a Internet se o gateway estiver inacessível pelo serviço do Power BI. |
| *. microsoftonline-p.com |443 |Usado para autenticação, dependendo da configuração. |
| dc.services.visualstudio.com  |443 |Usado pelo AppInsights para coletar telemetria. |

### <a name="force-https"></a>Forçando a comunicação HTTPS com o barramento de serviço do Azure

Você pode forçar o gateway a se comunicar com o barramento de serviço do Azure usando HTTPS em vez de TCP direto; no entanto, isso pode reduzir significativamente o desempenho. Você pode modificar o arquivo *Microsoft. PowerBI. datamover. Pipeline. GatewayCore. dll. config* alterando o valor de `AutoDetect` para `Https`. Esse arquivo geralmente está localizado em *C:\Program programas\gateway data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Passos seguintes 

Os artigos a seguir estão incluídos no conteúdo geral do gateway de dados local que se aplica a todos os serviços aos quais o gateway dá suporte:

* [FAQ sobre o gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Usar o aplicativo de gateway de dados local](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Administração no nível do locatário](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Definir configurações de proxy](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Ajustar as configurações de comunicação](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Configurar arquivos de log](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Resolução de problemas](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorar e otimizar o desempenho do gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
