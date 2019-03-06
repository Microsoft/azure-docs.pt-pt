---
title: 'Tutorial: Simular uma falha ao aceder ao armazenamento redundante com acesso de leitura no Azure | Microsoft Docs'
description: Simular um erro ao aceder ao armazenamento georedundante com acesso de leitura
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.openlocfilehash: 0144d68ecfdb1cc3309c462d00fa8f30e66bab34
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441360"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Tutorial: Simular uma falha ao aceder ao armazenamento redundante com acesso de leitura

Este tutorial é a segunda parte de uma série. Nela, ficará a conhecer as vantagens de um [acesso de leitura georredundante](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) mediante a simulação de uma falha.

Para simular uma falha, pode usar [encaminhamento estático](#simulate-a-failure-with-an-invalid-static-route) ou [Fiddler](#simulate-a-failure-with-fiddler). Ambos os métodos, poderá simular a falha de pedidos para o ponto final primário da sua [acesso de leitura georredundante](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) conta de armazenamento (RA-GRS), fazendo com que o aplicativo ler a partir do ponto final secundário em vez disso. 

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Executar e colocar em pausa a aplicação
> * Simular uma falha com [uma rota estática inválida](#simulate-a-failure-with-an-invalid-static-route) ou [Fiddler](#simulate-a-failure-with-fiddler) 
> * Simular o restauro do ponto final primário

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, conclua o tutorial anterior: [Tornar os dados da aplicação de elevada disponibilidade com armazenamento do Azure][previous-tutorial].

Para simular uma falha com o encaminhamento estático, usará uma linha de comandos elevada.

Para simular uma falha com o Fiddler, transfira e [instalar o Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simular uma falha com uma rota estática inválida

Pode criar uma rota estática inválida para todos os pedidos para o ponto final primário da sua conta de armazenamento [georredundante com acesso de leitura](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS). Neste tutorial, o anfitrião local é utilizado como o gateway de encaminhamento de pedidos para a conta de armazenamento. A utilização do anfitrião local como o gateway faz com que todos os pedidos para o ponto final primário da conta de armazenamento sejam redirecionados para dentro do anfitrião, o que, posteriormente, origina falhas. Siga os passos seguintes para simular uma falha e o restauro do ponto final primário com uma rota estática inválida. 

### <a name="start-and-pause-the-application"></a>Iniciar e colocar em pausa a aplicação

Utilize as instruções no [tutorial anterior] [ previous-tutorial] para iniciar o exemplo e transfira o ficheiro de teste, confirmar que são provenientes de armazenamento primário. Dependendo da sua plataforma de destino, pode, em seguida, manualmente o exemplo de colocar em pausa ou esperar numa linha de comandos. 

### <a name="simulate-failure"></a>Simular falha

Enquanto o aplicativo estiver em pausa, abra uma linha de comandos no Windows como administrador ou execute o terminal como raiz no Linux.

Obter informações sobre o domínio de ponto final primário da conta de armazenamento ao introduzir o seguinte comando num comando de linha de comandos ou terminal, substituindo `STORAGEACCOUNTNAME` com o nome da conta de armazenamento.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 

Copie o endereço IP da sua conta de armazenamento para um editor de texto, para utilização posterior.

Para obter o endereço IP do anfitrião local, escreva `ipconfig` na linha de comandos do Windows, ou `ifconfig` no terminal do Linux. 

Para adicionar uma rota estática para um anfitrião de destino, escreva o seguinte comando numa linha de comandos do Windows ou num terminal do Linux, substituindo `<destination_ip>` com o seu endereço IP da conta de armazenamento e `<gateway_ip>` com o seu endereço IP do anfitrião local.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

Na janela com o exemplo em execução, retomar a aplicação ou prima a tecla apropriada para transferir o ficheiro de exemplo e confirme que são provenientes de armazenamento secundário. Pode, em seguida, colocar em pausa a amostra novamente ou aguarde na linha de comandos. 

### <a name="simulate-primary-endpoint-restoration"></a>Simular o restauro do ponto final primário

Para simular o ponto final primário se tornar funcional novamente, elimine a rota estática inválida da tabela de encaminhamento. Isto permite que todos os pedidos para o ponto final primário sejam encaminhados através de um gateway predefinido. Escreva o seguinte comando numa linha de comandos do Windows ou no terminal do Linux.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

Em seguida, pode retomar a aplicação ou prima a tecla apropriada para transferir o exemplo de ficheiro novamente, este tempo, confirmar que são mais uma vez provenientes de armazenamento primário.

## <a name="simulate-a-failure-with-fiddler"></a>Simular uma falha com o Fiddler

Para simular falha com o Fiddler, insira uma resposta com falhas de pedidos para o ponto final primário da sua conta de armazenamento RA-GRS.

As secções seguintes descrever como simular uma falha e o restauro de ponto final primário com o fiddler.

### <a name="launch-fiddler"></a>Iniciar o fiddler

Abra o Fiddler, selecione **Regras** e **Personalizar Regras**.

![Personalizar regras do Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

O Fiddler scripteditor é é iniciado e exibe os **Samplerules** ficheiro. Este ficheiro é utilizado para personalizar o Fiddler.

Cole o seguinte exemplo de código na `OnBeforeResponse` funcione, substituindo `STORAGEACCOUNTNAME` com o nome da conta de armazenamento. Consoante o exemplo, também poderá ter de substituir `HelloWorld` com o nome do ficheiro de teste (ou um prefixo como `sampleFile`) a ser transferido. O novo código é comentado para garantir que não é executado imediatamente.

Quando tiver terminado, selecione **arquivo** e **guardar** para guardar as alterações. Deixe a janela de scripteditor é aberta para utilização nos passos seguintes.

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

Utilize as instruções no [tutorial anterior] [ previous-tutorial] para iniciar o exemplo e transfira o ficheiro de teste, confirmar que são provenientes de armazenamento primário. Dependendo da sua plataforma de destino, pode, em seguida, manualmente o exemplo de colocar em pausa ou esperar numa linha de comandos. 

### <a name="simulate-failure"></a>Simular falha

Enquanto o aplicativo estiver em pausa, mude novamente para o Fiddler e anule os comentários a regra personalizada que guardou no `OnBeforeResponse` função. Verifique se seleciona **arquivo** e **guardar** para guardar as alterações para que a regra entrarão em vigor. Esse código procura pedidos para a conta de armazenamento RA-GRS e, se o caminho contém o nome do ficheiro de exemplo, retorna um código de resposta de `503 - Service Unavailable`.

Na janela com o exemplo em execução, retomar a aplicação ou prima a tecla apropriada para transferir o ficheiro de exemplo e confirme que são provenientes de armazenamento secundário. Pode, em seguida, colocar em pausa a amostra novamente ou aguarde na linha de comandos. 

### <a name="simulate-primary-endpoint-restoration"></a>Simular o restauro do ponto final primário

No Fiddler, remover ou comentar novamente a regra personalizada. Selecione **arquivo** e **guardar** para garantir que a regra já não entrarão em vigor.

Na janela com o exemplo em execução, retomar a aplicação ou prima a tecla apropriada para transferir o ficheiro de exemplo e confirme que são provenientes de armazenamento primário mais uma vez. Pode sair, em seguida, o exemplo. 

## <a name="next-steps"></a>Passos Seguintes

A parte dois da série, aprendeu sobre como simular uma falha ao testar o armazenamento georredundante de acesso de leitura.

Para saber mais sobre como o armazenamento RA-GRS funciona, bem como os riscos associados, leia o artigo seguinte:

> [!div class="nextstepaction"]
> [Conceber aplicações HA com RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
