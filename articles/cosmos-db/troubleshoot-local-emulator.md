---
title: Problemas de resolução de problemas ao usar o emulador Azure Cosmos
description: Aprenda a trabalhar com problemas indisponíveis, certificados, encriptação e versões ao utilizar o emulador Azure Cosmos.
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperfq1
ms.openlocfilehash: af9122aaa0233fe5248f31ffe805e01a98831eae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447426"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-emulator"></a>Problemas de resolução de problemas ao usar o emulador Azure Cosmos

O emulador Azure Cosmos proporciona um ambiente local que imita o serviço DB Azure Cosmos para fins de desenvolvimento. Utilize as dicas deste artigo para ajudar a resolver problemas que encontra ao instalar ou utilizar o Emulador Azure Cosmos. 

Se instalou uma nova versão do emulador e surgirem erros, certifique-se de que repõe os dados. Pode redefinir os seus dados clicando à direita no ícone emulador Azure Cosmos na bandeja do sistema e, em seguida, clicando em Dados de Reset.... Se isso não corrigir os erros, pode desinstalar o emulador e quaisquer versões mais antigas do emulador se for encontrado, remover *C:\Ficheiros do programa\Azure Cosmos DB Emulator* e reinstalar o emulador. Veja [Desinstalar o emulador local](local-emulator.md#uninstall) para obter instruções. Alternativamente, se a reposição dos dados não funcionar, navegue até à `%LOCALAPPDATA%\CosmosDBEmulator` localização e elimine a pasta.

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>Resolução de problemas corrompidos contadores de desempenho de janelas

* Se o emulador Azure Cosmos se despenhar, recolha os ficheiros de despejo da `%LOCALAPPDATA%\CrashDumps` pasta, comprima-os e abra um bilhete de apoio a partir do [portal Azure](https://portal.azure.com).

* Se tiver `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` acidentes, este pode ser um sintoma em que os Contadores de Desempenho estão em estado de corrupção. Normalmente executando o seguinte comando a partir de um pedido de comando de administração corrige o problema:

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>Resolver problemas de conectividade

* Se encontrar um problema de conectividade, [recolha ficheiros](#trace-files)de rastreios, comprima-os e abra um bilhete de apoio no [portal Azure](https://portal.azure.com).

* Se receber uma mensagem de **serviço indisponível**, o emulador poderá estar a falhar ao inicializar a pilha de rede. Verifique se tem as redes de cliente seguro Pulse ou Juniper instaladas, dado que os respetivos controladores de filtro de rede poderão causar o problema. Desinstalar os controladores de filtro de rede de terceiros normalmente corrige o problema. Em alternativa, inicie o emulador com /DisableRIO, que irá mudar a comunicação da rede do emulador para winsock regular. 

* Se encontrar **"Proibido", mensagem":"O pedido está a ser feito com uma encriptação proibida no protocolo de trânsito ou cifra. Consulte a conta SSL/TLS de definição mínima permitida de protocolo..."** problemas de conectividade, que podem ser causados por alterações globais no SISTEMA (por exemplo, Insider Preview Build 20170) ou pelas configurações do navegador que permitem o TLS 1.3 como padrão. Pode ocorrer um erro semelhante ao utilizar o SDK para executar um pedido contra o emulador cosmos, como **Microsoft.Azure.Documents.DocumentClientExcepção: O pedido está a ser feito com uma encriptação proibida no protocolo de trânsito ou cifra. Consulte a conta SSL/TLS definição mínima de protocolo permitido**. Atualmente, este é um problema esperado, uma vez que o emulador do Cosmos só aceita e funciona com o protocolo TLS 1.2. O trabalho-volta recomendado é alterar as definições e o predefinição para TLS 1.2; por exemplo, no IIS Manager navegue para "Sites" - > "Web Sites predefinidos" e localize as "Ligações do Site" para a porta 8081 e edite-os para desativar o TLS 1.3. Pode ser realizada uma operação semelhante no browser através das opções nas “Definições”.

* Enquanto o emulador estiver em execução, se o computador entrar no modo de suspensão ou executar quaisquer atualizações do SO, poderá ver a mensagem **O serviço está indisponível neste momento**. Repor os dados do emulador, clicando à direita no ícone que aparece no tabuleiro de notificação do Windows e selecione **Dados de Reset**.

## <a name="collect-trace-files"></a><a id="trace-files"></a>Recolher ficheiros de rastreio

Para recolher rastreios de depuração, execute os seguintes comandos a partir de uma linha de comandos administrativa:

1. Navegue até ao caminho onde o emulador está instalado:

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. Desligue o emulador e observe a bandeja do sistema para se certificar de que o programa foi desligado. Pode levar um minuto para completar. Também pode selecionar **Saída** na interface de utilizador emulador Azure Cosmos.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. Comece a iniciar sessão com o seguinte comando:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. Lançar o emulador

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. Reproduza o problema. Se o explorador de dados não estiver a funcionar, basta esperar que o navegador abra durante alguns segundos para apanhar o erro.

1. Pare de iniciar sessão com o seguinte comando:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. Navegue pelo `%ProgramFiles%\Azure Cosmos DB Emulator` caminho e encontre o ficheiro *docdbemulator_000001.etl.*

1. Abra um bilhete de apoio no [portal Azure](https://portal.azure.com) e inclua o ficheiro .etl juntamente com os passos de repro.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a depurar problemas com o emulador local. Pode agora passar para os próximos artigos:

* [Exporte os certificados emuladores Azure Cosmos para uso com aplicativos Java, Python e Node.js](local-emulator-export-ssl-certificates.md)
* [Utilize parâmetros de linha de comando e comandos PowerShell para controlar o emulador](emulator-command-line-parameters.md)
