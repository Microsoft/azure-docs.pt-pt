---
title: 'Tutorial: Inicializar hardware - Azure FXT Edge Filer'
description: Como definir uma palavra-passe inicial nos nódosos De Arquivo de Borda Azure FXT
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 8cb5f639deb0630575c46db30efe70ad967324a8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75550896"
---
# <a name="tutorial-set-hardware-passwords"></a>Tutorial: Definir senhas de hardware

A primeira vez que ligar um nó de Filer de Borda Azure FXT, tem de definir uma palavra-passe de raiz. Os nódosos de hardware não são enviados com uma senha padrão. 

As portas de rede são desativadas até que a palavra-passe seja definida e o utilizador da raiz insere-se.

Faça este passo depois de instalar e cabor o nó, mas antes de tentar criar o cluster. 

Este tutorial explica como se conectar ao nó de hardware e definir a palavra-passe. 

Neste tutorial, vai aprender a: 

> [!div class="checklist"]
> * Ligue um teclado e monitor ao nó e ligue-o
> * Detete palavras-passe para a porta iDRAC e utilizador de raiz neste nó
> * Inscreva-se como raiz 

Repita estes passos para cada nó que utilizará no seu cluster. 

Este tutorial leva aproximadamente 15 minutos para ser concluído. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, complete estes passos: 

* [Instale](fxt-install.md) cada nó de Filer de Borda Azure FXT num rack de equipamento e fixe cabos de alimentação e acesso à rede, conforme descrito no [tutorial anterior.](fxt-network-power.md) 
* Encontre um teclado ligado a USB e um monitor ligado à VGA que pode ligar aos nódosos de hardware. (A porta de série do nó está inativa antes de definir a senha.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Ligue um teclado e monitor ao nó

Ligue fisicamente um monitor e teclado ao nó de Ficheiros de Borda Azure FXT. 

* Ligue o monitor à porta VGA.
* Ligue o teclado a uma das portas USB. 

Utilize este diagrama de referência para localizar as portas na parte de trás do chassis. 

> [!NOTE]
> A porta de série está inativa até que a palavra-passe seja definida. 

![diagrama de back of Azure FXT Edge Filer com portas em série, VGA e USB rotuladas](media/fxt-back-serial-vga-usb.png)

Pode utilizar um interruptor KVM se quiser ligar mais do que um nó aos mesmos periféricos. 

Ligar no nó premindo o botão de alimentação na parte da frente. 

![diagrama da frente do Azure FXT Edge Filer - botão de potência redondo é rotulado perto do canto superior direito](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Definir senhas iniciais 

O nó De ficheiro de borda Azure FXT irá imprimir várias mensagens ao monitor durante o arranque. Após alguns momentos, mostra um ecrã de configuração inicial como este:

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

A palavra-passe que introduz é usada para duas coisas: 

* É a senha de raiz temporária para este nó de Filer de Borda Azure FXT. 

  Esta palavra-passe mudará quando criar um cluster utilizando este nó, ou quando adicionar este nó ao cluster. A palavra-passe de gestão ``admin``de cluster (associada ao utilizador) é também a palavra-passe-raiz para todos os nós de um cluster.

* É a senha de longo prazo para a porta de gestão de hardware iDRAC/IPMI.

  Certifique-se de que se lembra da senha caso precise de iniciar sessão com o IPMI mais tarde para resolver um problema de hardware.

Insira e confirme a palavra-passe: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Depois de introduzir a palavra-passe, o sistema continua a arrancar. Quando termina, dá um ``login:`` aviso. 

## <a name="sign-in-as-root"></a>Inscreva-se como raiz

Inscreva-se ``root`` com a senha que acabou de definir. 

```
login: root
Password:**********
```

Depois de iniciar sessão como raiz, as portas de rede estão ativas e entrarão em contacto com o servidor DHCP para endereços IP. 

## <a name="next-steps"></a>Passos seguintes

O nó está pronto para fazer parte de um aglomerado. Pode usá-lo para criar o cluster Azure FXT Edge Filer, ou pode [adicioná-lo a um cluster existente](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Criar um cluster](fxt-cluster-create.md)
