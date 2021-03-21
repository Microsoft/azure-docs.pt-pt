---
title: Azure VMware Solution by CloudSimple - Use máquinas virtuais de carga de trabalho em Nuvem Privada usando o Veeam
description: Descreve como pode fazer o back up das suas máquinas virtuais que estão a funcionar numa Nuvem Privada CloudSimple baseada em Azure usando o Veeam B&R 9.5
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be606b3e23a594e67acf3f169d88353403d8577
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899342"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Apoiar VMs de carga de trabalho na CloudSimple Private Cloud usando Veeam B&R

Este guia descreve como pode fazer o back up das suas máquinas virtuais que estão a funcionar numa CloudSimple Private Cloud baseada em Azure, utilizando o Veeam B&R 9.5.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Sobre a solução de back up e recuperação do Veeam

A solução Veeam inclui os seguintes componentes.

**Servidor de backup**

O servidor de backup é um servidor Windows (VM) que serve como centro de controlo para o Veeam e executa estas funções: 

* Coordena cópias de segurança, replicação, verificação de recuperação e restauro de tarefas
* Controla o agendamento de empregos e a atribuição de recursos
* Permite-lhe configurar e gerir componentes de infraestrutura de backup e especificar configurações globais para a infraestrutura de backup

**Servidores Proxy**

Os servidores Proxy são instalados entre o servidor de backup e outros componentes da infraestrutura de backup. Gerem as seguintes funções:

* Recuperação de dados VM do armazenamento de produção
* Compressão
* Eliminação de duplicados
* Encriptação
* Transmissão de dados para o repositório de backup

**Repositório de reserva**

O repositório de backup é o local de armazenamento onde Veeam guarda ficheiros de backup, cópias VM e metadados para VMs replicados.  O repositório pode ser um servidor Windows ou Linux com discos locais (ou NFS/SMB montado) ou um aparelho de deduplica de armazenamento de hardware.

### <a name="veeam-deployment-scenarios"></a>Cenários de implantação do Veeam
Você pode aproveitar a Azure para fornecer um repositório de reserva e um alvo de armazenamento para backup de longo prazo e arquivamento. Todo o tráfego de rede de backup entre VMs na Nuvem Privada e o repositório de backup em Azure viaja sobre uma alta largura de banda, ligação de latência baixa. O tráfego de replicação através das regiões viaja através da rede interna de backplane Azure, o que reduz os custos de largura de banda para os utilizadores.

**Implantação básica**

Para ambientes com menos de 30 TB para fazer o back up, o CloudSimple recomenda a seguinte configuração:

* Servidor de backup veeam e servidor proxy instalado no mesmo VM na Cloud Privada.
* Um repositório de apoio primário baseado em Linux em Azure configurado como um alvo para trabalhos de reserva.
* `azcopy` usado para copiar os dados do repositório de backup primário para um recipiente de bolhas Azure que é replicado para outra região.

![Diagrama que mostra cenários básicos de implantação do Veeam.](media/veeam-basicdeployment.png)

**Implantação avançada**

Para ambientes com mais de 30 TB para fazer o back up, o CloudSimple recomenda a seguinte configuração:

* Um servidor proxy por nó no cluster vSAN, como recomendado pelo Veeam.
* O repositório de backup primário baseado no Windows na Nuvem Privada para cache de cinco dias de dados para restauros rápidos.
* O repositório de backup Linux em Azure como alvo de trabalhos de cópia de cópia suplente para uma retenção de maior duração. Este repositório deve ser configurado como um repositório de reserva de escala.
* `azcopy` usado para copiar os dados do repositório de backup primário para um recipiente de bolhas Azure que é replicado para outra região.

![Cenários básicos de implantação](media/veeam-advanceddeployment.png)

Na figura anterior, note que o proxy de backup é um VM com acesso Hot Add aos discos VM de carga de trabalho na loja de dados vSAN. Veeam usa o modo de transporte de procuração de reserva do Aparelho Virtual para vSAN.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>Requisitos para a solução Veeam no CloudSimple

A solução Veeam requer que faça o seguinte:

* Forneça as suas próprias licenças Veeam.
* Implemente e gere o Veeam para fazer backup das cargas de trabalho em execução na CloudSimple Private Cloud.

Esta solução proporciona-lhe total controlo sobre a ferramenta de backup Veeam e oferece a opção de usar a interface Veeam nativa ou o plug-in Veeam vCenter para gerir trabalhos de backup VM.

Se for um utilizador veeam existente, pode saltar a secção em Componentes de Solução Veeam e proceder diretamente aos [Cenários de Implantação do Veeam](#veeam-deployment-scenarios).

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Instale e configuga backups do Veeam na sua CloudSimple Private Cloud

As secções seguintes descrevem como instalar e configurar uma solução de backup Veeam para a sua CloudSimple Private Cloud.

O processo de implantação consiste nestas etapas:

1. [vCenter UI: Criar serviços de infraestrutura na sua Nuvem Privada](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [Portal CloudSimple: Configurar rede de nuvem privada para o Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [CloudSimple portal: Intensificar privilégios](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Portal Azure: Ligue a sua rede virtual à Nuvem Privada](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Portal Azure: Criar um repositório de reserva em Azure](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Portal Azure: Configurar o armazenamento de bolhas Azure para retenção de dados a longo prazo](#configure-azure-blob-storage-for-long-term-data-retention)
7. [vCenter UI of Private Cloud: Instalar Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Consola Veeam: Software de backup & recovery da Veeam Configure](#veeam-console-install-veeam-backup-and-recovery-software)
9. [Portal CloudSimple: Configurar o acesso ao Veeam e desescalar privilégios](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Antes de começar

São necessários os seguintes antes de iniciar a implantação do Veeam:

* Uma subscrição da Azure propriedade de si
* Um grupo de recursos Azure pré-criado
* Uma rede virtual Azure na sua subscrição
* Uma conta de armazenamento do Azure
* Uma [Nuvem Privada](create-private-cloud.md) criada usando o portal CloudSimple.  

São necessários os seguintes itens durante a fase de implementação:

* Modelos VMware para o Windows instalar o Veeam (tal como o Windows Server 2012 R2 - imagem de 64 bits)
* Um VLAN disponível identificado para a rede de backup
* CIDR da sub-rede a atribuir à rede de backup
* Veeam 9.5 u3 u3 media instalados (ISO) enviados para a loja de dados vSAN da Nuvem Privada

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter UI: Criar serviços de infraestrutura na sua Nuvem Privada

Configure os serviços de infraestrutura na Nuvem Privada para facilitar a gestão das suas cargas de trabalho e ferramentas.

* Pode adicionar um fornecedor de identidade externo, conforme descrito no [set up vCenter sources para utilizar](set-vcenter-identity.md) o Ative Directory se algum dos seguintes se aplicar:

  * Pretende identificar os utilizadores a partir do seu Ative Directory (AD) no seu Private Cloud.
  * Você quer configurar um AD na sua Nuvem Privada para todos os utilizadores.
  * Quer usar a Azure AD.
* Para fornecer serviços de procura de endereços IP, gestão de endereços IP e resolução de nomes para as suas cargas de trabalho na Nuvem Privada, crie um servidor DHCP e DNS, conforme descrito na [Configuração de aplicações e cargas de trabalho DNS e DHCP na cloudSimple Private Cloud.](dns-dhcp-setup.md)

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple Private Cloud: Configurar rede de nuvem privada para o Veeam

Aceda ao portal CloudSimple para configurar a rede Private Cloud para a solução Veeam.

Crie um VLAN para a rede de backup e atribua-lhe um CIDR de sub-rede. Para obter instruções, consulte [Criar e gerir VLANs/Subnets](create-vlan-subnet.md).

Crie regras de firewall entre a sub-rede de gestão e a rede de backup para permitir o tráfego de rede nas portas utilizadas pelo Veeam. Consulte o tópico Veeam [Portos Usados.](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) Para obter instruções sobre a criação de regras de firewall, consulte [configurar as tabelas e regras de firewall](firewall.md).

A tabela a seguir fornece uma lista portuária.

| Ícone | Description | Ícone | Description |
| ------------ | ------------- | ------------ | ------------- |
| Servidor de backup  | vCenter  | HTTPS / TCP  | 443 |
| Servidor de backup <br> *Necessário para a implementação de componentes de replicação de & de backup do Veeam* | Proxy de reserva  | TCP/UDP  | 135, 137 a 139 e 445 |
    | Servidor de backup   | DNS  | UDP  | 53  | 
    | Servidor de backup   | Servidor de notificação de atualização de Veeam  | TCP  | 80  | 
    | Servidor de backup   | Servidor de atualização de licença de Veeam  | TCP  | 443  | 
    | Proxy de reserva   | vCenter |   |   | 
    | Proxy de reserva  | Repositório de Backup Linux   | TCP  | 22  | 
    | Proxy de reserva  | Repositório de backup do Windows  | TCP  | 49152 - 65535   | 
    | Repositório de Reserva  | Proxy de reserva  | TCP  | 2500 -5000  | 
    | Repositório de Backup de Origem<br> *Usado para trabalhos de cópia de cópias de backup*  | Repositório de Backup alvo  | TCP  | 2500 - 5000  | 

Criar regras de firewall entre a sub-rede de carga de trabalho e a rede de backup, conforme descrito na [configuração de tabelas e regras de firewall](firewall.md).  Para a aplicação, devem ser [abertas portas adicionais](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) nos VMs de carga de trabalho que acolhem aplicações específicas.

Por padrão, o CloudSimple fornece uma ligação ExpressRoute de 1Gbps. Para tamanhos de ambiente maiores, pode ser necessária uma ligação de largura de banda mais alta. Contacte o suporte da Azure para obter mais informações sobre ligações de largura de banda mais elevadas.

Para continuar a configuração, precisa da chave de autorização e do circuito de pares URI e acesso à sua Assinatura Azure.  Esta informação está disponível na página De Ligação de Rede Virtual no portal CloudSimple. Para obter instruções, consulte [obter informações de espreitar a rede virtual Azure para a ligação CloudSimple](virtual-network-connection.md). Se tiver algum problema em obter as informações, [contacte o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple Private Cloud: Intensificar privilégios para o turvador

O utilizador 'cloudowner' padrão não tem privilégios suficientes no VCenter Private Cloud para instalar o VEEAM, pelo que os privilégios vCenter do utilizador devem ser aumentados. Para mais informações, consulte [os privilégios Escalate.](escalate-private-cloud-privileges.md)

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Portal Azure: Ligue a sua rede virtual à Nuvem Privada

Ligue a sua rede virtual à Nuvem Privada seguindo as instruções na [Ligação da Rede Virtual Azure utilizando o ExpressRoute](azure-expressroute-connection.md).

### <a name="azure-portal-create-a-backup-repository-vm"></a>Portal Azure: Criar um VM repositório de backup

1. Crie um D2 v3 VM padrão com (2 vCPUs e 8 GB de memória).
2. Selecione a imagem baseada no CentOS 7.4.
3. Configure um grupo de segurança de rede (NSG) para o VM. Verifique se o VM não tem um endereço IP público e não é acessível a partir da internet pública.
4. Crie um nome de utilizador e uma conta de utilizador baseada em palavra-passe para o novo VM. Para obter instruções, consulte [Criar uma máquina virtual Linux no portal Azure](../virtual-machines/linux/quick-create-portal.md).
5. Crie 1x512 GiB standard HDD e prenda-o ao VM repositório.  Para obter instruções, consulte [como anexar um disco de dados gerido a um VM do Windows no portal Azure](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Criar um volume XFS no disco gerido](https://www.digitalocean.com/docs/volumes/how-to/). Faça login no VM utilizando as credenciais anteriormente mencionadas. Execute o seguinte script para criar um volume lógico, adicione o disco a ele, crie uma [partição](https://www.digitalocean.com/docs/volumes/how-to/partition/) do sistema de ficheiros XFS e [monte](https://www.digitalocean.com/docs/volumes/how-to/mount/) a partição sob o caminho /backup1.

    Script de exemplo:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Exponha /backup1 como um ponto de montagem NFS para o servidor de backup Veeam que está em execução na Cloud Privada. Para obter instruções, consulte o artigo do Oceano Digital [Como Configurar um Monte NFS no CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Utilize este nome de partilha NFS quando configurar o repositório de backup no servidor de backup veeam.

8. Configure as regras de filtragem no NSG para o repositório de backup VM para permitir explicitamente todo o tráfego de rede de e para o VM.

> [!NOTE]
> A Replicação de cópias de segurança do Veeam & utiliza o protocolo SSH para comunicar com os repositórios de backup do Linux e requer o utilitário SCP nos repositórios Linux. Verifique se o daemon SSH está devidamente configurado e que o SCP está disponível no anfitrião Linux.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Configure armazenamento de bolhas Azure para retenção de dados a longo prazo

1. Crie uma conta de armazenamento para fins gerais (GPv2) de tipo padrão e um recipiente blob, conforme descrito no vídeo da Microsoft [Getting Started with Azure Storage](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage).
2. Crie um recipiente de armazenamento Azure, conforme descrito na referência [Do Recipiente Criar.](/rest/api/storageservices/create-container)
2. Descarregue o `azcopy` utilitário da linha de comando para Linux da Microsoft. Pode utilizar os seguintes comandos na casca de choque no CentOS 7.5.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Utilize o `azcopy` comando para copiar ficheiros de cópia de e para o recipiente blob.  Consulte [os dados de transferência com a AzCopy no Linux](../storage/common/storage-use-azcopy-v10.md) para obter comandos detalhados.

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>vCenter consola de Nuvem Privada: Instalar Veeam B&R

Aceda vCenter a partir da sua Nuvem Privada para criar uma conta de serviço Veeam, instale Veeam B&R 9.5 e configuure o Veeam utilizando a conta de serviço.

1. Crie uma nova função chamada 'Veeam Backup Role' e atribua-lhe as permissões necessárias, conforme recomendado pelo Veeam. Para mais informações consulte o tópico Veeam [Permissões Necessárias](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Crie um novo grupo 'Veeam User Group' no vCenter e atribua-lhe a 'Função de Backup Veeam'.
3. Crie um novo utilizador da 'Conta de Serviço Veeam' e adicione-a ao 'Grupo utilizador Veeam'.

    ![Criação de uma conta de serviço Veeam](media/veeam-vcenter01.png)

4. Crie um grupo porta distribuído no vCenter utilizando a rede de backup VLAN. Para mais detalhes, consulte o vídeo VMware [Criando um Grupo de Portas Distribuído no vSphere Web Client](https://www.youtube.com/watch?v=wpCd5ZbPOpA).
5. Crie os VMs para os servidores de backup e proxy veeam em vCenter de acordo com os requisitos do [sistema Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95). Pode utilizar o Windows 2012 R2 ou o Linux. Para obter mais informações consulte [os requisitos para a utilização de repositórios de backup Linux](https://www.veeam.com/kb2216).
6. Monte o Veeam ISO instalado como um dispositivo CDROM no servidor de backup Veeam VM.
7. Utilizando uma sessão RDP para a máquina R2 do Windows 2012 (o alvo da instalação do Veeam), [instale o Veeam B&R 9.5u3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) num VM R2 do Windows 2012.
8. Encontre o endereço IP interno do servidor de backup VM do Veeam e configuure o endereço IP para estar estático no servidor DHCP. Os passos exatos necessários para o fazer dependem do servidor DHCP. Como exemplo, os <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">mapeamentos estáticos do DHCP</a> do artigo netgate explicam como configurar um servidor DHCP usando um router pfSense.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Consola Veeam: Instalar software de backup e recuperação do Veeam

Utilizando a consola Veeam, configuure o software de backup e recuperação da Veeam. Para mais detalhes, consulte [a Cópia de Segurança do Veeam & replicação v9 - Instalação e implantação](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. Adicione vMware vSphere como um ambiente de servidor gerido. Quando solicitado, forneça as credenciais da Conta de Serviço Veeam que criou no início do [vCenter Console of Private Cloud: Instalar o Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br).

    * Utilize definições predefinidos para controlo de carga e definições avançadas predefinidos.
    * Desa esta medida a localização do servidor de montagem é o servidor de reserva.
    * Altere a localização de backup de configuração para o servidor Veeam para o repositório remoto.

2. Adicione o servidor Linux em Azure como o repositório de reserva.

    * Utilize definições predefinições para controlo de carga e para as definições avançadas. 
    * Desa esta medida a localização do servidor de montagem é o servidor de reserva.
    * Altere a localização de backup de configuração para o servidor Veeam para o repositório remoto.

3. Ativar a encriptação da cópia de segurança da configuração utilizando **as definições de backup de configuração de configuração do home>**.

4. Adicione um VM do servidor Windows como um servidor proxy para ambiente VMware. Utilizando as "Regras de Tráfego" para um representante, criptografe os dados de backup através do fio.

5. Configurar trabalhos de apoio.
    * Para configurar trabalhos de backup, siga as instruções na [Criação de um Trabalho de Reserva](https://www.youtube.com/watch?v=YHxcUFEss4M).
    * Ativar a encriptação de ficheiros de cópia de segurança em **Definições Avançadas > Armazenamento**.

6. Configurar trabalhos de cópia suplente.

    * Para configurar trabalhos de cópia de cópias de cópia, siga as instruções no vídeo [Criando um Trabalho de Cópia de Cópia de Cópia de Cópia](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)de Cópia .
    * Ativar a encriptação de ficheiros de cópia de segurança em **Definições Avançadas > Armazenamento**.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>Portal CloudSimple: Configurar o acesso ao Veeam e desescalar privilégios
Crie um endereço IP público para o servidor de backup e recuperação do Veeam. Para obter instruções, consulte [atribuir endereços IP públicos](public-ips.md).

Crie uma regra de firewall utilizando para permitir que o servidor de backup veeam crie uma ligação de saída ao site veeam para descarregar atualizações/patches na porta TCP 80. Para obter instruções, consulte [configurar as tabelas e regras de firewall](firewall.md).

Para desescalar os privilégios, veja [os privilégios de De-escalate.](escalate-private-cloud-privileges.md#de-escalate-privileges)

## <a name="references"></a>Referências

### <a name="cloudsimple-references"></a>Referências CloudSimple

* [Criar uma nuvem privada](create-private-cloud.md)
* [Criar e gerir VLANs/Subnets](create-vlan-subnet.md)
* [vCenter Fontes de Identidade](set-vcenter-identity.md)
* [Configuração DE DNS e DHCP de carga](dns-dhcp-setup.md)
* [Escalar privilégios](escalate-privileges.md)
* [Configurar tabelas e regras de firewall](firewall.md)
* [Permissões private Cloud](learn-private-cloud-permissions.md)
* [Alocar endereços IP públicos](public-ips.md)

### <a name="veeam-references"></a>Referências Veeam

* [Portos Usados](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Permissões Necessárias](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Requisitos do sistema](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Instalação da replicação & de backup do Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Módulos e permissões necessários para suporte ao FlR e repositório multi-OS para o Linux](https://www.veeam.com/kb2216)
* [Cópia de segurança veeam & replicação v9 - Instalação e Implantação - Vídeo](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 Criando um trabalho de backup - Vídeo](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 Criando um trabalho de cópia de backup - vídeo](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Referências a azul

* [Configurar uma porta de rede virtual para o ExpressRoute utilizando o portal Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Ligue um VNet a um circuito - subscrição diferente](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Criar uma máquina virtual Linux no portal Azure](../virtual-machines/linux/quick-create-portal.md)
* [Como anexar um disco de dados gerido a um Windows VM no portal Azure](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Começar com Azure Storage - Vídeo](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Criar Contentor](/rest/api/storageservices/create-container)
* [Transferir dados com o AzCopy no Linux](../storage/common/storage-use-azcopy-v10.md)

### <a name="vmware-references"></a>Referências VMware

* [Criação de um Grupo De Portas Distribuídos no vSphere Web Client - Vídeo](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Outras referências

* [Criar um volume XFS no disco gerido - RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Como Configurar um monte NFS no CentOS 7 - HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [Configuração do Servidor DHCP - Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
