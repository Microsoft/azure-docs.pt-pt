---
title: Azure VMware Solutions (AVS) - Configurar a Cloud Privada AVS como um local de recuperação de desastres utilizando o Gestor de Recuperação do Site VMware
description: Descreve como configurar a sua Nuvem Privada AVS como um local de recuperação de desastres para cargas de trabalho vMware no local
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 209eb6ed93ed12f97b116b648a36d14e09b822f7
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461186"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Configurar a Nuvem Privada AVS como alvo de recuperação de desastres com o Gestor de Recuperação do Site VMware

Você pode usar o seu AVS Private Cloud como um site de recuperação de desastres (DR) para cargas de trabalho VMware no local.

A solução DR baseia-se na replicação vSphere e no Gestor de Recuperação do Site VMware (SRM). Uma abordagem semelhante pode ser seguida para permitir a sua Nuvem Privada AVS como um local primário que está protegido pelo seu local de recuperação no local.

A solução AVS:

* Elimina a necessidade de criar um datacenter especificamente para dr.
* Permite-lhe aproveitar as localizações do Azure onde o AVS é implantado para resiliência geográfica mundial.
* Dá-lhe a opção de reduzir os custos de implantação e o custo total de propriedade para o estabelecimento de DR.

A solução AVS requer que faça o seguinte:

* Instale, configure e gere a replicação vSphere e srm na sua Nuvem Privada AVS.
* Forneça as suas próprias licenças para a SRM quando a Nuvem Privada AVS for o site protegido. Não necessita de licenças RM adicionais para o site AVS quando é usado como o local de recuperação.

Com esta solução, tem controlo total sobre a replicação vSphere e srm. A interface do usuário familiar, API e interfaces CLI permitem o uso de seus scripts e ferramentas existentes.

![Implantação do Gestor de Recuperação do Site](media/srm-deployment.png)

Pode utilizar quaisquer versões de vRA e SRM compatíveis com a sua Nuvem Privada AVS e ambientes no local. Os exemplos deste guia utilizam vRA 6.5 e SRM 6.5. Estas versões são compatíveis com a vSphere 6.5, que é suportada por AVS.

## <a name="deploy-the-solution"></a>Implementar a solução

As seguintes secções descrevem como implementar uma solução DR utilizando o SRM na sua Nuvem Privada AVS.

1. [Verifique se as versões de produtos VMware são compatíveis](#verify-that-vmware-product-versions-are-compatible)
2. [Calcule o tamanho do seu ambiente DER](#estimate-the-size-of-your-dr-environment)
3. [Crie uma Nuvem Privada AVS para o seu ambiente](#create-an-avs-private-cloud-for-your-environment)
4. [Configurar a rede DeVs Private Cloud para a solução SRM](#set-up-avs-private-cloud-networking-for-the-srm-solution)
5. [Instale uma ligação VPN site-to-site entre a sua rede no local e a Nuvem Privada AVS e portas abertas necessárias](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports)
6. [Instale serviços de infraestrutura na sua Nuvem Privada AVS](#set-up-infrastructure-services-in-your-avs-private-cloud)
7. [Instale o aparelho de replicação vSphere no seu ambiente no local](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Instale o aparelho de replicação vSphere no seu ambiente AVS Private Cloud](#install-vsphere-replication-appliance-in-your-avs-private-cloud-environment)
9. [Instale o servidor SRM no seu ambiente no local](#install-srm-server-in-your-on-premises-environment)
10. [Instale o servidor SRM na sua Nuvem Privada AVS](#install-srm-server-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Verificar se as versões do produto VMware são compatíveis

As configurações deste guia estão sujeitas aos seguintes requisitos de compatibilidade:

* A mesma versão do SRM deve ser implantada na sua Nuvem Privada AVS e no seu ambiente no local.
* A mesma versão da replicação vSphere deve ser implantada na sua Nuvem Privada AVS e no seu ambiente no local.
* As versões do Controlador de Serviços da Plataforma (PSC) na sua Nuvem Privada AVS e no seu ambiente no local devem ser compatíveis.
* As versões do vCenter na sua Nuvem Privada AVS e no seu ambiente no local devem ser compatíveis.
* As versões de replicação SRM e vSphere devem ser compatíveis entre si e com as versões de PSC e vCenter.

Para ligações à documentação e informação de compatibilidade da VMware relevantes, vá à documentação do Gestor de Recuperação do [Site VMware.](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)

Para saber as versões de vCenter e PSC na sua Nuvem Privada AVS, abra o portal AVS. Vá a **Recursos,** selecione a sua Nuvem Privada AVS e clique no separador **VSphere Management Network.**

![versões vCenter & PSC em AVS Private Cloud](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Calcule o tamanho do seu ambiente DER

1. Verifique se a configuração identificada no local está dentro dos limites suportados. Para o SRM 6.5, os limites estão documentados no artigo da base de conhecimentos VMware sobre [os limites operacionais para](https://kb.vmware.com/s/article/2147110)o Gestor de Recuperação do Site 6.5 .
2. Certifique-se de que tem largura de banda de rede suficiente para satisfazer o tamanho da carga de trabalho e os requisitos de RPO. O artigo de base de conhecimento VMware sobre o cálculo dos requisitos de largura de [banda para a replicação da vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) fornece orientação sobre os limites de largura de banda.
3. Utilize a ferramenta de sizer AVS para estimar os recursos necessários no seu site DR para proteger o seu ambiente no local.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>Crie uma Nuvem Privada AVS para o seu ambiente

Crie uma Nuvem Privada AVS a partir do portal AVS seguindo as instruções e recomendações de dimensionamento em [Criar uma Nuvem Privada AVS](create-private-cloud.md).

### <a name="set-up-avs-private-cloud-networking-for-the-srm-solution"></a>Configurar a rede DeVs Private Cloud para a solução SRM

Aceda ao portal AVS para criar rede DeVs Private Cloud para a solução SRM.

Crie um VLAN para a rede de soluções SRM e atribua-lhe um CIDR de sub-rede. Para obter instruções, consulte [Criar e gerir VLANs/Subnets](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports"></a>Instale uma ligação VPN site-to-site entre a sua rede no local e a Nuvem Privada AVS e portas abertas necessárias

Para configurar a conectividade Site-to-Site entre a sua rede no local e a sua Nuvem Privada AVS. Para obter instruções, consulte [Configure uma ligação VPN à sua Nuvem Privada AVS](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-avs-private-cloud"></a>Instale serviços de infraestrutura na sua Nuvem Privada AVS

Configure os serviços de infraestrutura na Nuvem Privada AVS para facilitar a gestão das suas cargas de trabalho e ferramentas.

Pode adicionar um fornecedor de identidade externo, tal como descrito no [Use Azure AD como fornecedor de identidade para vCenter em AVS Private Cloud,](azure-ad.md) se quiser fazer qualquer um dos seguintes:

* Identifique os utilizadores do seu Diretório Ativo (AD) no seu AVS Private Cloud.
* Instale um Anúncio na sua Nuvem Privada AVS para todos os utilizadores.
* Use Azure AD.

Para fornecer serviços de procura de endereçoip, gestão de endereços IP e serviços de resolução de nomes para as suas cargas de trabalho na Nuvem Privada AVS, configurar um servidor DHCP e DNS conforme descrito nas [aplicações dNS e DHCP e cargas](dns-dhcp-setup.md)de trabalho na sua Nuvem Privada AVS .

O domínio *.cloudsimple.io é utilizado por VMs de gestão e anfitriões na sua Nuvem Privada AVS. Para resolver os pedidos a este domínio, configure o reinício do DNS no servidor DNS conforme descrito em [Criar um Forwarder Condicional](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Instale o aparelho de replicação vSphere no seu ambiente no local

Instale o VSphere Replication Appliance (vRA) no seu ambiente no local, seguindo a documentação vMware. A instalação consiste nestes passos de alto nível:

1. Prepare o seu ambiente no local para a instalação vRA.

2. Coloque a vRA no seu ambiente no local utilizando o OVF no VR ISO a partir de vmware.com. Para vRA 6.5, [este blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) tem as informações relevantes.

3. Registe o seu vCenter Single Sign-On no local. Para instruções detalhadas para a replicação da vSphere 6.5, consulte o documento [VMware VMware VMware vSphere Replication 6.5 Instalação e Configuração](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-avs-private-cloud-environment"></a>Instale o aparelho de replicação vSphere no seu ambiente AVS Private Cloud

Antes de começar, verifique se você tem o seguinte:

* Alcançabilidade IP de subredes no seu ambiente no local para a sub-rede de gestão da sua Nuvem Privada AVS
* Alcance IP desde a subnet de replicação no seu ambiente vSphere no local até à subnet de solução SRM da sua Nuvem Privada AVS

Para obter instruções, consulte [Configure uma ligação VPN à sua Nuvem Privada AVS](set-up-vpn.md). Os passos são semelhantes aos da instalação no local.

O AVS recomenda a utilização de FQDNs em vez de endereços IP durante a instalação vRA e SRM. Para descobrir o FQDN do vCenter e PSC na sua Nuvem Privada AVS, abra o portal AVS. Vá a **Recursos,** selecione a sua Nuvem Privada AVS e clique no separador **VSphere Management Network.**

![Encontrar FQDN de vCenter/PSC em AVS Private Cloud](media/srm-resources.png)

O AVS exige que não instale vRA e SRM utilizando o utilizador 'cloudowner' predefinido, mas sim criar um novo utilizador. Isto é feito para ajudar a garantir o alto tempo e disponibilidade para o seu ambiente VCenter De Nuvem Privada AVS. No entanto, o utilizador predefinido do cloudowner no VCenter De Cloud Privado AVS não tem privilégios suficientes para criar um novo utilizador com privilégios administrativos.

Antes de instalar vRA e SRM, deve aumentar os privilégios vCenter do utilizador do cloudowner e, em seguida, criar um utilizador com privilégios administrativos no domínio VCenter SSO. Para mais detalhes sobre o modelo padrão de utilizador e permissão AVS Private Cloud, consulte O modelo de [permissão AVS Private Cloud](learn-private-cloud-permissions.md).

A instalação consiste nestes passos de alto nível:

1. [Escalar privilégios.](escalate-private-cloud-privileges.md)
2. Crie um utilizador na sua Nuvem Privada AVS para a replicação vSphere e instalação SRM. Explicado abaixo no [vCenter UI: Criar um utilizador na Nuvem Privada AVS para instalação vRA & SRM](#vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation).
3. Prepare o seu ambiente AVS Private Cloud para a instalação vRA.
4. Desloque a vRA na sua Nuvem Privada AVS utilizando o OVF no VR ISO a partir de vmware.com. Para vRA 6.5, [este blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) tem informações relevantes.
5. Configure as regras de firewall para vRA. Explicado abaixo no [portal AVS: Configure as regras de Firewall para vRA](#avs-portal-configure-firewall-rules-for-vra).
6. Registe a AVS Private Cloud vRA com vCenter Single Sign-On no site da AVS Private Cloud.
7. Configure as ligações de replicação vSphere entre os dois aparelhos. Certifique-se de que as portas necessárias são abertas através das firewalls. Consulte este artigo de base de [conhecimento vMware](https://kb.vmware.com/s/article/2087769) para obter uma lista de números de porta que devem estar abertos para a replicação vSphere 6.5.

Para instruções de instalação detalhadas para a replicação vSphere 6.5, consulte o documento [VMware VMware VMware vSphere Replication 6.5 Instalação e Configuração](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation"></a>vCenter UI: Criar um utilizador na Nuvem Privada AVS para instalação vRA e SRM

Inscreva-se no vCenter utilizando credenciais de utilizador do cloudowner após a escalada de privilégios do portal AVS.

Crie um novo utilizador, `srm-soln-admin`, no vCenter e adicione-o ao grupo de administradores no vCenter.
Assine o vCenter como utilizador do cloudowner e inscreva-se como utilizador *srm-soln-administrador.*

#### <a name="avs-portal-configure-firewall-rules-for-vra"></a>Portal AVS: Configure regras de firewall para vRA

Configure as regras de firewall descritas em [tabelas e regras](firewall.md) de firewall configurar portas abertas para permitir a comunicação entre:

* vRA na rede de soluções SRM e vCenter e ESXi anfitriões na rede de gestão.
* vRA eletrodomésticos nos dois locais.

Consulte este artigo de base de [conhecimento vMware](https://kb.vmware.com/s/article/2087769) para obter uma lista de números de porta que devem estar abertos para a replicação vSphere 6.5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Instale o servidor SRM no seu ambiente no local

Antes de começar, verifique o seguinte:

* VSphere Replication Appliance está instalado nos seus ambientes no local e na Nuvem Privada AVS.
* Os aparelhos de replicação vSphere em ambos os locais estão ligados entre si.
* Reviu as informações da VMware sobre pré-requisitos e boas práticas. Para SRM 6.5, pode consultar os [pré-requisitos e as melhores práticas do](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)documento VMware para o SRM 6.5 .

Siga a documentação vMware para realizar a instalação do servidor SRM no modelo de implementação 'Topologia de dois locais com uma instância vCenter por controlador de serviços de plataforma', conforme descrito neste [documento VMWare](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). As instruções de instalação para SRM 6.5 estão disponíveis no documento VMware Instalando o [Gestor de Recuperação](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)do Site .

### <a name="install-srm-server-in-your-avs-private-cloud"></a>Instale o servidor SRM na sua Nuvem Privada AVS

Antes de começar, verifique o seguinte:

* VSphere Replication Appliance está instalado nos seus ambientes no local e na Nuvem Privada AVS.
* Os aparelhos de replicação vSphere em ambos os locais estão ligados entre si.
* Reviu as informações da VMware sobre pré-requisitos e boas práticas. Para SRM 6.5, pode consultar [pré-requisitos e boas práticas para](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)a instalação do Servidor Gestor de Recuperação do Site 6.5 .

Os seguintes passos descrevem a instalação AVS Private Cloud SRM.

1. [vCenter UI: Instalar SRM](#vcenter-ui-install-srm)
2. [Portal AVS: Configure regras de firewall para SRM](#avs-portal-configure-firewall-rules-for-srm)
3. [vCenter UI: Configure SRM](#vcenter-ui-configure-srm)
4. [Portal AVS: desescalar privilégios](#avs-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI: Instalar SRM

Depois de iniciar sessão no vCenter utilizando credenciais de administração srm-soln-admin, siga a documentação vMware para executar a instalação do servidor SRM no modelo de implementação 'Topologia de dois locais com uma instância vCenter por controlador de serviços de plataforma', conforme descrito neste [documento VMWare.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html) As instruções de instalação para SRM 6.5 estão disponíveis no documento VMware Instalando o [Gestor de Recuperação](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)do Site .

#### <a name="avs-portal-configure-firewall-rules-for-srm"></a>Portal AVS: Configure regras de firewall para SRM

Configure as regras de firewall descritas em [tabelas e regras](firewall.md) de firewall configurar para permitir a comunicação entre:

O servidor SRM e vCenter/PSC na Nuvem Privada AVS.
Os servidores SRM em ambos os sites

Consulte este artigo de base de [conhecimento vMware](https://kb.vmware.com/s/article/2087769) para obter uma lista de números de porta que devem estar abertos para a replicação vSphere 6.5.

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI: Configure SRM

Depois de o SRM ser instalado na Nuvem Privada AVS, execute as seguintes tarefas descritas nas secções do Guia de Instalação e Configuração do Gestor de Recuperação do Site VMware. Para o SRM 6.5, as instruções estão disponíveis no documento VMware [Instalando o Gestor de Recuperação](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)do Site .

1. Ligue as instâncias do servidor do Gestor de Recuperação do Site nos sites protegidos e de recuperação.
2. Estabeleça uma ligação com o cliente à instância do servidor do gestor de recuperação do site remoto.
3. Instale a chave de licença do Gestor de Recuperação do Site.

#### <a name="avs-portal-de-escalate-privileges"></a>Portal AVS: Desescalar privilégios

Para desescalar privilégios, ver [desescalar privilégios.](escalate-private-cloud-privileges.md#de-escalate-privileges)

## <a name="ongoing-management-of-your-srm-solution"></a>Gestão contínua da sua solução SRM

Tem controlo total sobre a replicação da vSphere e o software SRM no seu ambiente AVS Private Cloud e espera-se que realize a gestão necessária do ciclo de vida do software. Certifique-se de que qualquer nova versão do software é compatível com o VCenter e PSC da Nuvem Privada AVS antes de atualizar ou atualizar a replicação da vSphere ou SRM.

> [!NOTE]
> A AVS está atualmente a explorar opções para oferecer um serviço de DR gerido. 

## <a name="multiple-replication-configuration"></a>Configuração de replicação múltipla

 Tanto as [tecnologias de replicação baseadas em matriz como as tecnologias de replicação vSphere podem ser utilizadas juntamente com](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) a SRM ao mesmo tempo. No entanto, devem ser aplicados a um conjunto separado de VMs (um dado VM pode ser protegido quer por replicação baseada em matriz, quer por replicação vSphere, mas não ambos). Além disso, o site AVS pode ser configurado como um local de recuperação para vários sites protegidos. Consulte [as opções de vários locais srm](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) para obter informações sobre configurações multi-site.

## <a name="references"></a>Referências

* [Documentação do Gestor de Recuperação do Site VMware](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Limites operacionais para O Gestor de Recuperação do Site 6.5](https://kb.vmware.com/s/article/2147110)
* [Cálculo dos requisitos de largura de banda para replicação vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [Opções OVF Ao implementar replicação vSphere 6.5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere Replication 6.5 Instalação e Configuração](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Pré-requisitos e Boas Práticas para a SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Gestor de recuperação de site em uma topologia de dois locais com uma instância de servidor vCenter por controlador de serviços de plataforma](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Site Recovery Manager 6.5 Guia de instalação e configuração](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [Blog VMware na SRM com replicação baseada em matriz vs. vSphere replicação](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [Blog VMware nas opções de vários sites da SRM](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Números de porta que devem estar abertos para a replicação vSphere 5.8.x, 6.x e 8](https://kb.vmware.com/s/article/2147112)
