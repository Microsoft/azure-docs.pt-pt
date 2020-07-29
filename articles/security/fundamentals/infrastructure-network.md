---
title: Arquitetura da rede Azure
description: Este artigo fornece uma descrição geral da rede de infraestruturas Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: c4756c36c2243840df69f3696e7ddac3628f3a00
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "68727179"
---
# <a name="azure-network-architecture"></a>Arquitetura da rede Azure
A arquitetura da rede Azure segue uma versão modificada do modelo padrão de core/distribuição/acesso da indústria, com camadas de hardware distintas. As camadas incluem:

- Núcleo (routers do datacenter)
- Distribuição (routers de acesso e agregação L2). A camada de distribuição separa o encaminhamento L3 da comutação L2.
- Acesso (comutadores de anfitrião L2)

A arquitetura da rede tem dois níveis de interruptores de camada 2. Uma camada agrega o tráfego da outra camada. A segunda camada dá voltas para incorporar a redundância. A arquitetura proporciona uma pegada VLAN mais flexível, e melhora a escala do porto. A arquitetura mantém L2 e L3 distintos, o que permite o uso de hardware em cada uma das camadas distintas da rede, e minimiza a falha numa camada de afetar a(s) outra camada. A utilização de troncos permite a partilha de recursos, como a conectividade com a infraestrutura L3.

## <a name="network-configuration"></a>Configuração de rede
A arquitetura de rede de um cluster Azure dentro de um datacenter consiste nos seguintes dispositivos:

- Routers (datacenter, router de acesso e routers de folhas de fronteira)
- Interruptores (agregação e interruptores topo de cremalheira)
- Digi CMs
- Unidades de distribuição de energia

Azure tem duas arquiteturas separadas. Alguns clientes azure existentes e serviços partilhados residem na arquitetura LAN padrão (DLA), enquanto novas regiões e clientes virtuais residem na arquitetura Quântica 10 (Q10). A arquitetura DLA é um design de árvore tradicional, com routers de acesso ativo/passivo e listas de controlo de acesso de segurança (ACLs) aplicadas aos routers de acesso. A arquitetura Quântica 10 é um design de close/malha de routers, onde os ACLs não são aplicados nos routers. Em vez disso, os ACLs são aplicados abaixo do encaminhamento, através do Equilíbrio de Carga de Software (SLB) ou VLANs definidos por software.

O diagrama a seguir fornece uma visão geral de alto nível da arquitetura da rede dentro de um cluster Azure:

![Diagrama da rede Azure](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Quantum 10 dispositivos
O design Quântico 10 conduz a comutação da camada 3 espalhada por vários dispositivos num design Clos/malha. As vantagens do desenho do Q10 incluem maior capacidade e maior capacidade de escalar a infraestrutura de rede existente. O design emprega routers de folhas de fronteira, interruptores de coluna e routers topo de série para passar o tráfego para clusters em várias rotas, permitindo tolerância a falhas. O equilíbrio da carga de software, em vez de dispositivos de hardware, trata de serviços de segurança como a tradução de endereços de rede.

### <a name="access-routers"></a>Routers de acesso
Os routers L3 de distribuição/acesso (ARs) executam a funcionalidade de encaminhamento primário para as camadas de distribuição e acesso. Estes dispositivos são implantados como um par, e são o portal padrão para sub-redes. Cada par AR pode suportar vários pares de interruptores de agregação L2, dependendo da capacidade. O número máximo depende da capacidade do dispositivo, bem como dos domínios de avaria. Um número típico é três pares de interruptores de agregação L2 por par AR.

### <a name="l2-aggregation-switches"></a>Interruptores de agregação L2  
Estes dispositivos servem como ponto de agregação para o tráfego L2. São a camada de distribuição do tecido L2, e podem lidar com grandes quantidades de tráfego. Como estes dispositivos agregam o tráfego, eles requerem funcionalidade de 802.1q, e tecnologias de alta largura de banda, como agregação portuária e 10GE.

### <a name="l2-host-switches"></a>Interruptores de anfitrião L2
Os anfitriões ligam-se diretamente a estes interruptores. Podem ser interruptores montados em cremalheira ou implantações de chassis. A norma 802.1q permite a designação de um VLAN como um VLAN nativo, tratando o VLAN como um enquadramento ethernet normal (não anotado). Em circunstâncias normais, os quadros do VLAN nativo são transmitidos e recebidos sem ser testados numa porta de tronco de 802.1q. Esta funcionalidade foi concebida para migração para 802.1q e compatibilidade com dispositivos não-802.1q capazes. Nesta arquitetura, apenas a infraestrutura de rede utiliza o VLAN nativo.

Esta arquitetura especifica um padrão para a seleção nativa de VLAN. A norma garante, sempre que possível, que os dispositivos AR tenham um VLAN nativo único para cada tronco e a agregação L2A agregadora a calções de agregação L2A. Os troncos L2A agregação para L2Host Switch têm um VLAN nativo não padrão.

### <a name="link-aggregation-8023ad"></a>Agregação de ligações (802.3ad)
A agregação de ligações permite que várias ligações individuais sejam agregadas e tratadas como uma única ligação lógica. Para facilitar a depuração operacional, o número utilizado para designar interfaces de canal portuário deve ser normalizado. O resto da rede utiliza o mesmo número em ambas as extremidades de um canal portuário.

Os números especificados para o interruptor L2Agg para L2Host são os números de canal de porta utilizados no lado L2Agg. Como a gama de números é mais limitada no lado L2Host, o padrão é usar os números 1 e 2 no lado L2Host. Estes referem-se ao canal de porta que vai para o lado "A" e para o lado "b", respectivamente.

### <a name="vlans"></a>VLANs
A arquitetura da rede usa VLANs para agrupar servidores em conjunto em um único domínio de transmissão. Os números VLAN estão em conformidade com a norma 802.1q, que suporta VLANs numerados 1-4094.

### <a name="customer-vlans"></a>VLANs de clientes
Tem várias opções de implementação VLAN que pode implementar através do portal Azure para atender às necessidades de separação e arquitetura da sua solução. Implementa estas soluções através de máquinas virtuais. Para exemplos de arquitetura de referência do cliente, consulte [as arquiteturas de referência Azure.](https://docs.microsoft.com/azure/architecture/reference-architectures/)

### <a name="edge-architecture"></a>Arquitetura de borda
Os centros de dados Azure baseiam-se em infraestruturas de rede altamente redundantes e bem avisionadas. A Microsoft implementa redes dentro dos datacenters do Azure com arquiteturas de redundância "need plus one" (N+1) ou melhor. As funcionalidades de failover completas dentro e entre centros de dados ajudam a garantir a disponibilidade de rede e serviço. Externamente, os datacenters são servidos por circuitos dedicados de rede de alta largura de banda. Estes circuitos ligam propriedades de forma redundante a mais de 1200 fornecedores de serviços de internet em vários pontos de observação. Isto fornece mais de 2.000 Gbps de capacidade de aresta potencial em toda a rede.

Filtrar os routers na borda e na camada de acesso da rede Azure proporciona uma segurança bem estabelecida ao nível do pacote e ajuda a evitar tentativas não autorizadas de ligação ao Azure. Os routers ajudam a garantir que o conteúdo real dos pacotes contenha dados no formato esperado e esteja em conformidade com o esquema de comunicação cliente/servidor esperado. O Azure implementa uma arquitetura hierarquizada, constituída pelos seguintes componentes de segregação de rede e de controlo de acesso:

- **Routers de borda.** Estes segregam o ambiente de aplicação da internet. Os routers de arestas são concebidos para fornecer proteção anti-spoof e limitar o acesso utilizando ACLs.
- **Routers de distribuição (acesso).** Estes permitem apenas endereços IP aprovados pela Microsoft, fornecem anti-falsificação e estabelecem ligações utilizando ACLs.

### <a name="ddos-mitigation"></a>Mitigação de DDOS
Os ataques de negação de serviço distribuídos (DDoS) continuam a representar uma ameaça real à fiabilidade dos serviços online. À medida que os ataques se tornam mais direcionados e sofisticados, e à medida que os serviços que a Microsoft fornece se tornam geograficamente mais diversificados, identificar e minimizar o impacto destes ataques é uma alta prioridade.

[A Azure DDoS Protection Standard](../../virtual-network/ddos-protection-overview.md) fornece defesa contra ataques DDoS. Ver [Proteção Azure DDoS: Boas práticas e arquiteturas de referência](ddos-best-practices.md) para saber mais.

> [!NOTE]
> A Microsoft fornece proteção DDoS por padrão para todos os clientes Azure.
>
>

## <a name="network-connection-rules"></a>Regras de ligação à rede
Na sua rede, o Azure implementa routers de borda que fornecem segurança ao nível do pacote para evitar tentativas não autorizadas de ligação ao Azure. Os routers edge garantem que o conteúdo real dos pacotes contêm dados no formato esperado e e estão em conformidade com o esquema de comunicação cliente/servidor esperado.

Os routers de borda segregam o ambiente de aplicação da internet. Estes routers são projetados para fornecer proteção anti-spoof, e limitar o acesso usando ACLs. A Microsoft configura os routers de borda utilizando uma abordagem ACL hierárquica, para limitar os protocolos de rede que são autorizados a transitar os routers de borda e os routers de acesso.

A Microsoft posiciona os dispositivos de rede nos locais de acesso e borda, para funcionar como pontos de fronteira onde são aplicados filtros de entrada ou saída.

## <a name="next-steps"></a>Próximos passos
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação azul](infrastructure-components.md)
- [Rede de produção Azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização das infraestruturas do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azul](infrastructure-integrity.md)
- [Proteção de dados do cliente Azure](protection-customer-data.md)


