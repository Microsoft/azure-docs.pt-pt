---
title: Arquiteturas de referência de proteção Azure DDoS
description: Aprenda arquiteturas de referência de proteção Azure DDoS.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: e5472620fe9b07d152a5325b0654044cb1505fd7
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992442"
---
# <a name="ddos-protection-reference-architectures"></a>Arquiteturas de referência de proteção DDoS

A DDoS Protection Standard é concebida [para serviços implantados numa rede virtual.](../virtual-network/virtual-network-for-azure-services.md) Para outros serviços, aplica-se o serviço básico de proteção DDoS predefinido. As seguintes arquiteturas de referência são organizadas por cenários, com padrões de arquitetura agrupados.

## <a name="virtual-machine-windowslinux-workloads"></a>Cargas de trabalho de máquina virtual (Windows/Linux)

### <a name="application-running-on-load-balanced-vms"></a>Aplicação em execução em VMs equilibrados em carga

Esta arquitetura de referência mostra um conjunto de práticas comprovadas para executar vários VMs Windows em uma escala definida atrás de um equilibrador de carga, para melhorar a disponibilidade e escalabilidade. Esta arquitetura pode ser usada para qualquer carga de trabalho apátrida, como um servidor web.

![Diagrama da arquitetura de referência para uma aplicação em execução em VMs equilibrados em carga](./media/ddos-best-practices/image-9.png)

Nesta arquitetura, uma carga de trabalho é distribuída por várias instâncias de VM. Existe um único endereço IP público, e o tráfego de internet é distribuído para os VMs através de um equilibrador de carga. A Norma de Proteção DDoS está ativada na rede virtual do equilibrador de carga Azure (internet) que tem o IP público associado.

O equilibrador de carga distribui os pedidos de entrada na Internet para as instâncias VM. Os conjuntos de escala de máquinas virtuais permitem que o número de VMs seja dimensionado manualmente ou eliminados manualmente, ou automaticamente com base em regras predefinidas. Isto é importante se o recurso estiver sob ataque DDoS. Para mais informações sobre esta arquitetura de referência, consulte [este artigo.](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)

### <a name="application-running-on-windows-n-tier"></a>Aplicação em execução no windows n-tier

Existem várias formas de implementar uma arquitetura de N camadas. O diagrama seguinte mostra uma aplicação web típica de três níveis. Esta arquitetura baseia-se no artigo [Executar VMs equilibrados em carga para escalabilidade e disponibilidade.](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm) As camadas Web e Business utilizam VMs com balanceamento de carga.

![Diagrama da arquitetura de referência para uma aplicação em execução no windows n-tier](./media/ddos-best-practices/image-10.png)

Nesta arquitetura, a DDoS Protection Standard está ativada na rede virtual. Todos os IPs públicos da rede virtual obtêm proteção DDoS para as camadas 3 e 4. Para a proteção da camada 7, coloque o Gateway de aplicação no SKU WAF. Para mais informações sobre esta arquitetura de referência, consulte [este artigo.](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)

> [!NOTE]
> Não são suportados cenários em que um único VM esteja a funcionar atrás de um IP público.

### <a name="paas-web-application"></a>Aplicação web PaaS

Esta arquitetura de referência mostra executar uma aplicação Azure App Service numa única região. Esta arquitetura mostra um conjunto de práticas comprovadas para uma aplicação web que utiliza [o Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) e a [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
Está criada uma região de prontidão para cenários de falhanço.

![Diagrama da arquitetura de referência para uma aplicação web PaaS](./media/ddos-best-practices/image-11.png)

O Gestor de Tráfego da Azure encaminha os pedidos de entrada para o Gateway de Aplicação numa das regiões. Durante as operações normais, encaminha os pedidos para o Gateway de Aplicação na região ativa. Se essa região ficar indisponível, o Gestor de Tráfego falha no Application Gateway na região de espera.

Todo o tráfego da internet destinado à aplicação web é encaminhado para o [endereço IP público do Gateway de Aplicação](../application-gateway/application-gateway-web-app-overview.md) através do Gestor de Tráfego. Neste cenário, o próprio serviço de aplicações (web app) não está diretamente virado para o exterior e está protegido pela App Gateway. 

Recomendamos que configuure o Gateway de Aplicação WAF SKU (modo de prevenção) para ajudar a proteger contra ataques da Camada 7 (HTTP/HTTPS/WebSocket). Além disso, as aplicações web estão configuradas para aceitar apenas o tráfego a partir do endereço [IP do Gateway de Aplicação.](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)

Para mais informações sobre esta arquitetura de referência, consulte [este artigo.](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="mitigation-for-non-web-paas-services"></a>Mitigação para serviços PaaS não web

### <a name="hdinsight-on-azure"></a>HDInsight em Azure

Esta arquitetura de referência mostra configurar o DDoS Protection Standard para um [cluster Azure HDInsight](../hdinsight/index.yml). Certifique-se de que o cluster HDInsight está ligado a uma rede virtual e que a Proteção DDoS está ativada na rede virtual.

![Painéis "HDInsight" e "Configurações avançadas", com configurações de rede virtuais](./media/ddos-best-practices/image-12.png)

![Seleção para permitir a proteção DDoS](./media/ddos-best-practices/image-13.png)

Nesta arquitetura, o tráfego destinado ao cluster HDInsight a partir da internet é encaminhado para o IP público associado ao equilibrador de carga de gateway HDInsight. Em seguida, o balançador de carga de gateway envia o tráfego para os nós da cabeça ou o trabalhador acena diretamente. Como o DDoS Protection Standard está ativado na rede virtual HDInsight, todos os IPs públicos da rede virtual obtêm proteção DDoS para as Camadas 3 e 4. Esta arquitetura de referência pode ser combinada com as arquiteturas de referência N-Tier e multi-regiões.

Para obter mais informações sobre esta arquitetura de referência, consulte o [Extend Azure HDInsight utilizando uma documentação da Rede Virtual Azure.](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json)


> [!NOTE]
> O Azure App Service Environment for PowerApps ou a gestão de API numa rede virtual com um IP público não são ambos suportados de forma nativa.

## <a name="next-steps"></a>Próximos passos

- Saiba como [criar um plano de proteção DDoS](manage-ddos-protection.md).