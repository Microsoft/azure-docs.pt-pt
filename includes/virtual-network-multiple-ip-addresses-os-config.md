---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 1febadbbf7821988600d6feddc94fce25d15e989
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
## <a name="os-config"></a>Adicionar endereços IP ao sistema operativo de uma VM

Ligue-se e inicie sessão numa VM criada por si com múltiplos endereços IP privados. Tem de adicionar manualmente todos os endereços IP privados (incluindo o principal) que adicionou à VM. Conclua os passos que seguem para o seu sistema de operativo VM.

### <a name="windows"></a>Windows

1. A partir de uma linha de comandos, escreva *ipconfig /all*.  Apenas verá o endereço IP privado *Principal* (através de DHCP).
2. Escreva *ncpa.cpl* na linha de comandos para abrir a janela **Ligações de rede**.
3. Abra as propriedades do adaptador adequado: **Ligação de Área Local**.
4. Faça duplo clique em protocolo IP versão 4 (IPv4).
5. Selecione **Utilizar o seguinte endereço IP** e introduza os seguinte valores:

    * **Endereço IP**: Introduza o endereço IP privado *Principal*
    * **Máscara de sub-rede**: Conjunto baseado na sua sub-rede. Por exemplo, se a sub-rede for uma sub-rede /24, a máscara de sub-rede será 255.255.255.0.
    * **Gateway predefinido**: O primeiro endereço IP na sub-rede. Se a sua sub-rede for 10.0.0.0/24, o endereço IP do gateway será 10.0.0.1.
    * Selecione **utilize os seguintes endereços de servidor DNS** e introduza os seguintes valores:
        * **Servidor DNS preferencial**: Se não estiver a utilizar o seu próprio servidor DNS, introduza 168.63.129.16.  Se estiver a utilizar o seu próprio servidor DNS, introduza o endereço IP do seu servidor.
    * Selecione o **avançadas** botão e adicionar endereços IP adicionais. Adicione cada os secundários endereços IP privados, que adicionou à interface de rede do Azure num passo anterior, a interface de rede do Windows que é atribuída o endereço IP principal atribuído à interface de rede do Azure.

        Nunca manualmente deve atribuir o endereço IP público atribuído a uma máquina virtual do Azure dentro do sistema operativo da máquina virtual. Ao definir o endereço IP no sistema operativo manualmente, certifique-se de que é o mesmo endereço como o endereço IP privado atribuído para o Azure [interface de rede](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings), ou pode perder a conectividade à máquina virtual. Saiba mais sobre [endereço IP privado](../articles/virtual-network/virtual-network-network-interface-addresses.md#private) definições. Nunca deve atribuir um endereço IP público do Azure no sistema operativo.

    * Clique em **OK** para fechar as definições de TCP/IP e, em seguida, novamente em **OK** para fechar as definições do adaptador. A ligação RDP é restabelecida.

6. A partir de uma linha de comandos, escreva *ipconfig /all*. Todos os endereços IP adicionados por si são apresentados e o DHCP é desativado.
7. Configure janelas de utilizar o endereço IP privado o principal da configuração de IP no Azure como o endereço IP primário para o Windows. Consulte [acesso não Internet de VM do Windows Azure que tenha vários endereços IP](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse) para obter mais detalhes. 

### <a name="validation-windows"></a>Validação (Windows)

Para se certificar de que pode estabelecer uma ligação à Internet a partir da configuração de IP secundário através do IP público associado, assim que o tiver adicionado corretamente com os passos acima, utilize o seguinte comando:

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>Para configurações de IP secundárias, pode apenas enviar ping à Internet se a configuração tem um endereço IP público com associados. Para configurações de IP primárias, um endereço IP público não é necessário para enviar um ping à Internet.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

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

### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS e outros)

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
>Para configurações de IP secundárias, pode apenas enviar ping à Internet se a configuração tem um endereço IP público com associados. Para configurações de IP primárias, um endereço IP público não é necessário para enviar um ping à Internet.

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
