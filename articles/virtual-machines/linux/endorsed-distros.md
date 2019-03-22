---
title: Distribuições do Linux apoiadas no Azure | Documentos da Microsoft
description: Saiba mais sobre o Linux em distribuições apoiadas pelo Azure, incluindo diretrizes para Ubuntu, CentOS, Oracle e SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: szark
ms.openlocfilehash: c0124d133fe2db9acb16251bc21ffb19f1b1268f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992206"
---
# <a name="linux-distributions-endorsed-on-azure"></a>Distribuições do Linux endossadas no Azure
Os parceiros fornecem imagens do Linux no Azure Marketplace. Estamos a trabalhar com várias comunidades do Linux para adicionar ainda mais tipos à lista de distribuição apoiadas pelo. Entretanto, para as distribuições que não estão disponíveis no mercado, pode sempre colocar seu próprio Linux, seguindo as diretrizes em [criar e carregar um disco rígido virtual que contém o sistema operativo Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Distribuições suportadas e versões
A tabela seguinte lista as distribuições de Linux e versões que são suportadas no Azure. Consulte a [suporte para imagens do Linux no Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) para obter mais informações sobre o suporte para Linux e tecnologia de código aberto no Azure.

Os controladores de serviços de integração do Linux (LIS) para o Hyper-V e do Azure são módulos de kernel que Microsoft contribui diretamente para o kernel do Linux a montante.  Alguns drivers LIS baseiam-se para o kernel da distribuição por predefinição. Distribuições mais antigas que se baseiam no Red Hat Enterprise (RHEL) / CentOS estão disponível para download em separado [4.2 versão de serviços de integração do Linux para Hyper-V e o Azure](https://www.microsoft.com/en-us/download/details.aspx?id=55106). Ver [requisitos de kernel de Linux](create-upload-generic.md#linux-kernel-requirements) para obter mais informações sobre os drivers LIS.

O agente Azure Linux já pré-instaladas nas imagens do Azure Marketplace e está normalmente disponível a partir do repositório de pacotes de distribuição. Código-fonte pode ser encontrado no [GitHub](https://github.com/azure/walinuxagent).

  
| Distribuição | Versão | Controladores | Agente |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3+, 7.0+ |CentOS 6.3: [Baixe o LIS](https://www.microsoft.com/en-us/download/details.aspx?id=55106)<p>CentOS 6.4 +: No kernel |Pacote: Na [repositório](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) em "WALinuxAgent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |No kernel |Código-fonte: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+, 8.2+ |No kernel |Pacote: No repositório em "waagent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |No kernel |Pacote: No repositório em "WALinuxAgent" <br/>Código-fonte: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7+, 7.1+ |No kernel |Pacote: No repositório em "WALinuxAgent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES para SAP<br>11 SP4<br>12 SP1+<br>15|No kernel |Pacote:<p> para 11 na [ferramentas de nuvem:](https://build.opensuse.org/project/show/Cloud:Tools) repositório<br>para 12 incluída no módulo de "Nuvem pública" em "python-azure-agent"<br/>Código-fonte: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |No kernel |Pacote: Na [ferramentas de nuvem:](https://build.opensuse.org/project/show/Cloud:Tools) repositório em "python-azure-agent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ **<sup>1</sup>** |No kernel |Pacote: No repositório em "walinuxagent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>**  suporte para o Ubuntu 12.04 sobre o Azure, consulte a [aviso EOL](https://azure.microsoft.com/blog/ubuntu-12-04-precise-pangolin-nearing-end-of-life/).


## <a name="partners"></a>Parceiros

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Partir do site do CoreOS:

*CoreOS destina-se a segurança, consistência e confiabilidade. Em vez de instalar os pacotes através de yum ou apt, CoreOS utiliza contentores do Linux para gerir os seus serviços num nível mais alto de abstração. Código de um único serviço e todas as dependências são empacotadas dentro de um contentor que pode ser executado numa ou muitas máquinas de CoreOS.*

### <a name="credativ"></a>credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ é um consultor independente e a empresa de serviços especializada no desenvolvimento e implementação de soluções de profissionais com o software gratuito. Como líder de open source especialistas, Credativ tem internacional reconhecimento com vários departamentos de TI que utilizam o seu suporte. Em conjunto com a Microsoft, Credativ está atualmente a preparar imagens Debian correspondentes para Debian 8 (Jessie) e Debian antes de 7 (Wheezy). Ambas as imagens são especialmente criadas para ser executado no Azure e podem ser facilmente geridas através da plataforma. Credativ também irá suportar a manutenção a longo prazo e a atualização de imagens do Debian para o Azure através de seus centros de suporte de origem aberto.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Estratégia do Oracle é oferecer um vasto portefólio de soluções para nuvens públicas e privadas. A estratégia oferece aos clientes escolhas e flexibilidade em como implementar software Oracle nas clouds do Oracle e de outras clouds. A parceria da Oracle com a Microsoft permite aos clientes implementar software Oracle nas clouds pública e privada da Microsoft sabendo que conta com a certificação e o suporte da Oracle.  Alocação e investimento em soluções de nuvem pública e privada do Oracle da Oracle não é alterada.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Fornecedor líder do mundo, de soluções de código-fonte aberto, Red Hat ajuda mais de 90% das empresas da Fortune 500 superar os desafios comerciais, alinhar seus IT e estratégias de negócios e se preparar para o futuro da tecnologia. Red Hat faz isso fornecendo soluções seguras através de um modelo de negócio aberto e um modelo de subscrição e acessível, previsível.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server no Azure é uma plataforma comprovada que fornece maior confiabilidade e segurança para a computação em nuvem. Plataforma de Linux versátil do SUSE integram-se perfeitamente com serviços cloud do Azure para entregar um ambiente de cloud facilmente gerenciáveis. Com mais de 9,200 aplicações certificadas de mais de 1.800 fornecedores independentes de software para o SUSE Linux Enterprise Server, SUSE garante que as cargas de trabalho em execução suportados no Centro de dados podem ser implementadas com confiança no Azure.

### <a name="canonical"></a>Canónico
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Engenharia Canonical e governação de Comunidade open unidade sucesso do Ubuntu no cliente, servidor e de informática na cloud, que inclui serviços cloud pessoal para consumidores. Visão da Canonical, de uma plataforma unificada, gratuita no Ubuntu, de telefone para a nuvem, oferece uma família de interfaces coerentes para o telefone, tablet, programas de TV e ambiente de trabalho. Essa visão torna Ubuntu a primeira opção para as instituições variadas de fornecedores de cloud pública para os criadores do electronics de consumidor e um favorito entre tecnólogos individuais.

Com os programadores e centros de engenharia em todo o mundo, o Canonical está exclusivamente posicionado para as parcerias com fabricantes de hardware, fornecedores de conteúdo e os desenvolvedores de software para oferecer soluções de Ubuntu no mercado de PCs, servidores e dispositivos portáteis.
