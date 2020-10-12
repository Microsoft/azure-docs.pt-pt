---
title: Quickstart - Criar um VMware VMware Azure numa Nuvem Privada - Solução VMware Azure por CloudSimple
description: Saiba como criar uma máquina virtual VMware na CloudSimple Private Cloud. Aceda ao portal CloudSimple a partir do portal Azure.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 32ac78c6bc613daf04fc98c03044699f387f290d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140858"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Crie máquinas virtuais VMware na sua Nuvem Privada

Para criar máquinas virtuais na sua Nuvem Privada, comece por aceder ao portal CloudSimple a partir do portal Azure.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

1. Selecione **Todos os serviços**.
2. Procure por **Serviços CloudSimple**.
3. Selecione o serviço CloudSimple no qual pretende criar a sua Cloud Privada.
4. A partir da página **'Vista Geral',** clique **em Ir ao portal CloudSimple** para abrir um novo separador de navegador para o portal CloudSimple.  Se solicitado, inscreva-se com o seu sinal de Azure em credenciais.  

    ![Lançar portal CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Lançamento vCenter web-ui

Agora pode lançar o vCenter para configurar máquinas e políticas virtuais.

Para aceder ao vCenter, comece a partir do portal CloudSimple. Na página inicial, em **Tarefas Comuns,** clique em **Launch vSphere Client**.  Selecione a Nuvem Privada e, em seguida, clique em **Lançar vSphere Client** na Nuvem Privada.

   ![Lançamento vSphere Cliente](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Faça upload de um modelo ISO ou vSphere

  > [!WARNING]
  > Para o upload ISO, utilize o cliente vSphere HTML5.  A utilização de um cliente Flash pode resultar num erro.

1. Obtenha o modelo ISO ou vSphere que pretende carregar para o vCenter para criar um VM e tê-lo disponível no seu sistema local.
2. No vCenter, clique no ícone **Disco** e selecione **vsanDatastore**. Clique em **Ficheiros** e, em seguida, clique **em Nova Pasta**.
    ![vCenter ISO](media/vciso00.png)

3. Crie uma pasta intitulada 'ISOs e Modelos'.

4. Navegue na pasta ISOs em ISOs e Modelos e clique em **Upload Files**. Siga as instruções no ecrã para fazer o upload da ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Criar uma máquina virtual em vCenter

1. No vCenter, clique no ícone **Anfitriões e Agrupamentos.**

2. Clique à direita **Na carga de trabalho** e selecione Nova Máquina **Virtual.**
    ![Novo VM](media/vcvm01.png)

3. **Selecione Criar uma nova máquina virtual** e clique em **Seguinte**.
    ![Novo VM](media/vcvm02.png)

4. Nomeie a máquina, selecione a **localização do VM de carga de trabalho** e clique em **Seguinte**.
    ![Novo VM](media/vcvm03.png)

5. Selecione o recurso **compute Workload** e clique em **Seguinte**.
    ![Novo VM](media/vcvm04.png)

6. Selecione **vsanDatastore** e clique em **Seguinte**.
    ![Novo VM](media/vcvm05.png)

7. Mantenha a seleção de compatibilidade padrão ESXi 6.5 e clique em **Seguinte**.
    ![Novo VM](media/vcvm06.png)

8. Selecione o SO convidado do ISO para o VM que está a criar e clique em **Seguinte**.
    ![Novo VM](media/vcvm07.png)

9. Selecione opções de disco rígido e de rede. Para nova unidade de CD/DVD, selecione **o ficheiro ISO da Loja de Dados**.  Se pretender permitir o tráfego do endereço IP público para este VM, selecione a rede como **vm-1**.
    ![Novo VM](media/vcvm08.png)

10. Abre-se uma janela de seleção. Selecione o ficheiro que já fez o upload para a pasta ISOs e Modelos e clique em **OK**.
    ![Novo VM](media/vcvm10.png)

11. Reveja as definições e clique **em OK** para criar o VM.
    ![Novo VM](media/vcvm11.png)

O VM é agora adicionado aos recursos de computação de carga de trabalho e está pronto para ser usado. 
![Novo VM](media/vcvm12.png)

A configuração básica está agora completa. Pode começar a usar a sua Nuvem Privada semelhante à forma como utilizaria a sua infraestrutura VM no local.

As seguintes secções contêm informações opcionais sobre a configuração de servidores DNS e DHCP para cargas de trabalho em Nuvem Privada e modificação da configuração de rede padrão.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Adicione utilizadores e fontes de identidade ao vCenter (Opcional)

O CloudSimple atribui uma conta de utilizador vCenter predefinida com o nome de utilizador `cloudowner@cloudsimple.local` . Não é necessária nenhuma configuração adicional de conta para que possa começar.  A CloudSimple atribui normalmente aos administradores os privilégios de que necessitam para realizar operações normais.  Confiúde o seu diretório ativo no local ou Azure AD como uma [fonte de identidade adicional](set-vcenter-identity.md) na sua Nuvem Privada.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Criar um servidor DNS e DHCP (Opcional)

Aplicações e cargas de trabalho em execução num ambiente private Cloud requerem resolução de nomes e serviços DHCP para procura e atribuição de endereços IP. É necessária uma infraestrutura DHCP e DNS adequada para a prestação destes serviços. Pode configurar uma máquina virtual no vCenter para fornecer estes serviços no seu ambiente Private Cloud.

Pré-requisitos

* Um grupo portuário distribuído com VLAN configurado

* Configuração de rota para instalações ou servidores DNS baseados na Internet

* Modelo de máquina virtual ou ISO para criar uma máquina virtual

As seguintes ligações fornecem orientação sobre a configuração de servidores DHCP e DNS no Linux e Windows.

#### <a name="linux-based-dns-server-setup"></a>Configuração do servidor DNS baseada em Linux

O Linux oferece vários pacotes para a configuração de servidores DNS.  Aqui está um link para instruções para configurar um servidor DE DNS BIND de código aberto.

[Configuração de exemplo](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Configuração baseada no Windows

Estes tópicos da Microsoft descrevem como configurar um servidor Windows como um servidor DNS e como um servidor DHCP.

[Servidor Windows como Servidor DNS](/windows-server/networking/dns/dns-top)

[Servidor Windows como Servidor DHCP](/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Personalizar a configuração de networking (opcional)

As páginas 'Rede' no portal CloudSimple permitem especificar a configuração para tabelas de firewall e endereços IP públicos para VMs.

### <a name="allocate-public-ips"></a>Alocar iPs públicos

1. Navegue para **a Rede > IP público** no portal CloudSimple.
2. Clique **em Alocar IP público**.
3. Insira um nome para identificar a entrada do endereço IP.
4. Mantenha a localização padrão.
5. Utilize o deslizador para alterar o tempo limite de marcha lenta, se desejar.
6. Insira o endereço IP local para o qual pretende atribuir um endereço IP público.
7. Introduza um nome DNS associado, se desejar.
8. Clique em **Done** (Concluído).

    ![IP público](media/quick-create-pc-public-ip.png)

Começa a tarefa de atribuir o endereço IP público. Pode verificar o estado da tarefa na página **'Tarefas >** Atividades'. Quando a atribuição estiver concluída, a nova entrada é mostrada na página de IPs público.

O VM para o qual este endereço IP deve ser mapeado tem de ser configurado com o endereço local acima especificado. O procedimento de configuração de um endereço IP é específico do sistema operativo VM. Consulte a documentação do seu sistema operativo VM para obter o procedimento correto.

#### <a name="example"></a>Exemplo

Por exemplo, aqui estão os detalhes de Ubuntu 16.04.

Adicione o método estático à configuração familiar do endereço inet no ficheiro /etc/rede/interfaces. Altere o endereço, a caixa e os valores do gateway. Para este exemplo, estamos a utilizar a interface eth0, endereço IP interno 192.168.24.10, endereço de gateway 192.168.24.1 e netmask 255.255.255.0. Para o seu ambiente, as informações de sub-redes disponíveis são fornecidas no e-mail de boas-vindas.

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
Volte a ativar manualmente a interface.

```
sudo ifup eth0
```

Por padrão, todo o tráfego que chega da Internet é **negado**. Se quiser abrir qualquer outra porta, crie uma [tabela de firewall](firewall.md).

Depois de configurar um endereço IP interno como o endereço IP estático, verifique se pode chegar à Internet a partir do VM.

```
ping 8.8.8.8
```
Verifique também se pode chegar ao VM a partir da Internet utilizando o endereço IP público.

Certifique-se de que quaisquer regras iptable no VM não estão a bloquear a entrada da porta 80.
        
```
netstat -an | grep 80
```

Inicie um servidor http que escuta na porta 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

ou

```
python3 -m http.server 80
```
Inicie um browser no seu ambiente de trabalho e aponte-o para a porta 80 para o endereço IP público para navegar nos ficheiros do seu VM.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Regras de firewall padrão CloudSimple para IP público

* Tráfego VPN: Todo o tráfego entre (de/para) a VPN e todas as redes de carga de trabalho e rede de gestão são permitidas.
* Tráfego interno em nuvem privada: Todo o tráfego leste-oeste entre redes de carga (de/para) carga de trabalho e a rede de gestão (acima) é permitido.
* Tráfego na Internet:
  * Todo o tráfego que chega da Internet é negado às redes de carga de trabalho e à rede de gestão.
  * É permitido todo o tráfego de saída para a Internet a partir de redes de carga de trabalho ou da rede de gestão.

Também pode modificar a forma como o seu tráfego é protegido, utilizando a função Regras de Firewall. Para obter mais informações, consulte [configurar tabelas e regras de firewall](firewall.md).

## <a name="install-solutions-optional"></a>Instalar soluções (Opcional)

Pode instalar soluções na cloudSimple Private Cloud para tirar o máximo partido do seu ambiente Private Cloud vCenter. Pode configurar backup, recuperação de desastres, replicação e outras funções para proteger as suas máquinas virtuais. Exemplos incluem VMware Site Recovery Manager (VMware SRM) e Veeam Backup & Replication.

Para instalar uma solução, deve solicitar privilégios adicionais por um período limitado. Ver [privilégios escalate.](escalate-private-cloud-privileges.md)

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Ligue-se à rede de instalações utilizando a Azure ExpressRoute](on-premises-connection.md)
* [Configurar gateways VPN na rede CloudSimple](vpn-gateway.md)
