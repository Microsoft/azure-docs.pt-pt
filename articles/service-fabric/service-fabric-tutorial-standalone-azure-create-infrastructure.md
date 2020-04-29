---
title: Criar infraestrutura para um cluster em VMs Azure
description: Neste tutorial, aprende-se a instalar a infraestrutura Azure VM para gerir um cluster de Tecido de Serviço.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 93a7e2507ab3a467ef83924479872694cae2dd5b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75614014"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Tutorial: Criar infraestrutura VM Azure para acolher um cluster de Tecido de Serviço

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Nesta série tutorial, você cria um cluster autónomo hospedado em VMs Azure e instala uma aplicação nele.

Este tutorial é a primeira parte de uma série. Neste artigo, gera os recursos Azure VM necessários para acolher o seu cluster autónomo de Tecido de Serviço. Em artigos futuros, terá de instalar o conjunto autónomo do Service Fabric, instalar uma aplicação de exemplo no cluster e, por fim, limpar o cluster.

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar um conjunto de instâncias AzureVM
> * Modificar o grupo de segurança
> * Iniciar sessão numa das instâncias
> * Preparar a instância do Service Fabric

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure.  Se ainda não tem uma conta, vá ao [portal Azure](https://portal.azure.com) para criar uma.

## <a name="create-azure-virtual-machine-instances"></a>Criar instâncias de máquina virtual Azure

1. Inscreva-se no portal Azure e selecione **máquinas Virtuais** (não Máquinas Virtuais (clássicas)).

   ![Azure portal VM][az-console]

2. Selecione o botão **Adicionar,** que abrirá o formulário **Criar uma máquina virtual.**

3. No separador **Basics,** certifique-se de escolher o grupo de subscrição e recursos que deseja (recomenda-se a utilização de um novo grupo de recursos).

4. Altere o tipo **de imagem** para **o Datacenter do Windows Server 2016**. 
 
5. Alterar a instância **Tamanho** para **Standard DS2 v2**. Detete um nome de **utilizador** e **palavra-passe**do administrador, observando o que são.

6. Deixar as Regras do **Porto de Entrada** bloqueadas por enquanto; vamos configurar os que estão na próxima secção.

7. No separador **Networking,** crie uma nova **Rede Virtual** e tome nota do seu nome.

8. Em seguida, coloque o grupo de segurança da **rede NIC** para **Advanced**. Crie um novo grupo de segurança, observando o seu nome, e crie as seguintes regras para permitir o tráfego de TCP a partir de qualquer fonte:

   ![sf-inbound][sf-inbound]

   * Porto, `3389`para RDP e ICMP (conectividade básica).
   * `19000-19003`Portas, para tecido de serviço.
   * `19080-19081`Portas, para tecido de serviço.
   * Porta, `8080`para pedidos de navegador web.

   > [!TIP]
   > Para ligar as suas máquinas virtuais entre si no Service Fabric, as VMs que alojam a infraestrutura precisam de ter as mesmas credenciais.  Existem duas formas comuns de obter credenciais consistentes: associá-las a todas ao mesmo domínio ou definir a mesma palavra-passe de administrador em cada VM. Felizmente, o Azure permite que todas as máquinas virtuais da mesma **rede Virtual** se conectem facilmente, pelo que teremos a certeza de ter todas as nossas ocorrências na mesma rede.

9. Adicione outra regra. Detete a fonte para ser **etiqueta de serviço** e detete tea etiqueta de serviço de origem para **VirtualNetwork**. O Tecido de Serviço exige que as seguintes portas estejam abertas para comunicação dentro do cluster: 135.137-139.445.20001-20031.20606-20861.

   ![vnet-inina][vnet-inbound]

10. O resto das opções são aceitáveis no seu estado padrão. Reveja-os se quiser, e depois lance a sua máquina virtual.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Criar mais instâncias para o seu cluster de Tecido de Serviço

Lançar mais duas **Máquinas Virtuais,** mantendo as mesmas definições descritas na secção anterior. Em particular, mantenha o mesmo nome de utilizador e senha do administrador. O grupo de segurança da **Rede Virtual** e da **rede NIC** não deve ser recriado; selecione os que já criou a partir do menu dropdown. Pode levar alguns minutos para que cada um dos seus casos seja implantado.

## <a name="connect-to-your-instances"></a>Conecte-se às suas instâncias

1. Selecione uma das suas instâncias na secção **Máquina Virtual.**

2. No separador **'Visão Geral',** tome nota do endereço IP *privado.* Em seguida, clique em **Ligar**.

3. No **separador RDP,** note que estamos a utilizar o endereço IP público e o porto 3389, que abrimos especificamente anteriormente. Descarregue o ficheiro RDP.
 
4. Abra o ficheiro RDP e, quando solicitado, introduza o nome de utilizador e a palavra-passe que forneceu na configuração VM.

5. Uma vez ligado a uma instância, é necessário validar que o registo remoto estava em execução, ativar o SMB e abrir as portas necessárias para SMB e registo remoto.

   Para ativar o SMB, este é o comando PowerShell:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. Para abrir as portas na firewall, o comando do PowerShell é:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Repita este processo para os outros casos, voltando a notar os endereços IP privados.

## <a name="verify-your-settings"></a>Verifique as suas definições

1. Para validar a conectividade básica, ligue-se a um dos VMs utilizando RDP.

2. Abra o Pedido de **Comando** a partir desse VM, então, use o comando de ping para ligar de um VM para outro, substituindo o endereço IP abaixo por um dos endereços IP privados que observou anteriormente (não o IP do VM a que já está ligado).

   ```
   ping 172.31.20.163
   ```

   Se o resultado for semelhante a `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` repetido quatro vezes, a ligação entre as instâncias está a funcionar.

3. Valide agora que a partilha SMB funciona com o seguinte comando:

   ```
   net use * \\172.31.20.163\c$
   ```

   Deverá devolver `Drive Z: is now connected to \\172.31.20.163\c$.` como resultado.


   Agora os seus casos estão devidamente preparados para o Tecido de Serviço.

## <a name="next-steps"></a>Passos seguintes

Na primeira parte da série, aprendeu a lançar três instâncias De VM Azure e configurá-las para a instalação do Tecido de Serviço:

> [!div class="checklist"]
> * Criar um conjunto de instâncias De VM Azure
> * Modificar o grupo de segurança
> * Iniciar sessão numa das instâncias
> * Preparar a instância do Service Fabric

Avance para a segunda parte da série para configurar o Service Fabric no seu cluster.

> [!div class="nextstepaction"]
> [Instalar o Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
