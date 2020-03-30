---
title: Solução Azure VMware by CloudSimple - Back up máquinas virtuais de carga de trabalho em Nuvem Privada usando Veeam
description: Descreve como pode fazer o back up das suas máquinas virtuais que estão a funcionar numa Nuvem Privada CloudSimple baseada em Azure usando Veeam B&R 9.5
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3262841efb9109b1de24fe501ea0a7bea0dd612d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025134"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Back up Volume de Trabalho VMs na CloudSimple Private Cloud usando Veeam B&R

Este guia descreve como pode fazer o back up das suas máquinas virtuais que estão a funcionar numa Nuvem Privada CloudSimple baseada em Azure, utilizando Veeam B&R 9.5.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Sobre a solução de volta e recuperação veeam

A solução Veeam inclui os seguintes componentes.

**Servidor de backup**

O servidor de backup é um servidor Windows (VM) que serve de centro de controlo para veeam e executa estas funções: 

* Coordena as tarefas de backup, replicação, verificação de recuperação e restauro de tarefas
* Controla o agendamento de postos de trabalho e a atribuição de recursos
* Permite-lhe configurar e gerir componentes de infraestrutura de backup e especificar configurações globais para a infraestrutura de backup

**Servidores proxy**

Os servidores proxy estão instalados entre o servidor de backup e outros componentes da infraestrutura de backup. Gerem as seguintes funções:

* Recuperação de dados vm do armazenamento de produção
* Compressão
* Eliminação de duplicados
* Encriptação
* Transmissão de dados para o repositório de backup

**Repositório de backup**

O repositório de reserva é o local de armazenamento onde veeam guarda ficheiros de backup, cópias VM e metadados para VMs replicados.  O repositório pode ser um servidor Windows ou Linux com discos locais (ou NFS/SMB montados) ou um aparelho de desduplicação de armazenamento de hardware.

### <a name="veeam-deployment-scenarios"></a>Cenários de implantação de Veeam
Você pode aproveitar o Azure para fornecer um repositório de reserva e um alvo de armazenamento para backup e arquivo de longo prazo. Todo o tráfego de rede de backup entre VMs na Nuvem Privada e o repositório de backup em Azure viaja sobre uma alta largura de banda, elo de latência baixa. O tráfego de replicação em regiões viaja sobre a rede interna de backplane Azure, o que reduz os custos de largura de banda para os utilizadores.

**Implantação básica**

Para ambientes com menos de 30 TB para fazer backback, o CloudSimple recomenda a seguinte configuração:

* Servidor de backup Veeam e servidor proxy instalado no mesmo VM na Nuvem Privada.
* Um repositório primário baseado em Linux em Azure configurado como um alvo para trabalhos de reserva.
* `azcopy`usado para copiar os dados do repositório de backup primário para um recipiente de blob Azure que é replicado para outra região.

![Cenários básicos de implantação](media/veeam-basicdeployment.png)

**Implantação avançada**

Para ambientes com mais de 30 TB para fazer apoio, o CloudSimple recomenda a seguinte configuração:

* Um servidor proxy por nó no cluster vSAN, conforme recomendado por Veeam.
* Repositório de backup primário baseado no Windows na Nuvem Privada para cache cinco dias de dados para restauros rápidos.
* O repositório de backup Linux em Azure como alvo para trabalhos de cópia de cópia de reserva para a retenção de maior duração. Este repositório deve ser configurado como um repositório de reserva scale-out.
* `azcopy`usado para copiar os dados do repositório de backup primário para um recipiente de blob Azure que é replicado para outra região.

![Cenários básicos de implantação](media/veeam-advanceddeployment.png)

Na figura anterior, note que o proxy de reserva é um VM com acesso Hot Add aos discos VM de carga de trabalho na loja de dados vSAN. A Veeam utiliza o modo de transporte de proxy de reserva de aparelhos virtuais para vSAN.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>Requisitos para a solução Veeam na CloudSimple

A solução Veeam requer que faça o seguinte:

* Forneça as suas próprias licenças Veeam.
* Desloque e gerencie veeam para fazer backup das cargas de trabalho em execução na CloudSimple Private Cloud.

Esta solução fornece-lhe o controlo total sobre a ferramenta de backup Veeam e oferece a opção de usar a interface veeam nativa ou o plug-in Veeam vCenter para gerir trabalhos de backup VM.

Se for um utilizador Veeam existente, pode ignorar a secção dos Componentes da Solução Veeam e seguir diretamente para cenários de implantação de [Veeam](#veeam-deployment-scenarios).

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Instale e configure backups Veeam na sua CloudSimple Private Cloud

As seguintes secções descrevem como instalar e configurar uma solução de backup Veeam para a sua CloudSimple Private Cloud.

O processo de implantação consiste nestas etapas:

1. [vCenter UI: Instale serviços de infraestrutura na sua Nuvem Privada](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [Portal CloudSimple: Configurar rede de nuvem privada para Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [Portal CloudSimple: Escalar privilégios](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Portal Azure: Ligue a sua rede virtual à Nuvem Privada](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Portal Azure: Criar um repositório de backup em Azure](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Portal Azure: Configure armazenamento de blob Azure para retenção de dados a longo prazo](#configure-azure-blob-storage-for-long-term-data-retention)
7. [vCenter UI da Nuvem Privada: Instale Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Consola Veeam: Configure Veeam Backup & Recovery](#veeam-console-install-veeam-backup-and-recovery-software)
9. [CloudSimple portal: Configurar o acesso veeam e desescalar privilégios](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Antes de começar

São necessários os seguintes antes de iniciar a implantação da Veeam:

* Uma subscrição Azure que lhe pertence
* Um grupo de recursos Azure pré-criado
* Uma rede virtual Azure na sua subscrição
* Uma conta de armazenamento do Azure
* Uma [Nuvem Privada](create-private-cloud.md) criada usando o portal CloudSimple.  

São necessários os seguintes itens durante a fase de implementação:

* Modelos VMware para o Windows instalar Veeam (como Windows Server 2012 R2 - 64 bits)
* Um VLAN disponível identificado para a rede de backup
* CIDR da subnet a atribuir à rede de cópias de segurança
* Veeam 9.5 u3 meios instaladores (ISO) enviados para a loja de dados vSAN da Nuvem Privada

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter UI: Instale serviços de infraestrutura na sua Nuvem Privada

Configure os serviços de infraestrutura na Nuvem Privada para facilitar a gestão das suas cargas de trabalho e ferramentas.

* Pode adicionar um fornecedor de identidade externo, conforme descrito na [Configuração de fontes de identidade vCenter para utilizar o Ative Directory](set-vcenter-identity.md) se algum dos seguintes se aplicar:

  * Pretende identificar utilizadores do seu Diretório Ativo (AD) no seu Cloud Privado.
  * Você quer configurar um Anúncio na sua Nuvem Privada para todos os utilizadores.
  * Quer usar o Azure AD.
* Para fornecer serviços de procura de endereçoip, gestão de endereços IP e serviços de resolução de nomes para as suas cargas de trabalho na Cloud Privada, configurar um servidor DHCP e DNS conforme descrito nas [aplicações DNS e DHCP e cargas](dns-dhcp-setup.md)de trabalho na sua CloudSimple Private Cloud .

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple Private Cloud: Configurar rede de nuvem privada para Veeam

Aceda ao portal CloudSimple para criar redes Private Cloud para a solução Veeam.

Crie um VLAN para a rede de backup e atribua-lhe um CIDR de sub-rede. Para obter instruções, consulte [Criar e gerir VLANs/Subnets](create-vlan-subnet.md).

Crie regras de firewall entre a subnet de gestão e a rede de backup para permitir o tráfego de rede em portas utilizadas pela Veeam. Consulte o tópico Veeam [Portas Usadas.](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) Para obter instruções sobre a criação de regras de firewall, consulte [Configurar tabelas e regras](firewall.md)de firewall .

A tabela seguinte fornece uma lista portuária.

| Ícone | Descrição | Ícone | Descrição |
| ------------ | ------------- | ------------ | ------------- |
| Servidor de backup  | vCenter  | HTTPS / TCP  | 443 |
| Servidor de backup <br> *Necessário para a implementação de componentes de replicação de & de backup veeam* | Procuração de backup  | TCP/UDP  | 135, 137 a 139 e 445 |
    | Servidor de backup   | DNS  | UDP  | 53  | 
    | Servidor de backup   | Servidor de notificação de atualização Veeam  | TCP  | 80  | 
    | Servidor de backup   | Servidor de atualização de licença Veeam  | TCP  | 443  | 
    | Procuração de backup   | vCenter |   |   | 
    | Procuração de backup  | Repositório de Backup Linux   | TCP  | 22  | 
    | Procuração de backup  | Repositório de backup do Windows  | TCP  | 49152 - 65535   | 
    | Repositório de backup  | Procuração de backup  | TCP  | 2500 -5000  | 
    | Repositório de Backup Source<br> *Usado para trabalhos de cópia de cópia de reserva*  | Repositório de Backup Alvo  | TCP  | 2500 - 5000  | 

Crie regras de firewall entre a subnet de carga de trabalho e a rede de backup, tal como descrito nas [tabelas e regras](firewall.md)de firewall configuradas .  Para obter cópias de segurança e restauro conscientes da aplicação, devem ser [abertas portas adicionais](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) sobre os VMs de carga de trabalho que acolhem aplicações específicas.

Por predefinição, o CloudSimple fornece uma ligação ExpressRoute de 1Gbps. Para tamanhos de ambiente maiores, pode ser necessária uma ligação de largura de banda mais elevada. Contacte o suporte azure para obter mais informações sobre ligações de largura de banda mais elevadas.

Para continuar a configuração, necessita da chave de autorização e do circuito de pares URI e acesso à sua Assinatura Azure.  Esta informação está disponível na página de Ligação de Rede Virtual no portal CloudSimple. Para obter instruções, consulte [Obter informações de procura de rede virtual Azure para a ligação CloudSimple](virtual-network-connection.md). Se tiver algum problema em obter a informação, [contacte o suporte.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple Private Cloud: Escalar privilégios para o proprietário da nuvem

O utilizador 'cloudowner' predefinido não tem privilégios suficientes no vCenter private Cloud para instalar veeam, pelo que os privilégios vCenter do utilizador devem ser aumentados. Para mais informações, consulte a [Escalate privileges](escalate-private-cloud-privileges.md).

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Portal Azure: Ligue a sua rede virtual à Nuvem Privada

Ligue a sua rede virtual à Nuvem Privada seguindo as instruções na [Ligação de Rede Virtual Azure utilizando expressRoute](azure-expressroute-connection.md).

### <a name="azure-portal-create-a-backup-repository-vm"></a>Portal Azure: Criar um VM de backup

1. Crie um VM D2 v3 padrão com (2 vCPUs e 8 GB de memória).
2. Selecione a imagem baseada em CentOS 7.4.
3. Configure um grupo de segurança de rede (NSG) para o VM. Verifique se o VM não tem um endereço IP público e não é acessível a partir da internet pública.
4. Crie uma conta de utilizador baseada em nome de utilizador e palavra-passe para o novo VM. Para obter instruções, consulte [Criar uma máquina virtual Linux no portal Azure](../virtual-machines/linux/quick-create-portal.md).
5. Crie 1x512 GiB standard HDD e prenda-o ao VM repositório.  Para obter instruções, consulte [Como anexar um disco de dados gerido a um VM do Windows no portal Azure](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Crie um volume XFS no disco gerido](https://www.digitalocean.com/docs/volumes/how-to/). Inicie sessão no VM utilizando as credenciais anteriormente mencionadas. Execute o seguinte script para criar um volume lógico, adicione o disco a ele, crie uma [partição](https://www.digitalocean.com/docs/volumes/how-to/partition/) do sistema de ficheiros XFS e [monte](https://www.digitalocean.com/docs/volumes/how-to/mount/) a divisória sob o caminho /backup1.

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

7. Exponha/backup1 como um ponto de montagem NFS para o servidor de backup Veeam que está em execução na Nuvem Privada. Para obter instruções, consulte o artigo Oceano Digital [Como Configurar um Monte NFS no CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Utilize este nome de partilha NFS quando configurar o repositório de cópia de segurança no servidor de backup Veeam.

8. Configure as regras de filtragem no NSG para o VM de reprovação de reserva para permitir explicitamente todo o tráfego de rede de e para o VM.

> [!NOTE]
> Veeam Backup & Replication usa o protocolo SSH para comunicar com os repositórios de backup linux e requer o utilitário SCP em repositórios Linux. Verifique se o daemon SSH está devidamente configurado e que o SCP está disponível no hospedeiro Linux.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Configure armazenamento de blob Azure para retenção de dados a longo prazo

1. Crie uma conta de armazenamento de finalidade geral (GPv2) de tipo padrão e um recipiente blob, conforme descrito no vídeo da Microsoft [Getting Started with Azure Storage](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage).
2. Crie um recipiente de armazenamento Azure, conforme descrito na referência [Create Container.](https://docs.microsoft.com/rest/api/storageservices/create-container)
2. Descarregue o utilitário da `azcopy` linha de comando para Linux da Microsoft. Pode utilizar os seguintes comandos na concha de festa em CentOS 7.5.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Utilize `azcopy` o comando para copiar ficheiros de reserva de e para o recipiente blob.  Consulte [os dados de transferência com a AzCopy no Linux](../storage/common/storage-use-azcopy-linux.md) para obter comandos detalhados.

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>consola vCenter de Private Cloud: Instale Veeam B&R

Aceda ao vCenter a partir da sua Cloud Privada para criar uma conta de serviço Veeam, instale Veeam B&R 9.5 e configure veeam usando a conta de serviço.

1. Crie um novo papel chamado "Veeam Backup Role" e atribua-lhe permissões necessárias, conforme recomendado por Veeam. Para mais detalhes consulte o tópico Veeam [Requerperições](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Crie um novo grupo 'Veeam User Group' no vCenter e atribua-lhe a 'Veeam Backup Role'.
3. Crie um novo utilizador da 'Conta de Serviço Veeam' e adicione-o ao 'Grupo de Utilizadores Veeam'.

    ![Criação de uma conta de serviço Veeam](media/veeam-vcenter01.png)

4. Crie um grupo portuário distribuído no vCenter utilizando a rede de backup VLAN. Para mais detalhes, consulte o vídeo VMware [Criando um Grupo portuário distribuído no vSphere Web Client](https://www.youtube.com/watch?v=wpCd5ZbPOpA).
5. Crie os VMs para os servidores de backup veeam e proxy em vCenter de acordo com os requisitos do [sistema Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95). Pode utilizar o Windows 2012 R2 ou Linux. Para mais informações consulte [Requisitos para a utilização de repositórios](https://www.veeam.com/kb2216)de backup linux .
6. Monte o Veeam ISO instalado como um dispositivo CDROM no VM do servidor de backup Veeam.
7. Utilizando uma sessão de RDP na máquina R2 do Windows 2012 (o alvo da instalação Veeam), [instale Veeam B&R 9.5u3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) num VM R2 Do Windows 2012.
8. Encontre o endereço IP interno do VM do servidor de backup Veeam e configure o endereço IP para ser estático no servidor DHCP. Os passos exatos necessários para o fazer dependem do servidor DHCP. Como exemplo, os <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">mapeamentos dHCP estáticos</a> do artigo netgate explicacomo configurar um servidor DHCP usando um router pfSense.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Consola Veeam: Instale software de backup e recuperação veeam

Utilizando a consola Veeam, configure o software de backup e recuperação veeam. Para mais detalhes, consulte [Veeam Backup & Replication v9 - Instalação e Implantação](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. Adicione vSphere VMware como um ambiente de servidor gerido. Quando solicitado, forneça as credenciais da Conta de Serviço Veeam que criou no início do [vCenter Console of Private Cloud: Instale Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br).

    * Utilize as definições predefinidas para o controlo de carga e definições avançadas predefinidas.
    * Detete a localização do servidor de montagem para ser o servidor de reserva.
    * Altere a localização de configuração do servidor Veeam para o repositório remoto.

2. Adicione o servidor Linux em Azure como o repositório de reserva.

    * Utilize as definições predefinidas para o controlo de carga e para as definições avançadas. 
    * Detete a localização do servidor de montagem para ser o servidor de reserva.
    * Altere a localização de configuração do servidor Veeam para o repositório remoto.

3. Ativar a encriptação da cópia de segurança de configuração utilizando **as definições de backup**de configuração do> .

4. Adicione um VM do servidor do Windows como um servidor proxy para ambiente VMware. Utilizando as 'Regras de Tráfego' para um proxy, criptografe os dados de backup através do fio.

5. Configure trabalhos de reserva.
    * Para configurar trabalhos de backup, siga as instruções para [criar um trabalho](https://www.youtube.com/watch?v=YHxcUFEss4M)de backup .
    * Ativar a encriptação de ficheiros de backup em **Definições Avançadas > Armazenamento**.

6. Configure trabalhos de cópia de cópia de reserva.

    * Para configurar trabalhos de cópia de cópia de cópia de cópia de cópia, siga as instruções no vídeo Criando um Trabalho de [Cópia de Cópia de Cópia de Cópia](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)de Cópia de Cópia de Cópia de Cópia de Cópia de Cópia .
    * Ativar a encriptação de ficheiros de backup em **Definições Avançadas > Armazenamento**.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>CloudSimple portal: Configurar o acesso veeam e desescalar privilégios
Crie um endereço IP público para o servidor de backup e recuperação Veeam. Para obter instruções, consulte [Alocar endereços IP públicos](public-ips.md).

Crie uma regra de firewall utilizando para permitir que o servidor de backup Veeam crie uma ligação de saída ao website Veeam para descarregar atualizações/patches na porta TCP 80. Para obter instruções, consulte [Configurar as tabelas e regras](firewall.md)de firewall .

Para desescalar privilégios, ver [desescalar privilégios.](escalate-private-cloud-privileges.md#de-escalate-privileges)

## <a name="references"></a>Referências

### <a name="cloudsimple-references"></a>Referências CloudSimple

* [Criar uma Cloud Privada](create-private-cloud.md)
* [Criar e gerir VLANs/Subnets](create-vlan-subnet.md)
* [vCenter Fontes de identidade](set-vcenter-identity.md)
* [Carga de trabalho DNS e Configuração DHCP](dns-dhcp-setup.md)
* [Escalar privilégios](escalate-privileges.md)
* [Configurar tabelas e regras de firewall](firewall.md)
* [Permissões privadas da Nuvem](learn-private-cloud-permissions.md)
* [Atribuir endereços IP públicos](public-ips.md)

### <a name="veeam-references"></a>Referências Veeam

* [Portas Usadas](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Permissões necessárias](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Requisitos do sistema](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Instalação de Backup Veeam & Replicação](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Módulos e permissões necessários para multi-OS FLR e suporte de repositório para Linux](https://www.veeam.com/kb2216)
* [Recópia de backup Veeam & replicação v9 - Instalação e Implantação - Vídeo](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 Criando um Trabalho de Backup - Vídeo](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 Criando um trabalho de cópia de backup - vídeo](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Referências azure

* [Configure um portal de rede virtual para expressRoute utilizando o portal Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Ligue um VNet a um circuito - subscrição diferente](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Crie uma máquina virtual Linux no portal Azure](../virtual-machines/linux/quick-create-portal.md)
* [Como anexar um disco de dados gerido a um VM do Windows no portal Azure](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Começar com armazenamento azure - vídeo](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Criar recipiente](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [Transferir dados com o AzCopy no Linux](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>Referências VMware

* [Criação de um Grupo Portuário Distribuído no VSphere Web Client - Vídeo](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Outras referências

* [Crie um volume XFS no disco gerido - RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Como configurar um monte NFS no CentOS 7 - HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [Configurar o Servidor DHCP - Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
