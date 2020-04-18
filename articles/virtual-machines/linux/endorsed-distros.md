---
title: Distribuições linux endossadas no Azure
description: Conheça o Linux sobre as distribuições apoiadas pelo Azure, incluindo orientações para Ubuntu, CentOS, Oracle e SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: guybo
ms.openlocfilehash: ed7755251feb04a5f811d6ed96b00a347fba8994
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605914"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Distribuições de Linux endossadas no Azure
Os parceiros fornecem imagens Linux no Mercado Azure. Estamos trabalhando com várias comunidades Linux para adicionar ainda mais sabores à lista de Distribuição Endossada. Entretanto, para distribuições que não estão disponíveis no Mercado, pode sempre trazer o seu próprio Linux seguindo as diretrizes da [Create e carregar um disco rígido virtual que contém o sistema operativo Linux.](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)

## <a name="supported-distributions-and-versions"></a>Distribuições e versões suportadas
A tabela seguinte lista as distribuições e versões linux que são suportadas no Azure. Consulte o [Suporte para imagens do Linux no Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) para obter informações mais detalhadas sobre suporte para linux e tecnologia de código aberto no Azure.

Os controladores linux Integration Services (LIS) para Hyper-V e Azure são módulos kernel que a Microsoft contribui diretamente para o kernel linux a montante.  Alguns condutores de LIS são incorporados no núcleo da distribuição por padrão. As distribuições mais antigas baseadas na Red Hat Enterprise (RHEL)/CentOS estão disponíveis como um download separado na Versão 4.2 dos Serviços de [Integração linux para Hyper-V e Azure](https://www.microsoft.com/download/details.aspx?id=55106). Consulte os requisitos do [kernel Linux](create-upload-generic.md#linux-kernel-requirements) para obter mais informações sobre os condutores do LIS.

O Agente Azure Linux já está pré-instalado nas imagens do Azure Marketplace e está normalmente disponível a partir do repositório de pacotes da distribuição. O código fonte pode ser encontrado no [GitHub](https://github.com/azure/walinuxagent).


| Distribuição | Versão | Controladores | Agente |
| --- | --- | --- | --- |
| CentOS |Centos 6.3+, 7.0+, 8.0+ |CentOS 6.3: [Download lis](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+: No núcleo |Pacote: Em [repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) em "WALinuxAgent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |No núcleo |Código fonte: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debiano 7.9+, 8.2+, 9, 10 |No núcleo |Pacote: Em repo em "waagent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |No núcleo |Pacote: Em repo em "WALinuxAgent" <br/>Código fonte: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 7.1+, 8.0+ |No núcleo |Pacote: Em repo em "WALinuxAgent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Empresa SUSE Linux |SLES/SLES para SAP<br>11 SP4<br>12 SP1+<br>15|No núcleo |Pacote:<p> para 11 em [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) repo<br>para 12 incluídos no Módulo "Nuvem Pública" em "python-azure-agent"<br/>Código fonte: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |abre SUSE Leap 42.2+ |No núcleo |Pacote: Em [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) repo em "python-azure-agent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ ** <sup>1</sup>** |No núcleo |Pacote: Em repo sob "walinuxagent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** Informações sobre o apoio alargado ao Ubuntu 12.04 e 14.04 podem ser consultadas aqui: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Cadência de atualização de imagem
A Azure exige que os editores das distribuições endossadas do Linux atualizem regularmente as suas imagens no Mercado Azure com as mais recentes correções e correções de segurança, numa cadência trimestral ou mais rápida. As imagens atualizadas no Azure Marketplace estão disponíveis automaticamente para os clientes como novas versões de uma imagem SKU. Mais informações sobre como encontrar imagens do Linux: [Encontre imagens VM Linux no Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Links adicionais
 - [Ciclo de vida da imagem da nuvem pública](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Núcleos afinados em Azure

A Azure trabalha em estreita colaboração com várias distribuições endossadas do Linux para otimizar as imagens que publicaram no Azure Marketplace. Um aspeto desta colaboração é o desenvolvimento de núcleos Linux "afinados" que estão otimizados para a plataforma Azure e entregues como componentes totalmente suportados da distribuição linux. Os núcleos azure-tuned incorporam novas funcionalidades e melhorias de desempenho, e a uma cadência mais rápida (tipicamente trimestral) em comparação com os núcleos padrão ou genéricos que estão disponíveis a partir da distribuição.

Na maioria dos casos encontrará estes núcleos pré-instalados nas imagens predefinidas no Mercado Azure, pelo que os clientes azure terão imediatamente o benefício destes núcleos otimizados. Mais informações sobre estes núcleos afinados em Azure podem ser encontradas nos seguintes links:

 - CentOS Azure-Tuned Kernel - Disponível através do CentOS Virtualization SIG - [Mais Informações](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian Cloud Kernel - Disponível com a imagem Debian 10 e Debian 9 "backports" em Azure - [Mais Informações](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - Kernel sintonizado sintonizado - [Mais informações](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu Azure-Tuned Kernel - [Mais Informações](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Parceiros

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Do site do CoreOS:

*O CoreOS foi concebido para segurança, consistência e fiabilidade. Em vez de instalar pacotes via yum ou apt, o CoreOS utiliza contentores Linux para gerir os seus serviços a um nível mais elevado de abstração. Um único código de serviço e todas as dependências são embaladas dentro de um recipiente que pode ser executado em uma ou muitas máquinas CoreOS.*

### <a name="credativ"></a>Credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

A Credativ é uma empresa independente de consultoria e serviços especializada no desenvolvimento e implementação de soluções profissionais através da utilização de software gratuito. Como principais especialistas em código aberto, a Credativ tem reconhecimento internacional com muitos departamentos de TI que usam o seu apoio. Em conjunto com a Microsoft, a Credativ está atualmente a preparar as correspondentes imagens de Debian para Debian 8 (Jessie) e Debian antes das 7 (Wheezy). Ambas as imagens são especialmente concebidas para serem executadas no Azure e podem ser facilmente geridas através da plataforma. A Credativ também apoiará a manutenção e atualização a longo prazo das imagens Debian para o Azure através dos seus Centros de Apoio a Fonte Aberta.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

A estratégia da Oracle é oferecer um vasto portfólio de soluções para nuvens públicas e privadas. A estratégia dá aos clientes a escolha e flexibilidade na forma como implantam o software Oracle nas nuvens da Oracle e outras nuvens. A parceria da Oracle com a Microsoft permite que os clientes implementem software Oracle em nuvens públicas e privadas da Microsoft com a confiança da certificação e suporte da Oracle.  O compromisso e o investimento da Oracle em soluções de nuvem pública e privada da Oracle não se alternam.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

O principal fornecedor mundial de soluções de código aberto, a Red Hat ajuda mais de 90% das empresas da Fortune 500 a resolver desafios de negócio, a alinhar as suas estratégias de TI e negócios e a preparar-se para o futuro da tecnologia. A Red Hat fá-lo fornecendo soluções seguras através de um modelo de negócio aberto e de um modelo de subscrição acessível e previsível.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server em Azure é uma plataforma comprovada que fornece uma fiabilidade e segurança superiores para a computação em nuvem. A versátil plataforma Linux da SUSE integra-se perfeitamente com os serviços de nuvem Azure para proporcionar um ambiente de nuvem facilmente controlável. Com mais de 9.200 aplicações certificadas de mais de 1.800 fornecedores independentes de software para o SUSE Linux Enterprise Server, a SUSE garante que as cargas de trabalho suportadas no centro de dados podem ser implementadas com confiança no Azure.

### <a name="canonical"></a>Canónico
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

A engenharia canónica e a governação comunitária aberta impulsionam o sucesso da Ubuntu na computação de clientes, servidores e nuvem, que inclui serviços pessoais na nuvem para os consumidores. A visão canónica de uma plataforma unificada e livre em Ubuntu, do telefone à nuvem, fornece uma família de interfaces coerentes para o telefone, tablet, TV e ambiente de trabalho. Esta visão faz da Ubuntu a primeira escolha para diversas instituições, desde fornecedores públicos de nuvem aos fabricantes de eletrónica de consumo e uma das favoritas entre os tecnólogos individuais.

Com desenvolvedores e centros de engenharia em todo o mundo, a Canonical está posicionada exclusivamente para parceria com fabricantes de hardware, fornecedores de conteúdos e desenvolvedores de software para colocar soluções Ubuntu no mercado para Computadores, servidores e dispositivos portáteis.
