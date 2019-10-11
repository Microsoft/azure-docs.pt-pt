---
title: Inicializar o hardware-Microsoft Azure FXT Edge infiler
description: Como definir uma senha inicial nos nós de filer do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 080aa05af77b996bc0eb71287a3dfef25c24629a
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256011"
---
# <a name="tutorial-set-hardware-passwords"></a>Tutorial: definir senhas de hardware

Na primeira vez que você ligar um nó de filer do Azure FXT Edge, deverá definir uma senha raiz. Os nós de hardware não são enviados com uma senha padrão. 

As portas de rede são desabilitadas até que a senha seja definida e o usuário raiz entre.

Siga esta etapa depois de instalar e cabear o nó, mas antes de tentar criar o cluster. 

Este tutorial explica como se conectar ao nó de hardware e definir a senha. 

Neste tutorial, vai aprender a: 

> [!div class="checklist"]
> * Conectar um teclado e um monitor ao nó e ligá-lo
> * Definir senhas para a porta iDRAC e o usuário raiz neste nó
> * Entrar como raiz 

Repita essas etapas para cada nó que você usará em seu cluster. 

Este tutorial leva aproximadamente 15 minutos para ser concluído. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, conclua estas etapas: 

* [Instale](fxt-install.md) cada nó de filer do Azure FXT Edge em um rack de equipamento e conecte os cabos de alimentação e o acesso à rede conforme descrito no [tutorial anterior](fxt-network-power.md). 
* Encontre um teclado conectado por USB e um monitor conectado por VGA que você pode anexar aos nós de hardware. (A porta serial do nó fica inativa antes de você definir a senha.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Conectar um teclado e um monitor ao nó

Conecte fisicamente um monitor e um teclado ao nó Filer do Azure FXT Edge. 

* Conecte o monitor à porta VGA.
* Conecte o teclado a uma das portas USB. 

Use este diagrama de referência para localizar as portas na parte traseira do chassi. 

> [!NOTE]
> A porta serial estará inativa até que a senha seja definida. 

![diagrama de trás do arquivo de borda do Azure FXT com portas serial, VGA e USB rotuladas](media/fxt-back-serial-vga-usb.png)

Você pode usar um comutador KVM se desejar conectar mais de um nó aos mesmos periféricos. 

Ligue o nó pressionando o botão de energia na frente. 

![diagrama da frente do Azure FXT Edge Filer-o botão de energia arredondado é rotulado próximo à parte superior direita](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Definir senhas iniciais 

O nó Filer do Azure FXT Edge imprimirá várias mensagens no monitor durante a inicialização. Depois de alguns instantes, ele mostra uma tela inicial de instalação como esta:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

A senha inserida é usada para duas coisas: 

* É a senha raiz temporária para este nó do Filer do Azure FXT Edge. 

  Essa senha será alterada quando você criar um cluster usando este nó ou quando adicionar esse nó ao cluster. A senha de gerenciamento de cluster (associada ao usuário ``admin``) também é a senha raiz para todos os nós em um cluster.

* É a senha de longo prazo para a porta de gerenciamento de hardware iDRAC/IPMI.

  Lembre-se de lembrar a senha caso você precise entrar com o IPMI mais tarde para solucionar um problema de hardware.

Insira e confirme a senha: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Depois de inserir a senha, o sistema continuará a inicialização. Quando ele é concluído, ele fornece um prompt ``login:``. 

## <a name="sign-in-as-root"></a>Entrar como raiz

Entre como ``root`` com a senha que você acabou de definir. 

```
login: root
Password:**********
```

Depois de entrar como raiz, as portas de rede estarão ativas e entrarão em contato com o servidor DHCP para obter os endereços IP. 

## <a name="next-steps"></a>Passos seguintes

O nó está pronto para ser parte de um cluster. Você pode usá-lo para criar o cluster de arquivos do Azure FXT Edge ou pode [adicioná-lo a um cluster existente](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Criar um cluster](fxt-cluster-create.md)
