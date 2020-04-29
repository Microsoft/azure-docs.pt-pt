---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 05/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: a9473f69d600a86ff71da69c7efe0dea3f2b0a08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76159505"
---
## <a name="add-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>Adicionar endereços IP ao sistema operativo de uma VM

Conecte-se e inscreva-se num VM que criou com vários endereços IP privados. Tem de adicionar manualmente todos os endereços IP privados (incluindo o principal) que adicionou à VM. Complete os passos que se seguem para o seu sistema operativo VM.

### <a name="windows"></a>Windows

1. A partir de uma linha de comandos, escreva *ipconfig /all*.  Apenas verá o endereço IP privado *Principal* (através de DHCP).
2. Escreva *ncpa.cpl* na linha de comandos para abrir a janela **Ligações de rede**.
3. Abra as propriedades do adaptador adequado: **Ligação de Área Local**.
4. Faça duplo clique em protocolo IP versão 4 (IPv4).
5. Selecione **Utilizar o seguinte endereço IP** e introduza os seguinte valores:

    * **Endereço IP**: Introduza o endereço IP privado *Principal*
    * **Máscara de sub-rede**: Conjunto baseado na sua sub-rede. Por exemplo, se a sub-rede for uma sub-rede /24, a máscara de sub-rede será 255.255.255.0.
    * **Gateway predefinido**: O primeiro endereço IP na sub-rede. Se a sua sub-rede for 10.0.0.0/24, o endereço IP do gateway será 10.0.0.1.
    * Selecione **Utilize os seguintes endereços do servidor DNS** e introduza os seguintes valores:
        * **Servidor DNS preferencial**: Se não estiver a utilizar o seu próprio servidor DNS, introduza 168.63.129.16.  Se estiver a utilizar o seu próprio servidor DNS, introduza o endereço IP do seu servidor.
    * Selecione o botão **Avançado** e adicione endereços IP adicionais. Adicione cada um dos endereços IP privados secundários, que adicionou à interface de rede Azure num passo anterior, à interface de rede Windows que é atribuída ao endereço IP primário atribuído à interface de rede Azure.

        Nunca deve atribuir manualmente o endereço IP público atribuído a uma máquina virtual Azure dentro do sistema operativo da máquina virtual. Quando definir manualmente o endereço IP dentro do sistema operativo, certifique-se de que é o mesmo endereço que o endereço IP privado atribuído à interface de [rede](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure, ou pode perder a conectividade com a máquina virtual. Saiba mais sobre as definições privadas de [endereço IP.](../articles/virtual-network/virtual-network-network-interface-addresses.md#private) Nunca deve atribuir um endereço IP público Azure dentro do sistema operativo.

    * Clique em **OK** para fechar as definições de TCP/IP e, em seguida, novamente em **OK** para fechar as definições do adaptador. A ligação RDP é restabelecida.

6. A partir de uma linha de comandos, escreva *ipconfig /all*. Todos os endereços IP adicionados por si são apresentados e o DHCP é desativado.
7. Configure o Windows para utilizar o endereço IP privado da configuração IP primária em Azure como o principal endereço IP para Windows. Não consulte [o Acesso à Internet a partir do Azure Windows VM que tenha vários endereços IP](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse) para mais detalhes. 

### <a name="validation-windows"></a>Validação (Windows)

Para se certificar de que pode estabelecer uma ligação à Internet a partir da configuração de IP secundário através do IP público associado, assim que o tiver adicionado corretamente com os passos acima, utilize o seguinte comando:

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>Para configurações ip secundárias, só pode fazer ping na Internet se a configuração tiver um endereço IP público associado ao mesmo. Para configurações primárias de IP, não é necessário um endereço IP público para ping para a Internet.

### <a name="linux-ubuntu-1416"></a>Linux (Ubuntu 14/16)

Recomendamos que veja a documentação mais recente para a sua distribuição Linux. 

1. Abra uma janela de terminal.
2. Certifique-se de que é o utilizador raiz. Se não for, introduza o seguinte comando:

   ```bash
   sudo -i
   ```

3. Atualize o ficheiro de configuração da interface de rede (assumindo “eth0”).

   * Mantenha o item de linha existente para dhcp. O endereço IP principal continua configurado como estava anteriormente.
   * Adicione uma configuração para um endereço IP estático adicional com os seguintes comandos:

     ```bash
     cd /etc/network/interfaces.d/
     ls
     ```

     Deverá ver um ficheiro .cfg.
4. Abra o ficheiro. Deverá ver as linhas seguintes no final do ficheiro:

   ```bash
   auto eth0
   iface eth0 inet dhcp
   ```

5. Adicione as seguintes linhas a seguir às linhas existentes neste ficheiro:

   ```bash
   iface eth0 inet static
   address <your private IP address here>
   netmask <your subnet mask>
   ```

6. Guarde o ficheiro com o comando seguinte:

   ```bash
   :wq
   ```

7. Reponha a interface de rede com o seguinte comando:

   ```bash
   sudo ifdown eth0 && sudo ifup eth0
   ```

   > [!IMPORTANT]
   > Execute ifdown e ifup na mesma linha se estiver a utilizar uma ligação remota.
   >

8. Certifique-se de que o endereço IP é adicionado à interface de rede com o seguinte comando:

   ```bash
   ip addr list eth0
   ```

   Deverá ver o endereço IP que adicionou como parte da lista.

### <a name="linux-ubuntu-1804"></a>Linux (Ubuntu 18.04+)

Ubuntu 18.04 ou acima `netplan` mudaram para gestão da rede osso. Recomendamos que veja a documentação mais recente para a sua distribuição Linux. 

1. Abra uma janela de terminal.
2. Certifique-se de que é o utilizador raiz. Se não for, introduza o seguinte comando:

    ```bash
    sudo -i
    ```

3. Crie um ficheiro para a segunda interface e abra-o num editor de texto:

    ```bash
    vi /etc/netplan/60-static.yaml
    ```

4. Adicione as seguintes linhas ao `10.0.0.6/24` ficheiro, substituindo pelo seu IP/netmask:

    ```bash
    network:
        version: 2
        ethernets:
            eth0:
                addresses:
                    - 10.0.0.6/24
    ```

5. Guarde o ficheiro com o comando seguinte:

    ```bash
    :wq
    ```

6. Teste as alterações utilizando [o netplan tente](http://manpages.ubuntu.com/manpages/cosmic/man8/netplan-try.8.html) confirmar a sintaxe:

    ```bash
    netplan try
    ```

> [!NOTE]
> `netplan try`aplicará as alterações temporariamente e relançará as alterações após 120 segundos. Se houver uma perda de conectividade, por favor, espere 120 segundos e, em seguida, reconecte-se. Nessa altura, as mudanças terão sido revertidas.

7. Assumindo que `netplan try`não há problemas com, aplique as alterações de configuração:

    ```bash
    netplan apply
    ```

8. Certifique-se de que o endereço IP é adicionado à interface de rede com o seguinte comando:

    ```bash
    ip addr list eth0
    ```

    Deverá ver o endereço IP que adicionou como parte da lista. Exemplo:

    ```bash
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 00:0d:3a:8c:14:a5 brd ff:ff:ff:ff:ff:ff
        inet 10.0.0.6/24 brd 10.0.0.255 scope global eth0
        valid_lft forever preferred_lft forever
        inet 10.0.0.4/24 brd 10.0.0.255 scope global secondary eth0
        valid_lft forever preferred_lft forever
        inet6 fe80::20d:3aff:fe8c:14a5/64 scope link
        valid_lft forever preferred_lft forever
    ```
    
### <a name="linux-red-hat-centos-and-others"></a>Linux (Chapéu Vermelho, CentOS, entre outros)

1. Abra uma janela de terminal.
2. Certifique-se de que é o utilizador raiz. Se não for, introduza o seguinte comando:

    ```bash
    sudo -i
    ```

3. Introduza a sua palavra-passe e siga as instruções, conforme solicitado. Assim que se tornar no utilizador raiz, navegue até à pasta de scripts de rede com o seguinte comando:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Liste os ficheiros ifcfg relacionados com o seguinte comando:

    ```bash
    ls ifcfg-*
    ```

    Deverá ver *ifcfg-eth0* como um dos ficheiros.

5. Para adicionar um endereço IP, crie um ficheiro de configuração para o mesmo como apresentado abaixo. Tenha em atenção que tem de criar um ficheiro para cada configuração de IP.

    ```bash
    touch ifcfg-eth0:0
    ```

6. Abra o ficheiro *ifcfg-eth0:0* com o seguinte comando:

    ```bash
    vi ifcfg-eth0:0
    ```

7. Adicione conteúdo ao ficheiro, que é *eth0:0* neste caso, com o seguinte comando. Certifique-se de que atualiza as informações com base no seu endereço IP.

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. Guarde o ficheiro com o comando seguinte:

    ```bash
    :wq
    ```

9. Reinicie os serviços de rede e certifique-se de que as alterações são realizadas com êxito ao executar os seguintes comandos:

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    Deverá ver o endereço IP que adicionou, *eth0:0*, na lista devolvida.

### <a name="validation-linux"></a>Validação (Linux)

Para se certificar de que pode estabelecer uma ligação à Internet a partir da configuração de IP secundário através do IP público associado, utilize o seguinte comando:

```bash
ping -I 10.0.0.5 hotmail.com
```
>[!NOTE]
>Para configurações ip secundárias, só pode fazer ping na Internet se a configuração tiver um endereço IP público associado ao mesmo. Para configurações primárias de IP, não é necessário um endereço IP público para ping para a Internet.

Para VMs de Linux, ao tentar validar a conectividade de saída a partir de um NIC secundário, poderá ter de adicionar rotas adequadas. Existem várias formas de efetuar este procedimento. Consulte a documentação adequada para a distribuição de Linux. O método seguinte é adequado para realizar este procedimento:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Certifique-se de que substitui:
    - **10.0.0.5** pelo endereço IP privado que tem um endereço IP público associado ao mesmo
    - **10.0.0.1** para o gateway predefinido
    - **eth2** para o nome do seu NIC secundário
