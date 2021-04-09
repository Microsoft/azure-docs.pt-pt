---
title: Distribuição linux endossada em Azure
description: Saiba mais sobre o Linux sobre as distribuições apoiadas pelo Azure, incluindo diretrizes para Ubuntu, CentOS, Oracle e SUSE.
services: virtual-machines
documentationcenter: ''
author: danielsollondon
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 01/03/2021
ms.author: guybo
ms.openlocfilehash: 091a06fd608763ac7265670733890e93bf71dde1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547412"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Distribuição de Linux endossada em Azure

Os parceiros fornecem imagens Linux no Azure Marketplace. A Microsoft trabalha com várias comunidades Linux para adicionar ainda mais sabores à lista de Distribuição Endossada. Para distribuições que não estão disponíveis no Marketplace, pode sempre trazer o seu próprio Linux seguindo as diretrizes da [Create e carregar um disco rígido virtual que contenha o sistema operativo Linux.](./create-upload-generic.md)

## <a name="supported-distributions-and-versions"></a>Distribuições e versões apoiadas

A tabela que se segue lista as distribuições e versões Linux que são suportadas no Azure. Para obter mais informações, consulte [Suporte para imagens Linux no Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure).

Os controladores linux Integration Services (LIS) para Hyper-V e Azure são módulos de kernel que a Microsoft contribui diretamente para o kernel linux a montante. Alguns condutores lis são incorporados no núcleo da distribuição por padrão. As distribuições mais antigas baseadas na Red Hat Enterprise (RHEL)/CentOS estão disponíveis como um download separado na [Versão 4.2 dos Serviços de Integração Linux para Hyper-V e Azure](https://www.microsoft.com/download/details.aspx?id=55106). Para mais informações, consulte [os requisitos do kernel do Linux.](create-upload-generic.md#linux-kernel-requirements)

O Agente Azure Linux já está pré-instalado em imagens do Azure Marketplace e está tipicamente disponível a partir do repositório de pacotes da distribuição. O código-fonte pode ser encontrado no [GitHub.](https://github.com/azure/walinuxagent)

| Distribuição | Versão | Controladores | Agente |
| --- | --- | --- | --- |
| CentOS by Rogue Wave Software |CentOS 6.x, 7.x, 8.x |CentOS 6.3: [Download LIS](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+: No núcleo |Pacote: Em [repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) em "WALinuxAgent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)<p> CoreOS está agora [no fim da vida a](https://coreos.com/os/eol/) partir de 26 de maio de 2020. |Já não está disponível | | |
| Debian por Credativ |8.x, 9.x, 10.x |Em kernel |Pacote: Em repo sob "waagent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
|Recipiente de carro chato Linux by Kinvolk| Pro, Estável, Beta| Em kernel | wa-linux-agent está instalado já em /usr/share/oem/bin/waagent |
| Oráculo Linux por Oráculo |6.x, 7.x, 8.x |Em kernel |Pacote: Em repo em "WALinuxAgent" <br/>Código fonte: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| [Red Hat Enterprise Linux por Chapéu Vermelho](../workloads/redhat/overview.md) |6.x, 7.x, 8.x |Em kernel |Pacote: Em repo em "WALinuxAgent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise by SUSE |SLES/SLES para SAP 11.x, 12.x, 15.x <br/> [SUSE Public Cloud Image Lifecycle](https://www.suse.com/c/suse-public-cloud-image-life-cycle/) |Em kernel |Pacote:<p> por 11 em [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) repo<br>para 12 incluídos no Módulo "Nuvem Pública" em "python-azure-agent"<br/>Código fonte: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE by SUSE |openSUSE Leap 15.x |Em kernel |Pacote: Em [Nuvem:Ferramentas](https://build.opensuse.org/project/show/Cloud:Tools) repo em "python-azure-agent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu por Canônico |Ubuntu Server e Pro. 16.x, 18.x, 20.x<p>Informações sobre suporte alargado para Ubuntu 12.04 e 14.04 podem ser consultadas aqui: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm). |Em kernel |Pacote: Em repo sob "walinuxagent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="image-update-cadence"></a>Cadência de atualização de imagem

O Azure exige que os editores das distribuições de Linux endossadas atualizem regularmente as suas imagens no Azure Marketplace com as mais recentes correções e correções de segurança, numa cadência trimestral ou mais rápida. As imagens atualizadas no Mercado estão disponíveis automaticamente para os clientes como novas versões de uma imagem SKU. Mais informações sobre como encontrar imagens Linux: [Encontre imagens Linux VM no Azure Marketplace](./cli-ps-findimage.md).

## <a name="azure-tuned-kernels"></a>Núcleos afinados a azulados

A Azure trabalha em estreita colaboração com várias distribuições de Linux endossadas para otimizar as imagens que publicaram no Azure Marketplace. Um aspeto desta colaboração é o desenvolvimento de núcleos Linux "afinados" que são otimizados para a plataforma Azure e entregues como componentes totalmente suportados da distribuição Linux. Os núcleos Azure-Tuned incorporam novas funcionalidades e melhorias de desempenho, e numa cadência mais rápida (tipicamente trimestral) em comparação com os núcleos padrão ou genéricos que estão disponíveis a partir da distribuição.

Na maioria dos casos, você encontrará estes núcleos pré-instalados nas imagens padrão no Azure Marketplace para que os clientes obtenham imediatamente o benefício destes núcleos otimizados. Mais informações sobre estes núcleos Azure-Tuned podem ser encontradas nos seguintes links:

- [CentOS Azure-Tuned Kernel - Disponível através do CentOS Virtualization SIG](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
- [Debian Cloud Kernel - Disponível com a imagem de Debian 10 e Debian 9 "backports" em Azure](https://wiki.debian.org/Cloud/MicrosoftAzure)
- [SLES Azure-Tuned Kernel](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
- [Ubuntu Azure-Tuned Kernel](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)
- [Recipiente de carro chato Linux Pro](https://azuremarketplace.microsoft.com/marketplace/apps/kinvolk.flatcar_pro)

## <a name="partners"></a>Parceiros

### <a name="coreos"></a>CoreOS

O CoreOS está previsto para [26 de](https://coreos.com/os/eol/) maio de 2020.
A Microsoft tem dois (2) canais de migração para utilizadores do CoreOS.

- Flatcar by Kinvolk (ver a entrada "Flatcar Container Linux by Kinvolk".)
- [Fedora Core OS](https://docs.fedoraproject.org/en-US/fedora-coreos/provisioning-azure/) (os clientes devem carregar a sua própria imagem. Aqui está a [documentação da migração).](https://docs.fedoraproject.org/en-US/fedora-coreos/migrate-cl/)

### <a name="credativ"></a>credativ

[https://www.credativ.de/en/portfolio/support/open-source-support-center/](https://www.credativ.de/en/portfolio/support/open-source-support-center/)

A credativ é uma empresa independente de consultoria e serviços especializado no desenvolvimento e implementação de soluções profissionais através da utilização de software gratuito. Como principais especialistas de código aberto, o credativ tem reconhecimento internacional com muitos departamentos de TI que usam o seu apoio. Em conjunto com a Microsoft, o credativ está a preparar imagens de Debian. As imagens são especialmente concebidas para funcionar em Azure e podem ser facilmente geridas através da plataforma. o credativ também apoiará a manutenção e atualização a longo prazo das imagens Debian para Azure através dos seus Centros de Apoio Open Source.

### <a name="kinvolk"></a>Kinvolk
[https://www.kinvolk.io/flatcar-container-linux/](https://www.kinvolk.io/flatcar-container-linux/)

A Kinvolk é a empresa por trás do Flatcar Container Linux, continuando a visão original do CoreOS para uma base mínima, imutável e de atualização automática para aplicações contentorizadas. Como distro mínimo, a Flatcar contém apenas os pacotes necessários para a colocação de contentores. O seu sistema de ficheiros imutáveis garante consistência e segurança, enquanto as suas capacidades de atualização automática permitem estar sempre atualizados com as mais recentes correções de segurança. 

O Flatcar Container Linux é apoiado pela equipa global da Kinvolk de linux e especialistas em tecnologia de contentores que oferecem uma subscrição opcional de suporte comercial que inclui resposta 24x7, alertas de segurança e técnicos, e imagens exclusivas otimizadas pelo Azure, incluindo um canal de suporte a longo prazo.


### <a name="oracle"></a>Oracle

[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

A estratégia da Oracle é oferecer um vasto portfólio de soluções para nuvens públicas e privadas. A estratégia dá aos clientes escolha e flexibilidade na forma como implantam o software Oracle nas nuvens da Oracle e outras nuvens. A parceria da Oracle com a Microsoft permite que os clientes implementem software Oracle em nuvems públicas e privadas da Microsoft com a confiança da certificação e suporte da Oracle.  O empenho e investimento da Oracle em soluções de nuvem pública e privada da Oracle mantêm-se inalterados.

### <a name="red-hat"></a>Red Hat

[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

A Red Hat ajuda mais de 90% das empresas da Fortune 500 a resolver desafios de negócio, alinhar as suas estratégias de TI e negócios e preparar-se para o futuro da tecnologia. A Red Hat consegue-o fornecendo soluções seguras através de um modelo de negócio aberto e de um modelo de subscrição acessível e previsível.

### <a name="suse"></a>SUSE

[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

O SUSE Linux Enterprise Server em Azure é uma plataforma comprovada que fornece uma fiabilidade e segurança superiores para a computação em nuvem. A versátil plataforma Linux da SUSE integra-se perfeitamente com os serviços em nuvem Azure para proporcionar um ambiente de nuvem facilmente manejável. Com mais de 9.200 aplicações certificadas de mais de 1.800 fornecedores independentes de software para o SUSE Linux Enterprise Server, a SUSE garante que as cargas de trabalho em execução suportadas no centro de dados podem ser implementadas com confiança no Azure.

### <a name="canonical"></a>Canónico

[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

A engenharia canónica e a governação da comunidade aberta impulsionam o sucesso da Ubuntu na computação de clientes, servidores e cloud, que inclui serviços pessoais na nuvem para os consumidores. A visão canónica de uma plataforma unificada e livre em Ubuntu, do telefone à nuvem, fornece uma família de interfaces coerentes para o telefone, tablet, TV e desktop. Esta visão faz da Ubuntu a primeira escolha para diversas instituições, desde fornecedores públicos de nuvem aos fabricantes de eletrónica de consumo e um dos favoritos entre os tecnólogos individuais.

Com desenvolvedores e centros de engenharia em todo o mundo, a Canonical está posicionada de forma única para parceria com fabricantes de hardware, fornecedores de conteúdos e desenvolvedores de software para trazer soluções Ubuntu para o mercado de computadores, servidores e dispositivos portáteis.
