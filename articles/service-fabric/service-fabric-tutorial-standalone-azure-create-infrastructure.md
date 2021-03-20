---
title: Criar infraestruturas para um cluster em VMs Azure
description: Neste tutorial, você aprende a configurar a infraestrutura Azure VM para executar um cluster de Tecido de Serviço.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: e9f62f944fff331bcf2dad1b380161e563614219
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90561845"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Tutorial: Criar infraestrutura VM Azure para acolher um cluster de tecido de serviço

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Nesta série tutorial, cria-se um cluster autónomo alojado nos VMs Azure e instala-se uma aplicação nele.

Este tutorial é a primeira parte de uma série. Neste artigo, gera os recursos Azure VM necessários para hospedar o seu cluster autónomo de Tecido de Serviço. Em artigos futuros, terá de instalar o conjunto autónomo do Service Fabric, instalar uma aplicação de exemplo no cluster e, por fim, limpar o cluster.

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar um conjunto de instâncias AzureVM
> * Modificar o grupo de segurança
> * Iniciar sessão numa das instâncias
> * Preparar a instância do Service Fabric

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure.  Se ainda não tem uma conta, vá ao [portal Azure](https://portal.azure.com) para criar uma.

## <a name="create-azure-virtual-machine-instances"></a>Criar instâncias de máquina virtual Azure

1. Inscreva-se no portal Azure e selecione **máquinas virtuais** (não máquinas virtuais (clássicas)).

   ![Azure portal VM][az-console]

2. Selecione o botão **Adicionar,** que abrirá a forma **de máquina virtual Criar.**

3. No separador **Básicos,** não se esqueça de escolher o grupo de subscrição e recursos que pretende (recomenda-se a utilização de um novo grupo de recursos).

4. Altere o tipo **de imagem** para o Centro **de Dados do Windows Server 2016**. 
 
5. Alterar o **tamanho** da instância para **DS2 v2 padrão**. Desabine o **nome de utilizador** e a **palavra-passe** do administrador, observando o que são.

6. Deixar as **Regras do Porto de Entrada** bloqueadas por enquanto; vamos configurá-los na próxima secção.

7. No **separador Networking,** crie uma nova **Rede Virtual** e tome nota do seu nome.

8. Em seguida, coloque o **grupo de segurança da rede NIC** em **Advanced.** Criar um novo grupo de segurança, anoting seu nome, e criar as seguintes regras para permitir o tráfego TCP a partir de qualquer fonte:

   ![A screenshot mostra a criação de regras para permitir o tráfego de TCP de entrada.][sf-inbound]

   * Porta `3389` , para PDR e ICMP (conectividade básica).
   * Portas, `19000-19003` para Tecido de Serviço.
   * Portas, `19080-19081` para Tecido de Serviço.
   * Porta `8080` , para pedidos de navegador web.

   > [!TIP]
   > Para ligar as suas máquinas virtuais entre si no Service Fabric, as VMs que alojam a infraestrutura precisam de ter as mesmas credenciais.  Existem duas formas comuns de obter credenciais consistentes: associá-las a todas ao mesmo domínio ou definir a mesma palavra-passe de administrador em cada VM. Felizmente, o Azure permite que todas as máquinas virtuais da mesma **rede Virtual** se conectem facilmente, pelo que teremos a certeza de ter todas as nossas instâncias na mesma rede.

9. Adicione outra regra. Desa estaba este o conjunto da fonte para ser **a Tag de Serviço** e desavenda a etiqueta de serviço de origem para a **VirtualNetwork**. O Tecido de Serviço exige que as seguintes portas estejam abertas para comunicação dentro do cluster: 135.137-139.445.20001-20031.20606-20861.

   ![O Screenshot mostra a criação de regras que permitem o tráfego de TCP para um cluster.][vnet-inbound]

10. As restantes opções são aceitáveis no seu estado padrão. Reveja-os se quiser e, em seguida, lance a sua máquina virtual.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Criando mais instâncias para o seu cluster de Tecido de Serviço

Lance mais **duas Máquinas Virtuais**, tendo a certeza de manter as mesmas definições descritas na secção anterior. Em particular, mantenha o mesmo nome de utilizador e senha do administrador. O grupo de segurança **da rede Virtual** e da rede **NIC** não deve ser recriado; selecione as que já criou a partir do menu suspenso. Pode levar alguns minutos para cada um dos seus casos ser implantado.

## <a name="connect-to-your-instances"></a>Ligue-se aos seus casos

1. Selecione uma das suas instâncias na secção **Máquina Virtual.**

2. No **separador 'Visão Geral',** tome nota do endereço IP *privado.* Em seguida, clique em **Connect**.

3. No separador **PDR,** note que estamos a utilizar o endereço IP público e a porta 3389, que abrimos especificamente anteriormente. Descarregue o ficheiro RDP.
 
4. Abra o ficheiro RDP e, quando solicitado, introduza o nome de utilizador e a palavra-passe que forneceu na configuração VM.

5. Uma vez ligado a um caso, precisa validar que o registo remoto estava em execução, ativar o SMB e abrir as portas necessárias para sMB e registo remoto.

   Para ativar o SMB, este é o comando PowerShell:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. Para abrir as portas na firewall, o comando do PowerShell é:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Repita este processo para as suas outras instâncias, observando novamente os endereços IP privados.

## <a name="verify-your-settings"></a>Verificar as definições

1. Para validar a conectividade básica, ligue-se a um dos VMs utilizando RDP.

2. Abra o Pedido de **Comando** a partir desse VM, em seguida, utilize o comando ping para ligar de um VM para outro, substituindo o endereço IP abaixo por um dos endereços IP privados a que observou anteriormente (não o IP do VM a que já está ligado).

   ```
   ping 172.31.20.163
   ```

   Se o resultado for semelhante a `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` repetido quatro vezes, a ligação entre as instâncias está a funcionar.

3. Valide agora que a partilha SMB funciona com o seguinte comando:

   ```
   net use * \\172.31.20.163\c$
   ```

   Deverá devolver `Drive Z: is now connected to \\172.31.20.163\c$.` como resultado.


   Agora as suas ocorrências estão devidamente preparadas para o Service Fabric.

## <a name="next-steps"></a>Passos seguintes

Na primeira parte da série, aprendeu a lançar três instâncias Azure VM e a configurá-las para a instalação do Service Fabric:

> [!div class="checklist"]
> * Criar um conjunto de instâncias VM Azure
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
