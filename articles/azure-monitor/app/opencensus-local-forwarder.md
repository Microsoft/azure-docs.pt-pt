---
title: Azure Application Insights OpenCensus avançado local (Pré-visualização)
description: Forward OpenCensus distribuiu vestígios e extensões de línguas como Python e Go to Azure Application Insights
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.openlocfilehash: bcf7ba495897eb1c9b40c78f00825e863390b5d1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669969"
---
# <a name="local-forwarder-preview"></a>Avançado local (Pré-visualização)

O avançado local é um agente que recolhe insights de aplicação ou telemetria [OpenCensus](https://opencensus.io/) de uma variedade de SDKs e o encaminha para Application Insights. É capaz de correr sob o Windows e o Linux. Você também pode ser capaz de executá-lo sob o macOS, mas isso não é oficialmente suportado neste momento.

## <a name="running-local-forwarder"></a>Executando o avançado local

O avançado local é um [projeto de código aberto no GitHub.](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) Há uma variedade de maneiras de executar o avançado local em várias plataformas.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Serviço Windows

A forma mais fácil de executar o avançado local no Windows é instalando-o como um Serviço Windows. O lançamento vem com um Serviço Windows executável *(WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*) que pode ser facilmente registado com o sistema operativo.

> [!NOTE]
> O serviço de avançados local requer um mínimo de .NET Framework 4.7. Se não tiver .NET Framework 4.7 o serviço instalará, mas não arrancará. Para aceder à última versão do .NET Framework **[visite a página de descarregamento .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** .

1. Baixe o LF. Ficheiro WindowsServiceHost.zip da página de lançamento do [avançado local](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) no GitHub.

    ![Screenshot da página de descarregamento de lançamento do avançado local](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. Neste exemplo, para facilitar a demonstração, vamos apenas extrair o ficheiro .zip para o caminho `C:\LF-WindowsServiceHost`.

    Para registar o serviço e configurá-lo para iniciar o arranque do sistema executar o seguinte da linha de comando como Administrador:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Deve receber uma resposta de:
    
    `[SC] CreateService SUCCESS`
    
    Para examinar o seu novo serviço através do tipo de serviços GUI ``services.msc``
        
     ![Screenshot do serviço de avançados locais](./media/opencensus-local-forwarder/002-services.png)

3. **Clique no** novo avançado local e selecione **Start**. O seu serviço entrará agora num estado de gestão.

4. Por padrão, o serviço é criado sem quaisquer ações de recuperação. Pode **clicar e** selecionar **propriedades** > **Recuperação** para configurar respostas automáticas a uma falha de serviço.

    Ou se preferir definir opções de recuperação automática programáticamente para quando ocorrerem falhas, pode utilizar:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. No mesmo local que o seu ficheiro ``Microsoft.LocalForwarder.WindowsServiceHost.exe``, que neste exemplo é ``C:\LF-WindowsServiceHost`` há um ficheiro chamado ``LocalForwarder.config``. Trata-se de um ficheiro baseado em xml que lhe permite ajustar a configuração do seu forwader local e especificar a chave de instrumentação do recurso Application Insights que deseja que os dados de rastreio distribuídos sejam reencaminhados. 

    Depois de editar o ficheiro ``LocalForwarder.config`` para adicionar a sua chave de instrumentação, certifique-se de reiniciar o **Serviço Avançado Local** para permitir que as suas alterações entrem em vigor.
    
6. Para confirmar que as definições desejadas estão em vigor e que o avançado local está a ouvir os dados dos vestígios como esperado, verifique o ficheiro ``LocalForwarder.log``. Deve ver resultados semelhantes à imagem abaixo na parte inferior do ficheiro:

    ![Screenshot do ficheiro LocalForwarder.log](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Aplicação de consola

Para certos casos de utilização, pode ser benéfico executar o avançado local como uma aplicação de consola. O lançamento vem com as seguintes versões executáveis do anfitrião da consola:
* um binário core dependente da estrutura .NET */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Executar este binário requer uma instalação de um prazo de funcionamento .NET Core; consulte esta [página](https://www.microsoft.com/net/download/dotnet-core/2.1) de descarregamento para mais detalhes.
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* um conjunto de binários .NET Core autossuficientes para plataformas x86 e x64. Estes não requerem tempo de execução .NET Core para correr. */ConsoleHost/win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Tal como acontece com o Windows, o lançamento vem com as seguintes versões executáveis do anfitrião da consola:
* um binário core dependente da estrutura .NET */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Executar este binário requer uma instalação de um prazo de funcionamento .NET Core; consulte esta [página](https://www.microsoft.com/net/download/dotnet-core/2.1) de descarregamento para mais detalhes.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* um conjunto de binários de núcleo .NET autossuficientes para linux-64. Este não requer tempo de funcionação .NET Core para correr. */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Muitos utilizadores do Linux vão querer executar o avançado local como um daemon. Os sistemas Linux vêm com uma variedade de soluções para a gestão de serviços, como Upstart, sysv ou sistemad. Qualquer que seja a sua versão em particular, pode usá-la para executar o avançado local de uma forma mais apropriada para o seu cenário.

Como exemplo, vamos criar um serviço daemon usando sistemad. Usaremos a versão dependente da estrutura, mas o mesmo pode ser feito para uma autossuficiente também.

* criar o seguinte ficheiro de serviço denominado *localforwarder.service* e colocá-lo em */lib/sistemad/systemd/system*.
Esta amostra pressupõe que o seu nome de utilizador é SAMPLE_USER e copiou binários dependentes do quadro de avançados locais (de */ConsoleHost/publish)* para */home/SAMPLE_USER/LOCALFORWARDER_DIR*.

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* Executar o seguinte comando para instruir sistematôs para iniciar o avançado local em cada bota

```
systemctl enable localforwarder
```

* Executar o seguinte comando para instruir sistemad para iniciar o avançado local imediatamente

```
systemctl start localforwarder
```

* Monitorize o serviço inspecionando os ficheiros * *.log* no diretório /casa/SAMPLE_USER/LOCALFORWARDER_DIR.

### <a name="mac"></a>Mac
O avançado local pode trabalhar com o macOS, mas atualmente não é apoiado oficialmente.

### <a name="self-hosting"></a>Auto-hospedagem
O avançado local também é distribuído como um pacote .NET Standard NuGet, permitindo-lhe hospedar dentro da sua própria aplicação .NET.

```csharp
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Configurar o avançado local

* Ao executar um dos anfitriões do próprio avançado local (Anfitrião de Consolas ou Anfitrião de Serviço windows), encontrará **LocalForwarder.config** colocado ao lado do binário.
* Ao auto-hospedar o avançado local NuGet, a configuração do mesmo formato deve ser fornecida em código (ver secção sobre auto-hospedagem). Para obter a sintaxe de configuração, consulte o [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) no repositório GitHub. 

> [!NOTE]
> A configuração pode mudar de lançamento para lançamento, por isso preste atenção à versão que está a usar.

## <a name="monitoring-local-forwarder"></a>Monitorização do avançado local

Os vestígios são escritos para o sistema de ficheiros ao lado do executável que executa o avançado local (procure ficheiros de log * *.log).* Pode colocar um ficheiro com um nome de *NLog.config* ao lado do executável para fornecer a sua própria configuração no lugar do padrão. Consulte a [documentação](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) para a descrição do formato.

Se não for fornecido nenhum ficheiro de configuração (que é o padrão), o avançado local utilizará a configuração predefinida, que pode ser encontrada [aqui](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>Passos seguintes

* [Recenseamento aberto](https://opencensus.io/)
