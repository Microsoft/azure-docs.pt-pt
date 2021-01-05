---
title: Azure VMware Solution by CloudSimple - Configurar a Nuvem Privada como um local de recuperação de desastres utilizando o VMware Site Recovery Manager
description: Descreve como configurar a cloud private cloud como um site de recuperação de desastres usando o VMware Site Recovery Manager.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d71f8aec1f35514ac6c10b17b6f7b69b79b05bd
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897914"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Configurar a Nuvem Privada como um alvo de recuperação de desastres com o VMware Site Recovery Manager

Pode utilizar a cloudsimple Private Cloud como um site de recuperação de desastres (DR) para cargas de trabalho VMware no local.

A solução DR baseia-se na replicação vSphere e no VMware Site Recovery Manager (SRM). Uma abordagem semelhante pode ser seguida para ativar a sua Nuvem Privada como um site primário que está protegido pelo seu local de recuperação no local.

A solução CloudSimple:

* Elimina a necessidade de configurar um datacenter especificamente para DR.
* Permite-lhe aproveitar as localizações do Azure onde o CloudSimple é implantado para resiliência geográfica mundial.
* Dá-lhe a opção de reduzir os custos de implantação e o custo total de propriedade para o estabelecimento de DR.

A solução CloudSimple requer que faça o seguinte:

* Instale, configuure e gere a replicação da vSphere e o SRM na sua Nuvem Privada.
* Forneça as suas próprias licenças para SRM quando a Nuvem Privada for o site protegido. Não precisa de licenças SRM adicionais para o site CloudSimple quando é utilizado como local de recuperação.

Com esta solução, você tem controlo total sobre a replicação vSphere e SRM. As interfaces familiares de UI, API e CLI permitem a utilização dos scripts e ferramentas existentes.

![Implementação do Gestor de Recuperação de Sítio](media/srm-deployment.png)

Pode utilizar quaisquer versões de vRA e SRM compatíveis com os seus ambientes Private Cloud e on-in. Os exemplos neste guia utilizam vRA 6.5 e SRM 6.5. Estas versões são compatíveis com o vSphere 6.5, que é suportado pela CloudSimple.

## <a name="deploy-the-solution"></a>Implementar a solução

As secções seguintes descrevem como implementar uma solução DR utilizando SRM na sua Nuvem Privada.

1. [Verifique se as versões de produtos VMware são compatíveis](#verify-that-vmware-product-versions-are-compatible)
2. [Estimeça o tamanho do seu ambiente DR](#estimate-the-size-of-your-dr-environment)
3. [Crie uma Nuvem Privada para o seu ambiente](#create-a-private-cloud-for-your-environment)
4. [Configurar a rede Private Cloud para a solução SRM](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Configurar uma ligação VPN site-to-site entre a sua rede no local e a Nuvem Privada e abrir portas necessárias](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Configurar serviços de infraestrutura na sua Nuvem Privada](#set-up-infrastructure-services-in-your-private-cloud)
7. [Instale o aparelho de replicação vSphere no seu ambiente no local](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Instale o aparelho de replicação vSphere no seu ambiente de Nuvem Privada](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Instale o servidor SRM no seu ambiente no local](#install-srm-server-in-your-on-premises-environment)
10. [Instale o servidor SRM na sua Nuvem Privada](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Verifique se as versões de produtos VMware são compatíveis

As configurações deste guia estão sujeitas aos seguintes requisitos de compatibilidade:

* A mesma versão de SRM deve ser implantada no seu Ambiente Private Cloud e no seu ambiente no local.
* A mesma versão da replicação da vSphere deve ser implantada na sua Nuvem Privada e no seu ambiente no local.
* As versões do Controlador de Serviços de Plataforma (PSC) na sua Nuvem Privada e o seu ambiente no local devem ser compatíveis.
* As versões do vCenter na sua Nuvem Privada e o seu ambiente no local devem ser compatíveis.
* As versões de replicação SRM e vSphere devem ser compatíveis entre si e com as versões de PSC e vCenter.

Para ligações à documentação e informações de compatibilidade vMware relevantes, aceda à documentação [do VMware Site Recovery Manager.](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)

Para descobrir as versões do vCenter e do PSC na sua Nuvem Privada, abra o portal CloudSimple. Vá a **Recursos,** selecione a sua Nuvem Privada e clique no separador **vSphere Management Network.**

![vCenter & versões PSC em Nuvem Privada](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Estimeça o tamanho do seu ambiente DR

1. Verifique se a configuração identificada no local está dentro dos limites suportados. Para o SRM 6.5, os limites estão documentados no artigo base de conhecimento VMware sobre [os limites operacionais para o Gestor de Recuperação do Local 6.5](https://kb.vmware.com/s/article/2147110).
2. Certifique-se de que tem largura de banda de rede suficiente para satisfazer o seu tamanho de carga de trabalho e os requisitos de RPO. O artigo de base de conhecimento VMware sobre [o cálculo dos requisitos de largura de banda para a replicação da vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) fornece orientação sobre os limites da largura de banda.
3. Utilize a ferramenta de tamanho CloudSimple para estimar os recursos necessários no seu site DR para proteger o ambiente no local.

### <a name="create-a-private-cloud-for-your-environment"></a>Crie uma Nuvem Privada para o seu ambiente

Crie uma Nuvem Privada a partir do portal CloudSimple seguindo as instruções e dimensionando as recomendações em [Criar uma Nuvem Privada.](create-private-cloud.md)

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Configurar a rede Private Cloud para a solução SRM

Aceda ao portal CloudSimple para configurar a rede Private Cloud para a solução SRM.

Crie um VLAN para a rede de soluções SRM e atribua-lhe um CIDR sub-rede. Para obter instruções, consulte [Criar e gerir VLANs/Subnets](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Configurar uma ligação VPN site-to-site entre a sua rede no local e a Nuvem Privada e abrir portas necessárias

Para configurar a conectividade Site-to-Site entre a sua rede no local e a sua Nuvem Privada. Para obter instruções, consulte [configurar uma ligação VPN à sua CloudSimple Private Cloud](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Configurar serviços de infraestrutura na sua Nuvem Privada

Configure os serviços de infraestrutura na Nuvem Privada para facilitar a gestão das suas cargas de trabalho e ferramentas.

Pode adicionar um fornecedor de identidade externo, conforme descrito no [Use Azure AD como fornecedor de identidade para vCenter na CloudSimple Private Cloud,](azure-ad.md) se quiser fazer alguma das seguintes coisas:

* Identifique os utilizadores a partir do seu Diretório Ativo (AD) no seu Private Cloud.
* Crie um AD na sua Cloud Privada para todos os utilizadores.
* Use o anúncio do Azure.

Para fornecer serviços de procura de endereços IP, gestão de endereços IP e resolução de nomes para as suas cargas de trabalho na Nuvem Privada, crie um servidor DHCP e DNS, conforme descrito na [Configuração de aplicações e cargas de trabalho DNS e DHCP na cloudSimple Private Cloud.](dns-dhcp-setup.md)

O domínio *.cloudsimple.io é utilizado por VMs de gestão e anfitriões na sua Nuvem Privada. Para resolver os pedidos a este domínio, configurar o encaminhamento de DNS no servidor DNS, conforme descrito no [Create a Conditional Forwarder](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Instale o aparelho de replicação vSphere no seu ambiente no local

Instale o aparelho de replicação vSphere (vRA) no seu ambiente no local, seguindo a documentação VMware. A instalação consiste nestes passos de alto nível:

1. Prepare o seu ambiente no local para a instalação de vRA.

2. Implemente o vRA no seu ambiente no local utilizando o OVF no VR ISO a partir de vmware.com. Para vRA 6.5, [este blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) tem as informações relevantes.

3. Registe o seu vRA no local com o vCenter Single Sign-On no local. Para obter instruções detalhadas para a replicação da vSphere 6.5, consulte o documento [VMware VMware vSphere Replication 6.5 Instalação e Configuração](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>Instale o aparelho de replicação vSphere no seu ambiente de Nuvem Privada

Antes de começar, verifique se tem o seguinte:

* Alcance IP de sub-redes no seu ambiente no local até à sub-rede de gestão da sua Nuvem Privada
* Alcance IP desde a sub-rede de replicação no ambiente vSphere no seu local até à sub-rede de solução SRM da sua Nuvem Privada

Para obter instruções, consulte [configurar uma ligação VPN à sua CloudSimple Private Cloud](set-up-vpn.md). Os degraus são semelhantes aos da instalação no local.

O CloudSimple recomenda a utilização de FQDNs em vez de endereços IP durante a instalação de vRA e SRM. Para descobrir o FQDN de vCenter e PSC na sua Nuvem Privada, abra o portal CloudSimple. Vá a **Recursos,** selecione a sua Nuvem Privada e clique no separador **vSphere Management Network.**

![Encontrar FQDN de vCenter/PSC em Nuvem Privada](media/srm-resources.png)

O CloudSimple requer que não instale vRA e SRM utilizando o utilizador 'cloudowner' padrão, mas sim criar um novo utilizador. Isto é feito para ajudar a garantir alta temperatura e disponibilidade para o seu ambiente Private Cloud vCenter. No entanto, o utilizador de cloudowner padrão no private Cloud vCenter não tem privilégios suficientes para criar um novo utilizador com privilégios administrativos.

Antes de instalar vRA e SRM, deve aumentar os privilégios vCenter do utilizador de cloudowner e, em seguida, criar um utilizador com privilégios Administrativos no domínio SSO vCenter. Para obter mais detalhes sobre o utilizador e o modelo de permissão private Cloud predefinidos, consulte [o modelo de permissão Private Cloud](learn-private-cloud-permissions.md).

A instalação consiste nestes passos de alto nível:

1. [Intensificar os privilégios.](escalate-private-cloud-privileges.md)
2. Crie um utilizador na sua Nuvem Privada para a replicação da vSphere e instalação SRM. Explicado abaixo em [vCenter UI: Criar um utilizador em Nuvem Privada para a instalação vRA & SRM](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation).
3. Prepare o seu ambiente Private Cloud para a instalação vRA.
4. Implemente o vRA na sua Nuvem Privada utilizando o OVF no VR ISO a partir de vmware.com. Para vRA 6.5, [este blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) tem informações relevantes.
5. Configure as regras de firewall para a VRA. Explicado abaixo no [portal CloudSimple: Configurar regras de Firewall para vRA](#cloudsimple-portal-configure-firewall-rules-for-vra).
6. Registe-se Private Cloud vRA com vCenter Single Sign-On no site Private Cloud.
7. Configurar as ligações de replicação da Sphere entre os dois aparelhos. Certifique-se de que as portas necessárias são abertas através das firewalls. Consulte [este artigo base de conhecimento VMware](https://kb.vmware.com/s/article/2087769) para obter uma lista de números de porta que devem estar abertos para a replicação da vSphere 6.5.

Para obter instruções de instalação detalhadas para a replicação da vSphere 6.5, consulte o documento [VMware vSSphere Replication 6.5 Instalação e Configuração](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter UI: Criar um utilizador em Nuvem Privada para instalação vRA e SRM

Inscreva-se no vCenter usando credenciais de utilizador de cloudowner após a escalada de privilégios a partir do portal CloudSimple.

Crie um novo `srm-soln-admin` utilizador, no vCenter e adicione-o ao grupo de administradores no vCenter.
Inscreva-se no vCenter como utilizador de cloudowner e inscreva-se como utilizador de *administração srm-soln..*

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple portal: Configurar regras de firewall para vRA

Configure as regras de firewall, tal como descrito na [configuração de tabelas e regras](firewall.md) de firewall para abrir portas para permitir a comunicação entre:

* vRA na rede de soluções SRM e vCenter e ESXi hospedam-se na rede de gestão.
* aparelhos vRA nos dois locais.

Consulte este [artigo base de conhecimento VMware](https://kb.vmware.com/s/article/2087769) para obter uma lista de números de porta que devem estar abertos para a replicação da vSphere 6.5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Instale o servidor SRM no seu ambiente no local

Antes de começar, verifique o seguinte:

* vSphere Replication Appliance está instalado no seu local e ambientes private Cloud.
* Os aparelhos de replicação vSphere em ambos os locais estão ligados entre si.
* Reviu as informações da VMware sobre pré-requisitos e boas práticas. Para SRM 6.5, pode consultar o documento VMware [Pré-requisitos e boas práticas para SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Siga a documentação VMware para executar a instalação do servidor SRM no modelo de implantação 'Topologia de dois locais com uma vCenter Instance per Platform Services Controller' conforme descrito neste [documento VMWare](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). As instruções de instalação do SRM 6.5 estão disponíveis no documento VMware [Installing Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-private-cloud"></a>Instale o servidor SRM na sua Nuvem Privada

Antes de começar, verifique o seguinte:

* vSphere Replication Appliance está instalado no seu local e ambientes private Cloud.
* Os aparelhos de replicação vSphere em ambos os locais estão ligados entre si.
* Reviu as informações da VMware sobre pré-requisitos e boas práticas. Para SRM 6.5, pode consultar [os Pré-requisitos e as Melhores Práticas para a Instalação do Gestor de Recuperação do Local 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Os seguintes passos descrevem a instalação Private Cloud SRM.

1. [vCenter UI: Instalar SRM](#vcenter-ui-install-srm)
2. [Portal CloudSimple: Configurar regras de firewall para SRM](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter UI: Configure SRM](#vcenter-ui-configure-srm)
4. [Portal CloudSimple: desescalar os privilégios](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI: Instalar SRM

Depois de iniciar sessão no vCenter utilizando credenciais de administração srm-soln-administrador, siga a documentação VMware para executar a instalação do servidor SRM no modelo de implantação 'Topology de dois sítios com uma vCenter Instance per Platform Services Controller' conforme descrito neste [documento VMWare](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). As instruções de instalação do SRM 6.5 estão disponíveis no documento VMware [Installing Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>Portal CloudSimple: Configurar regras de firewall para SRM

Configure as regras de firewall, tal como descrito na [configuração de tabelas e regras](firewall.md) de firewall para permitir a comunicação entre:

O servidor SRM e vCenter /PSC na Nuvem Privada.
Os servidores SRM em ambos os sites

Consulte [este artigo base de conhecimento VMware](https://kb.vmware.com/s/article/2087769) para obter uma lista de números de porta que devem estar abertos para a replicação da vSphere 6.5.

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI: Configure SRM

Depois de o SRM ser instalado na nuvem privada, execute as seguintes tarefas descritas nas secções do Guia de Instalação e Configuração do Gestor de Recuperação do Site VMware. Para SRM 6.5, as instruções estão disponíveis no documento VMware [Instalar o Gestor de Recuperação do Local](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Ligue as instâncias do servidor do gestor de recuperação do site nos sites protegidos e de recuperação.
2. Estabelecer uma ligação ao cliente com a instância do servidor do gestor de recuperação do site remoto.
3. Instale a chave de licença do Gestor de Recuperação do Site.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple portal: De-escalate privilégios

Para desescalar os privilégios, veja [os privilégios de De-escalate.](escalate-private-cloud-privileges.md#de-escalate-privileges)

## <a name="ongoing-management-of-your-srm-solution"></a>Gestão contínua da sua solução SRM

Tem total controlo sobre o software vSphere Replication e SRM no seu ambiente Private Cloud e espera-se que realize a gestão necessária do ciclo de vida do software. Certifique-se de que qualquer nova versão do software é compatível com o VCenter e o PSC da Nuvem Privada antes de atualizar ou atualizar vSphere Replication ou SRM.

> [!NOTE]
> A CloudSimple está atualmente a explorar opções para oferecer um serviço DR gerido. 

## <a name="multiple-replication-configuration"></a>Configuração de replicação múltipla

 [Tanto as tecnologias de replicação baseadas em matriz como as tecnologias de replicação vSphere podem ser usadas juntamente com](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) a SRM ao mesmo tempo. No entanto, devem ser aplicados a um conjunto separado de VMs (um dado VM pode ser protegido quer por replicação baseada em matrizes quer por replicação vSphere, mas não ambos). Além disso, o site CloudSimple pode ser configurado como um local de recuperação para vários sites protegidos. Consulte [opções multi-locais SRM](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) para obter informações sobre configurações de vários locais.

## <a name="references"></a>Referências

* [Documentação do gestor de recuperação do site VMware](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Limites operacionais para Gestor de Recuperação de Sítio 6.5](https://kb.vmware.com/s/article/2147110)
* [Cálculo dos requisitos de largura de banda para a replicação da vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [Opções OVF Ao implementar a replicação vSphere 6.5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere Replicação 6.5 Instalação e Configuração](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Pré-requisitos e boas práticas para SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Gestor de Recuperação de Site em uma Topologia Two-Site com uma instância de servidor vCenter por controlador de serviços de plataforma](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Gestor de Recuperação do Site 6.5 Guia de Instalação e Configuração](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [VMware Blog na SRM com replicação baseada em matriz vs. vSphere replicação](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [Blog VMware em opções de vários sites SRM](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Números de porta que devem estar abertos para vSphere Replication 5.8.x, 6.x e 8](https://kb.vmware.com/s/article/2147112)
