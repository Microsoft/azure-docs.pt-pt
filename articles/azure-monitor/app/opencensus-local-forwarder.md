---
title: Azure OpenCensus de informações da aplicação distribuída rastreio reencaminhador local (pré-visualização) | Documentos da Microsoft
description: Reencaminhar rastreios OpenCensus distribuído e distribuições de linguagens, como o Python e Go para o Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.author: mbullwin
ms.openlocfilehash: a7efe663a75fa29a31e7157c5eab24c2973a3758
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002816"
---
# <a name="local-forwarder-preview"></a>Reencaminhador local (pré-visualização)

Reencaminhador local é um agente que recolhe Application Insights ou [OpenCensus](https://opencensus.io/) telemetria a partir de vários SDKs e a encaminha para o Application Insights. Ele é capaz de em execução no Windows e Linux. Também poderá executá-lo no macOS, mas que não é oficialmente suportado neste momento.

## <a name="running-local-forwarder"></a>A executar o reencaminhador de local

Reencaminhador local é uma [projeto de código-fonte aberto no GitHub](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Existem diversas formas de executar o reencaminhador de local em várias plataformas.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Serviço do Windows

É a maneira mais fácil das reencaminhador local a ser executado Windows instalando-o como um serviço do Windows. A versão fornecida com um executável de serviço do Windows (*WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*) que podem ser facilmente registado com o sistema operativo.

> [!NOTE]
> O serviço do reencaminhador local requer um mínimo de .NET Framework 4.7. Se não tiver .NET Framework 4.7 o serviço de instalação, mas não iniciará. Para acessar a versão de disponibilidade mais recente do .NET Framework **[visite a página de download do .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)**.

1. Baixe o LF. WindowsServiceHost.zip ficheiro a partir da [página de versão do reencaminhador local](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) no GitHub.

    ![Captura de ecrã da página de transferência de versão do reencaminhador local](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. Neste exemplo para facilitar a demonstração, vamos apenas extrairá o ficheiro. zip para o caminho `C:\LF-WindowsServiceHost`.

    Registe o serviço e configurá-la para iniciar o arranque de sistema, execute o seguinte a partir da linha de comandos como administrador:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Deverá receber uma resposta de:
    
    `[SC] CreateService SUCCESS`
    
    Para examinar seu novo serviço através do tipo de GUI de serviços ``services.msc``
        
     ![Captura de ecrã do serviço do reencaminhador local](./media/opencensus-local-forwarder/002-services.png)

3. **Com o botão direito** o novo reencaminhador local e selecione **iniciar**. O serviço irá agora introduzir um Estado de execução.

4. Por predefinição, o serviço for criado sem quaisquer ações de recuperação. Pode **com o botão direito** e selecione **propriedades** > **recuperação** para configurar respostas automáticas a uma falha de serviço.

    Ou, se preferir definir opções de recuperação automática através de programação para quando ocorrem falhas, pode utilizar:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. Na mesma localização que sua ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` arquivo, que, neste exemplo, é ``C:\LF-WindowsServiceHost`` existe um arquivo chamado ``LocalForwarder.config``. Este é um arquivo xml com base em que lhe permite ajustar a configuração do seu localforwader e especificar a chave de instrumentação do recurso do Application Insights que pretende que os seus dados de rastreio distribuído reencaminhados. 

    Depois de editar a ``LocalForwarder.config`` ficheiro para adicionar a sua chave de instrumentação, certifique-se de que reinicie o **serviço de reencaminhador Local** para permitir que as alterações entrem em vigor.
    
6. Para confirmar que as definições desejadas são cumpridos e que o reencaminhador local está escutando para dados de rastreio como verificação esperado o ``LocalForwarder.log`` ficheiro. Deverá ver resultados semelhantes à imagem abaixo, na parte inferior do ficheiro:

    ![Arquivo de captura de ecrã de LocalForwarder.log](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Aplicação de consola

Para determinados casos de utilização, poderá ser vantajoso ser executado o reencaminhador local como um aplicativo de console. A versão é fornecido com as seguintes versões executáveis do host de console:
* um binário de .NET Core framework dependente */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Executar este binário requer um tempo de execução do .NET Core a serem instalados; consulte este download [página](https://www.microsoft.com/net/download/dotnet-core/2.1) para obter detalhes.
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* um conjunto de .NET Core independente de binários para plataformas x86 e x64. Isso não requer o tempo de execução do .NET Core para executar. */ConsoleHost/win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Tal como acontece com o Windows, a versão é fornecido com as seguintes versões executáveis do host de console:
* um binário de .NET Core framework dependente */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Executar este binário requer um tempo de execução do .NET Core a serem instalados; consulte este download [página](https://www.microsoft.com/net/download/dotnet-core/2.1) para obter detalhes.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* um núcleo de .NET autônomo conjunto de binários para linux-64. Este não necessita de tempo de execução do .NET Core para executar. */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Muitos utilizadores de Linux vão querer executar reencaminhador local como um daemon. Sistemas Linux vem com uma variedade de soluções para a gestão de serviço, como Upstart, divisores ou systemd. Tudo o que é a versão específica, pode usá-lo para executar o reencaminhador de local de forma mais adequada para o seu cenário.

Por exemplo, vamos criar um serviço de daemon com systemd. Vamos utilizar a versão de dependente do framework, mas o mesmo pode ser feito para um autossuficiente também.

* criar o seguinte ficheiro de serviço com o nome *localforwarder.service* e coloque-a na */lib/systemd/system*.
Este exemplo assume que seu nome de utilizador é SAMPLE_USER e ter copiado os binários do framework dependente de reencaminhador local (de */ConsoleHost/publicação*) para */home/SAMPLE_USER/LOCALFORWARDER_DIR*.

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

* Execute o seguinte comando para instruir o systemd para iniciar o reencaminhador de local em cada inicialização

```
systemctl enable localforwarder
```

* Execute o seguinte comando para instruir o systemd para iniciar imediatamente o reencaminhador de local

```
systemctl start localforwarder
```

* Monitorizar o serviço através da inspeção **. log* ficheiros no diretório /home/SAMPLE_USER/LOCALFORWARDER_DIR.

### <a name="mac"></a>Mac
Reencaminhador local pode funcionar com o macOS, mas atualmente não é oficialmente suportado.

### <a name="self-hosting"></a>Hospedagem interna
Reencaminhador local também é distribuído como um pacote de NuGet padrão do .NET, permitindo-lhe hospedá-lo dentro de seu aplicativo .NET.

```C#
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Configurar o reencaminhador de local

* Ao executar um dos anfitriões do reencaminhador local (Host de Console ou Host de serviço do Windows), encontrará **LocalForwarder.config** colocado junto do binário.
* Quando o reencaminhador local NuGet a hospedagem interna, a configuração do mesmo formato tem de ser fornecida no código (consulte a seção sobre hospedagem interna). Para a sintaxe de configuração, consulte a [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) no repositório do GitHub. 

> [!NOTE]
> A configuração pode alterar a partir da versão para versão, por isso, preste atenção ao qual é a versão que está a utilizar.

## <a name="monitoring-local-forwarder"></a>Monitorização local reencaminhador

Rastreios são escritos para o sistema de ficheiros junto ao executável que executa o reencaminhador local (procure **. log* ficheiros). Pode colocar um ficheiro com o nome *NLog.config* junto ao executável para fornecer a sua própria configuração em vez do predefinido. Ver [documentação](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) para a descrição do formato.

Se nenhum ficheiro de configuração não for fornecido (o que é o padrão), o reencaminhador Local irá utilizar a configuração predefinida, que pode ser encontrada [aqui](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>Passos Seguintes

* [Recenseamento aberto](https://opencensus.io/)
