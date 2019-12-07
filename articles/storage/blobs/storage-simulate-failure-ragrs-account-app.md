---
title: Tutorial-simular uma falha na leitura de dados da região primária
titleSuffix: Azure Storage
description: Simule um erro na leitura de dados da região primária quando o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) estiver habilitado para a conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: 44c5d037797d845aa9c68af2d7b8e5e45bf418fb
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892452"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Tutorial: simular uma falha na leitura de dados da região primária

Este tutorial é a segunda parte de uma série. Nele, você aprende os benefícios de um ra-GRS (redundância de [acesso de leitura](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) ) simulando uma falha.

Para simular uma falha, você pode usar o [Roteamento estático](#simulate-a-failure-with-an-invalid-static-route) ou o [Fiddler](#simulate-a-failure-with-fiddler). Os dois métodos permitirão que você simule a falha de solicitações para o ponto de extremidade primário da sua conta de armazenamento com [redundância geográfica com acesso de leitura](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (ra-grs), fazendo com que o aplicativo Leia o ponto de extremidade secundário em vez disso.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Executar e colocar em pausa a aplicação
> * Simular uma falha com [uma rota estática ou um](#simulate-a-failure-with-an-invalid-static-route) [Fiddler](#simulate-a-failure-with-fiddler) inválido
> * Simular o restauro do ponto final primário

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, conclua o tutorial anterior: [tornar os dados do aplicativo altamente disponíveis com o armazenamento do Azure][previous-tutorial].

Para simular uma falha com o roteamento estático, você usará um prompt de comando com privilégios elevados.

Para simular uma falha usando o Fiddler, baixe e [Instale o Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simular uma falha com uma rota estática inválida

Pode criar uma rota estática inválida para todos os pedidos para o ponto final primário da sua conta de armazenamento [georredundante com acesso de leitura](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS). Neste tutorial, o anfitrião local é utilizado como o gateway de encaminhamento de pedidos para a conta de armazenamento. A utilização do anfitrião local como o gateway faz com que todos os pedidos para o ponto final primário da conta de armazenamento sejam redirecionados para dentro do anfitrião, o que, posteriormente, origina falhas. Siga os passos seguintes para simular uma falha e o restauro do ponto final primário com uma rota estática inválida.

### <a name="start-and-pause-the-application"></a>Iniciar e colocar em pausa a aplicação

Use as instruções no [tutorial anterior][previous-tutorial] para iniciar o exemplo e baixar o arquivo de teste, confirmando que ele vem do armazenamento primário. Dependendo da sua plataforma de destino, você pode pausar manualmente o exemplo ou aguardar um prompt.

### <a name="simulate-failure"></a>Simular falha

Enquanto o aplicativo estiver em pausa, abra um prompt de comando no Windows como administrador ou execute o terminal como raiz no Linux.

Obtenha informações sobre o domínio de ponto de extremidade primário da conta de armazenamento digitando o seguinte comando em um prompt de comando ou terminal, substituindo `STORAGEACCOUNTNAME` pelo nome da sua conta de armazenamento.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Copie o endereço IP da sua conta de armazenamento para um editor de texto, para utilização posterior.

Para obter o endereço IP do anfitrião local, escreva `ipconfig` na linha de comandos do Windows, ou `ifconfig` no terminal do Linux.

Para adicionar uma rota estática para um host de destino, digite o seguinte comando em um prompt de comando do Windows ou terminal do Linux, substituindo `<destination_ip>` pelo endereço IP da sua conta de armazenamento e `<gateway_ip>` pelo endereço IP do host local.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

Na janela com o exemplo em execução, retome o aplicativo ou pressione a tecla apropriada para baixar o arquivo de exemplo e confirmar que ele vem do armazenamento secundário. Em seguida, você pode pausar o exemplo novamente ou aguardar o prompt.

### <a name="simulate-primary-endpoint-restoration"></a>Simular o restauro do ponto final primário

Para simular o ponto de extremidade primário se tornando funcional novamente, exclua a rota estática inválida da tabela de roteamento. Isto permite que todos os pedidos para o ponto final primário sejam encaminhados através de um gateway predefinido. Digite o seguinte comando em um prompt de comando do Windows ou terminal do Linux.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

Em seguida, você pode retomar o aplicativo ou pressionar a tecla apropriada para baixar o arquivo de exemplo novamente, desta vez confirmando que ele novamente vem do armazenamento primário.

## <a name="simulate-a-failure-with-fiddler"></a>Simular uma falha com o Fiddler

Para simular uma falha com o Fiddler, você injeta uma resposta com falha para solicitações ao ponto de extremidade primário da sua conta de armazenamento RA-GRS.

As seções a seguir descrevem como simular uma falha e a restauração do ponto de extremidade primário com o Fiddler.

### <a name="launch-fiddler"></a>Iniciar o fiddler

Abra o Fiddler, selecione **Regras** e **Personalizar Regras**.

![Personalizar regras do Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

O Fiddler ScriptEditor inicia e exibe o arquivo **SampleRules. js** . Este ficheiro é utilizado para personalizar o Fiddler.

Cole o exemplo de código a seguir na função `OnBeforeResponse`, substituindo `STORAGEACCOUNTNAME` pelo nome da sua conta de armazenamento. Dependendo do exemplo, talvez você também precise substituir `HelloWorld` pelo nome do arquivo de teste (ou um prefixo, como `sampleFile`) que está sendo baixado. O novo código é comentado para garantir que ele não seja executado imediatamente.

Depois de concluído, selecione **arquivo** e **salvar** para salvar suas alterações. Deixe a janela ScriptEditor aberta para uso nas etapas a seguir.

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

Use as instruções no [tutorial anterior][previous-tutorial] para iniciar o exemplo e baixar o arquivo de teste, confirmando que ele vem do armazenamento primário. Dependendo da sua plataforma de destino, você pode pausar manualmente o exemplo ou aguardar um prompt.

### <a name="simulate-failure"></a>Simular falha

Enquanto o aplicativo estiver em pausa, volte para o Fiddler e remova a marca de comentário da regra personalizada que você salvou na função `OnBeforeResponse`. Certifique-se de selecionar **arquivo** e **salvar** para salvar as alterações para que a regra entre em vigor. Esse código procura solicitações para a conta de armazenamento RA-GRS e, se o caminho contiver o nome do arquivo de exemplo, retornará um código de resposta de `503 - Service Unavailable`.

Na janela com o exemplo em execução, retome o aplicativo ou pressione a tecla apropriada para baixar o arquivo de exemplo e confirmar que ele vem do armazenamento secundário. Em seguida, você pode pausar o exemplo novamente ou aguardar o prompt.

### <a name="simulate-primary-endpoint-restoration"></a>Simular o restauro do ponto final primário

No Fiddler, remova ou comente a regra personalizada novamente. Selecione **arquivo** e **salvar** para garantir que a regra não estará mais em vigor.

Na janela com o exemplo em execução, retome o aplicativo ou pressione a tecla apropriada para baixar o arquivo de exemplo e confirme se ele vem do armazenamento primário mais uma vez. Em seguida, você pode sair do exemplo.

## <a name="next-steps"></a>Passos seguintes

Na parte dois da série, você aprendeu sobre a simulação de uma falha no teste do armazenamento com redundância geográfica com acesso de leitura.

Para saber mais sobre como funciona o armazenamento RA-GRS, bem como seus riscos associados, leia o seguinte artigo:

> [!div class="nextstepaction"]
> [Conceber aplicações HA com RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
