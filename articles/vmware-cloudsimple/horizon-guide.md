---
title: Azure VMware Solution by CloudSimple - Use o site Private Cloud para hospedar uma infraestrutura virtual de desktop usando VMware Horizon
description: Descreve como pode usar o seu site CloudSimple Private Cloud para hospedar uma infraestrutura virtual de desktop usando VMware Horizon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77025253"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Use o site CloudSimple Private Cloud para hospedar uma infraestrutura virtual de desktop usando VMware Horizon

Pode utilizar o seu site CloudSimple Private Cloud para hospedar uma infraestrutura virtual de ambiente de trabalho (VDI) utilizando o VMware Horizon 7.x. A figura a seguir mostra a arquitetura da solução lógica para o VDI.

![Implantação de horizonte](media/horizon-deployment.png)

Com esta solução, tem total controlo sobre o Horizon View Manager e o Volume de Aplicações. As interfaces familiares de UI, API e CLI permitem a utilização dos scripts e ferramentas existentes.

A solução CloudSimple requer que faça o seguinte:

* Instale, confivena e gere o VMware Horizon 7.x na sua Cloud Privada.
* Forneça as suas próprias licenças Horizon.

## <a name="deploy-the-solution"></a>Implementar a solução

As secções seguintes descrevem como implementar uma solução VDI utilizando o Horizon na sua Nuvem Privada.

1. [Verifique se as versões de produtos VMware são compatíveis](#verify-that-vmware-product-versions-are-compatible)
2. [Estimar o tamanho do seu ambiente de trabalho](#estimate-the-size-of-your-desktop-environment)
3. [Crie uma Nuvem Privada para o seu ambiente](#create-a-private-cloud-for-your-environment)
4. [Instale o Horizonte VMware na sua Nuvem Privada](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Verifique se as versões de produtos VMware são compatíveis

* Verifique se as suas versões atuais e planeadas de Horizonte, Volumes de Aplicações, Gateway de Acesso Unificado e Gestor de Ambiente do Utilizador são compatíveis entre si e com vCenter e PSC na Cloud Privada. Para obter informações de compatibilidade, consulte [a Matriz de Compatibilidade VMware para horizonte 7.5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Para saber as versões atuais do vCenter e do PSC na sua Nuvem Privada, vá a **Recursos** no [portal CloudSimple,](access-cloudsimple-portal.md)selecione a cloud privada e clique no separador **vSphere Management Network.**

![versões vCenter e PSC](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Estimar o tamanho do seu ambiente de trabalho

* Verifique se a sua configuração identificada está dentro dos limites operacionais da VMware.
* Estimeça os recursos necessários para todos os seus desktops e componentes de gestão horizonte.

### <a name="create-a-private-cloud-for-your-environment"></a>Crie uma Nuvem Privada para o seu ambiente

1. Crie uma Nuvem Privada a partir do portal CloudSimple seguindo as instruções em [Configurar um ambiente de Nuvem Privada](quickstart-create-private-cloud.md).  CloudSimple cria um utilizador vCenter padrão chamado 'cloudowner' em todas as Cloud Privadas recém-criadas. Para obter mais detalhes sobre o modelo de padrão de utilizador e permissão Private Cloud, consulte [o modelo de permissões Private Cloud](learn-private-cloud-permissions.md).
2. Crie um VLAN na sua Nuvem Privada para o plano de gestão Horizonte e atribua-lhe um CIDR de sub-rede. Para obter instruções, consulte [Criar e gerir VLANs/Subnets](create-vlan-subnet.md). Esta é a rede onde todos os componentes da solução (Portal de Acesso Unificado, Servidor de Conexão, Servidor de Volume de Aplicações e Servidor de Ambiente do Utilizador) serão instalados.
3. Decida se deseja utilizar um fornecedor de identidade externo com o seu VCenter Private Cloud. Se sim, escolha uma destas opções:
    * Utilize o seu Ative Directory no local como fornecedor de identidade externa. Para obter instruções, consulte [fontes de identidade do vCenter](set-vcenter-identity.md).
    * Crie um servidor Ative Directory no plano de gestão VLAN da Private Cloud in Horizon para utilizar como seu fornecedor de identidade externo. Para obter instruções, consulte [fontes de identidade do vCenter](set-vcenter-identity.md).
    * Crie um servidor DHCP e DNS no plano de gestão Horizonte VLAN na Nuvem Privada. Para obter instruções, consulte [configurar aplicações DNS e DHCP e cargas de trabalho na sua CloudSimple Private Cloud](dns-dhcp-setup.md).
4. Configurar o encaminhamento de DNS no servidor DNS instalado na Nuvem Privada. Para obter instruções, consulte [Criar um reencaminhador condicional](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-private-cloud"></a>Instale o Horizonte VMware na sua Nuvem Privada

O diagrama de implementação que se segue retrata uma solução Horizon implantada numa Nuvem Privada. O Portal de Acesso Unificado, AD/DC, View e App Volume Server estão instalados no VLAN 234 criado pelo utilizador. O Portal de Acesso Unificado tem um endereço IP público atribuído que é acessível a partir da Internet. Os VMs da piscina de ambiente de trabalho horizon estão implantados em VLAN 235 para proporcionar isolamento e segurança adicionais.

![Implantação de horizonte na Nuvem Privada](media/horizon-private-cloud.png)

As secções seguintes descrevem as instruções para configurar uma implantação semelhante à que é representada na figura. Antes de começar, verifique se tem o seguinte:

* Uma Nuvem Privada criada utilizando o portal CloudSimple com capacidade suficiente para executar as suas piscinas de desktop.
* Largura de banda suficiente entre o ambiente no local e o ambiente Private Cloud para suportar o tráfego de rede para os seus desktops.
* Um túnel VPN local-para-local configurado entre o centro de dados no local e a Nuvem Privada.
* Alcance IP das sub-redes do utilizador final no seu ambiente no local até às sub-redes CloudSimple Private Cloud.
* AD/DHCP/DNS instalado para a sua Nuvem Privada.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>Portal CloudSimple: Criar uma VLAN/sub-rede dedicada para piscinas de desktop

Crie um VLAN para as piscinas de ambiente de trabalho Horizon e atribua-lhe um CIDR de sub-rede. Para obter instruções, consulte [Criar e gerir VLANs/Subnets](create-vlan-subnet.md). Esta é a rede onde todas as máquinas virtuais de desktop irão funcionar.

Siga as melhores práticas de segurança padrão para garantir a sua implantação no Horizonte:

* Permitir apenas tráfego RDP / Tráfego SSH no seu ambiente de trabalho VMs.
* Permitir apenas tráfego de gestão entre o plano de gestão Horizonte VLAN e desktop pool VLAN.
* Permitir apenas tráfego de gestão a partir da rede de instalações.

Pode aplicar estas boas práticas configurando [as regras](firewall.md) de firewall a partir do portal CloudSimple.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>Portal CloudSimple: Configurar regras de firewall para garantir plano de gestão Horizon

Crie as seguintes regras no portal CloudSimple. Para obter instruções, consulte [configurar as tabelas e regras de firewall](firewall.md).

1. Configure as regras de firewall na firewall CloudSimple N-S para permitir a comunicação entre as sub-redes de bordo e a gestão da Horizon VLAN para que apenas sejam permitidas as portas de rede listadas na [lista de portas horizonte](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) de documento vMware.

2. Crie regras de firewall E-W entre a gestão horizonte VLAN e o desktop pool VLAN na Nuvem Privada.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>Portal CloudSimple: Criar um endereço IP público para Portal de Acesso Unificado

Crie um endereço IP público para o aparelho Unified Access Gateway para permitir ligações de clientes de secretária a partir da internet. Para obter instruções, consulte [atribuir endereços IP públicos](public-ips.md).

Quando a configuração estiver concluída, o endereço IP público é atribuído e listado na página de IPs público.

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple portal: Intensificar privilégios

O utilizador 'cloudowner' padrão não tem privilégios suficientes no VCenter Private Cloud para instalar o Horizon, pelo que os privilégios vCenter do utilizador devem ser aumentados. Para mais informações, consulte [os privilégios Escalate.](escalate-private-cloud-privileges.md)

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter UI: Criar um utilizador em Nuvem Privada para instalação Horizonte

1. Inscreva-se no vCenter utilizando as credenciais de utilizador 'cloudowner'.
2. Crie um novo utilizador, 'horizonte-soln-admin', em vCenter e adicione o utilizador ao grupo de administradores no vCenter.
3. Assine fora do vCenter como o utilizador 'cloudowner' e inscreva-se como o utilizador 'horizonte-soln-admin'.

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI: Instalar vMware Horizon

Como mencionado na secção de arquitetura lógica anterior, a solução Horizon tem os seguintes componentes:

* Vista horizonte VMware
* VMware Porta de acesso unificado
* Gestor de volume de aplicativos VMware
* VMware User Environment Manager

Instale os componentes da seguinte forma:

1. Instale e configure o Portal de Acesso Unificado seguindo as instruções fornecidas no documento VMware [Implementando e configurando o Gateway de acesso Unificado VMware VMware](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Instale a Vista Horizonte na Nuvem Privada seguindo as instruções no [Guia de Instalação Ver](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html).

3. Instale o Gestor de Volume de Aplicações seguindo as instruções em [Instalar e Configurar volumes de aplicações VMware](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html).

4. Instale e configure o Gestor de Ambiente do Utilizador seguindo as instruções [sobre a instalação e configuração do Gestor de Ambiente do Utilizador VMware](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Arquive um pedido de suporte para carregar volumes de aplicações pré-embalados da VMware Horizon

Como parte do processo de instalação, o App Volume Manager utiliza volumes pré-embalados para provisão de pilhas de aplicações e volumes prenunciáveis. Estes volumes servem como modelos para pilhas de aplicativos e volumes writable.

O upload dos volumes para a loja de dados Private Cloud requer a palavra-passe raiz ESXi. Para assistência, apresente um pedido de [apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Fixe o pacote de instaladores AppVolumes para que o pessoal de suporte cloudSimple possa enviar os modelos para o seu ambiente Private Cloud.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple portal: De-escalate privilégios

Pode agora [desescalar os privilégios](escalate-private-cloud-privileges.md#de-escalate-privileges) do utilizador 'cloudowner'.

## <a name="ongoing-management-of-your-horizon-solution"></a>Gestão contínua da sua solução Horizonte

Tem total controlo sobre o software Horizon e App Volume Manager no seu ambiente Private Cloud e espera-se que realize a gestão necessária do ciclo de vida do software. Certifique-se de que quaisquer novas versões de software são compatíveis com o private Cloud vCenter e o PSC antes de atualizar ou atualizar o Horizonte ou o Volume de Aplicações.
