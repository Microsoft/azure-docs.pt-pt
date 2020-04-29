---
title: Quickstart - Crie um VM VM Azure numa nuvem privada - Solução Azure VMware by CloudSimple
description: Descreve como criar um VMware Azure numa nuvem privada CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ac818cfd267b781366c0e32c9f93cc885dff99c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77566153"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Crie máquinas virtuais VMware na sua Nuvem Privada

Para criar máquinas virtuais na sua Nuvem Privada, comece por aceder ao portal CloudSimple a partir do portal Azure.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

1. Selecione **Todos os serviços**.
2. Pesquisa por **Serviços CloudSimple**.
3. Selecione o serviço CloudSimple no qual pretende criar a sua Cloud Privada.
4. A partir da página **'Overview',** clique em **ir ao portal CloudSimple** para abrir um novo separador de navegador para o portal CloudSimple.  Se solicitado, inscreva-se com o seu sinal Azure em credenciais.  

    ![Lançar portal CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Lançar vCenter web-ui

Agora pode lançar o vCenter para configurar máquinas e políticas virtuais.

Para aceder ao vCenter, comece pelo portal CloudSimple. Na página Inicial, em **tarefas comuns,** clique em **Lançar vSphere Client**.  Selecione a Nuvem Privada e, em seguida, clique em **Lançar vSphere Client** na Nuvem Privada.

   ![Lançar cliente vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Faça upload de um modelo ISO ou vSphere

  > [!WARNING]
  > Para o upload ISO, utilize o cliente vSphere HTML5.  A utilização de um cliente Flash pode resultar num erro.

1. Obtenha o modelo ISO ou vSphere que pretende enviar para vCenter para criar um VM e tê-lo disponível no seu sistema local.
2. No vCenter, clique no ícone **Disk** e selecione **vsanDatastore**. Clique em **Ficheiros** e, em seguida, clique em **Nova Pasta**.
    ![vCenter ISO](media/vciso00.png)

3. Crie uma pasta intitulada "ISOs and Templates".

4. Navegue para a pasta ISOs em ISOs e Modelos e clique em **Upload Files**. Siga as instruções no ecrã para fazer o upload da ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Criar uma Máquina Virtual no vCenter

1. No vCenter, clique no ícone **Anfitriões e Clusters.**

2. Clique na carga de **trabalho** do clique à direita e selecione **Nova Máquina Virtual**.
    ![Novo VM](media/vcvm01.png)

3. Selecione **Criar uma nova máquina virtual** e clique em **Next**.
    ![Novo VM](media/vcvm02.png)

4. Nomeie a máquina, selecione a localização **do VM de carga de trabalho** e clique em **Next**.
    ![Novo VM](media/vcvm03.png)

5. Selecione o recurso computacional **workload** e clique **em Seguinte**.
    ![Novo VM](media/vcvm04.png)

6. Selecione **vsanDatastore** e clique **em Seguinte**.
    ![Novo VM](media/vcvm05.png)

7. Mantenha a seleção de compatibilidade ESXi 6.5 padrão e clique **em Seguinte**.
    ![Novo VM](media/vcvm06.png)

8. Selecione o oss o de hóspedes do ISO para o VM que está a criar e clique **em Next**.
    ![Novo VM](media/vcvm07.png)

9. Selecione o disco rígido e as opções de rede. Para uma nova unidade de CD/DVD, selecione **ficheiro ISO**da Datastore .  Se pretender permitir o tráfego do endereço IP público para este VM, selecione a rede como **vm-1**.
    ![Novo VM](media/vcvm08.png)

10. Abre-se uma janela de seleção. Selecione o ficheiro que já fez o upload para a pasta ISOs e Templates e clique em **OK**.
    ![Novo VM](media/vcvm10.png)

11. Reveja as definições e clique **em OK** para criar o VM.
    ![Novo VM](media/vcvm11.png)

O VM é agora adicionado aos recursos computacionais da Carga de Trabalho e está pronto para ser utilizado. 
![Novo VM](media/vcvm12.png)

A configuração básica está agora completa. Pode começar a usar a sua Nuvem Privada semelhante à forma como utilizaria a sua infraestrutura VM no local.

As seguintes secções contêm informações opcionais sobre a configuração de servidores DNS e DHCP para cargas de trabalho em Nuvem Privada e modificando a configuração de rede predefinida.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Adicionar Utilizadores e fontes de identidade ao vCenter (Opcional)

O CloudSimple atribui uma conta de `cloudowner@cloudsimple.local`utilizador vCenter predefinida com nome de utilizador . Não é necessária nenhuma configuração adicional da conta para que possa começar.  A CloudSimple atribui normalmente aos administradores os privilégios de que necessitam para realizar operações normais.  Instale o seu diretório ativo no local ou azure ad como [uma fonte de identidade adicional](set-vcenter-identity.md) na sua Nuvem Privada.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Criar um servidor DNS e DHCP (Opcional)

Aplicações e cargas de trabalho em funcionamento num ambiente privado de nuvem requerem resolução de nomes e serviços DHCP para procura e atribuição de endereços IP. É necessária uma infraestrutura dHCP e DNS adequada para prestar estes serviços. Pode configurar uma máquina virtual no vCenter para fornecer estes serviços no seu ambiente Cloud Privado.

Pré-requisitos

* Um grupo portuário distribuído com VLAN configurado

* Configuração de rota para servidores DNS baseados na Internet ou na Internet

* Modelo de máquina virtual ou ISO para criar uma máquina virtual

Os seguintes links fornecem orientações sobre a configuração de servidores DHCP e DNS no Linux e Windows.

#### <a name="linux-based-dns-server-setup"></a>Configuração do servidor DNS baseado em Linux

O Linux oferece vários pacotes para a configuração de servidores DNS.  Aqui está um link para instruções para a configuração de um servidor BIND DNS de código aberto.

[Configuração de exemplo](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Configuração baseada no Windows

Estes tópicos da Microsoft descrevem como configurar um servidor Windows como um servidor DNS e como um servidor DHCP.

[Servidor do Windows como Servidor DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Servidor do Windows como Servidor DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Personalizar configuração de rede (Opcional)

As páginas da Rede no portal CloudSimple permitem especificar a configuração para tabelas de firewall e endereços IP públicos para VMs.

### <a name="allocate-public-ips"></a>Atribuição de IPs públicos

1. Navegue para **Rede > IP Público** no portal CloudSimple.
2. Clique em **Alocar IP Público**.
3. Insira um nome para identificar a entrada do endereço IP.
4. Mantenha a localização padrão.
5. Utilize o slider para alterar o tempo inativo, se desejar.
6. Insira o endereço IP local para o qual pretende atribuir um endereço IP público.
7. Introduza um nome DNS associado, se desejar.
8. Clique em **Concluído**.

    ![IP público](media/quick-create-pc-public-ip.png)

Inicia-se a tarefa de atribuição do endereço IP público. Pode verificar o estado da tarefa na página **Tarefas > Atividade.** Quando a atribuição estiver concluída, a nova entrada é mostrada na página de IPs públicos.

O VM a que este endereço IP deve ser mapeado tem de ser configurado com o endereço local acima especificado. O procedimento para configurar um endereço IP é específico do sistema operativo VM. Consulte a documentação do seu sistema operativo VM para o procedimento correto.

#### <a name="example"></a>Exemplo

Por exemplo, aqui estão os detalhes para Ubuntu 16.04.

Adicione o método estático à configuração familiar inet address no ficheiro /etc/rede/interfaces. Altere os valores de endereço, máscara de rede e gateway. Para este exemplo, estamos a utilizar a interface eth0, o endereço IP interno 192.168.24.10, o endereço de entrada 192.168.24.1 e o netmask 255.255.255.0. Para o seu ambiente, as informações de sub-rede disponíveis são fornecidas no e-mail de boas-vindas.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Desative manualmente a interface.

```
sudo ifdown eth0
```
Ative manualmente a interface novamente.

```
sudo ifup eth0
```

Por defeito, todo o tráfego de entrada da Internet é **negado.** Se quiser abrir qualquer outra porta, crie uma mesa de [firewall.](firewall.md)

Depois de configurar um endereço IP interno como endereço IP estático, verifique se pode chegar à Internet a partir do VM.

```
ping 8.8.8.8
```
Verifique também se pode chegar ao VM a partir da Internet utilizando o endereço IP público.

Certifique-se de que quaisquer regras iptable sobre o VM não estão bloqueando a entrada da porta 80.
        
```
netstat -an | grep 80
```

Inicie um servidor http que ouça na porta 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

ou

```
python3 -m http.server 80
```
Inicie um navegador no seu ambiente de trabalho e aponte-o para a porta 80 para o endereço IP público para navegar nos ficheiros no seu VM.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Regras de firewall Default CloudSimple para IP público

* Tráfego VPN: Todo o tráfego entre (de/para) a VPN e todas as redes de carga de trabalho e rede de gestão é permitido.
* Tráfego interno em nuvem privada: É permitido todo o tráfego este-oeste entre (de/para) redes de carga de trabalho e a rede de gestão (acima) é permitido.
* Tráfego na Internet:
  * Todo o tráfego de entrada da Internet é negado às redes de carga de trabalho e à rede de gestão.
  * É permitido todo o tráfego de saída para a Internet a partir de redes de carga de trabalho ou da rede de gestão.

Também pode modificar a forma como o seu tráfego está seguro, utilizando a funcionalidade Firewall Rules. Para mais informações, consulte [Configurar tabelas e regras](firewall.md)de firewall .

## <a name="install-solutions-optional"></a>Instalar soluções (Opcional)

Pode instalar soluções na sua CloudSimple Private Cloud para tirar o máximo partido do ambiente vCenter da Cloud Privada. Pode configurar backup, recuperação de desastres, replicação e outras funções para proteger as suas máquinas virtuais. Exemplos incluem VMware Site Recovery Manager (VMware SRM) e Veeam Backup & Replication.

Para instalar uma solução, deve solicitar privilégios adicionais por um período limitado. Ver [privilégios Escalate.](escalate-private-cloud-privileges.md)

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Ligue-se à rede no local utilizando o Azure ExpressRoute](on-premises-connection.md)
* [Configurar gateways VPN na rede CloudSimple](vpn-gateway.md)
