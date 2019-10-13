---
title: Início rápido-criar uma zona DNS privada do Azure usando o portal do Azure
description: Neste guia de início rápido, você cria e testa uma zona DNS privada e registro no DNS do Azure. Este é um guia passo a passo para criar e gerenciar sua primeira zona e registro de DNS privado usando o portal do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: victorh
ms.openlocfilehash: fe12a1a9f954718dfacb59ae0ed54e69309da650
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293796"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Início rápido: criar uma zona DNS privada do Azure usando o portal do Azure

Este guia de início rápido orienta você pelas etapas para criar sua primeira zona e registro de DNS privado usando o portal do Azure.

Uma zona DNS é utilizada para alojar os registos de DNS de um domínio específico. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Para publicar uma zona DNS privada na sua rede virtual, especifique a lista de redes virtuais autorizadas a resolver registos na zona.  Elas são chamadas de redes virtuais *vinculadas* . Quando o Autoregistro está habilitado, o DNS do Azure também atualiza os registros de zona sempre que uma máquina virtual é criada, altera seu ' endereço IP ' ou é excluído.

Neste início rápido, vai aprender a:

> [!div class="checklist"]
> * Criar uma zona DNS privada
> * Criar rede virtual
> * Vincular a rede virtual
> * Criar máquinas virtuais de teste
> * Criar um registo DNS adicional
> * Testar a zona privada

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se preferir, você pode concluir este início rápido usando [Azure PowerShell](private-dns-getstarted-powershell.md) ou [CLI do Azure](private-dns-getstarted-cli.md).

## <a name="create-a-private-dns-zone"></a>Criar uma zona DNS privada

O exemplo a seguir cria uma zona DNS chamada **Private.contoso.com** em um grupo de recursos chamado **MyAzureResourceGroup**.

Uma zona DNS contém as entradas DNS para um domínio. Para iniciar a hospedagem de seu domínio no DNS do Azure, você cria uma zona DNS para esse nome de domínio.

![Pesquisa de zonas de DNS privado](media/private-dns-portal/search-private-dns.png)

1. Na barra de pesquisa do portal, digite **zonas DNS privadas** na caixa de texto Pesquisar e pressione **Enter**.
1. Selecione **DNS privado zona**.
2. Selecione **criar zona DNS privada**.

1. Na página **criar zona de DNS privado** , digite ou selecione os seguintes valores:

   - **Grupo de recursos**: selecione **criar novo**, insira *MyAzureResourceGroup*e selecione **OK**. O nome do grupo de recursos deve ser exclusivo na assinatura do Azure. 
   -  **Nome**: digite *Private.contoso.com* para este exemplo.
1. Para **local do grupo de recursos**, selecione **EUA Central ocidental**.

1. Selecione **revisão + criar**.

1. Selecione **Criar**.

A criação da zona pode demorar alguns minutos.

## <a name="create-a-virtual-network"></a>Criar rede virtual

1. Na parte superior esquerda da página do portal, selecione **criar um recurso**, depois **rede**e, em seguida, selecione **rede virtual**.
2. Para **nome**, digite **myAzureVNet**.
3. Para **grupo de recursos**, selecione **MyAzureResourceGroup**.
4. Para **local**, selecione **Oeste EUA Central**.
5. Aceite os outros valores padrão e selecione **criar**.

## <a name="link-the-virtual-network"></a>Vincular a rede virtual

Para vincular a zona DNS privada a uma rede virtual, você cria um link de rede virtual.

![Adicionar link de rede virtual](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Abra o grupo de recursos **MyAzureResourceGroup** e selecione a zona privada **Private.contoso.com** .
2. No painel esquerdo, selecione **links de rede virtual**.
3. Selecione **Adicionar**.
4. Digite **MyLink** para o **nome do link**.
5. Para **rede virtual**, selecione **myAzureVNet**.
6. Marque a caixa de seleção **habilitar registro automático** .
7. Selecione **OK**.

## <a name="create-the-test-virtual-machines"></a>Criar as máquinas virtuais de teste

Agora, vai criar duas máquinas virtuais para testar a zona DNS privada:

1. Na parte superior esquerda da página do portal, selecione **criar um recurso**e, em seguida, selecione **Windows Server 2016 datacenter**.
1. Selecione **MyAzureResourceGroup** para o grupo de recursos.
1. Digite **myVM01** -para o nome da máquina virtual.
1. Selecione **EUA Central ocidental** para a **região**.
1. Digite **azureadmin** para o nome de usuário do administrador.
2. Digite **Azure12345678** para a senha e confirme a senha.

5. Para **portas de entrada públicas**, selecione **permitir portas selecionadas**e, em seguida, selecione **RDP (3389)** para **selecionar portas de entrada**.
10. Aceite os outros padrões para a página e clique em **Avançar: discos >** .
11. Aceite os padrões na página **discos** e clique em **avançar: rede >** .
1. Verifique se **myAzureVNet** está selecionado para a rede virtual.
1. Aceite os outros padrões para a página e clique em **Avançar: > de gerenciamento**.
2. Para **diagnóstico de inicialização**, selecione **desativado**, aceite os outros padrões e, em seguida, selecione **revisar + criar**.
1. Examine as configurações e clique em **criar**.

Repita essas etapas e crie outra máquina virtual chamada **myVM02**.

Levará alguns minutos para que ambas as máquinas virtuais sejam concluídas.

## <a name="create-an-additional-dns-record"></a>Criar um registo DNS adicional

 O exemplo a seguir cria um registro com o nome relativo **DB** na zona DNS **Private.contoso.com**, no grupo de recursos **MyAzureResourceGroup**. O nome totalmente qualificado do conjunto de registros é **DB.Private.contoso.com**. O tipo de registro é "A", com o endereço IP de **myVM01**.

1. Abra o grupo de recursos **MyAzureResourceGroup** e selecione a zona privada **Private.contoso.com** .
2. Selecione **+ Conjunto de registos**.
3. Para **nome**, digite **DB**.
4. Para **endereço IP**, digite o endereço IP que você vê para **myVM01**. Isso deve ser registrado automaticamente quando a máquina virtual for iniciada.
5. Selecione **OK**.

## <a name="test-the-private-zone"></a>Testar a zona privada

Agora você pode testar a resolução de nome para sua zona privada **Private.contoso.com** .

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

Quando não for mais necessário, exclua o grupo de recursos **MyAzureResourceGroup** para excluir os recursos criados neste guia de início rápido.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Cenários de Zonas Privadas do DNS do Azure](private-dns-scenarios.md)

