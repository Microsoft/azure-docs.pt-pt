---
title: Azure VMware Solution by CloudSimple - Use site private cloud para hospedar uma infraestrutura de ambiente de trabalho virtual usando VMware Horizon
description: Descreve como pode usar o seu site CloudSimple Private Cloud para hospedar uma infraestrutura de ambiente de trabalho virtual usando o VMware Horizon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77025253"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Use o site CloudSimple Private Cloud para hospedar uma infraestrutura de ambiente de trabalho virtual usando vMware Horizon

Pode utilizar o seu site CloudSimple Private Cloud para alojar uma infraestrutura virtual de ambiente de trabalho (VDI) utilizando o VMware Horizon 7.x. A figura que se segue mostra a arquitetura de solução lógica para o VDI.

![Implantação do horizonte](media/horizon-deployment.png)

Com esta solução, você tem total controlo sobre O Gestor de Visão horizonte e Volume de Aplicações. As interfaces ui, API e CLI familiares permitem a utilização dos seus scripts e ferramentas existentes.

A solução CloudSimple requer que faça o seguinte:

* Instale, configure e gerencie o VMware Horizon 7.x na sua Nuvem Privada.
* Forneça as suas próprias licenças Horizon.

## <a name="deploy-the-solution"></a>Implementar a solução

As seguintes secções descrevem como implementar uma solução VDI utilizando o Horizon na sua Nuvem Privada.

1. [Verifique se as versões de produtos VMware são compatíveis](#verify-that-vmware-product-versions-are-compatible)
2. [Calcule o tamanho do seu ambiente de trabalho](#estimate-the-size-of-your-desktop-environment)
3. [Crie uma Nuvem Privada para o seu ambiente](#create-a-private-cloud-for-your-environment)
4. [Instale o VMware Horizon na sua nuvem privada](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Verifique se as versões de produtos VMware são compatíveis

* Verifique se as suas versões atuais e planeadas de Horizon, Volumes de Aplicações, Gateway de Acesso Unificado e Gestor de Ambiente de Utilizador são compatíveis entre si e com vCenter e PSC na Cloud Privada. Para obter informações sobre compatibilidade, consulte A Matriz de [Compatibilidade VMware para horizonte 7.5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Para saber as versões atuais do vCenter e PSC na sua Nuvem Privada, vá a **Recursos** no [portal CloudSimple](access-cloudsimple-portal.md), selecione a sua Nuvem Privada e clique no separador **VSphere Management Network.**

![versões vCenter e PSC](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Calcule o tamanho do seu ambiente de trabalho

* Verifique se a sua configuração identificada está dentro dos limites operacionais da VMware.
* Calcule os recursos necessários para todos os seus desktops e componentes de gestão Horizon.

### <a name="create-a-private-cloud-for-your-environment"></a>Crie uma Nuvem Privada para o seu ambiente

1. Crie uma Nuvem Privada a partir do portal CloudSimple seguindo as instruções em [Configurar um ambiente cloud privado](quickstart-create-private-cloud.md).  A CloudSimple cria um utilizador vCenter padrão chamado 'cloudowner' em cada nuvem privada recém-criada. Para mais detalhes sobre o modelo de permissão padrão do utilizador e permissão da Cloud Privada, consulte o modelo de [permissões Da Nuvem Privada](learn-private-cloud-permissions.md).
2. Crie um VLAN na sua Nuvem Privada para o plano de gestão horizonte e atribua-lhe um CIDR de sub-rede. Para obter instruções, consulte [Criar e gerir VLANs/Subnets](create-vlan-subnet.md). Esta é a rede onde serão instalados todos os componentes da solução (Gateway de Acesso Unificado, Servidor de Ligação, Servidor de Volume de Aplicações e Servidor de Ambiente de Utilizador).
3. Decida se pretende utilizar um fornecedor de identidade externo com o seu vCenter Private Cloud. Se sim, escolha uma destas opções:
    * Utilize o seu Diretório Ativo no local como fornecedor de identidade externa. Para obter instruções, consulte fontes de [identidade vCenter](set-vcenter-identity.md).
    * Instale um servidor de Diretório Ativo no plano de gestão De cloud privado no Horizonte VLAN para usar como seu fornecedor de identidade externo. Para obter instruções, consulte fontes de [identidade vCenter](set-vcenter-identity.md).
    * Instale um servidor DHCP e DNS no plano de gestão Horizonte VLAN na Nuvem Privada. Para obter instruções, consulte [Configurar aplicações dNS e DHCP e cargas](dns-dhcp-setup.md)de trabalho na sua CloudSimple Private Cloud .
4. Configure o reencaminhado DNS no servidor DNS instalado na Cloud Privada. Para obter instruções, consulte [Criar um Forwardr Condicional](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-private-cloud"></a>Instale o VMware Horizon na sua nuvem privada

O diagrama de implantação seguinte retrata uma solução Horizon implantada numa Nuvem Privada. Gateway de acesso unificado, AD/DC, View e App Volume Server estão instalados no VLAN 234 criado pelo utilizador. O Portal de Acesso Unificado tem um endereço IP público atribuído que é acessível a partir da Internet. Os VMs do ambiente de trabalho horizon estão implantados em VLAN 235 para fornecer isolamento e segurança adicionais.

![Implantação do Horizonte na Nuvem Privada](media/horizon-private-cloud.png)

As seguintes secções descrevem as instruções para configurar uma implantação semelhante à que está representada na figura. Antes de começar, verifique se tem o seguinte:

* Uma Nuvem Privada criada usando o portal CloudSimple com capacidade suficiente para executar as suas piscinas de ambiente de trabalho.
* Largura de banda suficiente entre o ambiente no local e o ambiente Private Cloud para suportar o tráfego de rede para os seus ambientes de trabalho.
* Um túnel VPN local-a-local configurado entre o centro de dados no local e a Nuvem Privada.
* Alcance IP das subredes de utilizador final no seu ambiente no local para as subnets CloudSimple Private Cloud.
* AD/DHCP/DNS instalado para a sua Nuvem Privada.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>Portal CloudSimple: Criar uma VLAN/subnet dedicada para piscinas de desktop

Crie um VLAN para as piscinas horizon desktop e atribua-lhe um CIDR de sub-rede. Para obter instruções, consulte [Criar e gerir VLANs/Subnets](create-vlan-subnet.md). Esta é a rede onde todas as máquinas virtuais de ambiente de trabalho funcionarão.

Siga as melhores práticas de segurança padrão para garantir a sua implementação no Horizonte:

* Permita apenas tráfego RDP / SSH para os seus VMs de secretária.
* Permitir apenas tráfego de gestão entre o plano de gestão Horizonte VLAN e o desktop pool VLAN.
* Permitir apenas tráfego de gestão a partir da rede no local.

Pode impor estas melhores práticas configurando regras de [firewall](firewall.md) a partir do portal CloudSimple.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>Portal CloudSimple: Configure regras de firewall para garantir plano de gestão horizonte

Estabeleça as seguintes regras no portal CloudSimple. Para obter instruções, consulte [Configurar as tabelas e regras](firewall.md)de firewall .

1. Configure as regras de firewall na firewall CloudSimple N-S para permitir a comunicação entre as subredes no local e a Gestão Horizonte VLAN de modo a que apenas sejam permitidas as portas de rede listadas na lista de porta sérs [horizonte documental](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) VMware.

2. Crie regras de firewall E-W entre a VLAN de gestão do Horizonte e o desktop pool VLAN na Nuvem Privada.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>Portal CloudSimple: Criar um endereço IP público para Gateway de Acesso Unificado

Crie um endereço IP público para o aparelho Gateway de Acesso Unificado para permitir as ligações dos clientes de ambiente de trabalho a partir da internet. Para obter instruções, consulte [Alocar endereços IP públicos](public-ips.md).

Quando a configuração estiver concluída, o endereço IP público é atribuído e listado na página de IPs públicos.

#### <a name="cloudsimple-portal-escalate-privileges"></a>Portal CloudSimple: Escalar privilégios

O utilizador 'cloudowner' padrão não tem privilégios suficientes no vCenter private Cloud para instalar o Horizon, pelo que os privilégios vCenter do utilizador devem ser aumentados. Para mais informações, consulte a [Escalate privileges](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter UI: Criar um utilizador em Nuvem Privada para a instalação Horizon

1. Inscreva-se no vCenter utilizando as credenciais de utilizador do 'cloudowner'.
2. Crie um novo utilizador, 'horizon-soln-admin', no vCenter e adicione o utilizador ao grupo de administradores no vCenter.
3. Assine o vCenter como o utilizador 'cloudowner' e inscreva-se como o utilizador 'horizon-soln-administrador'.

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI: Instalar VMware Horizon

Como mencionado na secção de arquitetura lógica anterior, a solução Horizon tem os seguintes componentes:

* Visão do horizonte vMware
* Gateway de acesso unificado VMware
* Gestor de volume de aplicativos VMware
* Gestor de ambiente de utilizador vMware

Instale os componentes da seguinte forma:

1. Instale e configure gateway de acesso unificado seguindo as instruções fornecidas no documento VMware Implementando e Configurando o Gateway de [Acesso Unificado VMware](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Instale a Vista Horizonte na Nuvem Privada seguindo as instruções no Guia de [Instalação View](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html).

3. Instale o Gestor de Volume de Aplicações seguindo as instruções em Volumes de [Aplicações VMware de Instalação e Configuração](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html).

4. Instale e configure o Gestor de Ambiente do Utilizador seguindo as instruções sobre [a instalação e configuração](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)do Gestor de Ambiente do Utilizador VMware .

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Faça um pedido de suporte para carregar volumes de aplicações pré-embalados vMware Horizon

Como parte do processo de instalação, o Gestor de Volume de Aplicações utiliza volumes pré-embalados para fornecer pilhas de aplicações e volumes de texto. Estes volumes servem como modelos para pilhas de aplicações e volumes writáveis.

O upload dos volumes para a loja de dados Private Cloud requer a palavra-passe de raiz ESXi. Para assistência, apresente um pedido de [apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Fixe o pacote de instalação AppVolumes para que o pessoal de suporte CloudSimple possa carregar os modelos para o seu ambiente Cloud Privado.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple portal: Desescalar privilégios

Agora pode [desescalar os privilégios](escalate-private-cloud-privileges.md#de-escalate-privileges) do utilizador 'cloudowner'.

## <a name="ongoing-management-of-your-horizon-solution"></a>Gestão contínua da sua solução Horizon

Você tem controlo total sobre o software Horizon e App Volume Manager no seu ambiente Private Cloud e espera-se que execute a gestão necessária do ciclo de vida do software. Certifique-se de que quaisquer novas versões de software são compatíveis com o vCenter e PSC private Cloud antes de atualizar ou atualizar o Volume horizonte ou app.
