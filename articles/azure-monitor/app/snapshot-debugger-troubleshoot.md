---
title: Resolver problemas relacionados com o Snapshot Debugger do Azure Application Insights | Documentos da Microsoft
description: Este artigo apresenta os passos de resolução de problemas e informações para ajudar os desenvolvedores que estão a ter problemas ao ativar ou utilizar o Snapshot Debugger do Application Insights.
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: mbullwin
ms.openlocfilehash: bf19d4f5ce60411413c21fce12f9fe9d2f391bf1
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2019
ms.locfileid: "58094944"
---
# <a id="troubleshooting"></a> Resolução de problemas de ativar o Snapshot Debugger do Application Insights ou ver instantâneos
Se ativar o Snapshot Debugger do Application Insights para a sua aplicação, mas não está a ver instantâneos para exceções, pode utilizar estas instruções para resolver. Pode haver muitas razões diferentes, por que os instantâneos não são gerados. Pode executar a verificação de estado de funcionamento do instantâneo para identificar algumas das possíveis causas comuns.

## <a name="use-the-snapshot-health-check"></a>Utilizar a verificação de estado de funcionamento de instantâneo
Vários problemas comuns resultam no instantâneo de depuração aberto não aparecer. Usando um desatualizado Recoletor de instantâneos, por exemplo; atingir o limite de carregamento diário; ou talvez o instantâneo estiver apenas a demorar muito tempo a carregar. Utilize a verificação de estado de funcionamento de instantâneo para resolver problemas comuns.

Existe uma ligação no painel de exceção da vista de rastreamento de ponta a ponta que leva-o para a verificação de estado de funcionamento de instantâneo.

![Introduza a verificação de estado de funcionamento de instantâneo](./media/snapshot-debugger/enter-snapshot-health-check.png)

A interface interativa, como bate-papo procura de problemas comuns e orienta-o para corrigi-los.

![Verificação de estado de funcionamento](./media/snapshot-debugger/healthcheck.png)

Se o que não resolve o problema, consulte, em seguida, o manual seguinte passos de resolução de problemas.

## <a name="verify-the-instrumentation-key"></a>Certifique-se a chave de instrumentação

Certifique-se de que está a utilizar a chave de instrumentação correto no seu aplicativo publicado. Normalmente, a chave de instrumentação é lidos a partir do ficheiro Applicationinsights Config. Certifique-se de que o valor é o mesmo que a chave de instrumentação para o recurso do Application Insights que vê no portal.

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Atualizar para a versão mais recente do pacote NuGet

Se o depurador de instantâneos foi ativada através da [painel do Application Insights no portal do](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), em seguida, seu aplicativo já deve estar em execução o pacote mais recente do NuGet. Se o depurador de instantâneos foi ativado, incluindo o [snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pacote NuGet, Gestor do use o Visual Studio de pacotes de NuGet para se certificar de que está a utilizar a versão mais recente do Snapshotcollector. Notas de versão podem ser encontradas em https://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Verifique os registos de carregador

Depois de um instantâneo é criado, é criado um ficheiro de minidespejo (. dmp) no disco. Um processo separado carregador cria esse arquivo de minidespejo e carrega-, juntamente com quaisquer PDBs associados, para o armazenamento do Snapshot Debugger do Application Insights. Depois do minidespejo tem carregados com êxito, este é eliminado do disco. Os ficheiros de registo para o processo de carregador são mantidos no disco. Num ambiente de serviço de aplicações, pode encontrar estes registos no `D:\Home\LogFiles`. Utilize o site de gestão de Kudu do serviço de aplicações para localizar estes ficheiros de registo.

1. Abra a aplicação do serviço de aplicações no portal do Azure.
2. Clique em **ferramentas avançadas**, ou procure **Kudu**.
3. Clique em **ir**.
4. Na **consola de depuração** caixa de lista pendente, selecione **CMD**.
5. Clique em **LogFiles**.

Deverá ver pelo menos um ficheiro com um nome que começa com `Uploader_` ou `SnapshotUploader_` e um `.log` extensão. Clique no item apropriado para transferir os ficheiros de registo ou abri-los num browser.
O nome de ficheiro inclui um sufixo exclusivo que identifica a instância de serviço de aplicações. Se a sua instância de serviço de aplicações é alojada em mais de uma máquina, existem ficheiros de registo separados para cada máquina. Quando o carregador Deteta um novo arquivo de minidespejo, ele será gravado no arquivo de log. Eis um exemplo de um instantâneo com êxito e o carregamento:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> O exemplo acima é da versão 1.2.0 do pacote NuGet snapshotcollector. Nas versões anteriores, o processo de carregador é chamado `MinidumpUploader.exe` e o registo é menos detalhado.

No exemplo anterior, a chave de instrumentação é `c12a605e73c44346a984e00000000000`. Este valor deve corresponder a chave de instrumentação para a sua aplicação.
O minidespejo está associado um instantâneo com o ID de `139e411a23934dc0b9ea08a626db16c5`. Pode utilizar este ID mais tarde para localizar a telemetria de exceção associada no Application Insights Analytics.

A carregar verifica a existência de novos PDBs sobre uma vez a cada 15 minutos. Segue-se um exemplo:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Para aplicativos que _não estão_ alojado no serviço de aplicações, os registos de carregador estão na mesma pasta que os minidespejos: `%TEMP%\Dumps\<ikey>` (onde `<ikey>` é a chave de instrumentação).

## <a name="troubleshooting-cloud-services"></a>Resolução de problemas de serviços Cloud
Para funções nos serviços Cloud, a pasta temporária do padrão pode ser demasiado pequena para manter os ficheiros de mini-cópia de segurança, que leva a instantâneos perdidos.
O espaço necessário depende do conjunto de trabalho total da sua aplicação e o número de instantâneos em simultâneo.
O conjunto de trabalho de uma função de web ASP.NET de 32 bits é normalmente entre 200 MB e 500 MB.
Permita a, pelo menos, dois instantâneos em simultâneo.
Por exemplo, se a sua aplicação utilizar 1 GB do conjunto de trabalho total, deve Certifique-se de que existe pelo menos 2 GB de espaço em disco para armazenar instantâneos.
Siga estes passos para configurar a sua função de serviço em nuvem com um recurso local dedicado para instantâneos.

1. Adicione um novo recurso local, ao seu serviço Cloud, ao editar o ficheiro de definição (. csdef) do serviço em nuvem. O exemplo seguinte define um recurso chamado `SnapshotStore` com um tamanho de 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Modificar o código de inicialização de sua função para adicionar uma variável de ambiente que aponta para o `SnapshotStore` recursos locais. Para funções de trabalho, o código deve ser adicionado à sua função `OnStart` método:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Para funções de Web (ASP.NET), o código deve ser adicionado ao seu aplicativo web `Application_Start` método:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Atualizar ficheiro applicationinsights. config de sua função para substituir a localização da pasta temporário utilizada por `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Substituir a pasta de cópia de sombra

Quando o Recoletor de instantâneos é iniciado, ele tenta encontrar uma pasta no disco que é adequado para a execução do processo de carregador de instantâneo. A pasta que escolheu é conhecida como a pasta de cópia de sombra.

O Recoletor de instantâneos verifica alguns locais bem conhecidos, certificar-se de que tem permissões para copiar os binários do carregador de instantâneo. As seguintes variáveis de ambiente são utilizadas:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Se não é possível encontrar uma pasta adequada, o Recoletor de instantâneos relatórios a indicar um erro _"Não foi possível localizar uma pasta de cópia de sombra adequado."_

Se falha a cópia, relatórios de Recoletor de instantâneos um `ShadowCopyFailed` erro.

Se o carregador não pode ser inicializado, relatórios de Recoletor de instantâneos um `UploaderCannotStartFromShadowCopy` erro. O corpo da mensagem, muitas vezes, contém `System.UnauthorizedAccessException`. Este erro ocorre normalmente porque o aplicativo for executado sob uma conta com permissões reduzidas. A conta tem permissão para escrever na pasta de cópia de sombra, mas ele não tem permissão para executar o código.

Uma vez que estes erros normalmente ocorrem durante o arranque,, normalmente, irá ser seguidos por um `ExceptionDuringConnect` indicação do erro _"Carregador Falha ao iniciar."_

Para contornar esses erros, pode especificar a pasta de cópia de sombra manualmente por meio do `ShadowCopyFolder` opção de configuração. Por exemplo, usando o applicationinsights. config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Em alternativa, se estiver a utilizar appSettings com uma aplicação .NET Core:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Utilize o Application Insights pesquisa para encontrar exceções com instantâneos

Quando um instantâneo é criado, a exceção throwing é marcada com um ID de instantâneo. Esse ID de instantâneo é incluído como uma propriedade personalizada quando a telemetria de exceção é reportada para o Application Insights. Usando **pesquisa** no Application Insights, pode encontrar toda a telemetria com o `ai.snapshot.id` propriedade personalizada.

1. Navegue para o recurso do Application Insights no portal do Azure.
2. Clique em **Procurar**.
3. Tipo de `ai.snapshot.id` na caixa de texto de pesquisa e prima Enter.

![Procure telemetria com um ID de instantâneo no portal](./media/snapshot-debugger/search-snapshot-portal.png)

Se esta pesquisa devolve os resultados não, não existem instantâneos foram comunicados ao Application Insights para a sua aplicação no intervalo de tempo selecionado.

Para procurar um ID específico de instantâneo dos logs de carregador, escreva esse ID na caixa de pesquisa. Se não conseguir encontrar a telemetria para um instantâneo que sabe que foi carregado, siga estes passos:

1. Verifique novamente que estiver olhando para o recurso do Application Insights correto, verificando a chave de instrumentação.

2. Utilizar o carimbo de hora a partir do registo de carregador, ajuste o filtro de intervalo de tempo da pesquisa para abordar esse intervalo de tempo.

Se ainda não vê uma exceção com esse ID de instantâneo, a telemetria de exceção não foi reportada para o Application Insights. Esta situação pode acontecer se a sua aplicação falhou após levou o instantâneo, mas antes de ele relatado a telemetria de exceção. Neste caso, verifique os registos de serviço de aplicações em `Diagnose and solve problems` para ver se houve reinícios inesperados ou exceções não processadas.

## <a name="edit-network-proxy-or-firewall-rules"></a>Editar regras de firewall ou um proxy de rede

Se seu aplicativo se conecta à Internet através de um proxy ou de uma firewall, terá de editar as regras para permitir que a aplicação comunicar com o serviço de depurador de instantâneos. Eis [uma lista de endereços IP e portas utilizadas pelo Snapshot Debugger](../../azure-monitor/app/ip-addresses.md#snapshot-debugger).
