---
title: Arquitetura de rede Azure
description: Este artigo fornece uma descrição geral da rede de infraestruturas do Microsoft Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727179"
---
# <a name="azure-network-architecture"></a>Arquitetura de rede Azure
A arquitetura da rede Azure segue uma versão modificada do modelo padrão de core/distribuição/acesso da indústria, com camadas de hardware distintas. As camadas incluem:

- Núcleo (routers datacenter)
- Distribuição (routers de acesso e agregação L2). A camada de distribuição separa o encaminhamento L3 da comutação L2.
- Acesso (interruptores de acolhimento L2)

A arquitetura da rede tem dois níveis de interruptores de camada 2. Uma camada agrega o tráfego da outra camada. A segunda camada dá voltas para incorporar o despedimento. A arquitetura proporciona uma pegada VLAN mais flexível, e melhora a escala portuária. A arquitetura mantém L2 e L3 distintas, o que permite o uso de hardware em cada uma das camadas distintas da rede, e minimiza a falha numa camada de afetar a outra camada. A utilização de troncos permite a partilha de recursos, como a conectividade com a infraestrutura L3.

## <a name="network-configuration"></a>Configuração da rede
A arquitetura de rede de um cluster Azure dentro de um datacenter consiste nos seguintes dispositivos:

- Routers (datacenter, router de acesso e routers de folhas de fronteira)
- Interruptores (agregação e interruptores topo de gama)
- Digi CMs
- Unidades de distribuição de energia

Azure tem duas arquiteturas separadas. Alguns clientes azure existentes e serviços partilhados residem na arquitetura PADRÃO LAN (DLA), enquanto novas regiões e clientes virtuais residem na arquitetura Quantum 10 (Q10). A arquitetura DLA é um design tradicional de árvores, com routers de acesso ativo/passivo e listas de controlo de acesso de segurança (ACLs) aplicadas aos routers de acesso. A arquitetura Quantum 10 é um design de close/malha de routers, onde os ACLs não são aplicados nos routers. Em vez disso, os ACLs são aplicados abaixo do encaminhamento, através do Equilíbrio de Carga de Software (SLB) ou do software definido VLANs.

O diagrama seguinte fornece uma visão geral de alto nível da arquitetura da rede dentro de um cluster Azure:

![Diagrama da rede Azure](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Dispositivos Quânticos 10
O design Quantum 10 conduz a comutação da camada 3 espalhada por vários dispositivos num design clos/malha. As vantagens do design Q10 incluem maior capacidade e maior capacidade de escalar a infraestrutura de rede existente. O design emprega routers de folhas de fronteira, interruptores de coluna e routers topo de gama para passar o tráfego para clusters em várias rotas, permitindo a tolerância à falha. O equilíbrio de carga de software, em vez de dispositivos de hardware, trata de serviços de segurança como a tradução de endereços de rede.

### <a name="access-routers"></a>Routers de acesso
Os routers L3 de distribuição/acesso (ARs) executam a principal funcionalidade de encaminhamento para as camadas de distribuição e acesso. Estes dispositivos são implantados como um par, e são a porta de entrada padrão para subredes. Cada par DE AR pode suportar vários pares de interruptores de agregação L2, dependendo da capacidade. O número máximo depende da capacidade do dispositivo, bem como dos domínios de avaria. Um número típico é três pares de interruptores de agregação L2 por par DE AR.

### <a name="l2-aggregation-switches"></a>Interruptores de agregação L2  
Estes dispositivos servem como ponto de agregação para o tráfego L2. São a camada de distribuição do tecido L2, e podem lidar com grandes quantidades de tráfego. Como estes dispositivos agregam o tráfego, necessitam de funcionalidade seletiva de 802,1q e tecnologias de alta largura de banda, como a agregação de portas e 10GE.

### <a name="l2-host-switches"></a>Interruptores de anfitriões L2
Os anfitriões ligam-se diretamente a estes interruptores. Podem ser interruptores montados em rack, ou implantações de chassis. A norma de 802.1q permite a designação de um VLAN como VLAN nativo, tratando esse VLAN como enquadramento ethernet normal (não marcado). Em circunstâncias normais, os quadros do VLAN nativo são transmitidos e recebidos sem marcação numa porta de tronco de 802,1q. Esta função foi concebida para migração para 802.1q e compatibilidade com dispositivos não-802.1q capazes. Nesta arquitetura, apenas a infraestrutura de rede utiliza o VLAN nativo.

Esta arquitetura especifica um padrão para a seleção vlan nativa. A norma garante, sempre que possível, que os dispositivos AR tenham um VLAN nativo único para cada tronco e a l2Aggregação aos troncos de gregação L2Aggregation. A l2Aggregação aos calções L2Host Switch tem um VLAN nativo não predefinido.

### <a name="link-aggregation-8023ad"></a>Agregação de ligações (802.3ad)
A agregação de ligações permite que várias ligações individuais sejam agregadas e tratadas como uma única ligação lógica. Para facilitar a depuração operacional, o número utilizado para designar interfaces de canais portuários deve ser normalizado. O resto da rede utiliza o mesmo número em ambas as extremidades de um canal portuário.

Os números especificados para o interruptor L2Agg a L2Host são os números do canal de porta utilizados no lado L2Agg. Como o intervalo de números é mais limitado no lado L2Host, o padrão é usar os números 1 e 2 no lado L2Host. Estes referem-se ao canal portuário que vai para o lado "a" e para o lado "b", respectivamente.

### <a name="vlans"></a>VLANs
A arquitetura de rede usa VLANs para agrupar servidores num único domínio de transmissão. Os números vLAN estão em conformidade com o padrão 802.1q, que suporta vlans numerados 1-4094.

### <a name="customer-vlans"></a>VLANs do cliente
Tem várias opções de implementação VLAN que pode implementar através do portal Azure para atender às necessidades de separação e arquitetura da sua solução. Implementa estas soluções através de máquinas virtuais. Para exemplos de arquitetura de referência do cliente, consulte [arquiteturade referência Azure.](https://docs.microsoft.com/azure/architecture/reference-architectures/)

### <a name="edge-architecture"></a>Arquitetura de borda
Os centros de dados Azure são construídos com infraestruturas de rede altamente redundantes e bem aprovisionadas. A Microsoft implementa redes dentro dos datacenters do Azure com arquiteturas de redundância "need plus one" (N+1) ou melhor. As funcionalidades completas de failover dentro e entre datacenters ajudam a garantir a disponibilidade de rede e serviço. Externamente, os datacenters são servidos por circuitos de rede dedicados e de alta largura de banda. Estes circuitos conectam redundantemente propriedades com mais de 1200 fornecedores de serviços de internet em todo o mundo em vários pontos de observação. Isto fornece mais de 2.000 Gbps de potencial capacidade de borda em toda a rede.

Filtrar routers na camada de borda e acesso da rede Azure fornece segurança bem estabelecida ao nível do pacote e ajuda a prevenir tentativas não autorizadas de ligação ao Azure. Os routers ajudam a garantir que o conteúdo real dos pacotes contém dados no formato esperado, e estão em conformidade com o esquema de comunicação cliente/servidor esperado. O Azure implementa uma arquitetura hierárquica, constituída pelos seguintes componentes de segregação de rede e controlo de acesso:

- **Routers de borda.** Estes segregam o ambiente de aplicação da internet. Os routers de borda são projetados para fornecer proteção anti-spoof e limitar o acesso utilizando ACLs.
- **Routers de distribuição (acesso).** Estes permitem apenas endereços IP aprovados pela Microsoft, fornecem anti-falsificação e estabelecem ligações utilizando ACLs.

### <a name="ddos-mitigation"></a>Mitigação do DDOS
Os ataques de negação de serviço distribuídos (DDoS) continuam a representar uma ameaça real à fiabilidade dos serviços online. À medida que os ataques se tornam mais direcionados e sofisticados, e à medida que os serviços que a Microsoft fornece se tornam mais diversificados geograficamente, identificar e minimizar o impacto destes ataques é uma prioridade.

A Norma de [Proteção DDoS Azure](../../virtual-network/ddos-protection-overview.md) fornece defesa contra ataques dDoS. Ver [Azure DDoS Protection: As melhores práticas e arquiteturas de referência](ddos-best-practices.md) para saber mais.

> [!NOTE]
> A Microsoft fornece proteção DDoS por padrão para todos os clientes Azure.
>
>

## <a name="network-connection-rules"></a>Regras de ligação à rede
Na sua rede, o Azure implementa routers de borda que fornecem segurança ao nível do pacote para evitar tentativas não autorizadas de ligação ao Azure. Os routers de borda garantem que o conteúdo real dos pacotes contém dados no formato esperado e estão em conformidade com o esquema de comunicação cliente/servidor esperado.

Os routers de borda segregam o ambiente de aplicação da internet. Estes routers são projetados para fornecer proteção anti-spoof, e limitar o acesso usando ACLs. A Microsoft confunde routers de borda utilizando uma abordagem ACL hierárquica, para limitar os protocolos de rede que são autorizados a transitar os routers de borda e os routers de acesso.

A Microsoft posiciona dispositivos de rede em locais de acesso e de borda, para funcionar como pontos de fronteira onde são aplicados filtros de entrada ou saída.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física do Azure](physical-security.md)
- [Disponibilidade de infraestruturas azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação Azure](infrastructure-components.md)
- [Rede de produção azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização de infraestruturas Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azure](infrastructure-integrity.md)
- [Proteção de dados dos clientes azure](protection-customer-data.md)


