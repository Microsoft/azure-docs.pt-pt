---
title: Tutorial de criação da infra-estrutura para um recurso de infraestrutura do serviço de cluster em VMs do Azure - Azure Service Fabric | Documentos da Microsoft
description: Neste tutorial, saiba como configurar a infraestrutura de VM do Azure para executar um cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: v-vasuke
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/25/2019
ms.author: v-vasuke
ms.custom: mvc
ms.openlocfilehash: b5f2f77b3caed483aed1736bd510096d44329284
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276007"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Tutorial: Criar a infraestrutura de VM do Azure para alojar um cluster do Service Fabric

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Esta série de tutoriais, criar um cluster autónomo alojado em VMs do Azure e instalar uma aplicação no mesmo.

Este tutorial é a primeira parte de uma série. Neste artigo, vai gerar os recursos de VM do Azure necessários para alojar o seu cluster autónomo do Service Fabric. Em artigos futuros, terá de instalar o conjunto autónomo do Service Fabric, instalar uma aplicação de exemplo no cluster e, por fim, limpar o cluster.

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar um conjunto de instâncias de AzureVM
> * Modificar o grupo de segurança
> * Iniciar sessão numa das instâncias
> * Preparar a instância do Service Fabric

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure.  Se ainda não tiver uma conta, vá para o [portal do Azure](https://portal.azure.com) para criar um.

## <a name="create-azure-virtual-machine-instances"></a>Criar instâncias de Máquina Virtual do Azure

1. Inicie sessão no portal do Azure e selecione **máquinas virtuais** (não máquinas virtuais (clássico)).

   ![Portal do Azure VM][az-console]

2. Selecione o **Add** botão, que abrirá a **criar uma máquina virtual** formulário.

3. Na **Noções básicas** separador, certifique-se de que escolher o grupo de recursos e subscrição que pretende (utilizando um novo grupo de recursos é recomendado).

4. Alteração da **imagem** escreva para **do Windows Server 2016 Datacenter**. 
 
5. Alterar a instância **tamanho** ao **Standard DS2 v2**. Definir administrador **nome de utilizador** e **palavra-passe**, observar o que são.

6. Deixe o **regras de porta de entrada** bloqueado por enquanto; vamos configurar aqueles na próxima seção.

7. Na **Networking** separador, crie uma nova **rede Virtual** e tome nota do nome.

8. Em seguida, configure as **grupo de segurança de rede NIC** ao **avançadas**. Criar um novo grupo de segurança, observar seu nome e crie as seguintes regras para permitir o tráfego TCP de qualquer origem:

   ![sf-inbound][sf-inbound]

   * Porta `3389`, para RDP e ICMP (conectividade básica).
   * Portas `19000-19003`, para o Service Fabric.
   * Portas `19080-19081`, para o Service Fabric.
   * Porta `8080`, solicitações de navegador da web.

   > [!TIP]
   > Para ligar as suas máquinas virtuais entre si no Service Fabric, as VMs que alojam a infraestrutura precisam de ter as mesmas credenciais.  Existem duas formas comuns de obter credenciais consistentes: associá-las a todas ao mesmo domínio ou definir a mesma palavra-passe de administrador em cada VM. Felizmente, o Azure permite todas as máquinas virtuais no mesmo **rede Virtual** ligar facilmente, por isso, podemos será-se de que tem todas as nossas instâncias na mesma rede.

9. Adicione outra regra. Definir a origem seja **etiquetas de serviço** e defina a etiqueta de serviço de origem como **VirtualNetwork**. Service Fabric requer as seguintes portas estejam abertas para comunicação dentro do cluster: 135,137-139,445,20001-20031,20606-20861.

   ![vnet-inbound][vnet-inbound]

10. O resto das opções são aceitáveis no respetivo estado predefinido. Reveja-os se assim o desejar e, em seguida, inicie sua máquina virtual.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Criação de mais instâncias para o seu cluster do Service Fabric

Iniciar mais dois **máquinas virtuais**, certificando-se de manter as mesmas definições descritas na secção anterior. Particularmente, manter o mesmo nome de utilizador de administrador e a palavra-passe. O **rede Virtual** e **grupo de segurança de rede NIC** não deverá ser recriada; selecione aqueles que já criou, no menu pendente. Pode demorar alguns minutos para cada uma das suas instâncias para ser implantado.

## <a name="connect-to-your-instances"></a>Ligar às instâncias de

1. Selecione uma das suas instâncias do **Máquina Virtual** secção.

2. Na **descrição geral** separador, tome nota do *privada* endereço IP. Em seguida, clique em **Connect**.

3. Na **RDP** separador, tenha em atenção que estamos a utilizar o endereço IP público e porta 3389, que podemos especificamente abertos anteriormente. Transfira o ficheiro RDP.
 
4. Abra o ficheiro RDP e quando lhe for pedido introduza o nome de utilizador e palavra-passe que forneceu na configuração da VM.

5. Assim que estiver ligado a uma instância, terá de validar que registo remoto estava em execução, ativar o SMB e abrir as portas necessários para SMB e de registo remoto.

   Para ativar o SMB, este é o comando do PowerShell:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. Para abrir as portas na firewall, o comando do PowerShell é:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Repita este processo para as outras instâncias, observar novamente os endereços IP privados.

## <a name="verify-your-settings"></a>Verificar as definições

1. Para validar a conectividade básica, ligar a uma das VMs através de RDP.

2. Abra o **linha de comandos** de dentro da VM, em seguida, utilize o comando ping para ligar a partir de uma VM para outra, substituindo o abaixo de endereço IP com um IP privado de endereços que anotou anteriormente (não o IP da VM está ligado a já).

   ```
   ping 172.31.20.163
   ```

   Se o resultado for semelhante a `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` repetido quatro vezes, a ligação entre as instâncias está a funcionar.

3. Valide agora que a partilha SMB funciona com o seguinte comando:

   ```
   net use * \\172.31.20.163\c$
   ```

   Deverá devolver `Drive Z: is now connected to \\172.31.20.163\c$.` como resultado.


   Agora as suas instâncias são devidamente preparadas para o Service Fabric.

## <a name="next-steps"></a>Passos Seguintes

Na primeira parte da série, aprendeu a iniciar três instâncias de VM do Azure e obtê-los configurado para a instalação do Service Fabric:

> [!div class="checklist"]
> * Criar um conjunto de instâncias de VM do Azure
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
