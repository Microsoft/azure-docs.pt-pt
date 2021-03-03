---
title: Ligue-se ao seu Azure Percept DK sobre sSH
description: Aprenda a SSH no seu Azure Percept DK com PuTTY
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 8d150228be2cf6deff3bc2fd0a0599cca70d24ac
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663017"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Ligue-se ao seu Azure Percept DK sobre sSH

Siga os passos abaixo para configurar uma ligação SSH ao seu Azure Percept DK através [de PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## <a name="prerequisites"></a>Pré-requisitos

- Um computador anfitrião baseado em Windows, Linux ou OS X com capacidade Wi-Fi
- Um cliente SSH
    - Se o computador anfitrião executa o Windows, [o PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) é um cliente SSH eficaz e será utilizado em todo este guia.
    - Se o computador anfitrião executar o Linux ou OS X, os serviços SSH estão incluídos nesses sistemas operativos e podem ser executados sem uma aplicação separada do cliente. Consulte a documentação do produto do seu sistema operativo para obter mais informações sobre como executar os serviços SSH.
- Azure Percept DK

## <a name="initiate-the-ssh-connection"></a>Iniciar a ligação SSH

1. Alimentação no seu Azure Percept DK (dev kit)

1. Se o seu kit dev já estiver ligado a uma rede sobre Ethernet ou Wi-Fi, salte para o passo seguinte. Caso contrário, ligue o computador anfitrião diretamente ao ponto de acesso Wi-Fi do dev, tal como ligar-se a qualquer outra rede Wi-Fi:
    - **nome da rede**: scz-xxxx (onde "xxxx" é os últimos quatro dígitos do endereço de rede MAC do kit dev)
    - **palavra-passe**: pode ser encontrado no Cartão de Boas-Vindas que veio com o kit dev

    > [!WARNING]
    > Enquanto está ligado ao Azure Percept DK Wi-Fi ponto de acesso, o computador anfitrião perderá temporariamente a sua ligação à Internet. Chamadas de videoconferência ativas, streaming web ou outras experiências baseadas em rede serão interrompidas até que o passo 3 da experiência de embarque do Azure Percept DK esteja concluído.

1. Abra o PuTTY. Introduza o seguinte e clique **em Abrir** para SSH no seu devkit:

    1. Nome do anfitrião: 10.1.1.1
    1. Porto: 22
    1. Tipo de ligação: SSH

    > [!NOTE]
    > O **Nome anfitrião** é o endereço IP do seu dispositivo. Se o seu kit dev estiver ligado ao ponto de acesso Wi-Fi do dev, o endereço IP será de 10.1.1.1. Se o seu kit dev estiver ligado ao Ethernet, utilize o endereço IP local do dispositivo, que pode obter do router ou hub Ethernet. Se o seu dispositivo estiver ligado ao Wi-Fi, deve utilizar o endereço IP que foi fornecido durante a [experiência de embarque Azure Percept DK](./quickstart-percept-dk-set-up.md).

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="A imagem.":::

1. Faça login no terminal PuTTY. Se configurar um nome de utilizador SSH e uma palavra-passe durante o OOBE, insira essas credenciais de login quando solicitado. Caso contrário, insira o seguinte:  

    1. login como: raiz
    1. Palavra-passe: p@ssw0rd

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/putty-terminal.png" alt-text="Janela do terminal PuTTY.":::  

## <a name="next-steps"></a>Passos seguintes

Depois de ligar com sucesso ao seu Azure Percept DK através de SSH, poderá executar uma variedade de tarefas, incluindo resolução de problemas, atualizações USB e execução da Ferramenta DiagTool ou SoftAP.


