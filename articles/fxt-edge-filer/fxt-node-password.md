---
title: Inicializar o hardware - filtro de borda do Microsoft Azure FXT
description: Como definir uma palavra-passe inicial em nós de ficheiros do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 11cf9f49014648fff1e78aff91c5a724a812e9e7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450297"
---
# <a name="tutorial-set-hardware-passwords"></a>Tutorial: Palavras-passe do conjunto de hardware

Na primeira vez que energia se um nó de filtro de borda de FXT do Azure, tem de definir uma palavra-passe de raiz. Os nós de hardware não são entregues com uma palavra-passe predefinida. 

Portas de rede estão desativadas até depois da palavra-passe é definida e o utilizador raiz inicia sessão.

Efetue este passo após a instalação e cablagem o nó, mas antes de tentar criar o cluster. 

Este tutorial explica como ligar ao nó de hardware e definir a palavra-passe. 

Neste tutorial, vai aprender a: 

> [!div class="checklist"]
> * Ligar um teclado e um monitor para o nó e ligue-o
> * Definir palavras-passe para o utilizador de porta e a raiz do iDRAC neste nó
> * Inicie sessão como raiz 

Repita estes passos para cada nó que irá utilizar no seu cluster. 

Este tutorial demora, aproximadamente 15 minutos a concluir. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, conclua estes passos: 

* [Instale](fxt-install.md) cada nó de filtro de borda do Azure FXT num equipamento em bastidor e ligar os cabos de energia e acesso de rede, conforme descrito no [tutorial anterior](fxt-network-power.md). 
* Encontre um teclado ligados por USB e um monitor VGA ligados que poderão ser anexados a nós de hardware. (A porta do nó serial está inativa antes de definir a palavra-passe.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Ligar um teclado e um monitor para o nó

Ligar fisicamente um monitor e o teclado para o nó de filtro de borda de FXT do Azure. 

* O monitor de ligação para a porta VGA.
* Ligar o teclado a uma das portas USB. 

Utilize este diagrama de referência para localizar as portas no verso do chassi. 

> [!NOTE]
> A porta serial é inativa até depois da palavra-passe está definida. 

![Diagrama de inverso de ficheiros do Azure FXT Edge com série, VGA e o nome de portas USB](media/fxt-back-serial-vga-usb.png)

Pode utilizar um comutador KVM se pretender ligar mais de um nó para os mesmo periféricos. 

Ligar o nó ao premir o botão de energia na frente. 

![Diagrama de início de ficheiros do Azure FXT Edge - arredondar botão de energia é rotulado como perto da parte superior direita](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Conjunto inicial de palavras-passe 

O nó de filtro de borda do Azure FXT imprimirá várias mensagens para o monitor durante a inicialização. Após alguns instantes, mostra um ecrã de configuração inicial como este:

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

A palavra-passe que introduziu é utilizada por duas coisas: 

* É a palavra-passe de raiz temporária para este nó de filtro de borda de FXT do Azure. 

  Esta palavra-passe será alterado quando cria um cluster com este nó ou quando adiciona este nó ao cluster. A palavra-passe de gestão do cluster (associados ao utilizador ``admin``) também é a palavra-passe de raiz para todos os nós num cluster.

* É a palavra-passe longo prazo para a porta de gestão de hardware do iDRAC/IPMI.

  Certifique-se de que não se esqueça da palavra-passe caso seja necessário iniciar sessão com IPMI mais tarde para resolver um problema de hardware.

Introduza e confirme a palavra-passe: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Depois de introduzir a palavra-passe, o sistema continua a arrancar. Quando terminar, ele fornece um ``login:`` prompt. 

## <a name="sign-in-as-root"></a>Inicie sessão como raiz

Inicie sessão como ``root`` com a palavra-passe que acabou de definir. 

```
login: root
Password:**********
```

Depois de iniciar sessão como raiz, as portas de rede estão ativas e irão contactar o servidor DHCP para endereços IP. 

## <a name="next-steps"></a>Passos Seguintes

O nó está pronto para ser parte de um cluster. Pode usá-lo para criar o cluster de ficheiros do Azure FXT Edge, ou pode [adicioná-lo a um cluster existente](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Criar um cluster](fxt-cluster-create.md)
