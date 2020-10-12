---
title: Alta disponibilidade e equilíbrio de carga - Azure AD Application Proxy
description: Como a distribuição de tráfego funciona com a sua implementação de Procuração de Aplicação. Inclui dicas para como otimizar o desempenho do conector e utilizar o equilíbrio de carga para servidores de back-end.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ab3b340654fd6d824edef0a33d1ea363a913654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764592"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Alta disponibilidade e equilíbrio de carga dos conectores e aplicações Proxy da aplicação

Este artigo explica como a distribuição de tráfego funciona com a sua implementação de Procuração de Aplicação. Vamos abordar:

- Como o tráfego é distribuído entre utilizadores e conectores, juntamente com dicas para otimizar o desempenho do conector

- Como o tráfego flui entre conectores e servidores de aplicações back-end, com recomendações para o equilíbrio de carga entre vários servidores de back-end

## <a name="traffic-distribution-across-connectors"></a>Distribuição de tráfego através de conectores

Os conectores estabelecem as suas ligações com base em princípios de elevada disponibilidade. Não há garantias de que o tráfego será sempre distribuído uniformemente pelos conectores e não há afinidade de sessão. No entanto, o uso varia e os pedidos são enviados aleatoriamente para instâncias de serviço de Procuração de Aplicação. Como resultado, o tráfego é normalmente distribuído quase uniformemente pelos conectores. O diagrama e os passos abaixo ilustram como as ligações são estabelecidas entre utilizadores e conectores.

![Diagrama que mostra ligações entre utilizadores e conectores](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Um utilizador num dispositivo cliente tenta aceder a uma aplicação no local publicada através do Application Proxy.
2. O pedido passa por um Balançador de Carga Azure para determinar qual a instância de serviço de procuração de aplicação que deve aceitar o pedido. Por região, existem dezenas de casos disponíveis para aceitar o pedido. Este método ajuda a distribuir uniformemente o tráfego através das instâncias de serviço.
3. O pedido é enviado para a [Service Bus.](https://docs.microsoft.com/azure/service-bus-messaging/)
4. Serviço de ônibus sinaliza para um conector disponível. Em seguida, o conector recolhe o pedido da Service Bus.
   - No passo 2, os pedidos vão para diferentes instâncias de serviço de Procuração de Aplicação, pelo que as ligações são mais propensas a serem feitas com diferentes conectores. Como resultado, os conectores são quase uniformemente utilizados dentro do grupo.
5. O conector transmite o pedido para o servidor back-end da aplicação. Em seguida, a aplicação envia a resposta de volta para o conector.
6. O conector completa a resposta abrindo uma ligação de saída à instância de serviço de onde veio o pedido. Então esta ligação está imediatamente fechada. Por predefinição, cada conector está limitado a 200 ligações de saída simultâneas.
7. A resposta é então transmitida ao cliente a partir da instância de serviço.
8. Os pedidos subsequentes da mesma ligação repetem os passos acima.

Uma aplicação tem muitas vezes muitos recursos e abre múltiplas ligações quando está carregada. Cada ligação passa pelos passos acima para ser atribuída a uma instância de serviço, selecione um novo conector disponível se a ligação ainda não tiver sido previamente emparelhada com um conector.


## <a name="best-practices-for-high-availability-of-connectors"></a>Melhores práticas para a elevada disponibilidade de conectores

- Devido à forma como o tráfego é distribuído entre conectores para uma elevada disponibilidade, é essencial ter sempre pelo menos dois conectores num grupo de conectores. Três conectores são preferidos para fornecer tampão adicional entre conectores. Para determinar o número correto de conectores necessários, siga a documentação de planeamento da capacidade.

- Coloque os conectores em diferentes ligações de saída para evitar um único ponto de falha. Se os conectores utilizarem a mesma ligação de saída, um problema de rede com a ligação pode ter impacto em todos os conectores que a utilizam.

- Evite forçar os conectores a reiniciar quando ligados às aplicações de produção. Fazê-lo pode ter um impacto negativo na distribuição do tráfego através dos conectores. O reiniciamento dos conectores faz com que mais conectores não estejam disponíveis e força as ligações ao conector disponível restante. O resultado é uma utilização desigual dos conectores inicialmente.

- Evite todas as formas de inspeção em linha nas comunicações TLS de saída entre conectores e Azure. Este tipo de inspeção inline causa degradação ao fluxo de comunicação.

- Certifique-se de que mantém as atualizações automáticas a funcionar para os seus conectores. Se o serviço de atualização do conector de aplicação proxy estiver em execução, os seus conectores atualizam-se automaticamente e recebem as últimas atualizações atualizadas. Se não vir o serviço de atualização do Conector no seu servidor, tem de reinstalar o seu conector para obter quaisquer atualizações.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Fluxo de tráfego entre conectores e servidores de aplicações de back-end

Outra área-chave onde a alta disponibilidade é um fator é a ligação entre os conectores e os servidores de back-end. Quando uma aplicação é publicada através do Azure AD Application Proxy, o tráfego dos utilizadores para as aplicações flui através de três lúpulos:

1. O utilizador conecta-se ao serviço público de procuração de aplicação Azure em Azure. A ligação é estabelecida entre o endereço IP do cliente de origem (público) do cliente e o endereço IP do ponto final do Application Proxy.
2. O conector de procuração de aplicação retira o pedido HTTP do cliente do Serviço de Procuração de Aplicações.
3. O conector Application Proxy liga-se à aplicação-alvo. O conector utiliza o seu próprio endereço IP para estabelecer a ligação.

![Diagrama do utilizador que se conecta a uma aplicação via Application Proxy](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>Considerações de campo de cabeçalho X-Forwarded
Em algumas situações (como auditoria, equilíbrio de carga, etc.), partilhar o endereço IP originário do cliente externo com o ambiente no local é um requisito. Para responder ao requisito, o conector Proxy de aplicação AD Azure adiciona o campo de cabeçalho X-Forwarded-For com o endereço IP do cliente de origem (público) ao pedido HTTP. O dispositivo de rede apropriado (balanceador de carga, firewall) ou o servidor web ou aplicação de back-end pode então ler e utilizar as informações.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Melhores práticas para equilibrar carga entre vários servidores de aplicações
Quando o grupo de conector que está atribuído à aplicação Application Proxy tem dois ou mais conectores, e está a executar a aplicação web de back-end em vários servidores (servidor farm), é necessária uma boa estratégia de equilíbrio de carga. Uma boa estratégia garante que os servidores captam os pedidos dos clientes de forma uniforme e evita a utilização excessiva ou subutilização dos servidores na fazenda de servidores.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Cenário 1: Aplicação de back-end não requer persistência da sessão
O cenário mais simples é onde a aplicação web back-end não requer pegajosa de sessão (persistência da sessão). Qualquer pedido do utilizador pode ser tratado por qualquer instância de aplicação de back-end na fazenda do servidor. Pode utilizar um equilibrador de carga de camada 4 e configurá-lo sem afinidade. Algumas opções incluem o Equilíbrio de Carga da Rede Microsoft e o Equilibr de Carga Azure ou um equilibrador de carga de outro fornecedor. Alternativamente, o DNS redondo pode ser configurado.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Cenário 2: Aplicação de back-end requer persistência da sessão
Neste cenário, a aplicação web back-end requer adesão de sessão (persistência da sessão) durante a sessão autenticada. Todos os pedidos do utilizador devem ser tratados pela instância de aplicação back-end que funciona no mesmo servidor na fazenda do servidor.
Este cenário pode ser mais complicado porque o cliente geralmente estabelece múltiplas ligações ao serviço Application Proxy. Pedidos sobre diferentes ligações podem chegar a diferentes conectores e servidores na fazenda. Como cada conector utiliza o seu próprio endereço IP para esta comunicação, o balanceador de carga não consegue garantir a pegajosa da sessão com base no endereço IP dos conectores. Fonte IP Affinity também não pode ser usada.
Aqui estão algumas opções para o cenário 2:

- Opção 1: Baseie a persistência da sessão num cookie de sessão definido pelo equilibrador de carga. Esta opção é recomendada porque permite que a carga seja distribuída de forma mais uniforme entre os servidores de back-end. Requer um equilibrador de carga de camada 7 com esta capacidade e que possa manusear o tráfego HTTP e terminar a ligação TLS. Pode utilizar o Gateway de Aplicações Azure (Session Affinity) ou um equilibrador de carga de outro fornecedor.

- Opção 2: Baseie a persistência da sessão no campo de cabeçalho X-Forwarded.For. Esta opção requer um equilibrador de carga de camada 7 com esta capacidade e que pode lidar com o tráfego HTTP e terminar a ligação TLS.  

- Opção 3: Configurar a aplicação back-end para não requerer persistência da sessão.

Consulte a documentação do seu fornecedor de software para entender os requisitos de equilíbrio de carga da aplicação back-end.

## <a name="next-steps"></a>Passos seguintes

- [Ativar procuração de aplicação](application-proxy-add-on-premises-application.md)
- [Ativar o início de sessão único](application-proxy-configure-single-sign-on-with-kcd.md)
- [Ativar o acesso condicional](application-proxy-integrate-with-sharepoint-server.md)
- [Resolver problemas com o Proxy da Aplicação](application-proxy-troubleshoot.md)
- [Saiba como a arquitetura AD AD AZure suporta alta disponibilidade](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
