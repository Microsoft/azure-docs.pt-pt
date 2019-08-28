---
title: Distribuições do Linux endossadas no Azure | Microsoft Docs
description: Saiba mais sobre o Linux em distribuições endossadas pelo Azure, incluindo diretrizes para Ubuntu, CentOS, Oracle e SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/16/2019
ms.author: szark
ms.openlocfilehash: 4b254b508e5fac5721e579620e207c2a8612352d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083409"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Distribuições do Linux endossadas no Azure
Os parceiros fornecem imagens do Linux no Azure Marketplace. Estamos trabalhando com várias comunidades do Linux para adicionar ainda mais tipos à lista de distribuição endossada. Enquanto isso, para as distribuições que não estão disponíveis no Marketplace, você sempre pode trazer seu próprio Linux seguindo as diretrizes em [criar e carregar um disco rígido virtual que contém o sistema operacional Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Distribuições e versões com suporte
A tabela a seguir lista as distribuições e versões do Linux com suporte no Azure. Consulte [suporte para imagens do Linux no Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) para obter informações mais detalhadas sobre o suporte para Linux e tecnologia de software livre no Azure.

Os drivers de LIS (Linux Integration Services) para Hyper-V e Azure são módulos de kernel que a Microsoft contribui diretamente para o kernel upstream do Linux.  Alguns drivers de LIS são incorporados no kernel da distribuição por padrão. As distribuições mais antigas baseadas no Red Hat Enterprise (RHEL)/CentOS estão disponíveis como um download separado em [Linux Integration Services versão 4,2 para Hyper-V e Azure](https://www.microsoft.com/download/details.aspx?id=55106). Consulte [requisitos de kernel do Linux](create-upload-generic.md#linux-kernel-requirements) para obter mais informações sobre os drivers Lis.

O agente Linux do Azure já está pré-instalado nas imagens do Azure Marketplace e normalmente está disponível no repositório de pacotes da distribuição. O código-fonte pode ser encontrado no [GitHub](https://github.com/azure/walinuxagent).


| Distribuição | Version | Controladores | Agente |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 +, 7.0 + |CentOS 6,3: [Download do LIS](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: No kernel |Pacote: No [repositório](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) em "WALinuxAgent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |No kernel |Código-fonte: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9 +, 8.2 + |No kernel |Pacote: No repositório em "waagent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |No kernel |Pacote: No repositório em "WALinuxAgent" <br/>Código-fonte: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7 +, 7.1 +, 8.0 + |No kernel |Pacote: No repositório em "WALinuxAgent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES para SAP<br>11 SP4<br>12 SP1 +<br>15|No kernel |Pacote:<p> para 11 na [nuvem:](https://build.opensuse.org/project/show/Cloud:Tools) repositório de ferramentas<br>para 12 incluído no módulo "nuvem pública" em "Python-Azure-Agent"<br/>Código-fonte: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2 + |No kernel |Pacote: Em [nuvem:](https://build.opensuse.org/project/show/Cloud:Tools) repositório de ferramentas em "Python-Azure-Agent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 +  **<sup>1</sup>** |No kernel |Pacote: No repositório em "walinuxagent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** informações sobre o suporte estendido para Ubuntu 12, 4 e 14, 4 podem ser encontradas aqui: [Manutenção de segurança estendida do Ubuntu](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Cadência da atualização da imagem
O Azure exige que os editores das distribuições do Linux endossadas atualizem regularmente suas imagens no Azure Marketplace com os patches e correções de segurança mais recentes, a uma cadência trimestral ou mais rápida. As imagens atualizadas no Azure Marketplace estão disponíveis automaticamente para clientes como novas versões de uma SKU de imagem. Mais informações sobre como encontrar imagens do Linux: [Encontre imagens de VM do Linux no Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Links adicionais
 - [Ciclo de vida da imagem de nuvem pública SUSE](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Kernels ajustados para o Azure

O Azure trabalha em conjunto com várias distribuições do Linux endossadas para otimizar as imagens publicadas no Azure Marketplace. Um aspecto dessa colaboração é o desenvolvimento de kernels do Linux "ajustados" que são otimizados para a plataforma Azure e fornecidos como componentes totalmente suportados da distribuição do Linux. Os kernels ajustados para o Azure incorporam novos recursos e melhorias de desempenho e em uma cadência mais rápida (normalmente trimestral) em comparação com os kernels padrão ou genéricos que estão disponíveis na distribuição.

Na maioria dos casos, você encontrará esses kernels pré-instalados nas imagens padrão no Azure Marketplace e, portanto, os clientes do Azure obterão imediatamente os benefícios desses kernels otimizados. Mais informações sobre esses kernels ajustados para o Azure podem ser encontradas nos links a seguir:

 - CentOS kernel para o Azure ajustado – disponível por meio do CentOS Virtualization SIG- [mais informações](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Kernel de nuvem Debian-disponível com a imagem "backports" do Debian 10 e Debian 9 no Azure- [mais informações](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - Kernel ajustado do SLES Azure- [mais informações](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Kernel do Ubuntu ajustado pelo Azure- [mais informações](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Parceiros

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

No site do CoreOS:

*O CoreOS foi projetado para segurança, consistência e confiabilidade. Em vez de instalar pacotes por meio de yum ou apt, o CoreOS usa contêineres do Linux para gerenciar seus serviços em um nível mais alto de abstração. O código de um único serviço e todas as dependências são empacotados em um contêiner que pode ser executado em um ou vários computadores CoreOS.*

### <a name="credativ"></a>credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

O credativ é uma empresa independente de consultoria e serviços que é especializada no desenvolvimento e na implementação de soluções profissionais usando software gratuito. Como especialistas de código-fonte aberto, a credativ tem reconhecimento internacional com muitos departamentos de ti que usam o seu suporte. Em conjunto com a Microsoft, o credativ está atualmente preparando imagens Debian correspondentes para Debian 8 (Jessie) e Debian antes de 7 (Wheezy). As duas imagens são especialmente projetadas para serem executadas no Azure e podem ser facilmente gerenciadas por meio da plataforma. O credativ também dará suporte à manutenção e à atualização de longo prazo das imagens Debian para o Azure por meio de seus centros de suporte de software livre.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

A estratégia da Oracle é oferecer um amplo portfólio de soluções para nuvens públicas e privadas. A estratégia oferece aos clientes a opção e a flexibilidade de como eles implantam o software Oracle em nuvens Oracle e em outras nuvens. A parceria da Oracle com a Microsoft permite aos clientes implementar software Oracle nas clouds pública e privada da Microsoft sabendo que conta com a certificação e o suporte da Oracle.  O compromisso e o investimento da Oracle em soluções de nuvem pública e privada da Oracle estão inalterados.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

O principal provedor de soluções de software livre do mundo, a Red Hat ajuda mais de 90% das empresas da Fortune 500 a resolverem desafios comerciais, a alinhar suas estratégias de ti e de negócios e a se preparar para o futuro da tecnologia. O Red Hat faz isso fornecendo soluções seguras por meio de um modelo de negócios aberto e um modelo de assinatura acessível e previsível.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

O SUSE Linux Enterprise Server no Azure é uma plataforma comprovada que fornece confiabilidade e segurança superiores para a computação em nuvem. A plataforma Linux versátil do SUSE integra-se perfeitamente aos serviços de nuvem do Azure para fornecer um ambiente de nuvem facilmente gerenciável. Com mais de 9.200 aplicativos certificados de mais de 1.800 fornecedores de software independentes para o SUSE Linux Enterprise Server, o SUSE garante que as cargas de trabalho com suporte no data center possam ser implantadas com segurança no Azure.

### <a name="canonical"></a>Canónico
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Engenharia canônica e abertura de governança da Comunidade da unidade de controle do Ubuntu no cliente, servidor e computação em nuvem, que inclui serviços de nuvem pessoal para consumidores. A visão canônica de uma plataforma unificada e gratuita no Ubuntu, do telefone à nuvem, fornece uma família de interfaces coerentes para o telefone, Tablet, TV e área de trabalho. Essa visão torna o Ubuntu a primeira opção para instituições diversificadas de provedores de nuvem pública para os criadores de eletrônicos de consumidores e um favorito entre tecnólogos individuais.

Com desenvolvedores e centros de engenharia em todo o mundo, a Canonical está posicionada exclusivamente para fazer parceria com criadores de hardware, provedores de conteúdo e desenvolvedores de software para trazer soluções Ubuntu para o mercado para PCs, servidores e dispositivos portáteis.
