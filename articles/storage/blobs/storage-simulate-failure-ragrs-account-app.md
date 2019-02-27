---
title: 'Tutorial: Simular uma falha ao aceder ao armazenamento redundante com acesso de leitura no Azure | Microsoft Docs'
description: Simular um erro ao aceder ao armazenamento georedundante com acesso de leitura
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.openlocfilehash: 0cbb4d2bc6449dc1cf12a374085b429743224995
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872884"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Tutorial: Simular uma falha ao aceder ao armazenamento redundante com acesso de leitura

Este tutorial é a segunda parte de uma série. Nela, ficará a conhecer as vantagens de um [acesso de leitura georredundante](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) mediante a simulação de uma falha.

Para simular uma falha, pode usar [Fiddler](#simulate-a-failure-with-fiddler) ou [encaminhamento estático](#simulate-a-failure-with-an-invalid-static-route). Qualquer um dos métodos irá permitir simular a falha de pedidos para o ponto final primário da sua [acesso de leitura georredundante](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) conta de armazenamento (RA-GRS), fazendo com que o aplicativo ler a partir do ponto final secundário em vez disso.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Executar e colocar em pausa a aplicação
> * Simular uma falha com o [fiddler](#simulate-a-failure-with-fiddler) ou [uma rota estática inválida](#simulate-a-failure-with-an-invalid-static-route) 
> * Simular o restauro do ponto final primário

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, conclua o tutorial anterior: [Tornar os dados da aplicação de elevada disponibilidade com armazenamento do Azure][previous-tutorial].

Para simular uma falha com o Fiddler: 

* Transferir e [instalar o Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-fiddler"></a>Simular uma falha com o Fiddler

Para simular falha com o Fiddler, insira uma resposta com falhas de pedidos para o ponto final primário da sua conta de armazenamento RA-GRS.

As secções seguintes descrever como simular uma falha e o restauro de ponto final primário com o fiddler.

### <a name="launch-fiddler"></a>Iniciar o fiddler

Abra o Fiddler, selecione **Regras** e **Personalizar Regras**.

![Personalizar regras do Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

O Fiddler scripteditor é é iniciado e exibe os **Samplerules** ficheiro. Este ficheiro é utilizado para personalizar o Fiddler.

Cole o seguinte exemplo de código na função `OnBeforeResponse`. O novo código é comentado para garantir que a lógica criada não é implementada imediatamente.

Quando tiver terminado, selecione **arquivo** e **guardar** para guardar as alterações.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![Colar regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="interrupting-the-application"></a>Interromper a aplicação

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python e Java v7](#tab/dotnet-python-java-v7)

Execute a aplicação no seu IDE ou o shell.

Assim que a aplicação começar a ler do ponto final primário, prima **qualquer tecla** da janela da consola para colocar a aplicação em pausa.

![Aplicação de cenário](media/storage-simulate-failure-ragrs-account-app/scenario.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Execute a aplicação no seu IDE ou o shell.

Uma vez que controla o exemplo, não terá de interrompê-la para simular uma falha. Apenas Certifique-se de que o ficheiro foi carregado para a sua conta de armazenamento ao executar o exemplo e introduzir **P**.

![Aplicação de cenário](media/storage-simulate-failure-ragrs-account-app/Java-put-list-output.png)

---

### <a name="simulate-failure"></a>Simular falha

Enquanto o aplicativo estiver em pausa, anule os comentários a regra personalizada que foi guardada no Fiddler.

O código de exemplo procura pedidos para a conta de armazenamento RA-GRS e, se o caminho contém o nome do ficheiro `HelloWorld`, devolve um código de resposta de `503 - Service Unavailable`.

Navegue para o Fiddler e selecione **Regras** -> **Personalizar Regras...**.

Anule os comentários as seguintes linhas, substitua `STORAGEACCOUNTNAME` com o nome da conta de armazenamento. Selecione **Ficheiro** -> **Guardar** para guardar as alterações. 

> [!NOTE]
> Se estiver a executar a aplicação de exemplo no Linux, tem de reiniciar o Fiddler sempre que edita o ficheiro **CustomRule.js**, para que o Fiddler instale a lógica personalizada.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python e Java v7](#tab/dotnet-python-java-v7)

Para retomar a aplicação, prima **qualquer tecla**.

Assim que a aplicação começar a ser novamente executada, os pedidos para o ponto final primário começam a falhar. A aplicação tenta restabelecer a ligação ao ponto final primário 5 vezes. Após o limiar de cinco tentativas falhadas, solicita a imagem do ponto final secundário só de leitura. Quando a aplicação com êxito obtém a imagem 20 vezes do ponto final secundário irá tentar ligar ao ponto final primário. Se o ponto final primário continuar inacessível, a aplicação retoma a leitura a partir do ponto final secundário.

Este padrão é o padrão de [Disjuntor Automático](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) descrito no tutorial anterior.

![Colar regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure3.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Agora que introduziu a falha, introduza **G** para testar a falha.

Ele informa-o que está a utilizar o pipeline secundário em oposição ao pipeline principal.

---

### <a name="simulate-primary-endpoint-restoration"></a>Simular o restauro do ponto final primário

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python e Java v7](#tab/dotnet-python-java-v7)

Com o conjunto de regras personalizadas do Fiddler definido no passo anterior, os pedidos para o ponto final primário falham.

Para simular novamente o funcionamento do ponto final primário, remova a lógica para inserir o erro `503`.

Para colocar a aplicação em pausa, prima **qualquer tecla**.

Navegue para o Fiddler e selecione **Regras** e **Personalizar Regras...**. 

Comente ou remova a lógica personalizada da função `OnBeforeResponse`, mantendo a função predefinida.

Selecione **Ficheiro** e **Guardar** para guardar as alterações.

![Remover regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Quando estiver concluído, prima **qualquer tecla** para retomar a aplicação. A aplicação continua a ler do ponto final primário até atingir 999 leituras.

![Retomar aplicação](media/storage-simulate-failure-ragrs-account-app/figure4.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Com o conjunto de regras personalizadas do Fiddler definido no passo anterior, os pedidos para o ponto final primário falham.

Para simular novamente o funcionamento do ponto final primário, remova a lógica para inserir o erro `503`.

Navegue para o Fiddler e selecione **Regras** e **Personalizar Regras...**.  Comente ou remova a lógica personalizada da função `OnBeforeResponse`, mantendo a função predefinida.

Selecione **Ficheiro** e **Guardar** para guardar as alterações.

Quando terminar, introduza **G** para testar o download. A aplicação irá reportar que está agora a utilizar o pipeline principal novamente.

---

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simular uma falha com uma rota estática inválida

Pode criar uma rota estática inválida para todos os pedidos para o ponto final primário da sua conta de armazenamento [georredundante com acesso de leitura](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS). Neste tutorial, o anfitrião local é utilizado como o gateway de encaminhamento de pedidos para a conta de armazenamento. A utilização do anfitrião local como o gateway faz com que todos os pedidos para o ponto final primário da conta de armazenamento sejam redirecionados para dentro do anfitrião, o que, posteriormente, origina falhas. Siga os passos seguintes para simular uma falha e o restauro do ponto final primário com uma rota estática inválida. 

### <a name="start-and-pause-the-application"></a>Iniciar e colocar em pausa a aplicação

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python e Java v7](#tab/dotnet-python-java-v7)

Execute a aplicação no seu IDE ou o shell. Assim que a aplicação começar a ler do ponto final primário, prima **qualquer tecla** da janela da consola para colocar a aplicação em pausa.

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Uma vez que controla o exemplo, não terá de interrompê-lo para testar a falha.

Certifique-se de que o ficheiro foi carregado para a sua conta de armazenamento ao executar o exemplo e introduzir **P**.

---

### <a name="simulate-failure"></a>Simular falha

Com a aplicação em pausa, inicie a linha de comandos no Windows como administrador ou execute o terminal como raiz no Linux.

Obtenha informações sobre o domínio de ponto final primário da conta de armazenamento ao introduzir o seguinte comando numa linha de comandos ou terminal.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Substitua `STORAGEACCOUNTNAME` pelo nome de sua conta de armazenamento. Copie o endereço IP da sua conta de armazenamento para um editor de texto, para utilização posterior.

Para obter o endereço IP do anfitrião local, escreva `ipconfig` na linha de comandos do Windows, ou `ifconfig` no terminal do Linux. 

Para adicionar uma rota estática para um anfitrião de destino, escreva o seguinte comando numa linha de comandos do Windows ou no terminal do Linux. 

#### <a name="linux"></a>Linux

`route add <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a>Windows

`route add <destination_ip> <gateway_ip>`

Substitua `<destination_ip>` pelo seu endereço IP da conta de armazenamento, e `<gateway_ip>` pelo seu endereço IP do anfitrião local.

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python e Java v7](#tab/dotnet-python-java-v7)

Para retomar a aplicação, prima **qualquer tecla**.

Assim que a aplicação começar a ser novamente executada, os pedidos para o ponto final primário começam a falhar. A aplicação tenta restabelecer a ligação para o ponto final primário cinco vezes. Após o limiar de cinco tentativas falhadas, solicita a imagem do ponto final secundário só de leitura. Depois de a aplicação obter a imagem 20 vezes com êxito do ponto final secundário, a aplicação tenta ligar ao ponto final primário. Se o ponto final primário continuar inacessível, a aplicação retoma a leitura a partir do ponto final secundário. Este padrão é o padrão de [Disjuntor Automático](/azure/architecture/patterns/circuit-breaker) descrito no tutorial anterior.

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Agora que introduziu a falha, introduza **G** para testar a falha. Ele informa-o que está a utilizar o pipeline secundário em oposição ao pipeline principal.

---

### <a name="simulate-primary-endpoint-restoration"></a>Simular o restauro do ponto final primário

Para simular novamente o funcionamento do ponto final primário, elimine a rota estática do ponto final primário da tabela de encaminhamento. Isto permite que todos os pedidos para o ponto final primário sejam encaminhados através de um gateway predefinido.

Para eliminar a rota estática de um anfitrião de destino, a conta de armazenamento, escreva o seguinte comando numa linha de comandos do Windows ou num terminal do Linux.

#### <a name="linux"></a>Linux

`route del <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a>Windows

`route delete <destination_ip>`

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python e Java v7](#tab/dotnet-python-java-v7)

Prima **qualquer tecla** para retomar a aplicação. A aplicação continua a ler do ponto final primário até atingir 999 leituras.

![Retomar aplicação](media/storage-simulate-failure-ragrs-account-app/figure4.png)


# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Introduza **G** para testar o download. A aplicação irá reportar que está agora a utilizar o pipeline principal novamente.

![Retomar aplicação](media/storage-simulate-failure-ragrs-account-app/java-get-pipeline-example-v10.png)

---

## <a name="next-steps"></a>Passos Seguintes

A parte dois da série, aprendeu sobre como simular uma falha ao testar o armazenamento georredundante de acesso de leitura.

Para saber mais sobre como o armazenamento RA-GRS funciona, bem como os riscos associados, leia o artigo seguinte:

> [!div class="nextstepaction"]
> [Conceber aplicações HA com RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
