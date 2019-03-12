---
title: Descrição geral das Ligações Híbridas | Microsoft Docs
description: Saiba mais sobre Ligações Híbridas, segurança, portas TCP e configurações suportadas. MABS, WABS.
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
ms.assetid: 216e4927-6863-46e7-aa7c-77fec575c8a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 18e65fc51f99aa19162daa3f6e5b9b7012214f64
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765548"
---
# <a name="hybrid-connections-overview"></a>Descrição geral das Ligações Híbridas

> [!IMPORTANT]
> As Ligações Híbridas do BizTalk são desativadas e substituídas por Ligações Híbridas do Serviço de Aplicações. Para obter mais informações, incluindo como gerir as Ligações Híbridas do BizTalk, veja [Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) (Ligações Híbridas do Serviço de Aplicações do Azure).

A introdução às Ligações Híbridas lista as configurações suportadas e as portas TCP necessárias.

## <a name="what-is-a-hybrid-connection"></a>O que é uma ligação híbrida
As Ligações Híbridas são uma funcionalidade dos BizTalk Services do Azure. As Ligações Híbridas proporcionam uma forma fácil e prática de ligar a funcionalidade Web Apps no Serviço de Aplicações do Azure (anteriormente Web Sites) e a funcionalidade Mobile Apps no Serviço de Aplicações do Azure (anteriormente Mobile Services) a recursos no local atrás da firewall.

![Ligações Híbridas][HCImage]

Benefícios das Ligações Híbridas:

* As Web Apps e a Mobile Apps podem aceder com segurança a dados e serviços no local existentes.
* Uma Ligação Híbrida pode ser partilhada por várias Web Apps ou Mobile Apps para acederem a um recurso no local.
* É preciso um mínimo de portas TCP para aceder à rede.
* As aplicações que utilizam Ligações Híbridas acedem apenas ao recurso específico no local que é publicado através da Ligação Híbrida.
* Pode ligar a qualquer recurso no local que utilize uma porta TCP estática, como SQL Server, MySQL, APIs da Web HTTP e a maioria dos Serviços Web personalizados.
  
  > [!NOTE]
  > Os serviços baseados em TCP que utilizam portas dinâmicas (como Modo Passivo de FTP ou Modo Passivo Expandido) não são atualmente suportados. O LDAP também não é suportado. O LDAP utiliza uma porta TCP estática mas, pode também utilizar o protocolo UDP. Consequentemente, não é suportado.
  > 
  > 
* Podem ser utilizadas com todas as estruturas suportadas pelas Web Apps (.NET, PHP, Java, Python, Node.js) e Mobile Apps (Node.js, .NET).
* As Web Apps e as Mobile Apps podem aceder a recursos no local exatamente da mesma forma como se a Web App ou Mobile App estivesse localizada na sua rede local. Por exemplo, a mesma cadeia de ligação utilizada no local também pode ser utilizada no Azure.

As Ligações Híbridas proporcionam também aos administradores da empresa controlo e visibilidade dos recursos da empresa acedidos por aplicações híbridas, incluindo:

* Com as definições da Política de Grupo, os administradores podem permitir Ligações Híbridas na rede e designar também recursos que podem ser acedidos por aplicações híbridas.
* Os registos de eventos e de auditoria na rede empresarial proporcionam visibilidade dos recursos acedidos pelas Ligações Híbridas.

## <a name="example-scenarios"></a>Cenários de exemplo
As Ligações Híbridas suportam as seguintes combinações de arquiteturas e aplicações:

* Acesso do .NET Framework ao SQL Server
* Acesso do .NET Framework aos serviços HTTP/HTTPS com WebClient
* Acesso do PHP ao SQL Server, MySQL
* Acesso do Java ao SQL Server, MySQL e Oracle
* Acesso do Java aos serviços HTTP/HTTPS

Ao utilizar Ligações Híbridas para aceder ao SQL Server no local, tenha em conta o seguinte:

* As Instâncias Nomeadas do SQL Express têm de ser configuradas para utilizarem portas estáticas. Por predefinição, as instâncias nomeadas do SQL Express utilizam portas dinâmicas.
* As Instâncias Predefinidas do SQL Express utilizam uma porta estática, mas o TCP tem de estar ativado. Por predefinição, o TCP não está ativado.
* Ao utilizar o Grupos de Clustering ou de Disponibilidade, o modo `MultiSubnetFailover=true` não é suportado.
* O `ApplicationIntent=ReadOnly` não é atualmente suportado.
* Poderá ser necessária a Autenticação SQL como método de autorização ponto a ponto suportado pela aplicação Azure e pelo SQL Server no local.

## <a name="security-and-ports"></a>Segurança e portas
As Ligações Híbridas utilizam a autorização de Assinatura de Acesso Partilhado (SAS) para proteger as ligações nas aplicações Azure e no Gestor de Ligações Híbridas no local para a Ligação Híbrida. São criadas chaves de ligação separadas para a aplicação e para o Gestor de Ligações Híbridas no local. Estas chaves de ligação podem ser revertidas e revogadas independentemente.

As Ligações Híbridas proporcionam uma distribuição totalmente integrada e segura das chaves às aplicações e ao Gestor de Ligações Híbridas no local.

Veja [Criar e Gerir Ligações Híbridas](integration-hybrid-connection-create-manage.md).

*A autorização da aplicação é separada da Ligação Híbrida*. Pode ser utilizado qualquer método de autorização adequado. O método de autorização depende dos métodos de autorização ponto a ponto suportados na nuvem do Azure e nos componentes no local. Por exemplo, a aplicação Azure acede a um SQL Server no local. Neste cenário, a Autorização SQL poderá ser o método de autorização que é suportado ponto a ponto.

#### <a name="tcp-ports"></a>Portas TCP
As Ligações Híbridas precisam apenas de conectividade TCP ou HTTP de saída da sua rede privada. Não precisa abrir portas de firewall ou alterar a configuração de perímetro da sua rede para permitir qualquer conectividade de entrada na rede.

As seguintes portas TCP são utilizadas pelas Ligações Híbridas:

| Porta | Porque precisa |
| --- | --- |
| 9350 – 9354 |Estas portas são utilizadas para a transmissão de dados. O gestor de reencaminhamento do Service Bus sonda a porta 9350 para determinar se a ligação TCP está disponível. Se estiver disponível, presume que a porta 9352 também está disponível. O tráfego de dados passa pela porta 9352. <br/><br/>Permita ligações de saída para estas portas. |
| 5671 |Quando a porta 9352 é utilizada para o tráfego de dados, a porta 5671 é utilizada como canal de controlo. <br/><br/>Permita ligações de saída para esta porta. |
| 80, 443 |Estas portas são utilizadas para alguns pedidos de dados ao Azure. Mais ainda, se as portas 9352 e 5671 não estiverem utilizáveis, *significa* que as portas 80 e 443 serão as portas de contingência utilizadas para a transmissão de dados e o canal de controlo.<br/><br/>Permita ligações de saída para estas portas. <br/><br/>**Nota** Não recomendamos a sua utilização como portas de contingência em vez das outras portas TCP. O HTTP/WebSocket é utilizado como protocolo em vez do TCP nativo dos canais de dados. Pode resultar num desempenho inferior. |

## <a name="next-steps"></a>Passos Seguintes
[Criar e Gerir Ligações Híbridas](integration-hybrid-connection-create-manage.md)

## <a name="see-also"></a>Consultar Também
[API REST para Gerir os BizTalk Services no Microsoft Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx)  
[Serviços BizTalk: Gráfico de edições](biztalk-editions-feature-chart.md)  
[Criar um Serviço BizTalk](biztalk-provision-services.md)  
[Serviços BizTalk: Separadores dashboard, monitorizar e dimensionar](biztalk-dashboard-monitor-scale-tabs.md)  

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
