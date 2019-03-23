---
title: Tutorial para criar a infraestrutura para um cluster do Service Fabric no AWS - Azure Service Fabric | Microsoft Docs
description: Neste tutorial, vai aprender a configurar a infraestrutura do AWS para executar um cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 9a0c56ecb20857b8fe2f5e55851e5d0d98ed3038
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369122"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>Tutorial: Criar a infraestrutura AWS para alojar um cluster do Service Fabric

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Nesta série de tutoriais, vai criar um cluster autónomo alojado no AWS e instale uma aplicação no mesmo.

Este tutorial é a primeira parte de uma série. Neste artigo, vai gerir os recursos do AWS necessários para alojar o cluster autónomo do Service Fabric. Em artigos futuros, terá de instalar o conjunto autónomo do Service Fabric, instalar uma aplicação de exemplo no cluster e, por fim, limpar o cluster.

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar um conjunto de instâncias do EC2
> * Modificar o grupo de segurança
> * Iniciar sessão numa das instâncias
> * Preparar a instância do Service Fabric

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma conta do AWS.  Se ainda não tiver uma conta, aceda à [consola do AWS](https://aws.amazon.com/) para criar uma.

## <a name="create-ec2-instances"></a>Criar instâncias do EC2

Inicie sessão consola do AWS > introduza **EC2** na caixa de pesquisa > **Servidores Virtuais do EC2 na Cloud**

![Pesquisa na consola do AWS][aws-console]

Selecione **Iniciar Instância** e, no ecrã seguinte, **Selecionar** junto a Microsoft Windows Server 2016 Base.

![Seleção de instâncias do EC2][aws-ec2instance]

Selecione **t2.medium**, em seguida, selecione **seguinte: Configure os detalhes de instância**, no ecrã seguinte alterar o número de instâncias para `3`, em seguida, selecione **Advanced detalhes** para expandir essa secção.

Para ligar as suas máquinas virtuais entre si no Service Fabric, as VMs que alojam a infraestrutura precisam de ter as mesmas credenciais.  Existem duas formas comuns de obter credenciais consistentes: associá-las a todas ao mesmo domínio ou definir a mesma palavra-passe de administrador em cada VM.  Para este tutorial, pode utilizar um script de dados de utilizador para definir as instâncias do EC2 para terem todas a mesma palavra-passe.  Num ambiente de produção, é mais seguro associar os anfitriões a um domínio do Windows.

Introduza o seguinte script no campo de dados de utilizador na consola:

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

Depois de introduzir o script do PowerShell, selecione **Rever e Iniciar**

![Rever e iniciar o EC2][aws-ec2configure2]

No ecrã de revisão, selecione **Iniciar**.  Em seguida, altere o menu pendente para **Continuar sem um par de chaves** e selecione a caixa de verificação que indica que sabe a palavra-passe.

![Seleção do par de chaves do AWS][aws-keypair]

Por fim, selecione **Iniciar Instâncias** e, em seguida, **Ver Instâncias**.  Tem a base para o cluster do Service Fabric criado, agora tem de adicionar algumas configurações finais às instâncias para prepará-las para a configuração do Service Fabric.

## <a name="modify-the-security-group"></a>Modificar o grupo de segurança

O Service Fabric requer um número de portas abertas entre os anfitriões do cluster. Para abrir estas portas na infraestrutura do AWS, selecione uma das instâncias que criou. Em seguida, selecione o nome do grupo de segurança, por exemplo, **launch-wizard-1**. Agora, selecione o separador **Entrada**.

Para evitar abrir estas portas para o mundo, abra-as apenas para anfitriões no mesmo grupo de segurança. Tome nota do ID de grupo de segurança, no exemplo, **sg-c4fb1eba**.  Em seguida, selecione **Editar**.

Adicione quatro regras ao grupo de segurança para as dependências do serviço e, em seguida, mais três para o próprio Service Fabric. A primeira regra é permitir o tráfego de ICMP, para verificações de conectividade básica. As outras regras abrem as portas necessárias para ativar o SMB e o Registo Remoto.

Para a primeira regra, selecione **Adicionar Regra** e, no menu pendente, selecione **Todo o ICMP - IPv4**. Selecione a caixa de entrada junto a Personalizado e introduza o ID do grupo de segurança acima.

Para as últimas três dependências, tem de seguir um processo semelhante.  Selecione **Adicionar Regra**, no menu pendente, selecione **Regra TCP Personalizada** e, no intervalo de portas, introduza `135`, `137-139` ou `445` para cada regra. Por fim, na caixa de origem, introduza o ID do grupo de segurança.

![Portas do grupo de segurança][aws-ec2securityports]

Agora que as portas das dependências estão abertas, tem de fazer o mesmo para as portas que o Service Fabric utiliza para comunicar. Selecione **Adicionar Regra**, no menu pendente, selecione **Regra TCP Personalizada**, no intervalo de portas, introduza `20001-20031` e introduza o grupo de segurança na caixa de origem.

Em seguida, adicione uma regra para o intervalo de portas efémeras.  Selecione **Adicionar Regra**, no menu pendente, selecione **Regra TCP Personalizada** e, no intervalo de portas, introduza `20606-20861`. Por fim, na caixa de origem, introduza o ID do grupo de segurança.

Para as duas regras finais do Service Fabric, abra-o para o mundo para que consiga gerir o cluster do service fabric a partir do seu computador pessoal. Selecione **Adicionar Regra**, no menu pendente, selecione **Regra TCP Personalizada** e, no intervalo de portas, introduza `19000-19003` ou `19080-19081` e altere o menu pendente Origem para Qualquer Local.

Por fim, apenas temos de abrir a porta 8080 para que possa ver a aplicação quando for implementada. Selecione **Adicionar Regra**, no menu pendente, selecione **Regra TCP Personalizada**, no intervalo de portas, introduza `8080` e altere o menu pendente Origem para Qualquer Local.

Todas as regras estão agora introduzidas. Selecione **Guardar**.

## <a name="connect-to-an-instance-and-validate-connectivity"></a>Ligar a uma instância e validar a conectividade

No separador do grupo de segurança, selecione **Instâncias** no menu à esquerda.  Selecione cada uma das instâncias que criou e anote os respetivos endereços IP privados que os exemplos abaixo utilizarão, `172.31.21.141` e `172.31.20.163`.

Depois de ter todos os endereços IP, selecione uma das instâncias à qual ligar, clique com o botão direito do rato na instância e selecione **Ligar**.  Aqui, pode transferir o ficheiro RDP para esta instância específica.  Selecione **Transferir Ficheiro de Ambiente de Trabalho Remoto** e, em seguida, abra o ficheiro transferido para estabelecer a ligação de ambiente de trabalho remoto (RDP) a esta instância.  Quando lhe for pedido, introduza a palavra-passe, `serv1ceF@bricP@ssword`.

![Transferir Ficheiro de Ambiente de Trabalho Remoto][aws-rdp]

Depois de ter ligado com êxito à sua instância, valide que podem ser ligadas entre si, bem como partilhar ficheiros.  Depois de recolher os endereços IP para todas as instâncias, selecione aquele a que não está atualmente ligado. Aceda a **Iniciar**, introduza `cmd` e selecione **Linha de Comandos**.

Nestes exemplos foi estabelecida a ligação de RDP para o seguinte endereço IP: 172.31.21.141. Toda a conectividade de teste, em seguida, ocorrem para outro endereço IP: 172.31.20.163.

Para validar que a conectividade básica funciona, utilize o comando ping.

```
ping 172.31.20.163
```

Se o resultado for semelhante a `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` repetido quatro vezes, a ligação entre as instâncias está a funcionar.  Valide agora que a partilha SMB funciona com o seguinte comando:

```
net use * \\172.31.20.163\c$
```

Deverá devolver `Drive Z: is now connected to \\172.31.20.163\c$.` como resultado.

## <a name="prep-instances-for-service-fabric"></a>Preparar instâncias do Service Fabric

Se for criar a partir do zero, tem de efetuar alguns passos adicionais.  Nomeadamente, tem de validar que o registo remoto estava a ser executado, ativar o SMB e abrir as portas necessárias para o SMB e o registo remoto.

Para facilitar, vai incorporar todo este trabalho quando colocar as instâncias no programa de arranque com o script de dados de utilizador.

Para ativar o SMB, este é o comando do PowerShell utilizado:

```powershell
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

Para abrir as portas na firewall, o comando do PowerShell é:

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
```

## <a name="next-steps"></a>Passos Seguintes

Na primeira parte da série, aprendeu a iniciar três instâncias do EC2 e a configurá-las para a instalação do Service Fabric:

> [!div class="checklist"]
> * Criar um conjunto de instâncias do EC2
> * Modificar o grupo de segurança
> * Iniciar sessão numa das instâncias
> * Preparar a instância do Service Fabric

Avance para a segunda parte da série para configurar o Service Fabric no seu cluster.

> [!div class="nextstepaction"]
> [Instalar o Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png