---
title: Gateway de dados local para Azure Analysis Services | Microsoft Docs
description: Um gateway local será necessário se o servidor de Analysis Services no Azure se conectar a fontes de dados locais.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f1fc00ced0d933884ca0fe6dce91fed4602eb825
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263443"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Conectando-se a fontes de dados locais com o gateway de dados local

O gateway de dados local fornece transferência segura de dados entre fontes de dados locais e seus servidores de Azure Analysis Services na nuvem. Além de trabalhar com vários servidores de Azure Analysis Services na mesma região, a versão mais recente do gateway também funciona com aplicativos lógicos do Azure, Power BI, Power apps e automação de energia. Embora o gateway que você instalar seja o mesmo em todos esses serviços, Azure Analysis Services e aplicativos lógicos têm algumas etapas adicionais.

As informações fornecidas aqui são específicas de como Azure Analysis Services funciona com o gateway de dados local. Para saber mais sobre o gateway em geral e como ele funciona com outros serviços, consulte [o que é um gateway de dados local?](/data-integration/gateway/service-gateway-onprem).

Por Azure Analysis Services, obter a configuração com o gateway na primeira vez é um processo de quatro partes:

- **Baixar e executar a instalação** – esta etapa instala um serviço de gateway em um computador em sua organização. Você também entra no Azure usando uma conta no Azure AD [do seu locatário](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) . Não há suporte para contas B2B (de convidado) do Azure.

- **Registrar seu gateway** -nesta etapa, você especifica um nome e uma chave de recuperação para seu gateway e seleciona uma região, registrando seu gateway com o serviço de nuvem do gateway. O recurso de gateway pode ser registrado em qualquer região, mas é recomendável que ele esteja na mesma região que seus servidores de Analysis Services. 

- **Criar um recurso de gateway no Azure** – nesta etapa, você cria um recurso de gateway em um Azure.

- **Conecte seus servidores ao recurso de gateway** -depois de ter um recurso de gateway, você pode começar a conectar seus servidores a ele. Você pode conectar vários servidores e outros recursos, desde que eles estejam na mesma região.



## <a name="how-it-works"> </a>Como funciona
O gateway que você instala em um computador da sua organização é executado como um serviço **do Windows, gateway de dados local**. Este serviço local é registado com o Serviço Cloud do Gateway através do Azure Service Bus. Em seguida, você cria um recurso de gateway de dados local para sua assinatura do Azure. Seus servidores de Azure Analysis Services são então conectados ao recurso de gateway do Azure. Quando os modelos em seu servidor precisam se conectar às suas fontes de dados locais para consultas ou processamentos, uma consulta e um fluxo de dados atravessam o recurso de gateway, o barramento de serviço do Azure, o serviço do gateway de dados local e suas fontes de dados. 

![Como funciona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Fluxo de dados e consultas:

1. Uma consulta é criada pelo serviço cloud com as credenciais encriptadas para a origem de dados no local. Em seguida, é enviada para uma fila, de modo a ser processada pelo gateway.
2. O serviço cloud do gateway analisa a consulta e envia o pedido para o [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. O gateway de dados no local consulta o Azure Service Bus quanto a pedidos pendentes.
4. O gateway obtém a consulta, desencripta as credenciais e liga à ou às origens de dados com essas credenciais.
5. O gateway envia a consulta para a origem de dados para execução.
6. Os resultados são enviados da origem de dados de volta para o gateway e, em seguida, para o serviço cloud e o seu servidor.

## <a name="installing"></a>Instalar o

Ao instalar o para um ambiente de Azure Analysis Services, é importante seguir as etapas descritas em [instalar e configurar o gateway de dados local para Azure Analysis Services](analysis-services-gateway-install.md). Este artigo é específico para Azure Analysis Services. Ele inclui etapas adicionais necessárias para configurar um recurso de gateway de dados local no Azure e conectar seu servidor de Azure Analysis Services ao recurso.

## <a name="ports-and-communication-settings"></a>Portas e configurações de comunicação

O gateway cria uma ligação de saída para o Azure Service Bus. Comunica com as portas de saída: TCP 443 (predefinida), 5671, 5672, 9350 a 9354.  O gateway não precisa de portas de entrada.

Talvez seja necessário incluir endereços IP para sua região de dados em seu firewall. Pode transferir a [lista de IPs do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Esta lista é atualizada semanalmente. Os Endereços IP listados na lista de IPs do Azure Datacenter estão em notação CIDR. Para saber mais, consulte [Roteamento entre domínios sem classificação](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Estes são os nomes de domínio totalmente qualificados usados pelo Gateway.

| Nomes de domínio | Portas de saída | Descrição |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP utilizado para transferir o instalador. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Avançadas Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Ouvintes no Reencaminhamento do Service Bus por TCP (precisa de 443 para aquisição do token de Controlo de Acesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Utilizado para testar a conectividade à Internet se o gateway estiver inacessível através do serviço Power BI. |
| *.microsoftonline-p.com |443 |Utilizado para autenticação, consoante a configuração. |
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
