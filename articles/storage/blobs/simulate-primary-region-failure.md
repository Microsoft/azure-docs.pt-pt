---
title: Tutorial - Simular uma falha na leitura de dados da região primária
titleSuffix: Azure Storage
description: Simular um erro na leitura dos dados da região primária quando a conta de armazenamento está configurada para o armazenamento de zonas de acesso de leitura -zona-redundante (RA-GZRS).
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: devx-track-js
ms.openlocfilehash: 5a0bef4fbfd4ee9aa720dab430a33bbfcd0b918b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91280356"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Tutorial: Simular uma falha na leitura dos dados da região primária

Este tutorial é a segunda parte de uma série. Nele, [aprende-se](../common/storage-redundancy.md) os benefícios do armazenamento de risco de acesso de leitura (RA-GZRS) simulando uma falha.

Para simular uma falha, pode utilizar o [encaminhamento estático](#simulate-a-failure-with-an-invalid-static-route) ou [o Violinista](#simulate-a-failure-with-fiddler). Ambos os métodos permitir-lhe-ão simular falhas de pedidos para o ponto final primário da sua conta de armazenamento [geo-redundante](../common/storage-redundancy.md) de acesso à leitura (RA-GZRS), levando a aplicação a ler a partir do ponto final secundário.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Executar e colocar em pausa a aplicação
> * Simular uma falha com [uma rota estática inválida](#simulate-a-failure-with-an-invalid-static-route) ou [violinista](#simulate-a-failure-with-fiddler)
> * Simular o restauro do ponto final primário

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, complete o tutorial anterior: [Torne os dados da sua aplicação altamente disponíveis com armazenamento Azure.][previous-tutorial]

Para simular uma falha com o encaminhamento estático, utilizará uma solicitação de comando elevada.

Para simular uma falha usando o Violinista, descarregue e [instale o Violinista](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simular uma falha com uma rota estática inválida

Pode criar uma rota estática inválida para todos os pedidos para o ponto final primário da sua conta de armazenamento [geo-redundante](../common/storage-redundancy.md) de acesso de leitura (RA-GZRS). Neste tutorial, o anfitrião local é utilizado como o gateway de encaminhamento de pedidos para a conta de armazenamento. A utilização do anfitrião local como o gateway faz com que todos os pedidos para o ponto final primário da conta de armazenamento sejam redirecionados para dentro do anfitrião, o que, posteriormente, origina falhas. Siga os passos seguintes para simular uma falha e o restauro do ponto final primário com uma rota estática inválida.

### <a name="start-and-pause-the-application"></a>Iniciar e colocar em pausa a aplicação

Utilize as instruções no [tutorial anterior][previous-tutorial] para lançar a amostra e descarregar o ficheiro de teste, confirmando que provém do armazenamento primário. Dependendo da plataforma-alvo, pode fazer uma pausa manual da amostra ou aguardar uma ómis.

### <a name="simulate-failure"></a>Simular falha

Enquanto a aplicação é interrompida, abra um pedido de comando no Windows como administrador ou corra o terminal como raiz no Linux.

Obtenha informações sobre o domínio do ponto final primário da conta de armazenamento, introduzindo o seguinte comando numa solicitação de comando ou terminal, `STORAGEACCOUNTNAME` substituindo-o pelo nome da sua conta de armazenamento.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Copie o endereço IP da sua conta de armazenamento para um editor de texto, para utilização posterior.

Para obter o endereço IP do anfitrião local, escreva `ipconfig` na linha de comandos do Windows, ou `ifconfig` no terminal do Linux.

Para adicionar uma rota estática para um anfitrião de destino, digite o seguinte comando num pedido de comando do Windows ou num terminal Linux, `<destination_ip>` substituindo-o pelo endereço IP da sua conta de armazenamento e `<gateway_ip>` pelo endereço IP do anfitrião local.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

Na janela com a amostra de funcionamento, retome a aplicação ou prima a chave apropriada para descarregar o ficheiro da amostra e confirmar que vem do armazenamento secundário. Em seguida, pode fazer uma pausa na amostra novamente ou esperar pelo pedido.

### <a name="simulate-primary-endpoint-restoration"></a>Simular o restauro do ponto final primário

Para simular o ponto final primário tornando-se funcional novamente, elimine a rota estática inválida da tabela de encaminhamento. Isto permite que todos os pedidos para o ponto final primário sejam encaminhados através de um gateway predefinido. Digite o seguinte comando num pedido de comando do Windows ou no terminal Linux.

#### <a name="linux"></a>Linux

```bash
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```console
route delete <destination_ip>
```

Em seguida, pode retomar a aplicação ou premir a tecla apropriada para descarregar novamente o ficheiro da amostra, desta vez confirmando que ele vem novamente do armazenamento primário.

## <a name="simulate-a-failure-with-fiddler"></a>Simular uma falha com o Fiddler

Para simular falhas com o Fiddler, injeta uma resposta falhada para pedidos no ponto final primário da sua conta de armazenamento RA-GZRS.

As secções seguintes retratam como simular uma falha e restauro primário de ponto final com violinista.

### <a name="launch-fiddler"></a>Iniciar o fiddler

Abra o Fiddler, selecione **Regras** e **Personalizar Regras**.

![Personalizar regras do Fiddler](media/simulate-primary-region-failure/figure1.png)

O Fiddler ScriptEditor lança e exibe o ficheiro **SampleRules.js.** Este ficheiro é utilizado para personalizar o Fiddler.

Cole a seguinte amostra de código na `OnBeforeResponse` função, `STORAGEACCOUNTNAME` substituindo-a pelo nome da sua conta de armazenamento. Dependendo da amostra, também poderá ter de ser `HelloWorld` substituído pelo nome do ficheiro de teste (ou por exemplo) a ser `sampleFile` descarregado. O novo código é comentado para garantir que não funciona imediatamente.

Uma vez concluído, selecione **'Ficheiro'** e **Guarde** para guardar as alterações. Deixe a janela ScriptEditor aberta para utilização nos seguintes passos.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![Colar regra personalizada](media/simulate-primary-region-failure/figure2.png)

### <a name="start-and-pause-the-application"></a>Iniciar e colocar em pausa a aplicação

Utilize as instruções no [tutorial anterior][previous-tutorial] para lançar a amostra e descarregar o ficheiro de teste, confirmando que provém do armazenamento primário. Dependendo da plataforma-alvo, pode fazer uma pausa manual da amostra ou aguardar uma ómis.

### <a name="simulate-failure"></a>Simular falha

Enquanto a aplicação estiver interrompida, volte para o Violino e descoduça a regra personalizada que guardou na `OnBeforeResponse` função. Certifique-se de selecionar **File** and **Save** para guardar as suas alterações para que a regra entre em vigor. Este código procura pedidos para a conta de armazenamento RA-GZRS e, se o caminho contiver o nome do ficheiro de amostra, devolve um código de resposta de `503 - Service Unavailable` .

Na janela com a amostra de funcionamento, retome a aplicação ou prima a chave apropriada para descarregar o ficheiro da amostra e confirmar que vem do armazenamento secundário. Em seguida, pode fazer uma pausa na amostra novamente ou esperar pelo pedido.

### <a name="simulate-primary-endpoint-restoration"></a>Simular o restauro do ponto final primário

No Fiddler, remova ou comente novamente a regra personalizada. Selecione **File** and **Save** para garantir que a regra deixará de estar em vigor.

Na janela com a amostra de funcionamento, retome a aplicação ou prima a chave apropriada para descarregar o ficheiro da amostra e confirmar que vem do armazenamento primário novamente. Em seguida, pode sair da amostra.

## <a name="next-steps"></a>Passos seguintes

Na segunda parte da série, aprendeu a simular uma falha em testar o armazenamento geo-redundante de acesso.

Para saber mais sobre como funciona o armazenamento ra-GZRS, bem como os seus riscos associados, leia o seguinte artigo:

> [!div class="nextstepaction"]
> [Projetar aplicativos HA com RA-GZRS](../common/geo-redundant-design.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
