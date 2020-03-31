---
title: Alta disponibilidade e equilíbrio de carga - Procuração de aplicação ad azure
description: Como a distribuição de tráfego funciona com a implementação do Proxy de Aplicação. Inclui dicas para otimizar o desempenho do conector e utilizar o equilíbrio de carga para servidores de back-end.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3202c2fbfedfce0b0b52be94b1e0d165a6e72546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481318"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Alta disponibilidade e equilíbrio de carga dos seus conectores e aplicações do Proxy de Aplicação

Este artigo explica como a distribuição de tráfego funciona com a sua implementação de Procuração de Aplicações. Vamos discutir:

- Como o tráfego é distribuído entre utilizadores e conectores, juntamente com dicas para otimizar o desempenho do conector

- Como o tráfego flui entre conectores e servidores de aplicações back-end, com recomendações para equilibrar a carga entre vários servidores back-end

## <a name="traffic-distribution-across-connectors"></a>Distribuição de tráfego através de conectores

Os conectores estabelecem as suas ligações com base em princípios de elevada disponibilidade. Não há garantias de que o tráfego será sempre distribuído uniformemente pelos conectores e não há afinidade de sessão. No entanto, o uso varia e os pedidos são enviados aleatoriamente para instâncias de serviço de Procuração de Aplicação. Como resultado, o tráfego é normalmente distribuído quase uniformemente pelos conectores. O diagrama e os passos abaixo ilustram como as ligações são estabelecidas entre utilizadores e conectores.

![Diagrama mostrando ligações entre utilizadores e conectores](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Um utilizador de um dispositivo cliente tenta aceder a uma aplicação no local publicada através do Application Proxy.
2. O pedido passa por um Balancer de Carga Azure para determinar qual a instância de serviço de procuração de aplicações deve atender o pedido. Por região, existem dezenas de casos disponíveis para aceitar o pedido. Este método ajuda a distribuir uniformemente o tráfego através das instâncias de serviço.
3. O pedido é enviado para [o Service Bus.](https://docs.microsoft.com/azure/service-bus-messaging/)
4. Serviço Bus verifica se a ligação anteriormente utilizada um conector existente no grupo de conectores. Em caso afirmativo, reutiliza a ligação. Se nenhum conector estiver emparelhado com a ligação ainda, ele escolhe um conector disponível aleatoriamente para sinalizar. Em seguida, o conector recolhe o pedido da Service Bus.

   - Na etapa 2, os pedidos vão para diferentes instâncias de serviço de Procuração de Aplicações, pelo que as ligações são mais propensas a serem feitas com diferentes conectores. Como resultado, os conectores são quase uniformemente utilizados dentro do grupo.

   - Uma ligação só é restabelecida se a ligação estiver quebrada ou ocorrer um período de 10 minutos. Por exemplo, a ligação pode ser quebrada quando uma máquina ou serviço de conector reinicia ou há uma rutura da rede.

5. O conector transmite o pedido para o servidor back-end da aplicação. Em seguida, a aplicação envia a resposta de volta para o conector.
6. O conector completa a resposta abrindo uma ligação de saída à instância de serviço de onde o pedido veio. Em seguida, esta ligação é imediatamente fechada. Por predefinição, cada conector está limitado a 200 ligações de saída simultâneas.
7. A resposta é então transmitida de volta para o cliente a partir da instância de serviço.
8. Os pedidos subsequentes da mesma ligação repitam os passos acima até que esta ligação esteja quebrada ou fique inativa durante 10 minutos.

Uma aplicação muitas vezes tem muitos recursos e abre múltiplas conexões quando é carregada. Cada ligação passa pelos passos acima para ser atribuída a uma instância de serviço, selecione um novo conector disponível se a ligação ainda não tiver sido previamente emparelhada com um conector.


## <a name="best-practices-for-high-availability-of-connectors"></a>Boas práticas para alta disponibilidade de conectores

- Devido à forma como o tráfego é distribuído entre conectores para alta disponibilidade, é essencial ter sempre pelo menos dois conectores num grupo de conectores. São preferíveis três conectores para fornecer tampão adicional entre conectores. Para determinar o número correto de conectores necessários, siga a documentação de planeamento da capacidade.

- Coloque conectores em diferentes ligações de saída para evitar um único ponto de avaria. Se os conectores utilizarem a mesma ligação de saída, um problema de rede com a ligação pode afetar todos os conectores que a utilizam.

- Evite forçar os conectores a reiniciar quando ligados às aplicações de produção. Ao fazê-lo, pode afetar negativamente a distribuição do tráfego através dos conectores. O reinício dos conectores faz com que mais conectores não estejam disponíveis e força as ligações ao restante conector disponível. O resultado é uma utilização desigual dos conectores inicialmente.

- Evite todas as formas de inspeção em linha nas comunicações TLS de saída entre conectores e Azure. Este tipo de inspeção inline causa degradação ao fluxo de comunicação.

- Certifique-se de que mantém as atualizações automáticas em funcionamento para os seus conectores. Se o serviço de atualização do Coector proxy da aplicação estiver em funcionamento, os seus conectores atualizam-se automaticamente e recebem o mais recente atualizado. Se não vir o serviço de Atualização do Conector no seu servidor, precisa de reinstalar o conector para obter quaisquer atualizações.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Fluxo de tráfego entre conectores e servidores de aplicações back-end

Outra área-chave onde a elevada disponibilidade é um fator é a ligação entre os conectores e os servidores de back-end. Quando uma aplicação é publicada através do Azure AD Application Proxy, o tráfego dos utilizadores para as aplicações flui através de três lúpulos:

1. O utilizador liga-se ao serviço de procuração de aplicações Azure AD proxy no Azure. A ligação está estabelecida entre o endereço IP do cliente originário (público) do cliente e o endereço IP do ponto final do Proxy de Aplicação.
2. O conector Proxy de Aplicação retira o pedido http do cliente do Serviço de Procuração de Pedidos.
3. O conector Proxy de Aplicação liga-se à aplicação-alvo. O conector utiliza o seu próprio endereço IP para estabelecer a ligação.

![Diagrama de ligação do utilizador a uma aplicação via Proxy de aplicação](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-Forwarded-Para considerações de campo de cabeçalho
Em algumas situações (como auditoria, equilíbrio de carga, etc.), partilhar o endereço IP originário do cliente externo com o ambiente no local é um requisito. Para responder ao requisito, o conector De procuração de aplicação Azure AD adiciona o campo de cabeçalho X-Forwarded-For com o endereço IP do cliente originário (público) ao pedido http. O dispositivo de rede apropriado (balanceor de carga, firewall) ou o servidor web ou aplicação back-end podem então ler e utilizar as informações.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>As melhores práticas para equilibrar a carga entre vários servidores de aplicações
Quando o grupo de conector que é atribuído à aplicação Proxy de aplicação tem dois ou mais conectores, e você está executando a aplicação web back-end em vários servidores (fazenda de servidores), é necessária uma boa estratégia de equilíbrio de carga. Uma boa estratégia garante que os servidores captam os pedidos dos clientes de forma uniforme e impedem a sobreutilização ou subutilização de servidores na exploração do servidor.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Cenário 1: Aplicação back-end não requer persistência da sessão
O cenário mais simples é onde a aplicação web back-end não requer a cisma da sessão (persistência da sessão). Qualquer pedido do utilizador pode ser tratado por qualquer instância de aplicação de back-end na exploração do servidor. Pode utilizar um equilibrante de carga de camada 4 e configurá-lo sem afinidade. Algumas opções incluem o Equilíbrio de Carga da Rede Microsoft e o Equilíbrio de Carga Azure ou um balancer de carga de outro fornecedor. Em alternativa, o DNS de robin redondo pode ser configurado.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Cenário 2: Aplicação back-end requer persistência de sessão
Neste cenário, a aplicação web back-end requer a persistência da sessão (persistência da sessão) durante a sessão autenticada. Todos os pedidos do utilizador devem ser tratados pela instância de aplicação de back-end que corre no mesmo servidor na exploração do servidor.
Este cenário pode ser mais complicado porque o cliente geralmente estabelece múltiplas ligações ao serviço Proxy de Aplicação. Pedidos sobre diferentes ligações podem chegar a diferentes conectores e servidores na quinta. Uma vez que cada conector utiliza o seu próprio endereço IP para esta comunicação, o equilibrista de carga não pode garantir a cisma da sessão com base no endereço IP dos conectores. Fonte IP Affinity também não pode ser usada.
Aqui estão algumas opções para o cenário 2:

- Opção 1: Baseie a persistência da sessão numa sessão definida pelo equilibrador de carga. Esta opção é recomendada porque permite que a carga seja distribuída de forma mais homogonada entre os servidores de back-end. Requer um equilibrador de carga de camada 7 com esta capacidade e que pode lidar com o tráfego HTTP e terminar a ligação TLS. Pode utilizar o Portal de Aplicações Azure (Session Affinity) ou um equilibrador de carga de outro fornecedor.

- Opção 2: Baseie a persistência da sessão no campo de cabeçalho X-Forwarded-For. Esta opção requer um equilibrador de carga de camada 7 com esta capacidade e que pode lidar com o tráfego HTTP e terminar a ligação TLS.  

- Opção 3: Configure a aplicação de back-end para não exigir persistência da sessão.

Consulte a documentação do seu fornecedor de software para compreender os requisitos de equilíbrio de carga da aplicação back-end.

## <a name="next-steps"></a>Passos seguintes

- [Ativar proxy de aplicação](application-proxy-add-on-premises-application.md)
- [Ativar o início de sessão único](application-proxy-configure-single-sign-on-with-kcd.md)
- [Ativar o acesso condicional](application-proxy-integrate-with-sharepoint-server.md)
- [Resolver problemas com o Proxy da Aplicação](application-proxy-troubleshoot.md)
- [Saiba como a arquitetura Azure AD suporta alta disponibilidade](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
