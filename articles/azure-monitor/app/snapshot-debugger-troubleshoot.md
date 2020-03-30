---
title: Troubleshoot Azure Application Insights Snapshot Debugger
description: Este artigo apresenta passos e informações de resolução de problemas para ajudar os desenvolvedores que estão com dificuldades em permitir ou usar O Snapshot Debugger da Aplicação Insights.
ms.topic: conceptual
author: brahmnes
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 485f35ed249ab7f6bbb987d8c79afe20287cd25a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671414"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a>Problemas de resolução de problemas que permitem a aplicação Insights Snapshot Debugger ou visualização de instantâneos
Se ativou o Application Insights Snapshot Debugger para a sua aplicação, mas não estiver a ver instantâneos para exceções, pode utilizar estas instruções para resolver problemas. Pode haver muitas razões diferentes para que os instantâneos não sejam gerados. Pode fazer o exame de saúde instantâneo para identificar algumas das possíveis causas comuns.

## <a name="use-the-snapshot-health-check"></a>Use o exame de saúde instantâneo
Vários problemas comuns resultam no Open Debug Snapshot não aparecer. Utilizando um Coletor instantâneo desatualizado, por exemplo; atingir o limite diário de upload; ou talvez a foto esteja apenas a demorar muito tempo a carregar. Utilize o Snapshot Health Check para resolver problemas comuns.

Há um link no painel de exceção da vista de traços de ponta a ponta que o leva ao Snapshot Health Check.

![Insira a verificação de saúde instantânea](./media/snapshot-debugger/enter-snapshot-health-check.png)

A interface interativa e chat-like procura problemas comuns e guia-o a corrigi-los.

![Verificação de Saúde](./media/snapshot-debugger/healthcheck.png)

Se isso não resolver o problema, consulte os seguintes passos manuais de resolução de problemas.

## <a name="verify-the-instrumentation-key"></a>Verifique a chave de instrumentação

Certifique-se de que está a utilizar a chave de instrumentação correta na sua aplicação publicada. Normalmente, a chave de instrumentação é lida a partir do ficheiro ApplicationInsights.config. Verifique se o valor é o mesmo que a chave de instrumentação para o recurso Application Insights que vê no portal.

## <a name="preview-versions-of-net-core"></a>Versões de pré-visualização de .NET Core
Se a aplicação utilizar uma versão de pré-visualização de .NET Core, e o Snapshot Debugger foi ativado através do [painel Deinsights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) de Aplicação no portal, então o Snapshot Debugger pode não começar. Siga as instruções no [Enable Snapshot Debugger para outros ambientes](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) primeiro para incluir o pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet com a aplicação, ***além*** de permitir através do painel de insights da [aplicação](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Upgrade para a versão mais recente do pacote NuGet

Se o Snapshot Debugger foi ativado através do painel de Insights de [Aplicação no portal,](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)então a sua aplicação já deve estar a executar o mais recente pacote NuGet. Se o Snapshot Debugger foi ativado ao incluir o pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet, utilize o NuGet Package Manager do Visual Studio para se certificar de que está a utilizar a versão mais recente do Microsoft.ApplicationInsights.SnapshotCollector. As notas de lançamento podem ser encontradas emhttps://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Verifique os registos do uploader

Após a criação de um instantâneo, um ficheiro de minidump (.dmp) é criado no disco. Um processo de uploader separado cria esse ficheiro de minidump e envia-o, juntamente com quaisquer PDBs associados, para armazenamento de Debugger de Insights de Aplicação. Depois de o minidump ter sido carregado com sucesso, é apagado do disco. Os ficheiros de registo do processo do uploader são mantidos no disco. Num ambiente de Serviço de Aplicações, `D:\Home\LogFiles`pode encontrar estes registos em . Utilize o site de gestão Kudu para o Serviço de Aplicações para encontrar estes ficheiros de registo.

1. Abra a sua aplicação app service no portal Azure.
2. Clique em **Ferramentas Avançadas,** ou procure **Kudu**.
3. Clique em **Ir**.
4. Na caixa de lista seletiva da **consola Debug,** selecione **CMD**.
5. Clique em **Ficheiros .**

Deve ver pelo menos um ficheiro com `Uploader_` `SnapshotUploader_` um `.log` nome que comece com ou com uma extensão. Clique no ícone apropriado para descarregar quaisquer ficheiros de registo ou abra-os num browser.
O nome do ficheiro inclui um sufixo único que identifica a instância do Serviço de Aplicações. Se a sua instância de Serviço de Aplicações estiver alojada em mais de uma máquina, existem ficheiros de registo separados para cada máquina. Quando o carregador deteta um novo ficheiro de mini-dump, está registado no ficheiro de registo. Aqui está um exemplo de um instantâneo bem sucedido e upload:

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
> O exemplo acima é da versão 1.2.0 do pacote Microsoft.ApplicationInsights.SnapshotCollector NuGet. Em versões anteriores, o `MinidumpUploader.exe` processo de uploader é chamado e o registo é menos detalhado.

No exemplo anterior, a chave `c12a605e73c44346a984e00000000000`de instrumentação é . Este valor deve corresponder à chave de instrumentação da sua aplicação.
O minidump está associado a uma `139e411a23934dc0b9ea08a626db16c5`foto com o ID . Pode utilizar este ID mais tarde para localizar a telemetria de exceção associada no Application Insights Analytics.

O carregador procura novos PDBs uma vez a cada 15 minutos. Segue-se um exemplo:

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

Para aplicações que _não estejam_ alojadas no Serviço de Aplicações, os registos `%TEMP%\Dumps\<ikey>` do `<ikey>` carregador estão na mesma pasta que as minilixeiras: (onde está a sua chave de instrumentação).

## <a name="troubleshooting-cloud-services"></a>Serviços de nuvem de resolução de problemas
Para funções em Serviços cloud, a pasta temporária padrão pode ser demasiado pequena para manter os ficheiros de mini-dump, levando a instantâneos perdidos.
O espaço necessário depende do conjunto de trabalho total da sua aplicação e do número de instantâneos simultâneos.
O conjunto de trabalho de uma função web de 32 bits ASP.NET é tipicamente entre 200 MB e 500 MB.
Deixe pelo menos duas fotos simultâneas.
Por exemplo, se a sua aplicação utilizar 1 GB de conjunto de trabalho total, deve certificar-se de que existe pelo menos 2 GB de espaço em disco para armazenar instantâneos.
Siga estes passos para configurar o seu papel de Serviço de Cloud com um recurso local dedicado para instantâneos.

1. Adicione um novo recurso local ao seu Serviço cloud editando o ficheiro de definição de Serviço cloud (.csdef). O exemplo seguinte define `SnapshotStore` um recurso chamado com um tamanho de 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Modifique o código de arranque do seu papel `SnapshotStore` para adicionar uma variável ambiental que aponta para o recurso local. Para funções de trabalhador, o código `OnStart` deve ser adicionado ao método do seu papel:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Para funções web (ASP.NET), o código deve `Application_Start` ser adicionado ao método da sua aplicação web:
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

3. Atualize o ficheiro ApplicationInsights.config do seu papel para anular a localização temporária da pasta utilizada por`SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Sobrepondo a pasta Shadow Copy

Quando o Snapshot Collector começa, tenta encontrar uma pasta no disco que seja adequada para executar o processo snapshot uploader. A pasta escolhida é conhecida como a pasta Shadow Copy.

O Snapshot Collector verifica alguns locais bem conhecidos, certificando-se de que tem permissões para copiar os binários do Uploader Snapshot. São utilizadas as seguintes variáveis ambientais:
- Fabric_Folder_App_Temp
- DADOS LOCALAPP
- APPDATA
- TEMP

Se não for possível encontrar uma pasta adequada, o Snapshot Collector relata um erro que diz _"Não consegui encontrar uma pasta de cópia de sombra adequada"._

Se a cópia falhar, o `ShadowCopyFailed` Snapshot Collector reporta um erro.

Se o carregador não puder ser lançado, `UploaderCannotStartFromShadowCopy` o Snapshot Collector reporta um erro. O corpo da mensagem `System.UnauthorizedAccessException`contém frequentemente. Este erro ocorre geralmente porque a aplicação está a ser feita sob uma conta com permissões reduzidas. A conta tem permissão para escrever para a pasta de cópias de sombra, mas não tem permissão para executar código.

Uma vez que estes erros costumam acontecer durante `ExceptionDuringConnect` o arranque, normalmente serão seguidos por um erro dizendo _"Uploader não começou"._

Para contornar estes erros, pode especificar manualmente a `ShadowCopyFolder` pasta da cópia de sombra através da opção de configuração. Por exemplo, utilizando applicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Ou, se estiver a utilizar as definições de apps.json com uma aplicação .NET Core:

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

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Utilize a pesquisa de Insights de Aplicação para encontrar exceções com instantâneos

Quando um instantâneo é criado, a exceção de arremesso é marcada com um ID instantâneo. Esse ID instantâneo é incluído como uma propriedade personalizada quando a telemetria de exceção é reportada a Application Insights. Utilizando **pesquisa** em Insights de Aplicação, você `ai.snapshot.id` pode encontrar toda a telemetria com a propriedade personalizada.

1. Navegue no seu recurso Application Insights no portal Azure.
2. Clique em **Procurar**.
3. Digite `ai.snapshot.id` na caixa de texto Pesquisar e prima Introduza.

![Pesquisa de telemetria com uma identificação instantânea no portal](./media/snapshot-debugger/search-snapshot-portal.png)

Se esta pesquisa não der resultados, então não foram reportados instantâneos aos Insights de Aplicação para a sua aplicação no intervalo de tempo selecionado.

Para procurar um ID instantâneo específico dos registos do Uploader, digite o ID na caixa de pesquisa. Se não encontrar telemetria para uma foto que sabe que foi carregada, siga estes passos:

1. Verifique duas vezes se está a olhar para o recurso certo da Application Insights, verificando a chave de instrumentação.

2. Utilizando a marca de tempo do registo do carregador, ajuste o filtro time range da procura para cobrir esse intervalo de tempo.

Se ainda não vê uma exceção com o ID instantâneo, então a telemetria de exceção não foi reportada à Aplicação Insights. Esta situação pode acontecer se a sua aplicação se despenhar depois de ter tirado a fotografia, mas antes de reportar a telemetria de exceção. Neste caso, verifique os registos `Diagnose and solve problems` do Serviço de Aplicações para ver se houve reinícios inesperados ou exceções não tratadas.

## <a name="edit-network-proxy-or-firewall-rules"></a>Editar regras de procuração de rede ou firewall

Se a sua aplicação se ligar à Internet através de um proxy ou de uma firewall, poderá ter de editar as regras para permitir que a sua aplicação se comunique com o serviço Snapshot Debugger. Os IPs utilizados pelo Snapshot Debugger estão incluídos na etiqueta de serviço Do Monitor Azure.
