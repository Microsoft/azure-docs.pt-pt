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
ms.openlocfilehash: 93caf39216ef0479ec2799267a9ba8181f37f802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84194200"
---
## <a name="add-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>Adicionar endereços IP ao sistema operativo de uma VM

Conecte-se e inscreva-se num VM que criou com vários endereços IP privados. Tem de adicionar manualmente todos os endereços IP privados (incluindo o principal) que adicionou à VM. Complete os passos que se seguem para o seu sistema operativo VM.

### <a name="windows-server"></a>Windows Server
<details>
  <summary>Expandir</summary>

1. A partir de uma linha de comandos, escreva *ipconfig /all*.  Apenas verá o endereço IP privado *Principal* (através de DHCP).
2. Escreva *ncpa.cpl* na linha de comandos para abrir a janela **Ligações de rede**.
3. Abra as propriedades para o adaptador apropriado: **Ethernet**.
4. Faça duplo clique em protocolo IP versão 4 (IPv4).
5. Selecione **Utilizar o seguinte endereço IP** e introduza os seguinte valores:

    * **Endereço IP**: Introduza o endereço IP privado *Principal*
    * **Máscara de sub-rede**: Conjunto baseado na sua sub-rede. Por exemplo, se a sub-rede for uma sub-rede /24, a máscara de sub-rede será 255.255.255.0.
    * **Gateway predefinido**: O primeiro endereço IP na sub-rede. Se a sua sub-rede for 10.0.0.0/24, o endereço IP do gateway será 10.0.0.1.
    * Selecione **Utilize os seguintes endereços do servidor DNS** e introduza os seguintes valores:
        * **Servidor DNS preferencial**: Se não estiver a utilizar o seu próprio servidor DNS, introduza 168.63.129.16.  Se estiver a utilizar o seu próprio servidor DNS, introduza o endereço IP do seu servidor.  (Para o Servidor DNS Alternativo, pode escolher qualquer endereço de servidor DNS público gratuito.)
    * Selecione o botão **Advanced** e adicione endereços IP adicionais. Adicione cada um dos endereços IP privados secundários, que adicionou à interface de rede Azure num passo anterior, à interface de rede do Windows que é atribuída o endereço IP primário atribuído à interface de rede Azure.

        Nunca deve atribuir manualmente o endereço IP público atribuído a uma máquina virtual Azure dentro do sistema operativo da máquina virtual. Quando definir manualmente o endereço IP dentro do sistema operativo, certifique-se de que é o mesmo endereço que o endereço IP privado atribuído à [interface de rede](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure , ou pode perder conectividade com a máquina virtual. Saiba mais sobre as definições [privadas de endereço IP.](../articles/virtual-network/virtual-network-network-interface-addresses.md#private) Nunca deverá atribuir um endereço IP público Azure dentro do sistema operativo.

    * Clique em **OK** para fechar as definições de TCP/IP e, em seguida, novamente em **OK** para fechar as definições do adaptador. A ligação RDP é restabelecida.

6. A partir de uma linha de comandos, escreva *ipconfig /all*. Verifique se todos os endereços IP adicionados são mostrados e o DHCP está desligado.
7. Configure o Windows para utilizar o endereço IP privado da configuração IP primária no Azure como o endereço IP primário para o Windows. Não consulte [o acesso à Internet a partir do Azure Windows VM que tenha vários endereços IP](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse) para mais detalhes. 

### <a name="validation-windows-server"></a>Validação (Windows Server)

Para garantir que é capaz de ligar à internet a partir da sua configuração IP secundária através do IP público que o associou, uma vez adicionado corretamente usando os passos acima, utilize o seguinte comando (substituindo 10.0.0.7 pelo endereço IP secundário e privado):

```bash
ping -S 10.0.0.7 outlook.com
```
>[!NOTE]
>Para configurações ip secundárias, só pode consultar a Internet se a configuração tiver um endereço IP público associado. Para as configurações ip primárias, um endereço IP público não é necessário para ping para a Internet.
</details>

### <a name="linux-ubuntu-1416"></a>Linux (Ubuntu 14/16)
<details>
  <summary>Expandir</summary>
Recomendamos que procure a documentação mais recente para a sua distribuição Linux. 

1. Abra uma janela do terminal.
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

### <a name="validation-ubuntu-1416"></a>Validação (Ubuntu 14/16)

Para se certificar de que pode estabelecer uma ligação à Internet a partir da configuração de IP secundário através do IP público associado, utilize o seguinte comando:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>Para configurações ip secundárias, só pode consultar a Internet se a configuração tiver um endereço IP público associado. Para as configurações ip primárias, um endereço IP público não é necessário para ping para a Internet.

Para VMs de Linux, ao tentar validar a conectividade de saída a partir de um NIC secundário, poderá ter de adicionar rotas adequadas. Existem várias formas de efetuar este procedimento. Consulte a documentação adequada para a distribuição de Linux. O método seguinte é adequado para realizar este procedimento:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Certifique-se de que substitui:
    - **10.0.0.5** pelo endereço IP privado que tem um endereço IP público associado ao mesmo
    - **10.0.0.1** para o gateway predefinido
    - **eth2** para o nome do seu NIC secundário </details>

### <a name="linux-ubuntu-1804"></a>Linux (Ubuntu 18.04+)
<details>
  <summary>Expandir</summary>
Ubuntu 18.04 ou acima mudaram para a `netplan` gestão da rede de OS. Recomendamos que procure a documentação mais recente para a sua distribuição Linux. 

1. Abra uma janela do terminal.
2. Certifique-se de que é o utilizador raiz. Se não for, introduza o seguinte comando:

    ```bash
    sudo -i
    ```

3. Crie um ficheiro para a segunda interface e abra-o num editor de texto:

    ```bash
    vi /etc/netplan/60-static.yaml
    ```

4. Adicione as seguintes linhas ao ficheiro, `10.0.0.6/24` substituindo-as pelo seu IP/netmask:

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

6. Testar as alterações utilizando [o plano net tente](http://manpages.ubuntu.com/manpages/cosmic/man8/netplan-try.8.html) confirmar a sintaxe:

    ```bash
    netplan try
    ```

> [!NOTE]
> `netplan try` aplicará as alterações temporariamente e reverterá as alterações após 120 segundos. Se houver uma perda de conectividade, por favor, aguarde 120 segundos e, em seguida, reconecte-se. Nessa altura, as alterações terão sido revoadas.

7. Assumindo que não há problemas com `netplan try` , aplique as alterações de configuração:

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
### <a name="validation-ubuntu-1804"></a>Validação (Ubuntu 18.04+)

Para se certificar de que pode estabelecer uma ligação à Internet a partir da configuração de IP secundário através do IP público associado, utilize o seguinte comando:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>Para configurações ip secundárias, só pode consultar a Internet se a configuração tiver um endereço IP público associado. Para as configurações ip primárias, um endereço IP público não é necessário para ping para a Internet.

Para VMs de Linux, ao tentar validar a conectividade de saída a partir de um NIC secundário, poderá ter de adicionar rotas adequadas. Existem várias formas de efetuar este procedimento. Consulte a documentação adequada para a distribuição de Linux. O método seguinte é adequado para realizar este procedimento:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Certifique-se de que substitui:
    - **10.0.0.5** pelo endereço IP privado que tem um endereço IP público associado ao mesmo
    - **10.0.0.1** para o gateway predefinido
    - **eth2** para o nome do seu NIC secundário </details>

### <a name="linux-red-hat-centos-and-others"></a>Linux (Chapéu Vermelho, CentOS, entre outros)
<details>
  <summary>Expandir</summary>

1. Abra uma janela do terminal.
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

### <a name="validation-red-hat-centos-and-others"></a>Validação (Chapéu Vermelho, CentOS, entre outros)

Para se certificar de que pode estabelecer uma ligação à Internet a partir da configuração de IP secundário através do IP público associado, utilize o seguinte comando:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>Para configurações ip secundárias, só pode consultar a Internet se a configuração tiver um endereço IP público associado. Para as configurações ip primárias, um endereço IP público não é necessário para ping para a Internet.

Para VMs de Linux, ao tentar validar a conectividade de saída a partir de um NIC secundário, poderá ter de adicionar rotas adequadas. Existem várias formas de efetuar este procedimento. Consulte a documentação adequada para a distribuição de Linux. O método seguinte é adequado para realizar este procedimento:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Certifique-se de que substitui:
    - **10.0.0.5** pelo endereço IP privado que tem um endereço IP público associado ao mesmo
    - **10.0.0.1** para o gateway predefinido
    - **eth2** para o nome do seu NIC secundário </details>