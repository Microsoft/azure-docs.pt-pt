---
title: Tutorial - Simular uma falha na leitura de dados da região primária
titleSuffix: Azure Storage
description: Simular um erro na leitura de dados da região primária quando o armazenamento geo-redundante de acesso de leitura (RA-GRS) estiver ativado para a conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: 3693ae8c9c425c35ab1a4ebf88b6cd3dfb58f92c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061294"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Tutorial: Simular uma falha na leitura de dados da região primária

Este tutorial é a segunda parte de uma série. Nele, aprende-se sobre os benefícios do [armazenamento geo-redundante](../common/storage-redundancy.md) de acesso à leitura (RA-GRS) simulando uma falha.

Para simular uma falha, pode utilizar o [Encaminhamento Estático](#simulate-a-failure-with-an-invalid-static-route) ou [o Violinista](#simulate-a-failure-with-fiddler). Ambos os métodos permitirão simular falhas de pedidos para o ponto final primário da sua conta de armazenamento [geo-redundante](../common/storage-redundancy.md) de acesso de leitura (RA-GRS), fazendo com que a aplicação seja lida a partir do ponto final secundário.

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Executar e colocar em pausa a aplicação
> * Simular uma falha com [uma rota estática inválida](#simulate-a-failure-with-an-invalid-static-route) ou [violinista](#simulate-a-failure-with-fiddler)
> * Simular o restauro do ponto final primário

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, complete o tutorial anterior: Disponibilize os dados da [sua aplicação com armazenamento Azure.][previous-tutorial]

Para simular uma falha com encaminhamento estático, utilizará um pedido de comando elevado.

Para simular uma falha usando o Fiddler, descarregue e [instale o Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simular uma falha com uma rota estática inválida

Pode criar uma rota estática inválida para todos os pedidos para o ponto final primário da sua conta de armazenamento [georredundante com acesso de leitura](../common/storage-redundancy.md) (RA-GRS). Neste tutorial, o anfitrião local é utilizado como o gateway de encaminhamento de pedidos para a conta de armazenamento. A utilização do anfitrião local como o gateway faz com que todos os pedidos para o ponto final primário da conta de armazenamento sejam redirecionados para dentro do anfitrião, o que, posteriormente, origina falhas. Siga os passos seguintes para simular uma falha e o restauro do ponto final primário com uma rota estática inválida.

### <a name="start-and-pause-the-application"></a>Iniciar e colocar em pausa a aplicação

Utilize as instruções no [tutorial anterior][previous-tutorial] para lançar a amostra e descarregar o ficheiro de teste, confirmando que provém do armazenamento primário. Dependendo da sua plataforma-alvo, pode então fazer uma pausa manual na amostra ou esperar a uma velocidade.

### <a name="simulate-failure"></a>Simular falha

Enquanto a aplicação estiver pausada, abra um pedido de comando no Windows como administrador ou executar terminal como raiz no Linux.

Obtenha informações sobre o domínio final primário da conta de armazenamento, inserindo o seguinte comando num comunicado de comando ou terminal, substituindo `STORAGEACCOUNTNAME` o nome da sua conta de armazenamento.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Copie o endereço IP da sua conta de armazenamento para um editor de texto, para utilização posterior.

Para obter o endereço IP do anfitrião local, escreva `ipconfig` na linha de comandos do Windows, ou `ifconfig` no terminal do Linux.

Para adicionar uma rota estática para um anfitrião de destino, escreva `<destination_ip>` o seguinte comando num `<gateway_ip>` comunicado de comando do Windows ou num terminal Linux, substituindo pelo endereço IP da sua conta de armazenamento e pelo seu endereço IP local.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

Na janela com a amostra de execução, retome a aplicação ou pressione a tecla apropriada para descarregar o ficheiro da amostra e confirme que provém de armazenamento secundário. Em seguida, pode fazer uma pausa na amostra novamente ou esperar no aviso.

### <a name="simulate-primary-endpoint-restoration"></a>Simular o restauro do ponto final primário

Para simular o ponto final primário a tornar-se funcional novamente, elimine a rota estática inválida da mesa de encaminhamento. Isto permite que todos os pedidos para o ponto final primário sejam encaminhados através de um gateway predefinido. Digite o seguinte comando num comunicado de comando windows ou num terminal Linux.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

Em seguida, pode retomar a aplicação ou premir a tecla adequada para descarregar novamente o ficheiro da amostra, desta vez confirmando que este vem novamente do armazenamento primário.

## <a name="simulate-a-failure-with-fiddler"></a>Simular uma falha com o Fiddler

Para simular a falha com o Fiddler, injeta uma resposta falhada para pedidos no ponto final primário da sua conta de armazenamento RA-GRS.

As seguintes secções retratam como simular uma falha e restauro de ponto final primário com violinista.

### <a name="launch-fiddler"></a>Iniciar o fiddler

Abra o Fiddler, selecione **Regras** e **Personalizar Regras**.

![Personalizar regras do Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

O Fiddler ScriptEditor lança e exibe o ficheiro **SampleRules.js.** Este ficheiro é utilizado para personalizar o Fiddler.

Colhe a seguinte amostra `OnBeforeResponse` de código `STORAGEACCOUNTNAME` na função, substituindo pelo nome da sua conta de armazenamento. Dependendo da amostra, também poderá `HelloWorld` ser necessário substituir pelo nome do ficheiro `sampleFile`de teste (ou prefixo como) a ser descarregado. O novo código é comentado para garantir que não funciona imediatamente.

Uma vez concluído, selecione **File** e **Guarde** para guardar as suas alterações. Deixe aberta a janela ScriptEditor para utilização nos seguintes passos.

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

![Colar regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="start-and-pause-the-application"></a>Iniciar e colocar em pausa a aplicação

Utilize as instruções no [tutorial anterior][previous-tutorial] para lançar a amostra e descarregar o ficheiro de teste, confirmando que provém do armazenamento primário. Dependendo da sua plataforma-alvo, pode então fazer uma pausa manual na amostra ou esperar a uma velocidade.

### <a name="simulate-failure"></a>Simular falha

Enquanto a aplicação estiver pausada, volte para o Fiddler `OnBeforeResponse` e descomente a regra personalizada que guardou na função. Certifique-se de selecionar **File** e **Guardar** para guardar as suas alterações para que a regra faça efeito. Este código procura pedidos para a conta de armazenamento RA-GRS e, se o caminho `503 - Service Unavailable`contiver o nome do ficheiro da amostra, devolve um código de resposta de .

Na janela com a amostra de execução, retome a aplicação ou pressione a tecla apropriada para descarregar o ficheiro da amostra e confirme que provém de armazenamento secundário. Em seguida, pode fazer uma pausa na amostra novamente ou esperar no aviso.

### <a name="simulate-primary-endpoint-restoration"></a>Simular o restauro do ponto final primário

No Fiddler, remova ou comente novamente a regra personalizada. Selecione **File** and **Save** para garantir que a regra deixará de estar em vigor.

Na janela com a amostra de execução, retome a aplicação ou pressione a tecla apropriada para descarregar o ficheiro da amostra e confirme que vem do armazenamento primário mais uma vez. Pode então sair da amostra.

## <a name="next-steps"></a>Passos seguintes

Na segunda parte da série, aprendeu a simular uma falha no teste de armazenamento geo-redundante.

Para saber mais sobre como funciona o armazenamento RA-GRS, bem como os seus riscos associados, leia o seguinte artigo:

> [!div class="nextstepaction"]
> [Conceber aplicações HA com RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
