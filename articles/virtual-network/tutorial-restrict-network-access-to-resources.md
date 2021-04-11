---
title: Restringir o acesso aos recursos paaS - tutorial - Portal Azure
description: Neste tutorial, vai aprender a limitar e restringir o acesso de rede a recursos do Azure, como o Armazenamento do Azure e a Base de Dados SQL do Azure, com pontos finais de serviço de rede virtual através do portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/11/2020
ms.author: kumud
ms.openlocfilehash: 22494d292077f4b6018a4512b45b5fe2caa9c8ee
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057206"
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
2. Selecione **Networking** e, em seguida, selecione **redes Virtuais**.
3. Clique **+ Adicionar** e introduzir as seguintes informações: 

   |Definição|Valor|
   |----|----|
   |Subscrição| Selecione a sua subscrição|
   |Grupo de recursos | Selecione **Criar novo** e introduza *myResourceGroup*.|
   |Name| Insira *myVirtualNetwork* |
   |Region| Selecione **(EUA) Leste dos EUA** |

   ![Introduza as informações básicas sobre a sua rede virtual](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

4. Clique **em seguida: endereços IP >**
   
   |Definição|Valor|
   |----|----|
   |Espaço IPv4Address| Sair como padrão |
   |Nome da sub-rede| Clique **em predefinição** e altere o nome de "predefinido" para "Público"|
   |Gama de endereços de sub-rede| Sair como padrão|

5. Clique **em seguida: >de segurança** 
   
   |Definição|Valor|
   |----|----|   
   |BastionHost| Desativar|
   |Proteção contra DDoS| Desativar|
   |Firewall| Desativar|

6. Quando estiver concluído, clique em **Rever e criar**.
7. Se a validação verificar o passe, clique em **Criar**.
8. Aguarde que a implementação termine e, em seguida, clique em **Ir para o recurso** ou passar para a secção seguinte. 

## <a name="enable-a-service-endpoint"></a>Ativar um ponto final de serviço

Os pontos finais de serviço são ativados por serviço, por sub-rede. Para criar uma sub-rede e ativar um ponto final de serviço para a sub-rede:

1. Se ainda não estiver na página de recursos da rede virtual, pode pesquisar a rede recém-criada na caixa de **recursos, serviços e docs** recém-criada no topo do portal, insira *a myVirtualNetwork* e selecione-a da lista.
2. No menu **Definições** (à esquerda), selecione **sub-redes** e, em seguida, selecione **+ Sub-rede,** como mostrado:

    ![Adicionar sub-rede](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. Em **Adicionar sub-rede**, selecione ou introduza as seguintes informações e selecione **OK**:

    |Definição|Valor|
    |----|----|
    |Nome| Privado |
    |Intervalo de endereços| Sair como padrão|
    |Pontos finais de serviço| Selecione **Microsoft.Storage**|
    |Políticas de ponto final do serviço | Deixar o padrão como 0 |

> [!CAUTION]
> Antes de ativar um ponto final de serviço para uma sub-rede existente que tem recursos, veja [Alterar definições da sub-rede](virtual-network-manage-subnet.md#change-subnet-settings).

4. Clique **em Guardar** e, em seguida, feche a janela Sub-rede à direita. A sub-rede recém-criada deve aparecer na lista.

## <a name="restrict-network-access-for-a-subnet"></a>Restringir o acesso de rede a uma sub-rede

Por predefinição, todas as instâncias de máquina virtual numa sub-rede podem comunicar com todos os recursos. Pode limitar a comunicação de e para todos os recursos de uma sub-rede, criando um grupo de segurança de rede e associando-a à sub-rede:

1. Selecione **Todos os serviços** no canto superior esquerdo do portal do Azure.
2. Selecione **Networking** e, em seguida, selecione (ou procure) **grupos de segurança da rede**.
3. A partir da página **dos grupos de segurança da Rede,** clique **+ Adicionar**.
4. Introduza as seguintes informações 

    |Definição|Valor|
    |----|----|
    |Subscrição| Selecione a sua subscrição|
    |Grupo de recursos | Selecione *myResourceGroup* da lista|
    |Name| Insira **myNsgPrivate** |
    |Localização| Selecione **E.U.A. Leste**. |

5. Clique **em Rever + criar**, e quando a verificação de validação for passada, clique em **Criar**.
6. Após a criação do grupo de segurança da rede, clique em **Ir ao recurso** ou procure o *myNsgPrivate*.
7. Em **Definições** à esquerda, selecione **regras de segurança de saída**.
8. Selecione **+ Adicionar**.
9. Crie uma regra que permita comunicações de saída para o serviço de Armazenamento do Azure. Introduza ou selecione as seguintes informações e selecione **Adicionar**:

    |Definição|Valor|
    |----|----|
    |Origem| Selecione **VirtualNetwork** |
    |Intervalo de portas de origem| * |
    |Destino | Selecione **Etiqueta do Serviço**|
    |Etiqueta do serviço de destino | Selecione **Armazenamento**|
    |Intervalos de portas de destino| Deixar o padrão como *8080* |
    |Protocolo|Qualquer|
    |Ação|Permitir|
    |Prioridade|100|
    |Nome|Renomeado para **Permitir-Armazenamento-All**|

10. Crie outra regra de segurança de saída que negue a comunicação à Internet. Esta regra substitui a regra predefinida em todos os grupos de segurança de rede que permite a comunicação de saída para a Internet. Passos completos 6-9 de cima utilizando os seguintes valores:

    |Definição|Valor|
    |----|----|
    |Origem| Selecione **VirtualNetwork** |
    |Intervalo de portas de origem| * |
    |Destino | Selecione **Etiqueta do Serviço**|
    |Etiqueta do serviço de destino| Selecione **Internet**|
    |Intervalos de portas de destino| * |
    |Protocolo|Qualquer|
    |Ação|**Alterar o padrão de *Negar*** |
    |Prioridade|110|
    |Name|Alteração para *Deny-Internet-All*|

11. Crie uma *regra de segurança de entrada* que permita o tráfego do Protocolo de Ambiente de Trabalho Remoto (RDP) para a sub-rede a partir de qualquer lugar. A regra substitui uma regra de segurança predefinida que nega todo o tráfego de entrada a partir da Internet. As ligações de ambiente de trabalho remoto são permitidas para a sub-rede, para que a conectividade possa ser testada num passo posterior. 
12. Em **Definições**, selecione **regras de segurança de entrada**.
13. **Selecione + Adicionar** e utilizar os seguintes valores:

    |Definição|Valor|
    |----|----|
    |Origem| Qualquer |
    |Intervalo de portas de origem| * |
    |Destino | Selecione **VirtualNetwork**|
    |Intervalos de portas de destino| Alteração para *3389* |
    |Protocolo|Qualquer|
    |Ação|Permitir|
    |Prioridade|120|
    |Name|Alteração para *Allow-RDP-All*|

   >[!WARNING] 
   > A porta RDP 3389 está exposta à Internet. Isto só é recomendado para testes. Para *ambientes de produção,* recomendamos a utilização de uma ligação VPN ou privada.

1.  Em **Definições**, selecione **Sub-redes**.
2.  Clique em **+ Associar**.
3.  Na **rede Virtual,** selecione **myVirtualNetwork**.
4.  No **sub-rede**, selecione **Private**, e, em seguida, selecione **Ok**.

## <a name="restrict-network-access-to-a-resource"></a>Restringir o acesso de rede a um recurso

As medidas necessárias para restringir o acesso à rede aos recursos criados através dos serviços Azure, que estão habilitados para os pontos finais de serviços variarão em todos os serviços. Veja a documentação relativa aos serviços individuais para obter os passos específicos dos mesmos. O resto deste tutorial inclui passos para restringir o acesso de rede a uma conta de Armazenamento do Azure, como um exemplo.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. Introduza "Conta de Armazenamento" na barra de pesquisa e selecione-a no menu suspenso.
3. Clique em **+ Adicionar**.
4. Introduza as seguintes informações:

    |Definição|Valor|
    |----|----|
    |Subscrição| Selecione a sua subscrição|
    |Grupo de recursos| Selecione *myResourceGroup*|
    |Nome da conta de armazenamento| Introduza um nome que seja exclusivo em todas as localizações do Azure, entre 3 e 24 carateres de comprimento, com números e letras minúsculas apenas.|
    |Localização| Selecione **(EUA) Leste dos EUA** |
    |Desempenho|Standard|
    |Tipo de conta| StorageV2 (fins gerais v2)|
    |Replicação| Armazenamento localmente redundante (LRS)|

5. **Selecione Criar + rever** e quando os controlos de validação tiverem passado, clique em **Criar**. 

>[!NOTE] 
> A colocação pode demorar alguns minutos a ser concluída.

6. Após a criação da conta de armazenamento, clique em **Ir para o recurso**

### <a name="create-a-file-share-in-the-storage-account"></a>Criar uma partilha de ficheiros na conta de Armazenamento

1. Vá à página geral da sua conta de armazenamento.
2. Selecione o ícone da aplicação **'Partilhas de Ficheiros'** e, em seguida, clique **em + partilha de ficheiros.**

    |Definição|Valor|
    |----|----|
    |Nome| my-file-share|
    |Quota| 'Definido ao máximo' |

   ![Conta de armazenamento](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Clique em **Criar**.
4. A partilha de ficheiros deve ser mostrada na janela Azure, se não clicar em **Refresh**

### <a name="restrict-network-access-to-a-subnet"></a>Restringir o acesso de rede a uma sub-rede

Por predefinição, as contas de armazenamento aceitam ligações de rede de clientes em qualquer rede, incluindo a Internet. Pode restringir o acesso à rede a partir da internet e de todas as outras sub-redes em todas as redes virtuais (exceto a sub-rede *Privada* na rede virtual *myVirtualNetwork.)* Para restringir o acesso da rede a uma sub-rede:

1. Em **Definições** para a sua conta de armazenamento (com nome único), **selecione Networking**.
2. Selecione **Redes selecionadas**.
3. **Selecione + Adicione a rede virtual existente.**
4. Em **Adicionar redes**, selecione os seguintes valores e selecione **Adicionar**:

    |Definição|Valor|
    |----|----|
    |Subscrição| Selecione a sua subscrição|
    |Redes virtuais| **myVirtualNetwork**|
    |Sub-redes| **Privado**|

    ![A screenshot mostra o painel de redes Add onde pode introduzir os valores especificados.](./media/tutorial-restrict-network-access-to-resources/virtual-networks.png)

5. Clique **em Adicionar** e, em seguida, clique imediatamente no ícone **Guardar** para guardar as alterações.
6. Em **Definições** para a conta de armazenamento, selecione **as teclas de acesso,** como mostra a seguinte imagem:

      ![A screenshot mostra as teclas de acesso selecionadas a partir de Definições onde pode obter uma chave.](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

7. Clique **nas teclas Mostrar** e note os valores **chave,** pois terá de introduzir manualmente a tecla1 num passo posterior ao mapear a partilha de ficheiros para uma letra de unidade num VM.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Para testar o acesso de rede a uma conta de Armazenamento, implemente uma VM em cada sub-rede.

### <a name="create-the-first-virtual-machine"></a>Criar a primeira máquina virtual

1. Dos "Recursos de pesquisa. . ." bar, procure **por máquinas virtuais.**
2. **Selecione + Adicionar > máquina virtual**. 
3. Insira, as seguintes informações:

   |Definição|Valor|
   |----|----|
   |Subscrição| Selecione a sua subscrição|
   |Grupo de recursos| Selecione **myResourceGroup, que foi criado anteriormente.|
   |Nome da máquina virtual| Insira *myVmPublic*|
   |Region | (EUA) E.U.A Leste
   |Opções de disponibilidade| Zona de disponibilidade|
   |Zona de disponibilidade | 1 |
   |Imagem | Windows Server 2019 Datacenter - Gen1 |
   |Tamanho | Selecione o tamanho de instância VM que pretende usar |
   |Nome de utilizador|Introduza um nome de utilizador à sua escolha.|
   |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Portas de entrada públicas | Permitir portas selecionadas |
   |Selecione as portas de entrada | Deixe o conjunto padrão de padrão para *RDP (3389)* |

   ![Selecionar uma rede virtual](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
  
4. Selecione o **separador 'Rede'** e, em seguida, selecione **myVirtualNetwork**. 
5. Selecione a sub-rede *Pública.*
6. No âmbito **do Grupo de Segurança da Rede NIC,** selecione **Advanced**. O portal cria automaticamente um grupo de segurança de rede que permite a porta 3389, que vai precisar de abrir para ligar à máquina virtual num passo posterior. 

   ![Introduzir informações básicas de uma máquina virtual](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)

7. Selecione **Rever e criar,** em **seguida, Criar** e esperar que a implementação termine.
8. Clique **em Ir para o recurso**, ou abrir a página **'Home >'s Virtual machines'** e selecione o VM que acaba de criar *o myVmPublic,* que deve ser iniciado.

### <a name="create-the-second-virtual-machine"></a>Criar a segunda máquina virtual

1. Complete os passos 1-8 novamente, mas no passo 3, nomeie a máquina virtual *myVmPrivate* e coloque a **porta de entrada pública para** "Nenhum". 
2. No passo 4-5, selecione a sub-rede **Privada.**

>[!NOTE]
> As definições **do grupo de segurança da rede NIC** e das portas de entrada **pública** devem espelhar a imagem mostrada abaixo, incluindo a janela de confirmação azul indicando: "todo o tráfego público de internet será bloqueado por padrão".

   ![Criar uma máquina virtual privada](./media/tutorial-restrict-network-access-to-resources/create-private-virtual-machine.png)

3. Selecione **Rever e criar,** em **seguida, Criar** e esperar que a implementação termine. 

>[!WARNING]
> Por favor, não continue para o próximo passo até que a colocação esteja completa.

4. Aguarde a janela de confirmação mostrada abaixo e clique em **Ir ao recurso**.

   ![Criar uma janela de confirmação de máquina virtual privada](./media/tutorial-restrict-network-access-to-resources/create-vm-confirmation-window.png)

## <a name="confirm-access-to-storage-account"></a>Confirmar o acesso à conta de Armazenamento

1. Assim que o *myVmPrivate* VM tiver sido criado, clique **em Ir para o recurso**. 
2. Ligue-se ao VM selecionando o **Connect > RDP**.
3. Depois de selecionar o botão **'Ligar',** é criado um ficheiro Remote Desktop Protocol (.rdp). Clique **em Baixar Ficheiro RDP** para descarregar para o seu computador.  
4. Abra o ficheiro rdp transferido. Se lhe for pedido, selecione **Ligar**. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM. Poderá ter de selecionar **Mais escolhas,** em seguida, **utilize uma conta diferente** para especificar as credenciais que introduziu quando criou o VM. Para o campo de e-mail, insira as credenciais "Administrador: nome de utilizador" que especificou anteriormente. 
5. Selecione **OK**.
6. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber o aviso, selecione **Sim** ou **Continuar** para prosseguir com a ligação. Deve ver o início do VM como mostrado:

   ![Mostrar máquina virtual privada funcionando](./media/tutorial-restrict-network-access-to-resources/virtual-machine-running.png)

7. Na janela VM, abra uma instância PowerShell CLI.
8. Utilizando o script abaixo, mapear a partilha de ficheiros Azure para conduzir Z usando PowerShell. Antes de executar os comandos que se seguem, substitua `<storage-account-key>` e ambos os campos pelos `<storage-account-name>` valores que forneceu e "d anteriormente na [Criação de uma conta de armazenamento](#create-a-storage-account).

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

9.   Feche a sessão de ambiente de trabalho remoto para a VM *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso à conta de Armazenamento é negado

1. Introduza *myVmPublic* na caixa **Procurar recursos, serviços e documentos**, na parte superior do portal.
2. Quando **myVmPublic** aparecer nos resultados de pesquisa, selecione-a.
3. Passos completos 1-8 acima em [Confirmar o acesso ao armazenamento conta](#confirm-access-to-storage-account) para o *VM myVmPublic.*

   Após uma breve espera, receberá um erro `New-PSDrive : Access is denied`. O acesso é negado porque a VM *myVmPublic* é implementada na sub-rede *Pública*. A sub-rede *Pública* não tem nenhum ponto final de serviço ativado para o Armazenamento do Azure. A conta de armazenamento só permite o acesso de rede da sub-rede *Privada*, mas não da sub-rede *Pública*.

4. Feche a sessão de ambiente de trabalho remoto para a VM *myVmPublic*.
5. De volta ao portal Azure, vá à conta de armazenamento com o nome único que criou anteriormente.
6. Em Serviço de Ficheiros, selecione **ações de ficheiros**, a *minha partilha de ficheiros,* criadas anteriormente.
7. Deverá receber a seguinte mensagem de erro:

   ![Erro de acesso negado](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)
   
>[!NOTE] 
> O acesso é negado porque o computador não está na sub-rede *Privada* da rede virtual *MyVirtualNetwork*.

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
