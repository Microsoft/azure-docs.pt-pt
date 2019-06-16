---
title: Criar uma zona privada do DNS do Azure no portal do Azure
description: Neste tutorial, vai criar e testar uma zona DNS privada e um registo no DNS do Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS privada e o registo no portal do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 6/15/2019
ms.author: victorh
ms.openlocfilehash: e3772d8f385ed93c96f8aa2f7ee2ded8f46d4e00
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147837"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-the-azure-portal"></a>Tutorial: Criar uma zona privada do DNS do Azure no portal do Azure

Este tutorial explica os passos para criar a sua primeira zona DNS privada e o registo no portal do Azure.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Para publicar uma zona DNS privada na sua rede virtual, especifique a lista de redes virtuais autorizadas a resolver registos na zona.  Estes são denominados *ligado* redes virtuais. Quando o registo automático está ativado, o DNS do Azure também atualiza os registos de zona sempre que uma máquina virtual for criado, as alterações seu "endereço IP, ou é eliminada.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma zona DNS privada
> * Criar uma rede virtual
> * Ligar a rede virtual
> * Criar máquinas virtuais de teste
> * Criar um registo DNS adicional
> * Testar a zona privada

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se preferir, pode concluir este tutorial com [do Azure PowerShell](private-dns-getstarted-powershell.md) ou [CLI do Azure](private-dns-getstarted-cli.md).

## <a name="create-a-dns-private-zone"></a>Criar uma zona DNS privada

O exemplo seguinte cria uma zona DNS denominada **private.contoso.com** num grupo de recursos chamado **MyAzureResourceGroup**.

Uma zona DNS contém as entradas de DNS para um domínio. Para começar a alojar o seu domínio no DNS do Azure, crie uma zona DNS para esse nome de domínio.

![Pesquisa de zonas de DNS privado](media/private-dns-portal/search-private-dns.png)

1. Na barra de pesquisa de portal, escreva **zonas dns privadas** na caixa de texto de pesquisa e prima **Enter**.
1. Selecione **zona DNS privada**.
2. Selecione **criar a zona de dns privado**.

1. Sobre o **zona de DNS privado criar** página, escreva ou selecione os seguintes valores:

   - **Grupo de recursos**: Selecione **criar novo**, introduza *MyAzureResourceGroup*e selecione **OK**. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição do Azure. 
   -  **Nome**: Tipo *private.contoso.com* para este exemplo.
1. Para **localização do grupo de recursos**, selecione **e.u.a. Centro-Oeste**.

1. Selecione **rever + criar**.

1. Selecione **Criar**.

A criação da zona pode demorar alguns minutos.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No canto superior esquerdo da página do portal, selecione **criar um recurso**, em seguida, **redes**, em seguida, selecione **rede Virtual**.
2. Para **Name**, tipo **myAzureVNet**.
3. Para **grupo de recursos**, selecione **MyAzureResourceGroup**.
4. Para **localização**, selecione **e.u.a. Centro-Oeste**.
5. Aceite os outros valores predefinidos e selecione **criar**.

## <a name="link-the-virtual-network"></a>Ligar a rede virtual

Para ligar a zona DNS privada a uma rede virtual, crie uma ligação de rede virtual.

![Adicionar ligação de rede virtual](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Abra o **MyAzureResourceGroup** recursos de grupo e selecione o **private.contoso.com** zona privada.
2. No painel esquerdo, selecione **ligações de rede Virtual**.
3. Selecione **Adicionar**.
4. Tipo **myLink** para o **nome da hiperligação**.
5. Para **rede Virtual**, selecione **myAzureVNet**.
6. Selecione o **ativar o registo automático** caixa de verificação.
7. Selecione **OK**.

## <a name="create-the-test-virtual-machines"></a>Criar as máquinas virtuais de teste

Agora, vai criar duas máquinas virtuais para testar a zona DNS privada:

1. No canto superior esquerdo da página do portal, selecione **criar um recurso**e, em seguida, selecione **do Windows Server 2016 Datacenter**.
1. Selecione **MyAzureResourceGroup** para o grupo de recursos.
1. Tipo **myVM01** - no nome da máquina virtual.
1. Selecione **EUA Centro-Oeste** para o **região**.
1. Tipo **azureadmin** para o nome de utilizador de administrador.
2. Tipo **Azure12345678** a palavra-passe e confirme a palavra-passe.

5. Para **portas de entrada públicas**, selecione **permitir portas selecionadas**e, em seguida, selecione **RDP (. 3389)** para **selecionar portas de entrada**.
10. Aceite as outras predefinições para a página e, em seguida, clique em **seguinte: Discos >** .
11. Aceite as predefinições no **discos** página, em seguida, clique em **seguinte: Sistema de rede >** .
1. Certifique-se de que **myAzureVNet** está selecionada para a rede virtual.
1. Aceite as outras predefinições para a página e, em seguida, clique em **seguinte: Gestão >** .
2. Para **diagnósticos de arranque**, selecione **desativar**, aceite as outras predefinições e, em seguida, selecione **rever + criar**.
1. Reveja as definições e, em seguida, clique em **criar**.

Repita estes passos e criar outra máquina virtual com o nome **myVM02**.

Irá demorar alguns minutos para que ambas as máquinas virtuais concluir.

## <a name="create-an-additional-dns-record"></a>Criar um registo DNS adicional

 O exemplo seguinte cria um registo com o nome relativo **db** na zona DNS **private.contoso.com**, no grupo de recursos **MyAzureResourceGroup**. O nome completamente qualificado do conjunto de registos está **db.private.contoso.com**. O tipo de registo é "A", com o endereço IP do **myVM01**.

1. Abra o **MyAzureResourceGroup** recursos de grupo e selecione o **private.contoso.com** zona privada.
2. Selecione **+ Conjunto de registos**.
3. Para **Name**, tipo **db**.
4. Para **endereço IP**, escreva o endereço IP que veja **myVM01**. Deve ser automaticamente registrado quando iniciar a máquina virtual.
5. Selecione **OK**.

## <a name="test-the-private-zone"></a>Testar a zona privada

Agora pode testar a resolução de nome para o seu **private.contoso.com** zona privada.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Configurar as VMs para permitir ICMP de entrada

Pode utilizar o comando ping para testar a resolução de nomes. Assim, configure a firewall em ambas as máquinas virtuais para permitir pacotes ICMP de entrada.

1. Ligue a myVM01 e abra uma janela do Windows PowerShell com privilégios de administrador.
2. Execute o seguinte comando:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Repita para myVM02.

### <a name="ping-the-vms-by-name"></a>Enviar pings para as VMs por nome

1. Na linha de comandos do Windows PowerShell para myVM02, envie um ping myVM01 com o nome de anfitrião registado automaticamente:
   ```
   ping myVM01.private.contoso.com
   ```
   Deverá ver um resultado semelhante ao seguinte:
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
   Deverá ver um resultado semelhante ao seguinte:
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

## <a name="delete-all-resources"></a>Eliminar todos os recursos

Quando já não for necessário, elimine o grupo de recursos **MyAzureResourceGroup** para eliminar os recursos criados neste tutorial.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, implementou uma zona DNS privada, criou um registo DNS e testou a zona.
Em seguida, pode saber mais sobre zonas DNS privadas.

> [!div class="nextstepaction"]
> [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md)
