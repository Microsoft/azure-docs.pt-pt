---
title: Tutorial Criando a infraestrutura para um Cluster Service Fabric em VMs do Azure-Service Fabric do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a configurar a infraestrutura de VM do Azure para executar um Cluster Service Fabric.
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
ms.date: 07/22/2019
ms.author: v-vasuke
ms.custom: mvc
ms.openlocfilehash: c9dd9cf0f0fb6d20d6837b07ab46d376e379ca25
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177721"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Tutorial: criar uma infraestrutura de VM do Azure para hospedar um Cluster Service Fabric

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Nesta série de tutoriais, você cria um cluster autônomo hospedado em VMs do Azure e instala um aplicativo nele.

Este tutorial é a primeira parte de uma série. Neste artigo, você gera os recursos de VM do Azure necessários para hospedar seu cluster autônomo do Service Fabric. Em artigos futuros, terá de instalar o conjunto autónomo do Service Fabric, instalar uma aplicação de exemplo no cluster e, por fim, limpar o cluster.

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar um conjunto de instâncias de AzureVM
> * Modificar o grupo de segurança
> * Iniciar sessão numa das instâncias
> * Preparar a instância do Service Fabric

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure.  Se você ainda não tiver uma conta, vá para o [portal do Azure](https://portal.azure.com) para criar uma.

## <a name="create-azure-virtual-machine-instances"></a>Criar instâncias de máquina virtual do Azure

1. Entre no portal do Azure e selecione **máquinas virtuais** (não máquinas virtuais (clássicas)).

   ![VM portal do Azure][az-console]

2. Selecione o botão **Adicionar** , que abrirá o formulário **criar uma máquina virtual** .

3. Na guia **noções básicas** , certifique-se de escolher a assinatura e o grupo de recursos que deseja (o uso de um novo grupo de recursos é recomendado).

4. Altere o tipo de **imagem** para **Windows Server 2016 datacenter**. 
 
5. Altere o **tamanho** da instância para **Standard DS2 v2**. Defina um **nome de usuário** e uma **senha**de administrador, observando o que eles são.

6. Deixe as **regras de porta de entrada** bloqueadas por enquanto; Iremos configurá-los na próxima seção.

7. Na guia **rede** , crie uma nova **rede virtual** e anote seu nome.

8. Em seguida, defina o **grupo de segurança de rede NIC** como **avançado**. Crie um novo grupo de segurança, observando seu nome e crie as regras a seguir para permitir o tráfego TCP de qualquer fonte:

   ![It-entrada][sf-inbound]

   * Porta `3389`, para RDP e ICMP (conectividade básica).
   * Portas `19000-19003`, por Service Fabric.
   * Portas `19080-19081`, por Service Fabric.
   * Porta `8080`, para solicitações de navegador da Web.

   > [!TIP]
   > Para ligar as suas máquinas virtuais entre si no Service Fabric, as VMs que alojam a infraestrutura precisam de ter as mesmas credenciais.  Existem duas formas comuns de obter credenciais consistentes: associá-las a todas ao mesmo domínio ou definir a mesma palavra-passe de administrador em cada VM. Felizmente, o Azure permite que todas as máquinas virtuais na mesma **rede virtual** se conectem facilmente, portanto, teremos certeza de ter todas as nossas instâncias na mesma rede.

9. Adicione outra regra. Defina a marca origem como **serviço** e defina a marca serviço de origem como **VirtualNetwork**. Service Fabric requer que as seguintes portas sejam abertas para comunicação dentro do cluster: 135137-139, 445, 20001-20031, 20606-20861.

   ![vnet-entrada][vnet-inbound]

10. O restante das opções é aceitável em seu estado padrão. Examine-os se desejar e, em seguida, inicie sua máquina virtual.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Criando mais instâncias para o Cluster Service Fabric

Inicie mais duas **máquinas virtuais**, certificando-se de manter as mesmas configurações descritas na seção anterior. Particularmente, mantenha o mesmo nome de usuário e senha de administrador. A **rede virtual** e o **grupo de segurança de rede NIC** não devem ser recriados; Selecione aqueles que você já criou no menu suspenso. Pode levar alguns minutos para que cada uma de suas instâncias seja implantada.

## <a name="connect-to-your-instances"></a>Conectar-se às suas instâncias

1. Selecione uma das instâncias na seção **máquina virtual** .

2. Na guia **visão geral** , anote o endereço IP *privado* . Em seguida, clique em **conectar**.

3. Na guia **RDP** , observe que estamos usando o endereço IP público e a porta 3389, que abrimos especificamente anteriormente. Baixe o arquivo RDP.
 
4. Abra o arquivo RDP e, quando solicitado, insira o nome de usuário e a senha fornecidos na configuração da VM.

5. Quando estiver conectado a uma instância do, você precisará validar se o registro remoto estava em execução e abrir as portas necessárias.

6. Para abrir as portas na firewall, o comando do PowerShell é:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139
   ```

7. Repita esse processo para suas outras instâncias, novamente observando os endereços IP privados.

## <a name="verify-your-settings"></a>Verifique suas configurações

1. Para validar a conectividade básica, conecte-se a uma das VMs usando o RDP.

2. Abra o **prompt de comando** de dentro dessa VM, em seguida, use o comando ping para se conectar de uma VM para outra, substituindo o endereço IP abaixo por um dos endereços IP privados que você anotou anteriormente (não o IP da VM à qual você está conectado já).

   ```
   ping 172.31.20.163
   ```

   Se o resultado for semelhante a `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` repetido quatro vezes, a ligação entre as instâncias está a funcionar.

   Agora suas instâncias estão adequadamente preparadas para Service Fabric.

## <a name="next-steps"></a>Passos seguintes

Na parte um da série, você aprendeu a iniciar três instâncias de VM do Azure e a configurá-las para a instalação do Service Fabric:

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
