---
title: Quickstart - Criar uma zona de DNS privada Azure utilizando o portal Azure
description: Neste arranque rápido, você cria e testa uma zona de DNS privada e grava em Azure DNS. Este é um guia passo a passo para criar e gerir a sua primeira zona privada de DNS e gravar usando o portal Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: d298dfd5f3ad0beb56a511c124bab056ca25fd27
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310054"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Quickstart: Criar uma zona de DNS privada Azure utilizando o portal Azure

Este quickstart acompanha-o através dos passos para criar a sua primeira zona privada de DNS e gravar usando o portal Azure.

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Para publicar uma zona DNS privada na sua rede virtual, especifique a lista de redes virtuais permitidas para resolver registos na zona.  Estas são chamadas redes virtuais *ligadas.* Quando o registo automático é ativado, o Azure DNS também atualiza os registos da zona sempre que uma máquina virtual é criada, altera o seu endereço IP ou é eliminado.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se preferir, pode completar este arranque rápido utilizando [a Azure PowerShell](private-dns-getstarted-powershell.md) ou [a Azure CLI](private-dns-getstarted-cli.md).

## <a name="create-a-private-dns-zone"></a>Criar uma zona privada de DNS

O exemplo a seguir cria uma zona de DNS chamada **private.contoso.com** num grupo de recursos chamado **MyAzureResourceGroup**.

Uma zona DNS contém as entradas DNS para um domínio. Para começar a hospedar o seu domínio em DNS Azure, cria uma zona DNS para esse nome de domínio.

![Pesquisa de zonas privadas de DNS](media/private-dns-portal/search-private-dns.png)

1. Na barra de pesquisa do portal, digite **zonas de dns privadas** na caixa de texto de pesquisa e prima **Enter**.
1. Selecione **a zona privada de DNS**.
2. **Selecione Criar zona privada de dns**.

1. Na página **da zona de IdS,** escreva ou selecione os seguintes valores:

   - **Grupo de recursos**: Selecione **Criar novo,** insira *o MyAzureResourceGroup*e selecione **OK**. O nome do grupo de recursos deve ser único dentro da assinatura Azure.
   -  **Nome**: Escreva *private.contoso.com* para este exemplo.
1. Para **a localização do grupo de recursos**, selecione West Central **US**.

1. Selecione **Review + Criar**.

1. Selecione **Criar**.

A criação da zona pode demorar alguns minutos.

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção, deverá substituir os seguintes parâmetros nos passos pelas informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | MyAzureResourceGroup (Selecione o grupo de recursos existente) |
| **\<virtual-network-name>** | MyAzureVNet          |
| **\<region-name>**          | E.U.A. Centro-Oeste      |
| **\<IPv4-address-space>**   | 10.2.0.0\16          |
| **\<subnet-name>**          | MyAzureSubnet        |
| **\<subnet-address-range>** | 10.2.0.0\24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>Ligue a rede virtual

Para ligar a zona DNS privada a uma rede virtual, crie uma ligação de rede virtual.

![Adicionar link de rede virtual](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Abra o grupo de recursos **MyAzureResourceGroup** e selecione a **private.contoso.com** zona privada.
2. No painel esquerdo, selecione **links de rede virtual.**
3. Selecione **Adicionar**.
4. Digite **myLink** para o **nome Link**.
5. Para **rede virtual**, selecione **myAzureVNet**.
6. Selecione a caixa de **verificação de registo automático Ativa.**
7. Selecione **OK**.

## <a name="create-the-test-virtual-machines"></a>Criar as máquinas virtuais de teste

Agora, vai criar duas máquinas virtuais para testar a zona DNS privada:

1. Na página superior esquerda do portal, selecione **Criar um recurso**e, em seguida, selecione o Centro de **Dados do Windows Server 2016**.
1. Selecione **MyAzureResourceGroup** para o grupo de recursos.
1. Digite **myVM01** - para o nome da máquina virtual.
1. Selecione **West Central US** para a **Região.**
1. Insira um nome para o nome de utilizador do administrador.
2. Introduza uma senha e confirme a senha.
5. Para **portas de entrada pública**, selecione Deixe as portas **selecionadas**e, em seguida, selecione **RDP (3389)** para **selecionar portas de entrada**.
10. Aceite as outras predefinições para a página e, em seguida, clique em **Seguinte: Discos >**.
11. Aceite as predefinições na página **Discos** e, em seguida, clique em **Seguinte: Networking >**.
1. Certifique-se de que **o myAzureVNet** está selecionado para a rede virtual.
1. Aceite os outros predefinidos para a página e, em seguida, clique em **Seguinte: Gestão >**.
2. Para **diagnósticos de Boot**, selecione **Off**, aceite as outras predefinições e, em seguida, selecione Review **+ create**.
1. Reveja as definições e clique em **Criar**.

Repita estes passos e crie outra máquina virtual chamada **myVM02**.

Levará alguns minutos para que ambas as máquinas virtuais sejam completadas.

## <a name="create-an-additional-dns-record"></a>Criar um registo DNS adicional

 O exemplo a seguir cria um registo com o nome relativo **db** na zona de DNS **private.contoso.com,** no grupo de recursos **MyAzureResourceGroup**. O nome totalmente qualificado do recorde é **db.private.contoso.com**. O tipo de registo é "A", com o endereço IP do **myVM01**.

1. Abra o grupo de recursos **MyAzureResourceGroup** e selecione a **private.contoso.com** zona privada.
2. Selecione **+ Conjunto de registos**.
3. Para **nome,** tipo **db**.
4. Para **endereço IP**, digite o endereço IP que vê para o **myVM01**. Isto deve ser registado automaticamente quando a máquina virtual começou.
5. Selecione **OK**.

## <a name="test-the-private-zone"></a>Testar a zona privada

Agora pode testar a resolução do nome para a sua zona privada **private.contoso.com.**

### <a name="configure-vms-to-allow-inbound-icmp"></a>Configurar as VMs para permitir ICMP de entrada

Pode utilizar o comando ping para testar a resolução de nomes. Assim, configure a firewall em ambas as máquinas virtuais para permitir pacotes ICMP de entrada.

1. Ligue a myVM01 e abra uma janela do Windows PowerShell com privilégios de administrador.
2. Execute o seguinte comando:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Repita para myVM02.

### <a name="ping-the-vms-by-name"></a>Enviar pings para as VMs por nome

1. Na linha de comandos do Windows PowerShell para myVM02, envie um ping myVM01 com o nome de anfitrião registado automaticamente:
   ```
   ping myVM01.private.contoso.com
   ```
   Deverá ver uma saída semelhante à seguinte:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Agora, envie um ping para o nome **db** criado anteriormente:
   ```
   ping db.private.contoso.com
   ```
   Deverá ver uma saída semelhante à seguinte:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos **MyAzureResourceGroup** para eliminar os recursos criados neste arranque rápido.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Cenários de Zonas Privadas Azure DNS](private-dns-scenarios.md)

