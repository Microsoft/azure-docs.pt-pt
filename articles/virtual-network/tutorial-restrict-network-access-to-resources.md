---
title: Restringir o acesso aos recursos paaS - tutorial - Portal Azure
description: Neste tutorial, vai aprender a limitar e restringir o acesso de rede a recursos do Azure, como o Armazenamento do Azure e a Base de Dados SQL do Azure, com pontos finais de serviço de rede virtual através do portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 08/23/2018
ms.author: kumud
ms.openlocfilehash: 85fc5687b82947ed16bde0c30ca2b947514ba958
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74186361"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Tutorial: Restringir o acesso de rede para recursos de PaaS com pontos finais de serviço de rede virtual através do portal do Azure

Os pontos finais de serviço de rede virtual permitem-lhe limitar o acesso de rede a alguns recursos de serviços do Azure a uma sub-rede de rede virtual. Também pode remover o acesso à Internet aos recursos. Os pontos finais de serviço proporcionam uma ligação direta a partir da sua rede virtual a serviços do Azure suportados, o que lhe permite utilizar o espaço de endereços privados da sua rede virtual para aceder aos serviços do Azure. O tráfego destinado aos recursos do Azure através de pontos finais de serviço permanece sempre na rede backbone do Microsoft Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma rede virtual com uma sub-rede
> * Adicionar uma sub-rede e ativar um ponto final de serviço
> * Criar um recurso do Azure e permitir o acesso de rede ao mesmo apenas a partir de uma sub-rede
> * Implementar uma máquina virtual (VM) em cada sub-rede
> * Confirmar o acesso a um recurso a partir de uma sub-rede
> * Confirmar que o acesso é negado a um recurso a partir de uma sub-rede e da Internet

Se preferir, pode concluir este tutorial com a [CLI do Azure](tutorial-restrict-network-access-to-resources-cli.md) ou o [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. Selecione **Redes** e, em seguida, selecione **Rede virtual**.
3. Introduza, ou selecione as seguintes informações e, em seguida, selecione **Criar**:

   |Definição|Valor|
   |----|----|
   |Nome| myVirtualNetwork |
   |Espaço de endereços| 10.0.0.0/16|
   |Subscrição| Selecione a sua subscrição|
   |Grupo de recursos | Selecione **Criar novo** e introduza *myResourceGroup*.|
   |Localização| Selecione **E.U.A. Leste**. |
   |Nome da Sub-rede| Público|
   |Intervalo de Endereços da Sub-rede| 10.0.0.0/24|
   |Proteção contra DDoS| Básico|
   |Pontos finais de serviço| Desativado|
   |Firewall| Desativado|

   ![Introduza as informações básicas sobre a sua rede virtual](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

## <a name="enable-a-service-endpoint"></a>Ativar um ponto final de serviço

Os pontos finais de serviço são ativados por serviço, por sub-rede. Crie uma sub-rede e ative um ponto final de serviço para a sub-rede.

1. Na caixa **Procurar recursos, serviços e documentos**, na parte superior do portal, introduzar *myVirtualNetwork.* Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-a.
2. Adicione uma sub-rede à rede virtual. Em **DEFINIÇÕES**, selecione **sub-redes**e, em seguida, selecione **+ Sub-rede,** como mostra a seguinte imagem:

    ![Adicionar sub-rede](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. Em **Adicionar sub-rede**, selecione ou introduza as seguintes informações e selecione **OK**:

    |Definição|Valor|
    |----|----|
    |Nome| Privado |
    |Intervalo de endereços| 10.0.1.0/24|
    |Pontos finais de serviço| Selecione **Microsoft.Storage** em **Serviços**|

> [!CAUTION]
> Antes de ativar um ponto final de serviço para uma sub-rede existente que tem recursos, veja [Alterar definições da sub-rede](virtual-network-manage-subnet.md#change-subnet-settings).

## <a name="restrict-network-access-for-a-subnet"></a>Restringir o acesso de rede a uma sub-rede

Por predefinição, todas as VMs numa sub-rede podem comunicar com todos os recursos. Pode limitar a comunicação de e para todos os recursos numa sub-rede através da criação de um grupo de segurança de rede e associá-lo à sub-rede.

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede** e selecione **Grupo de segurança de rede**.
3. Em **Criar um grupo de segurança de rede**, introduza ou selecione as seguintes informações e selecione **Criar**:

    |Definição|Valor|
    |----|----|
    |Nome| myNsgPrivate |
    |Subscrição| Selecione a sua subscrição|
    |Grupo de recursos | Selecione **Utilizar existente** e selecione *myResourceGroup*.|
    |Localização| Selecione **E.U.A. Leste**. |

4. Depois de criar o grupo de segurança de rede, introduza *myNsgPrivate*, na caixa **Procurar recursos, serviços e documentos**, na parte superior do portal. Quando **myNsgPrivate** aparecer nos resultados da pesquisa, selecione-os.
5. Em **DEFINIÇÕES**, selecione **regras de segurança de saída**.
6. Selecione **+ Adicionar**.
7. Crie uma regra que permita comunicações de saída para o serviço de Armazenamento do Azure. Introduza ou selecione as seguintes informações e selecione **Adicionar**:

    |Definição|Valor|
    |----|----|
    |Origem| Selecione **VirtualNetwork** |
    |Intervalo de portas de origem| * |
    |Destino | Selecione **Etiqueta do Serviço**|
    |Etiqueta do serviço de destino | Selecione **Armazenamento**|
    |Intervalos de portas de destino| * |
    |Protocolo|Qualquer|
    |Ação|Permitir|
    |Prioridade|100|
    |Nome|Allow-Storage-All|

8. Crie outra regra de segurança de saída que negue a comunicação à Internet. Esta regra substitui a regra predefinida em todos os grupos de segurança de rede que permite a comunicação de saída para a Internet. Conclua os passos 5 a 7 novamente, com os seguintes valores:

    |Definição|Valor|
    |----|----|
    |Origem| Selecione **VirtualNetwork** |
    |Intervalo de portas de origem| * |
    |Destino | Selecione **Etiqueta do Serviço**|
    |Etiqueta do serviço de destino| Selecione **Internet**|
    |Intervalos de portas de destino| * |
    |Protocolo|Qualquer|
    |Ação|Negar|
    |Prioridade|110|
    |Nome|Deny-Internet-All|

9. Em **DEFINIÇÕES**, selecione **Regras de segurança de entrada**.
10. Selecione **+ Adicionar**.
11. Crie uma regra de segurança de entrada que permita o tráfego de protocolo RDP (Remote Desktop Protocol) na sub-rede a partir de qualquer lugar. A regra substitui uma regra de segurança predefinida que nega todo o tráfego de entrada a partir da Internet. As ligações de ambiente de trabalho remoto são permitidas para a sub-rede, para que a conectividade possa ser testada num passo posterior. Em **DEFINIÇÕES** selecione **Regras de segurança de entrada**, selecione **+ Adicionar**, introduza os seguintes valores e, em seguida, selecione **Adicionar**:

    |Definição|Valor|
    |----|----|
    |Origem| Qualquer |
    |Intervalo de portas de origem| * |
    |Destino | Selecione **VirtualNetwork**|
    |Intervalos de portas de destino| 3389 |
    |Protocolo|Qualquer|
    |Ação|Permitir|
    |Prioridade|120|
    |Nome|Allow-RDP-All|

12. Em **DEFINIÇÕES**, selecione **sub-redes**.
13. Selecione **+ Associar**
14. Em **Associar sub-rede**, selecione **Rede Virtual** e selecione **myVirtualNetwork**, em **Escolha uma rede virtual**.
15. Em **Escolher sub-rede**, selecione **Privada** e selecione **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Restringir o acesso de rede a um recurso

Os passos necessários para restringir o acesso de rede a recursos criados através de serviços do Azure ativados para pontos finais de serviço varia de serviço para serviço. Veja a documentação relativa aos serviços individuais para obter os passos específicos dos mesmos. O resto deste tutorial inclui passos para restringir o acesso de rede a uma conta de Armazenamento do Azure, como um exemplo.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. Selecione **Armazenamento** e selecione **Conta de Armazenamento - blob, ficheiro, tabela, fila**.
3. Introduza ou selecione as seguintes informações, aceite as predefinições restantes e selecione **Criar**:

    |Definição|Valor|
    |----|----|
    |Nome| Introduza um nome que seja exclusivo em todas as localizações do Azure, entre 3 e 24 carateres de comprimento, com números e letras minúsculas apenas.|
    |Tipo de conta|StorageV2 (fins gerais v2)|
    |Localização| Selecione **E.U.A. Leste**. |
    |Replicação| Armazenamento localmente redundante (LRS)|
    |Subscrição| Selecione a sua subscrição|
    |Grupo de recursos | Selecione **Utilizar existente** e selecione *myResourceGroup*.|

### <a name="create-a-file-share-in-the-storage-account"></a>Criar uma partilha de ficheiros na conta de Armazenamento

1. Depois de criada a conta de armazenamento, introduza o nome da mesma na caixa **Procurar recursos, serviços e documentos**, na parte superior do portal. Quando o nome da sua conta de Armazenamento for apresentado nos resultados de pesquisa, selecione-o.
2. Selecione **Ficheiros**, conforme mostrado na imagem seguinte:

   ![Conta de armazenamento](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Selecione **+ Partilha de ficheiros**.
4. Introduza *my-file-share*, em **Nome**, e selecione **OK**.
5. Feche a caixa **Serviço de ficheiros**.

### <a name="restrict-network-access-to-a-subnet"></a>Restringir o acesso de rede a uma sub-rede

Por predefinição, as contas de armazenamento aceitam ligações de rede de clientes em qualquer rede, incluindo a Internet. Negue o acesso de rede da Internet e de todas as outras sub-redes em todas as redes virtuais, exceto a sub-rede *Privada* na rede virtual *myVirtualNetwork*.

1. Nas **DEFINIÇÕES** da conta de Armazenamento, selecione **Firewalls e redes virtuais**.
2. Selecione **Redes selecionadas**.
3. **Selecione +Adicionar a rede virtual existente.**
4. Em **Adicionar redes**, selecione os seguintes valores e selecione **Adicionar**:

    |Definição|Valor|
    |----|----|
    |Subscrição| Selecione a sua subscrição.|
    |Redes virtuais|Selecione **myVirtualNetwork**, em **Redes virtuais**|
    |Sub-redes| Selecione **privada**, em **sub-redes**|

    ![Firewalls e redes virtuais](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png)

5. Selecione **Guardar**.
6. Feche a caixa **Firewalls e redes virtuais.**
7. Nas **DEFINIÇÕES** da conta de Armazenamento, selecione **Chaves de acesso**, conforme mostrado na imagem seguinte:

      ![Firewalls e redes virtuais](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Aponte o valor **Key** (Chave), pois vai ter de introduzi-lo manualmente num passo posterior, quando for mapear a partilha de ficheiros para uma letra de unidade numa VM.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Para testar o acesso de rede a uma conta de Armazenamento, implemente uma VM em cada sub-rede.

### <a name="create-the-first-virtual-machine"></a>Criar a primeira máquina virtual

1. Selecione **+ Criar um recurso**, disponível no canto superior esquerdo do portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**.
3. Introduza ou selecione as seguintes informações e, em seguida, selecione **OK**:

   |Definição|Valor|
   |----|----|
   |Nome| myVmPublic|
   |Nome de utilizador|Introduza um nome de utilizador à sua escolha.|
   |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Subscrição| Selecione a sua subscrição.|
   |Grupo de recursos| Selecione **Utilizar existente** e selecione **myResourceGroup**.|
   |Localização| Selecione **East US**.|

   ![Introduzir informações básicas de uma máquina virtual](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Escolha um tamanho para a máquina virtual e selecione **Selecionar**.
5. Em **Definições**, selecione **Rede** e selecione **myVirtualNetwork**. Em seguida, selecione **Sub-rede** e selecione **Pública**, conforme mostrado na imagem seguinte:

   ![Selecionar uma rede virtual](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)

6. Sob **Grupo de Segurança de Rede**, selecione **Avançado**. O portal cria automaticamente um grupo de segurança de rede que permite a porta 3389, que vai precisar de abrir para ligar à máquina virtual num passo posterior. Selecione **OK** na página **Definições**.
7. Na página **Resumo**, selecione **Criar** para iniciar a implementação da máquina virtual. A VM demora alguns minutos a ser implementada, mas pode continuar para o passo seguinte enquanto o processo decorre.

### <a name="create-the-second-virtual-machine"></a>Criar a segunda máquina virtual

Conclua os passos 1 a 7 novamente, mas, no passo 3, dê à máquina virtual o nome *myVmPrivate* e, no passo 5, selecione a sub-rede **Privada**.

A implementação da VM demora alguns minutos. Não avance para o próximo passo enquanto não for criada e as definições não forem abertas no portal.

## <a name="confirm-access-to-storage-account"></a>Confirmar o acesso à conta de Armazenamento

1. Após a criação da VM *myVmPrivate*, o Azure abre as definições da mesma. Selecione o botão **Ligar** para ligar à VM, conforme mostrado na imagem seguinte:

   ![Ligar a uma máquina virtual](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. Depois de selecionar o botão **Ligar**, é criado e transferido um ficheiro Remote Desktop Protocol (.rdp) para o computador.  
3. Abra o ficheiro rdp transferido. Se lhe for pedido, selecione **Ligar**. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM. Poderá ter de selecionar **Mais opções** e **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM. 
4. Selecione **OK**.
5. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber o aviso, selecione **Sim** ou **Continuar** para prosseguir com a ligação.
6. Na VM *myVmPrivate*, mapeie a partilha de ficheiros do Azure para a unidade Z com o PowerShell. Antes de executar os comandos que se seguem, substitua `<storage-account-key>` e `<storage-account-name>` pelos valores que indicou e obteve em [Criar uma conta de Armazenamento](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   O PowerShell devolve resultados semelhantes à saída de exemplo seguinte:

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   A partilha de ficheiros do Azure mapeada com êxito para a unidade Z.

7. Confirme que a VM não tem conectividade de saída para a Internet numa linha de comandos:

   ```
   ping bing.com
   ```

   Não vai receber respostas, porque o grupo de segurança de rede associado à sub-rede *Privada* não permite o acesso de saída para a Internet.

8. Feche a sessão de ambiente de trabalho remoto para a VM *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso à conta de Armazenamento é negado

1. Introduza *myVmPublic* na caixa **Procurar recursos, serviços e documentos**, na parte superior do portal.
2. Quando **myVmPublic** aparecer nos resultados de pesquisa, selecione-a.
3. Conclua os passos 1 a 6 em [Confirmar o acesso à conta de Armazenamento](#confirm-access-to-storage-account) na VM *myVmPublic*.

   Após uma breve espera, receberá um erro `New-PSDrive : Access is denied`. O acesso é negado porque a VM *myVmPublic* é implementada na sub-rede *Pública*. A sub-rede *Pública* não tem nenhum ponto final de serviço ativado para o Armazenamento do Azure. A conta de armazenamento só permite o acesso de rede da sub-rede *Privada*, mas não da sub-rede *Pública*.

4. Feche a sessão de ambiente de trabalho remoto para a VM *myVmPublic*.

5. No computador, navegue para o [portal do Azure](https://portal.azure.com).
6. Introduza o nome da conta de armazenamento que criou na caixa **Procurar recursos, serviços e documentos**. Quando o nome da sua conta de Armazenamento for apresentado nos resultados de pesquisa, selecione-o.
7. Selecione **Ficheiros**.
8. Recebe o erro mostrado na imagem seguinte:

   ![Erro de acesso negado](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

   O acesso é negado porque o computador não está na sub-rede *Privada* da rede virtual *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos contidos no mesmo:

1. Introduza *myResourceGroup* na caixa **Pesquisar**, na parte superior do portal. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza *myResourceGroup* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ativou um ponto final de serviço para uma sub-rede de rede virtual. Aprendeu que pode ativar os pontos finais de serviço para os recursos implementados a partir de vários serviços do Azure. Criou uma conta de Armazenamento do Azure e restringiu o acesso de rede à mesma apenas para os recursos dentro de uma sub-rede de uma rede virtual. Para saber mais sobre os pontos finais de serviço, veja [Descrição geral dos pontos finais de serviço](virtual-network-service-endpoints-overview.md) e [Manage subnets](virtual-network-manage-subnet.md) (Gerir sub-redes).

Se tiver várias redes virtuais na sua conta, poderá pretender ligar duas redes virtuais para que os recursos dentro de ambas possam comunicar entre si. Para saber como ligar redes virtuais, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ligar redes virtuais](./tutorial-connect-virtual-networks-portal.md)
