---
title: 'Tutorial: Inicialize hardware - Azure FXT Edge Filer'
description: Aprenda a ligar-se ao nó de hardware e desafine uma palavra-passe inicial nos nós do Azure FXT Edge Filer.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 4fae7795cdd61e3eb465285163bcd6c0dd9f7db2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88184929"
---
# <a name="tutorial-set-hardware-passwords"></a>Tutorial: Definir senhas de hardware

A primeira vez que ligar um nó de Ficheiro de Borda Azure FXT, tem de definir uma palavra-passe de raiz. Os nós de hardware não são enviados com uma senha padrão. 

As portas da rede são desativadas até que a palavra-passe seja definida e o utilizador raiz se insi.000.

Faça este passo depois de instalar e colocar o nó, mas antes de tentar criar o cluster. 

Este tutorial explica como ligar ao nó de hardware e definir a palavra-passe. 

Neste tutorial, vai aprender a: 

> [!div class="checklist"]
> * Ligue um teclado e monitor ao nó e ligue-o
> * Definir palavras-passe para a porta iDRAC e utilizador de raiz neste nó
> * Inscreva-se como raiz 

Repita estes passos para cada nó que utilizará no seu agrupamento. 

Este tutorial leva aproximadamente 15 minutos para ser concluído. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, complete estes passos: 

* [Instale](fxt-install.md) cada nó de Filete de Borda Azure FXT num rack de equipamento e prenda os cabos de alimentação e o acesso à rede, conforme descrito no [tutorial anterior](fxt-network-power.md). 
* Encontre um teclado ligado a USB e um monitor ligado à VGA que pode ligar aos nós de hardware. (A porta de série do nó está inativa antes de definir a palavra-passe.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Ligue um teclado e monitor ao nó

Ligue fisicamente um monitor e um teclado ao nó Azure FXT Edge Filer. 

* Ligue o monitor à porta VGA.
* Ligue o teclado a uma das portas USB. 

Utilize este diagrama de referência para localizar as portas na parte de trás do chassis. 

> [!NOTE]
> A porta em série está inativa até que a palavra-passe esteja definida. 

![diagrama de parte de trás do Azure FXT Edge Filer com portas em série, VGA e USB etiquetadas](media/fxt-back-serial-vga-usb.png)

Pode utilizar um interruptor KVM se quiser ligar mais do que um nó aos mesmos periféricos. 

Ligue o nó premindo o botão de alimentação na parte frontal. 

![diagrama da frente do Azure FXT Edge Filer - botão de potência redondo é rotulado perto do direito superior](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Definir senhas iniciais 

O nó de Filer de Borda Azure FXT irá imprimir várias mensagens no monitor durante o arranque. Após alguns momentos, mostra um ecrã de configuração inicial como este:

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

  Esta palavra-passe mudará quando criar um cluster utilizando este nó, ou quando adicionar este nó ao cluster. A palavra-passe de gestão do cluster (associada ao ``admin`` utilizador) é também a palavra-passe de raiz para todos os nós num cluster.

* É a senha de longo prazo para a porta de gestão de hardware iDRAC/IPMI.

  Certifique-se de que se lembra da palavra-passe no caso de precisar de iniciar sessão com o IPMI mais tarde para resolver um problema de hardware.

Insira e confirme a senha: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Depois de introduzir a palavra-passe, o sistema continua a arrancar. Quando termina, dá uma ``login:`` indicação. 

## <a name="sign-in-as-root"></a>Inscreva-se como raiz

Inscreva-se como ``root`` com a senha que acabou de definir. 

```
login: root
Password:**********
```

Depois de iniciar sina como raiz, as portas de rede estão ativas e entrarão em contacto com o servidor DHCP para obter endereços IP. 

## <a name="next-steps"></a>Passos seguintes

O nó está pronto para fazer parte de um aglomerado. Pode usá-lo para criar o cluster Azure FXT Edge Filer, ou pode [adicioná-lo a um cluster existente.](fxt-add-nodes.md) 

> [!div class="nextstepaction"]
> [Criar um cluster](fxt-cluster-create.md)
