---
title: Ligue-se ao seu Azure Percept DK sobre sSH
description: Aprenda a SSH no seu Azure Percept DK com PuTTY
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39ee1c1cc5b52dc62e3199536234c1f7d9381436
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721482"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Ligue-se ao seu Azure Percept DK sobre sSH

Siga os passos abaixo para configurar uma ligação SSH ao seu Azure Percept DK através de OpenSSH ou [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## <a name="prerequisites"></a>Pré-requisitos

- Um computador anfitrião baseado em Windows, Linux ou OS X com capacidade Wi-Fi
- Um cliente SSH (consulte a secção seguinte para orientação de instalação)
- Um Azure Percept DK (dev kit)
- Um login SSH, criado durante a [experiência de configuração Azure Percept DK](./quickstart-percept-dk-set-up.md)

## <a name="install-your-preferred-ssh-client"></a>Instale o seu cliente SSH preferido

Se o computador anfitrião executar o Linux ou OS X, os serviços SSH estão incluídos nesses sistemas operativos e podem ser executados sem uma aplicação separada do cliente. Consulte a documentação do produto do seu sistema operativo para obter mais informações sobre como executar os serviços SSH.

Se o computador anfitrião executa o Windows, poderá ter duas opções de cliente SSH à escolha: OpenSSH e PuTTY.

### <a name="openssh"></a>OpenSSH

O Windows 10 inclui um cliente SSH incorporado chamado OpenSSH que pode ser executado com um simples comando dentro de uma solicitação de comando. Recomendamos a utilização do OpenSSH com a Azure Percept se estiver disponível para si. Para verificar se o seu computador Windows tem o OpenSSH instalado, siga estes passos:

1. Ir para **iniciar**  ->  **definições**.

1. Selecione **Apps**.

1. No **âmbito de apps & funcionalidades**, selecione **funcionalidades opcionais.**

1. Digite **o Cliente OpenSSH** na barra de pesquisa **de funcionalidades instaladas.** Se o OpenSSH aparecer, o cliente já está instalado e poderá passar para a secção seguinte. Se não vir o OpenSSH, clique em **Adicionar uma funcionalidade**.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="Screenshot de definições que mostram o estado de instalação do OpenSSH.":::

1. Selecione **OpenSSH Client** e clique em **Instalar.** Pode passar para a próxima secção. Se o OpenSSH não estiver disponível para instalar no seu computador, siga os passos abaixo para instalar o PuTTY, um cliente SSH de terceiros.

### <a name="putty"></a>Rio Putty

Se o seu computador Windows não incluir o OpenSSH, recomendamos a utilização [de PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html). Para descarregar e instalar o PuTTY, complete os seguintes passos:

1. Aceda à [página de descarregamento putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. Nos **ficheiros Pacote**, clique no ficheiro de .msi de 32 ou 64 bits para descarregar o instalador. Se não tem a certeza de qual versão escolher, confira as [PERGUNTAS Frequentes](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit).

1. Clique no instalador para iniciar o processo de instalação. Siga as instruções conforme necessário.

1. Parabéns! Instalou com sucesso o cliente Putty SSH.

## <a name="initiate-the-ssh-connection"></a>Iniciar a ligação SSH

1. Ligue o seu Azure Percept DK.

1. Se o seu kit dev já estiver ligado a uma rede sobre Ethernet ou Wi-Fi, salte para o passo seguinte. Caso contrário, ligue o computador anfitrião diretamente ao ponto de acesso Wi-Fi do dev. Como ligar-se a qualquer outra rede Wi-Fi, abrir as definições de rede e internet no seu computador, clicar na seguinte rede e introduzir a palavra-passe de rede quando solicitado:

    - **Nome da rede**: dependendo da versão do sistema operativo do seu dev kit, o nome do ponto de acesso Wi-Fi é **scz-xxxx** ou **apd-xxxx** (onde "xxxx" é os últimos quatro dígitos do endereço MAC do kit dev)
    - **Senha**: pode ser encontrado no Cartão de Boas-Vindas que veio com o kit dev

    > [!WARNING]
    > Enquanto está ligado ao Azure Percept DK Wi-Fi ponto de acesso, o computador anfitrião perderá temporariamente a sua ligação à Internet. Chamadas de videoconferência ativas, streaming web ou outras experiências baseadas em rede serão interrompidas.

1. Complete o processo de ligação SSH de acordo com o seu cliente SSH.

### <a name="using-openssh"></a>Usando openssh

1. Abra uma solicitação de comando **(Pedido de**  ->  **Comando de Início).**

1. Introduza o seguinte na solicitação de comando:

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    Se o computador estiver ligado ao ponto de acesso Wi-Fi do dev, o endereço IP será 10.1.1.1. Se o seu kit dev estiver ligado ao Ethernet, utilize o endereço IP local do dispositivo, que pode obter do router ou hub Ethernet. Se o seu kit dev estiver ligado através do Wi-Fi, deve utilizar o endereço IP que foi atribuído ao seu kit dev durante a experiência de [configuração do Azure Percept DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Se o seu kit dev estiver ligado a uma rede Wi-Fi mas não souber o seu endereço IP, vá ao Azure Percept Studio e [abra o fluxo de vídeo do seu dispositivo](./how-to-view-video-stream.md). A barra de endereço no separador de navegador de transmissão de vídeo mostrará o endereço IP do seu dispositivo.

1. Insira a sua palavra-passe SSH quando solicitado.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="Screenshot do início de instrução do comando Open SSH.":::

1. Se esta for a primeira vez que se liga ao seu kit dev através do OpenSSH, também poderá ser solicitado que aceite a chave do anfitrião. **Insira sim** para aceitar a chave.

1. Parabéns! Você ligou com sucesso ao seu dev kit sobre SSH.

### <a name="using-putty"></a>Usando PuTTY

1. Abra o PuTTY. Introduza o seguinte na janela **de configuração PuTTY** e clique **em Abrir** para SSH no seu kit dev:

    1. Nome do anfitrião: [endereço IP]
    1. Porto: 22
    1. Tipo de ligação: SSH

    O **Nome anfitrião** é o endereço IP do seu kit de dev. Se o computador estiver ligado ao ponto de acesso Wi-Fi do dev, o endereço IP será 10.1.1.1. Se o seu kit dev estiver ligado ao Ethernet, utilize o endereço IP local do dispositivo, que pode obter do router ou hub Ethernet. Se o seu kit dev estiver ligado através do Wi-Fi, deve utilizar o endereço IP que foi atribuído ao seu kit dev durante a experiência de [configuração do Azure Percept DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Se o seu kit dev estiver ligado a uma rede Wi-Fi mas não souber o seu endereço IP, vá ao Azure Percept Studio e [abra o fluxo de vídeo do seu dispositivo](./how-to-view-video-stream.md). A barra de endereço no separador de navegador de transmissão de vídeo mostrará o endereço IP do seu dispositivo.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Screenshot da janela de configuração PuTTY.":::

1. Um terminal PuTTY vai abrir. Quando solicitado, introduza o seu nome de utilizador SSH e a palavra-passe no terminal.

1. Parabéns! Você ligou com sucesso ao seu dev kit sobre SSH.

## <a name="next-steps"></a>Passos seguintes

Depois de ligar ao seu Azure Percept DK através de SSH, poderá executar uma variedade de tarefas, [incluindo resolução](./troubleshoot-dev-kit.md) de problemas do dispositivo e [atualizações USB](./how-to-update-via-usb.md).