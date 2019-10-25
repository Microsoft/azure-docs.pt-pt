---
title: Encaminhador local de rastreamento distribuído do Aplicativo Azure insights OpenCensus (visualização) | Microsoft docs
description: Encaminhe rastreamentos distribuídos do OpenCensus e alcance de linguagens como Python e vá para Aplicativo Azure insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.openlocfilehash: b0d0bc4d711b05dd2206b7437f1f4c7b3444a0c6
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819219"
---
# <a name="local-forwarder-preview"></a>Encaminhador local (visualização)

O encaminhador local é um agente que coleta a telemetria Application Insights ou [OpenCensus](https://opencensus.io/) de uma variedade de SDKs e a encaminha para Application insights. Ele é capaz de ser executado no Windows e no Linux. Você também poderá executá-lo no macOS, mas isso não é oficialmente suportado no momento.

## <a name="running-local-forwarder"></a>Encaminhador local em execução

O encaminhador local é um [projeto de código-fonte aberto no GitHub](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Há várias maneiras de executar o encaminhador local em várias plataformas.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Serviço do Windows

A maneira mais fácil de executar o encaminhador local no Windows é instalá-lo como um serviço do Windows. A versão vem com um executável de serviço do Windows (*WindowsServiceHost/Microsoft. LocalForwarder. WindowsServiceHost. exe*) que pode ser facilmente registrado com o sistema operacional.

> [!NOTE]
> O serviço de encaminhador local requer um mínimo de .NET Framework 4,7. Se você não tiver .NET Framework 4,7, o serviço será instalado, mas não iniciará. Para acessar a versão mais recente do .NET Framework **[visite a página de download do .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** .

1. Baixe a LF. Arquivo WindowsServiceHost. zip da [página de versão do encaminhador local](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) no github.

    ![Captura de tela da página de download da versão do encaminhador local](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. Neste exemplo, para facilitar a demonstração, vamos apenas extrair o arquivo. zip para o caminho `C:\LF-WindowsServiceHost`.

    Para registrar o serviço e configurá-lo para iniciar na inicialização do sistema, execute o seguinte na linha de comando como administrador:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Você deve receber uma resposta de:
    
    `[SC] CreateService SUCCESS`
    
    Para examinar seu novo serviço por meio do tipo de GUI de serviços ``services.msc``
        
     ![Captura de tela do serviço de encaminhador local](./media/opencensus-local-forwarder/002-services.png)

3. **Clique com o botão direito do mouse** no novo encaminhador local e selecione **Iniciar**. Agora, seu serviço entrará em um estado de execução.

4. Por padrão, o serviço é criado sem nenhuma ação de recuperação. Você pode **clicar com o botão direito do mouse** e selecionar **Propriedades** > **recuperação** para configurar respostas automáticas para uma falha de serviço.

    Ou, se preferir definir opções de recuperação automática programaticamente para quando ocorrerem falhas, você poderá usar:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. No mesmo local que o arquivo de ``Microsoft.LocalForwarder.WindowsServiceHost.exe``, que neste exemplo é ``C:\LF-WindowsServiceHost`` há um arquivo chamado ``LocalForwarder.config``. Esse é um arquivo baseado em XML que permite que você ajuste a configuração de seu localforwader e especifique a chave de instrumentação do recurso de Application Insights que você deseja que seus dados de rastreamento distribuído sejam encaminhados. 

    Depois de editar o arquivo de ``LocalForwarder.config`` para adicionar sua chave de instrumentação, certifique-se de reiniciar o **serviço de encaminhador local** para permitir que as alterações entrem em vigor.
    
6. Para confirmar que as configurações desejadas estão em vigor e que o encaminhador local está escutando dados de rastreamento conforme o esperado, verifique o arquivo de ``LocalForwarder.log``. Você deverá ver resultados semelhantes à imagem abaixo na parte inferior do arquivo:

    ![Captura de tela do arquivo LocalForwarder. log](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Aplicativo de console

Para certos casos de uso, pode ser benéfico executar o encaminhador local como um aplicativo de console. A versão vem com as seguintes versões executáveis do host do console:
* um */ConsoleHost/Publish/Microsoft.LocalForwarder.ConsoleHost.dll*binário do .NET Core dependente da estrutura. A execução deste binário requer que um tempo de execução do .NET Core seja instalado; consulte esta [página](https://www.microsoft.com/net/download/dotnet-core/2.1) de download para obter detalhes.
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* um conjunto de binários do .NET Core independente para plataformas x86 e x64. Eles não exigem o tempo de execução do .NET Core para serem executados. */ConsoleHost/Win-x86/Publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/Win-x64/Publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Assim como no Windows, a versão vem com as seguintes versões executáveis do host do console:
* um */ConsoleHost/Publish/Microsoft.LocalForwarder.ConsoleHost.dll*binário do .NET Core dependente da estrutura. A execução deste binário requer que um tempo de execução do .NET Core seja instalado; consulte esta [página](https://www.microsoft.com/net/download/dotnet-core/2.1) de download para obter detalhes.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* um conjunto de binários do .NET Core independente para Linux-64. Este não requer que o tempo de execução do .NET Core seja executado. */ConsoleHost/Linux-x64/Publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Muitos usuários do Linux deverão executar o encaminhador local como um daemon. Os sistemas Linux são fornecidos com uma variedade de soluções para o gerenciamento de serviços, como o Upstart, o SysV ou o sistema. Independentemente de sua versão específica, você pode usá-la para executar o encaminhador local de uma maneira mais apropriada para seu cenário.

Como exemplo, vamos criar um serviço daemon usando o sistema. Usaremos a versão dependente da estrutura, mas a mesma também pode ser feita para uma conta independente.

* Crie o seguinte arquivo de serviço chamado *localforwarder. Service* e coloque-o em */lib/systemd/System*.
Este exemplo pressupõe que seu nome de usuário é SAMPLE_USER e você copiou os binários dependentes da estrutura de encaminhamento local (de */ConsoleHost/Publish*) para */Home/SAMPLE_USER/LOCALFORWARDER_DIR*.

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

* Execute o seguinte comando para instruir o sistema a iniciar o encaminhador local em cada inicialização

```
systemctl enable localforwarder
```

* Execute o seguinte comando para instruir o sistema a iniciar o encaminhador local imediatamente

```
systemctl start localforwarder
```

* Monitore o serviço inspecionando * *. log* arquivos no diretório/home/SAMPLE_USER/LOCALFORWARDER_DIR

### <a name="mac"></a>Mac
O encaminhador local pode funcionar com o macOS, mas atualmente não é suportado oficialmente.

### <a name="self-hosting"></a>Hospedagem interna
O encaminhador local também é distribuído como um .NET Standard pacote NuGet, permitindo que você o hospede dentro de seu próprio aplicativo .NET.

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

## <a name="configuring-local-forwarder"></a>Configurando encaminhador local

* Ao executar um dos próprios hosts do encaminhador local (host do console ou host de serviço do Windows), você encontrará **LocalForwarder. config** posicionado ao lado do binário.
* Ao hospedar internamente o NuGet do encaminhador local, a configuração do mesmo formato deve ser fornecida no código (consulte a seção sobre hospedagem interna). Para obter a sintaxe de configuração, verifique o [LocalForwarder. config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) no repositório do github. 

> [!NOTE]
> A configuração pode mudar de liberação para liberação, portanto, preste atenção para qual versão você está usando.

## <a name="monitoring-local-forwarder"></a>Monitorando encaminhador local

Os rastreamentos são gravados no sistema de arquivos ao lado do executável que executa o encaminhador local (Procure arquivos * *. log* ). Você pode inserir um arquivo com um nome de *NLog. config* ao lado do executável para fornecer sua própria configuração no lugar do padrão. Consulte a [documentação](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) para obter a descrição do formato.

Se nenhum arquivo de configuração for fornecido (que é o padrão), o encaminhador local usará a configuração padrão, que pode ser encontrada [aqui](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>Passos seguintes

* [Abrir censo](https://opencensus.io/)
