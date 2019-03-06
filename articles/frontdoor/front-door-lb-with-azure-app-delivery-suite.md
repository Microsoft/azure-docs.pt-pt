---
title: Serviço de porta de entrada do Azure - balanceamento de carga com o conjunto de entrega de aplicativos do Azure | Documentos da Microsoft
description: Este artigo ajuda-o a saber mais sobre como o Azure recomenda balanceamento de carga com de entrega conjunto de aplicativos
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: a89043f814bc97aeb081789e92d9e4488712a465
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439031"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Balanceamento de carga com o conjunto de entrega de aplicações do Azure

## <a name="introduction"></a>Introdução
O Microsoft Azure fornece vários serviços globais e regionais para gerir a forma como o seu tráfego de rede é distribuído e com balanceamento de carga: Gestor de tráfego, o serviço de porta de entrada, o Gateway de aplicação e o Balanceador de carga.  Juntamente com muitas regiões que escolheu do Azure e zonais o arquitetura, em conjunto a utilizar estes serviços permitem-lhe criar aplicações de alto desempenho robustas e escaláveis.

![Conjunto de entrega de aplicativos ][1]
 
Estes serviços são divididos em duas categorias:
1. **Serviços de balanceamento de carga global** , como o Gestor de tráfego e a porta da frente distribuem o tráfego dos seus utilizadores finais em sua back-ends regionais, entre nuvens ou até mesmo seus serviços do híbrida no local. Balanceamento de carga global encaminha o tráfego para o back-end de serviço mais próximo e reagirá as alterações na fiabilidade do serviço ou de desempenho para manter o desempenho de sempre ativa, máximo para os seus utilizadores. 
2. **Serviços de balanceamento de carga de regional** , como o Gateway de aplicação ou Balanceador de carga Standard oferecem a capacidade de distribuir o tráfego nas redes virtuais (VNETs) por suas máquinas virtuais (VMs) ou pontos finais de serviço zonal dentro de uma região.

A combinação de serviços global e regionais em seu aplicativo fornece um desempenho fiável e de ponto-a-ponto e forma segura para encaminhar o tráfego de e para os seus utilizadores para o IaaS, PaaS ou serviços no local. Na próxima seção, descrevemos de cada um desses serviços.

## <a name="global-load-balancing"></a>Balanceamento de carga global
**O Gestor de tráfego** fornece balanceamento de carga de global DNS. Ele examina os pedidos recebidos de DNS e responde com um bom estado de funcionamento back-end em conformidade com a política de encaminhamento, que o cliente tiver selecionado. Opções de métodos de encaminhamento são:
- Encaminhamento para enviar o requerente para o back-end mais próximo em termos de latência do desempenho.
- Prioridade de encaminhamento para direcionar todo o tráfego para um back-end, com outros back-ends como cópia de segurança.
- Round robin ponderado roteamento, que distribui o tráfego com base no peso atribuído a cada back-end.
- Encaminhamento geográfico para garantir que os requerentes localizados em regiões geográficas específicas são direcionadas para os back-ends mapeados para essas regiões (por exemplo, todos os pedidos de Espanha devem ser direcionados para a região do Azure de França Central)
- Encaminhamento de sub-rede, que permite mapear o endereço IP intervalos para back-ends para que solicitações futuras daqueles serão enviadas para o back-end especificado (por exemplo, todos os utilizadores a ligar a partir do intervalo de endereços IP do seu sede empresarial devem obter conteúdo do web diferente que gerais utilizadores)

O cliente liga-se diretamente a esse back-end. O Gestor de tráfego do Azure Deteta quando um back-end está danificado e, em seguida, redireciona os clientes para outra instância de bom estado de funcionamento. Consulte a [Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) documentação para saber mais sobre o serviço.

**O serviço do Azure desde início** fornece a aceleração de site dinâmico (DSA), incluindo o balanceamento de carga HTTP global.  Ele verificará os pedidos HTTP recebidos rotas para o back-end de serviço mais próximo / região para o nome de anfitrião especificado, o caminho de URL e o regras configuradas.  
Porta de entrada termina a pedidos HTTP na borda da rede da Microsoft e sondas ativamente para detectar alterações de estado de funcionamento ou latência de aplicação ou a infraestrutura.  Porta de entrada, em seguida, sempre encaminha o tráfego para o mais rápido e disponível (bom) back-end. Consulte da porta da frente [arquitetura de roteamento](front-door-routing-architecture.md) detalhes e [métodos de encaminhamento de tráfego](front-door-routing-methods.md) para saber mais sobre o serviço.

## <a name="regional-load-balancing"></a>Balanceamento de carga regional
Gateway de aplicação fornece o controlador de entrega de aplicações (ADC) como um serviço, oferecendo vários grupos de recursos de balanceamento de carga de camada 7 para a sua aplicação. Ele permite que os clientes otimizem a produtividade do web farm ao descarregar a terminação de SSL intensiva da CPU para o gateway de aplicação. Outras capacidades de encaminhamento de camada 7 incluem a distribuição round robin de tráfego de entrada, afinidade por sessões com base no cookie, encaminhamento baseado no caminho URL e a capacidade de alojar vários Web sites atrás de um gateway de aplicação único. Gateway de aplicação pode ser configurado como um gateway de acesso à Internet, um gateway só interno ou uma combinação de ambos. Gateway de aplicação é totalmente do Azure gerida, dimensionável e de elevada disponibilidade. Proporciona um conjunto avançado de capacidades de registo e diagnóstico, para uma melhor capacidade de gestão.
Balanceador de carga é uma parte integral da pilha SDN do Azure, fornecendo alto desempenho e de baixa latência camada 4 balanceamento de carga serviços para todos os protocolos UDP e TCP. Gere ligações de entrada e saídas. Pode configurar pontos finais públicos e internos com balanceamento de carga e definir regras para mapear as conexões de entrada para destinos do conjunto de back-end com opções de pesquisa de estado de funcionamento TCP e HTTP para gerir a disponibilidade do serviço.


## <a name="choosing-a-global-load-balancer"></a>Escolher um balanceador de carga global
Ao escolher um balanceador de carga global entre o Gestor de tráfego e a porta de entrada do Azure para o encaminhamento global, deve considerar o que é semelhante e quais são as diferenças sobre os dois serviços.   Fornecem ambos os serviços
- **Redundância de multi-geo:** Se uma região ficar inativo, tráfego de forma totalmente integrada encaminha para a região mais próxima sem qualquer intervenção do proprietário do aplicativo.
- **Encaminhamento de região mais próxima:** Automaticamente o tráfego é encaminhado para a região mais próxima

</br>A tabela seguinte descreve as diferenças entre o Gestor de tráfego e o serviço de porta de entrada do Azure:</br>

| Gestor de Tráfego | Azure Front Door Service |
| --------------- | ------------------------ |
|**Qualquer protocolo:** Como o Gestor de tráfego funciona na camada DNS, pode encaminhar qualquer tipo de tráfego de rede: HTTP, TCP, UDP, etc. | **Aceleração de HTTP:** Com a porta de entrada, o tráfego é transmitidas por proxy na rede do Edge da Microsoft.  Por este motivo, os pedidos de HTTP (S) veja melhorias de latência e débito, reduzindo a latência para negociação de SSL e usando conexões de acesso frequente do AFD para seu aplicativo.|
|**No local encaminhamento:** Com o encaminhamento numa camada DNS, o tráfego segue sempre de ponto a ponto.  Encaminhamento da sua sucursal para o seu datacenter no local no pode demorar um caminho direto; mesmo em sua própria rede utilizando o Gestor de tráfego. | **Escalabilidade independente:** Porque a porta da frente funciona com o pedido HTTP, os pedidos para diferentes caminhos de URL podem ser roteados para back-end diferente / regional conjuntos (microsserviços) com base em regras e o estado de funcionamento de cada microsserviço de aplicação de serviço.|
|**Formato de faturação:** Faturação baseada no DNS dimensiona com os seus utilizadores e serviços com mais usuários, plateaus para reduzir custos, em utilização superior. |**Segurança de inline:** Porta de entrada ativa regras, como a limitação de velocidade e a ACL, ing IP para permitem-lhe proteger o seu back-ends antes do tráfego chega a sua aplicação. 

</br>Por causa do desempenho, capacidade de operação e benefícios de segurança para cargas de trabalho HTTP com a porta da frente, recomendamos que os clientes utilizam a porta de entrada para as cargas de trabalho HTTP.    Gestor de tráfego e a porta de entrada podem ser utilizada em paralelo para servir de todo o tráfego para a sua aplicação. 

## <a name="building-with-azures-application-delivery-suite"></a>A criar com o conjunto de entrega de aplicativos do Azure 
Recomendamos que todos os serviços de Web sites, APIs, serem geograficamente redundantes e enviar o tráfego para os utilizadores a partir a mais próxima (latência mais baixa) localização aos mesmos sempre que possível.  A combinação de serviços do Gestor de tráfego, o serviço de porta de entrada, o Gateway de aplicação e o Balanceador de carga permite-lhe criar geograficamente e zonally redundantes para maximizar a confiabilidade, dimensionamento e desempenho.

O diagrama a seguir, descrevemos um serviço de exemplo que utiliza uma combinação de todos esses serviços para criar um serviço global web.   Neste caso, o arquiteto utilizou o Gestor de tráfego para encaminhar para back-ends global para a prestação de ficheiro e de objeto, ao utilizar a porta de entrada para encaminhar globalmente os caminhos de URL que corresponde ao padrão/store / * para o serviço tiver migrado para o serviço de aplicações ao encaminhamento de todas as outras pedidos para Gateways de aplicação regional.

Na região, seu serviço de IaaS, o desenvolvedor de aplicativos tenha decidido que todos os URLs que corresponde ao padrão/imagens / * são servidos a partir de um conjunto dedicado de VMs que são diferentes do restante da web farm.

Além disso, o conjunto VM predefinido, que serve o conteúdo dinâmico tem de comunicar com um banco de dados de back-end que está alojado num cluster de elevada disponibilidade. Toda a implantação é configurada através do Gestor de recursos do Azure.

O diagrama seguinte mostra a arquitetura de neste cenário:

![Arquitetura detalhada de conjunto de entrega de aplicativos][2] 

> [!NOTE]
> Neste exemplo é apenas uma das muitas configurações possíveis dos serviços de balanceamento de carga que o Azure oferece. O Gestor de tráfego, porta de entrada, o Gateway de aplicação e Balanceador de carga podem ser misturadas e correspondentes para se adequar melhor às suas necessidades de balanceamento de carga. Por exemplo, se a descarga de SSL ou não seja necessário processamento de camada 7, Balanceador de carga pode ser utilizado em vez do Gateway de aplicação.


## <a name="next-steps"></a>Próximos Passos

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
