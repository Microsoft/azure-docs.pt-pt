---
title: Ligue-se ao seu Azure Percept DK em série
description: Saiba como configurar uma ligação em série ao seu Azure Percept DK com PuTTY e um cabo de série USB a TTL
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 93b8ab0ce53202402e86b059abe3c600590d549e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662920"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>Ligue-se ao seu Azure Percept DK em série

Siga os passos abaixo para configurar uma ligação em série ao seu Azure Percept DK através [da PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

> [!WARNING]
> **NÃO** tente ligar o seu devkit em série, exceto em casos de falha extrema (por exemplo, emparedou o seu dispositivo). Desmontar o invólucro da placa de transporte para ligar o cabo de série é muito difícil e quebrará os cabos de antena Wi-Fi.

## <a name="prerequisites"></a>Pré-requisitos

- [Rio Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- Pc anfitrião
- Azure Percept DK
- [Cabo de série USB para TTL](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="USB para cabo de série TTL.":::

## <a name="initiate-the-serial-connection"></a>Iniciar a ligação em série

1. Se a sua placa de transporte estiver ligada a um carril de 80/20, retire-a do carril utilizando a chave hexadinha (incluída no cartão de boas-vindas devkit).

1. Retire os parafusos na parte inferior do invólucro da placa do porta-malas e extraa a placa-mãe.

    > [!WARNING]
    > A remoção da placa-mãe partirá os cabos da antena Wi-Fi. **NÃO** proceda à ligação em série a não ser que seja o último recurso para recuperar o seu dispositivo.

1. Retire o dissipadores de calor.

1. Retire a placa de salto dos pinos GPIO.

    > [!TIP]
    > Note a orientação da placa de salto antes de a retirar. Por exemplo, desenhe uma seta ou fixe um autocolante à placa de salto que aponta para o circuito para referência. A placa de ponte não está com a chave e pode ser acidentalmente ligada ao contrário quando voltar a montar a sua placa de transporte.

1. Ligue o [cabo de série USB ao TTL](https://www.adafruit.com/product/954) aos pinos GPIO na placa-mãe, como mostrado abaixo. Por favor, note que o fio vermelho não está ligado.

    - Ligue o cabo preto (GND) ao pino 6.
    - Ligue o cabo branco (RX) ao pino 8.
    - Ligue o cabo verde (TX) ao pino 10.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="Ligações de pinos de porta-aviões.":::

1. Ligue o seu devkit e ligue o lado USB do cabo de série ao seu PC.

1. No Windows, aceda às definições **de**  ->  **Atualização do Windows** Ver  ->  **atualizações opcionais** Do  ->  **controlador**. Procure uma atualização serial para USB na lista, verifique a caixa ao lado e clique em **Baixar e Instalar**.  

1. Em seguida, abra o Gestor de Dispositivos do Windows **(Start**  ->  **Device Manager**). Vá a **Ports** e clique **em USB para UART** para abrir **propriedades**. Note a que porta COM o seu dispositivo está ligado.

1. Clique no **separador Definições** de Portas. Certifique-se de que **bits por segundo** está definido para 115200.

1. Abra o PuTTY. Introduza o seguinte e clique **em Abrir** para ligar ao seu devkit via série:

    1. Linha em série: COM[porta #]
    1. Velocidade: 115200
    1. Tipo de ligação: Série

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="Janela de sessão PuTTY com parâmetros de série selecionados.":::

## <a name="next-steps"></a>Passos seguintes

Para atualizar um dispositivo inabitável em série com o [cabo de série USB para TTL,](https://www.adafruit.com/product/954)consulte o guia de atualização USB para situações não padrão.

[comment]: # (Adicione link ao guia de atualização USB quando disponível.)