---
title: Início rápido - criar uma VM do VMware numa nuvem privada
description: Descreve como criar e uma VM do VMware na nuvem privada CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 217154578ad11755cd658ff3b106cfbe266277ab
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154618"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Criar máquinas virtuais VMware em sua nuvem privada

Para criar máquinas virtuais na sua nuvem privada, comece por aceder ao portal do CloudSimple do portal do Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

1. Selecione **Todos os serviços**.
2. Procure **CloudSimple serviços**.
3. Selecione o serviço de CloudSimple no qual pretende criar a sua nuvem privada.
4. Do **descrição geral** página, clique em **vá para o portal de CloudSimple** para abrir um novo separador do browser para o portal de CloudSimple.  Se lhe for pedido, inicie sessão com o Azure inscrever-se nas credenciais.  

    ![Iniciar CloudSimple portal](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Iniciar a IU da web do vCenter

Agora, pode iniciar o vCenter para configurar máquinas virtuais e as políticas.

Para aceder ao vCenter, inicie a partir do portal do CloudSimple. Na Home page, sob **tarefas comuns**, clique em **iniciar vSphere Client**.  Selecione a nuvem privada e, em seguida, clique em **inicie vSphere Client** sobre a nuvem privada.

   ![Inicie o vSphere Client](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Carregar um modelo ISO ou vSphere

> [!WARNING]
> Para carregamentos ISO, utilize o cliente de vSphere HTML5.  Utilizar o cliente Flash pode resultar num erro.

1. Obter o modelo ISO ou vSphere que pretende carregar para o vCenter para criar uma máquina virtual e o tiver disponível no sistema local.

2. No vCenter, clique nas **disco** ícone e selecione **vsanDatastore**. Clique em **arquivos** e, em seguida, clique em **nova pasta**.

    ![vCenter ISO](media/vcenter-create-folder.png)

3. Crie uma pasta para armazenar arquivos ISO.

4. Navegue para a nova pasta que criou e clique em **carregar ficheiros**. Siga na tela instruções para carregar o ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Criar uma Máquina Virtual no vCenter

1. No vCenter, clique nas **anfitriões e Clusters** ícone.

2. Com o botão direito **carga de trabalho** e selecione **Nova Máquina Virtual**.
    
    ![Criar a máquina virtual](media/create-vcenter-virtual-machine-01.png)

3. Selecione **criar nova máquina virtual** e clique em **próxima**.

    ![Assistente de nova máquina virtual](media/create-vcenter-virtual-machine-02.png)

4. Nome da máquina, selecione o **da carga de trabalho de VM** e, clique em **próxima**.

    ![Selecione o nome e a pasta](media/create-vcenter-virtual-machine-03.png)

5. Selecione o **carga de trabalho** recurso de computação e clique em **próxima**.

    ![Selecione o recurso de computação](media/create-vcenter-virtual-machine-04.png)

6. Selecione **vsanDatastore** e clique em **próxima**.

    ![Selecionar armazenamento](media/create-vcenter-virtual-machine-05.png)

7. Mantenha a seleção de compatibilidade do ESXi 6.5 predefinida e clique em **seguinte**.

    ![Selecione a compatibilidade](media/create-vcenter-virtual-machine-06.png)

8. Selecione o SO convidado do ISO para a máquina virtual e clique em **seguinte**.

    ![Personalizar o SO convidado](media/create-vcenter-virtual-machine-07.png)

9. Selecione o disco rígido e opções de rede. Para a unidade de CD/DVD novo, selecione **arquivo ISO de arquivo de dados**.  Se pretender permitir o tráfego do endereço IP público para esta máquina virtual, selecione a rede como **1 da vm**.

    ![Selecione a personalização de hardware](media/create-vcenter-virtual-machine-08.png)

10. É aberta uma janela de seleção. Selecione o ficheiro carregado anteriormente para a pasta de modelos e ISOs e clique em **OK**.

    ![Selecione a ISO](media/create-vcenter-virtual-machine-10.png)

11. Reveja as definições e clique em **OK** para criar a máquina virtual.

    ![Opções de revisão](media/create-vcenter-virtual-machine-11.png)

A máquina virtual foi adicionada aos recursos de computação de carga de trabalho e está pronta a utilizar. 

![Nova máquina virtual no vCenter](media/create-vcenter-virtual-machine-12.png)

A configuração básica está agora concluída. Pode começar a utilizar a sua nuvem privada semelhante à forma como usaria a infraestrutura de máquina virtual no local.

Secções seguintes contêm informações opcionais sobre como configurar DNS e DHCP servidores para a nuvem privada cargas de trabalho e modificar a configuração de rede padrão.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Adicionar utilizadores e origens de identidade do vCenter (opcional)

CloudSimple atribui uma conta de usuário padrão vCenter com o nome de utilizador **cloudowner@cloudsimple.local** . Nenhuma configuração de conta adicional é necessária para começar.  CloudSimple normalmente atribui os administradores de privilégios necessários para efetuar as operações normais.  Configurar o active directory no local ou o Azure AD como um [origem de identidades adicionais](https://docs.azure.cloudsimple.com/set-vcenter-identity/) na sua nuvem privada.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Criar um servidor DHCP e DNS (opcional)

Aplicações e cargas de trabalho em execução num ambiente de nuvem privada requerem resolução de nomes e os serviços do DHCP para pesquisa e a atribuição de endereços IP. Uma infra-estrutura DHCP e DNS adequada é necessário para fornecer esses serviços. Pode configurar uma máquina virtual no vCenter para fornecer estes serviços no seu ambiente de nuvem privada.

### <a name="prerequisites"></a>Pré-requisitos

* Um grupo de portas distribuído com VLAN configurado

* Rota configurar para servidores DNS baseado na Internet ou no local

* Modelo de máquina virtual ou ISO para criar uma máquina virtual

As ligações seguintes fornecem orientações sobre como configurar servidores DHCP e DNS no Linux e Windows.

### <a name="linux-based-dns-server-setup"></a>Configuração do servidor DNS baseado no Linux

Linux oferece vários pacotes para configurar servidores DNS.  Aqui está uma ligação para instruções sobre como configurar um servidor de BIND DNS de código-fonte aberto.

[Exemplo de configuração](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Instalação baseada em Windows

Estes artigos de Microsoft descrevem como configurar um servidor Windows como um servidor DNS e como um servidor DHCP.
<br>
[Windows Server como servidor DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server como servidor DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Personalizar a configuração de rede (opcional)

As páginas de rede no portal do CloudSimple permitem-lhe especificar a configuração para tabelas de firewall e endereços IP públicos para máquinas virtuais.

### <a name="allocate-public-ips"></a>Atribuir IPs públicos

1. Navegue para **rede > IP público** no portal do CloudSimple.
2. Clique em **alocar o IP público**.
3. Introduza um nome para identificar a entrada de endereço IP.
4. Selecione a localização da sua nuvem privada.
5. Utilize o controlo de deslize para alterar o tempo limite de inatividade, se assim o desejar.
6. Introduza o endereço IP local para o qual pretende atribuir um endereço IP público.
7. Introduza um nome DNS associado, se assim o desejar.
8. Clique em **Concluído**.

    ![IP público](media/quick-create-pc-public-ip.png)

Começa a tarefa de alocar o endereço IP público. Pode verificar o estado da tarefa a **atividade > tarefas** página. Quando a alocação estiver concluída, a nova entrada é apresentada na página de IPs públicos.

A máquina virtual ao qual este endereço IP tem de ser mapeado tem de ser configurado com o endereço local especificado acima. O procedimento para configurar um endereço IP é específico para o sistema de operativo da máquina virtual. Consulte a documentação do seu sistema de operativo da máquina virtual para o procedimento correto.

#### <a name="example"></a>Exemplo

Por exemplo, aqui estão os detalhes para o Ubuntu 16.04.

Adicione o método estático à configuração de família de endereços da inet no ficheiro ```/etc/network/interfaces```. Altere os valores de endereço, a máscara de rede e o gateway. Neste exemplo, estamos a utilizar a eth0 interface, endereço IP interno 192.168.24.10, endereço de gateway 192.168.24.1 e máscara de rede 255.255.255.0. 

Editar o ```interfaces``` ficheiro.

```
sudo vi /etc/network/interfaces
```

Atualize a seguinte secção em ```interfaces``` ficheiro.

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

Desative a interface.

```
sudo ifdown eth0
```

Ative novamente a interface.

```
sudo ifup eth0
```

Por predefinição, é de todo o tráfego de entrada da Internet **negado**. Se gostaria de abrir qualquer outra porta, crie uma [tabela de firewall](https://docs.azure.cloudsimple.com/firewall/).

Depois de configurar um endereço IP interno como o endereço IP estático, certifique-se de que pode entrar na Internet de dentro da máquina virtual.

```
ping 8.8.8.8
```

Certifique-se de que pode aceder a máquina virtual a partir da Internet com o endereço IP público.

Certifique-se de que quaisquer regras de firewall (iptable) na máquina virtual não estão a bloquear a porta 80 entrada.

```
netstat -an | grep 80
```

Inicie um servidor de http que escuta na porta 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

ou

```
python3 -m http.server 80
```

Iniciar um browser no seu ambiente de trabalho e apontá-lo para a porta 80 para o endereço IP público procurar os ficheiros na sua máquina virtual. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Regras de firewall de CloudSimple predefinidas para IP público

* Tráfego VPN: Todo o tráfego entre (de/para) da VPN e todas as redes de carga de trabalho e rede de gestão é permitido.
* Tráfego interno de nuvem privada: Todo este-oeste o tráfego entre (de/para) redes de carga de trabalho e a rede de gestão (mostrado acima) é permitido.
* Tráfego da Internet:
    * Todo o tráfego de entrada da Internet é negado a redes de carga de trabalho e a rede de gestão.
    * Todo o tráfego de saída à Internet de redes de carga de trabalho ou a rede de gestão é permitido.

Também pode modificar a forma como o tráfego está protegido, usando o recurso de regras de Firewall. Para obter mais informações, consulte [configurar tabelas de firewall e regras](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Instalar soluções (opcionais)
Pode instalar soluções na sua nuvem privada CloudSimple para aproveitar ao máximo sua nuvem privada ambiente do vCenter. Pode configurar cópia de segurança, recuperação após desastre, replicação e outras funções para proteger as suas máquinas virtuais. Os exemplos incluem o Gestor de recuperação de Site de VMware (VMware SRM) e de cópia de segurança do Veeam & de replicação.

Para instalar uma solução, tem de pedir privilégios adicionais durante um período limitado. Ver [aumentar os privilégios](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges).

## <a name="next-steps"></a>Passos Seguintes

* [Consumir máquinas de virtuais de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Ligar à rede no local utilizam o Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [Configurar gateways de VPN na rede de CloudSimple](https://docs.azure.cloudsimple.com/vpn-gateway)
