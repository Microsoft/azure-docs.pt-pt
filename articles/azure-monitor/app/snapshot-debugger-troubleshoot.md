---
title: Solucionar problemas com o Aplicativo Azure insights Depurador de Instantâneos | Microsoft Docs
description: Este artigo apresenta as etapas e as informações de solução de problemas para ajudar os desenvolvedores que estão tendo problemas para habilitar ou usar Application Insights Depurador de Instantâneos.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: brahmnes
ms.author: mbullwin
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: ec70f202a496ec368a483278994c7c5ccb24f40b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899832"
---
# <a id="troubleshooting"></a>Solucionar problemas ao habilitar Application Insights Depurador de Instantâneos ou exibir instantâneos
Se você tiver habilitado Application Insights Depurador de Instantâneos para seu aplicativo, mas não estiver vendo instantâneos para exceções, poderá usar estas instruções para solucionar problemas. Pode haver vários motivos diferentes pelos quais os instantâneos não são gerados. Você pode executar a verificação de integridade de instantâneo para identificar algumas das possíveis causas comuns.

## <a name="use-the-snapshot-health-check"></a>Usar a verificação de integridade do instantâneo
Vários problemas comuns resultam na não exibição do instantâneo de depuração aberto. Usando um Snapshot Collector desatualizado, por exemplo; atingindo o limite de upload diário; ou talvez o instantâneo esteja demorando muito tempo para ser carregado. Use a verificação de integridade de instantâneo para solucionar problemas comuns.

Há um link no painel de exceção do modo de exibição de rastreamento de ponta a ponta que leva você para a verificação de integridade do instantâneo.

![Inserir verificação de integridade do instantâneo](./media/snapshot-debugger/enter-snapshot-health-check.png)

A interface interativa, semelhante ao chat, procura problemas comuns e orienta você a corrigi-los.

![Verificação de integridade](./media/snapshot-debugger/healthcheck.png)

Se isso não resolver o problema, consulte as etapas de solução de problemas manuais a seguir.

## <a name="verify-the-instrumentation-key"></a>Verificar a chave de instrumentação

Verifique se você está usando a chave de instrumentação correta em seu aplicativo publicado. Normalmente, a chave de instrumentação é lida no arquivo ApplicationInsights. config. Verifique se o valor é o mesmo que a chave de instrumentação para o recurso de Application Insights que você vê no Portal.

## <a name="preview-versions-of-net-core"></a>Versões prévias do .NET Core
Se o aplicativo usar uma versão de visualização do .NET Core e Depurador de Instantâneos tiver sido habilitado por meio do [painel de Application insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) no portal, depurador de instantâneos talvez não seja iniciado. Siga as instruções em [habilitar depurador de instantâneos para outros ambientes](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) primeiro para incluir o pacote NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) com o aplicativo, ***além*** de habilitar por meio do [ Painel de Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Atualizar para a versão mais recente do pacote NuGet

Se Depurador de Instantâneos tiver sido habilitado por meio do [painel de Application insights no portal](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), seu aplicativo já deverá estar executando o pacote NuGet mais recente. Se Depurador de Instantâneos foi habilitado incluindo o pacote NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) , use o Gerenciador de pacotes NuGet do Visual Studio para verificar se você está usando a versão mais recente do Microsoft. ApplicationInsights. SnapshotCollector. As notas de versão podem ser encontradas em https://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Verificar os logs do carregador

Depois que um instantâneo é criado, um arquivo de minidespejo (. dmp) é criado no disco. Um processo do carregador separado cria esse arquivo de minidespejo e o carrega, juntamente com qualquer PDBs associado, para Application Insights Depurador de Instantâneos armazenamento. Depois que o minidespejo for carregado com êxito, ele será excluído do disco. Os arquivos de log para o processo do carregador são mantidos em disco. Em um ambiente do serviço de aplicativo, você pode encontrar esses logs em `D:\Home\LogFiles`. Use o site de gerenciamento do kudu para o serviço de aplicativo para localizar esses arquivos de log.

1. Abra o aplicativo do serviço de aplicativo no portal do Azure.
2. Clique em **ferramentas avançadas**ou procure **kudu**.
3. Clique em **ir**.
4. Na caixa de listagem suspensa do **console de depuração** , selecione **cmd**.
5. Clique em arquivos de **log**.

Você deve ver pelo menos um arquivo com um nome que começa com `Uploader_` ou `SnapshotUploader_` e uma extensão de `.log`. Clique no ícone apropriado para baixar todos os arquivos de log ou abri-los em um navegador.
O nome do arquivo inclui um sufixo exclusivo que identifica a instância do serviço de aplicativo. Se a instância do serviço de aplicativo estiver hospedada em mais de um computador, haverá arquivos de log separados para cada computador. Quando o carregador detecta um novo arquivo de minidespejo, ele é registrado no arquivo de log. Aqui está um exemplo de um instantâneo e upload bem-sucedidos:

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
> O exemplo acima é da versão 1.2.0 do pacote NuGet Microsoft. ApplicationInsights. SnapshotCollector. Em versões anteriores, o processo do carregador é chamado de `MinidumpUploader.exe` e o log é menos detalhado.

No exemplo anterior, a chave de instrumentação é `c12a605e73c44346a984e00000000000`. Esse valor deve corresponder à chave de instrumentação do seu aplicativo.
O minidespejo está associado a um instantâneo com a ID `139e411a23934dc0b9ea08a626db16c5`. Você pode usar essa ID posteriormente para localizar a telemetria de exceção associada no Application Insights Analytics.

O carregador verifica se há novos PDBs cerca de uma vez a cada 15 minutos. Segue-se um exemplo:

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

Para aplicativos que _não são_ hospedados no serviço de aplicativo, os logs do carregador estão na mesma pasta que os minidespejos: `%TEMP%\Dumps\<ikey>` (em que `<ikey>` é sua chave de instrumentação).

## <a name="troubleshooting-cloud-services"></a>Solucionando problemas de serviços de nuvem
Para funções nos serviços de nuvem, a pasta temporária padrão pode ser muito pequena para manter os arquivos de minidespejo, levando a instantâneos perdidos.
O espaço necessário depende do conjunto de trabalho total do seu aplicativo e do número de instantâneos simultâneos.
O conjunto de trabalho de uma função Web ASP.NET de 32 bits geralmente está entre 200 MB e 500 MB.
Permita pelo menos dois instantâneos simultâneos.
Por exemplo, se seu aplicativo usa 1 GB do conjunto de trabalho total, você deve verificar se há pelo menos 2 GB de espaço em disco para armazenar instantâneos.
Siga estas etapas para configurar sua função de serviço de nuvem com um recurso local dedicado para instantâneos.

1. Adicione um novo recurso local ao serviço de nuvem editando o arquivo de definição do serviço de nuvem (. csdef). O exemplo a seguir define um recurso chamado `SnapshotStore` com um tamanho de 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Modifique o código de inicialização da função para adicionar uma variável de ambiente que aponta para o `SnapshotStore` recurso local. Para funções de trabalho, o código deve ser adicionado ao método de `OnStart` da função:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Para ASP.NET (funções Web), o código deve ser adicionado ao método de `Application_Start` do seu aplicativo Web:
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

3. Atualize o arquivo ApplicationInsights. config da função para substituir o local da pasta temporária usado pelo `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Substituindo a pasta de cópia de sombra

Quando o Snapshot Collector é iniciado, ele tenta localizar uma pasta no disco que é adequada para executar o processo de carregador de instantâneo. A pasta escolhida é conhecida como a pasta de cópia de sombra.

O Snapshot Collector verifica alguns locais bem conhecidos, certificando-se de que ele tenha permissões para copiar os binários do carregador de instantâneos. As seguintes variáveis de ambiente são usadas:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Se não for possível encontrar uma pasta adequada, Snapshot Collector relatará um erro dizendo _"não foi possível encontrar uma pasta de cópia de sombra adequada"._

Se a cópia falhar, Snapshot Collector relatará um erro `ShadowCopyFailed`.

Se o carregador não puder ser iniciado, Snapshot Collector relatará um erro `UploaderCannotStartFromShadowCopy`. O corpo da mensagem geralmente contém `System.UnauthorizedAccessException`. Esse erro geralmente ocorre porque o aplicativo está sendo executado em uma conta com permissões reduzidas. A conta tem permissão para gravar na pasta de cópia de sombra, mas não tem permissão para executar o código.

Como esses erros geralmente ocorrem durante a inicialização, eles normalmente serão seguidos por um erro `ExceptionDuringConnect` dizendo que _"o carregador falhou ao iniciar"._

Para contornar esses erros, você pode especificar a pasta de cópia de sombra manualmente por meio da opção de configuração `ShadowCopyFolder`. Por exemplo, usando ApplicationInsights. config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Ou, se você estiver usando appSettings. JSON com um aplicativo .NET Core:

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

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Use Application Insights Search para localizar exceções com instantâneos

Quando um instantâneo é criado, a exceção de lançamento é marcada com uma ID de instantâneo. Essa ID de instantâneo é incluída como uma propriedade personalizada quando a telemetria de exceção é relatada para Application Insights. Usando a **pesquisa** no Application insights, você pode encontrar toda a telemetria com a propriedade personalizada `ai.snapshot.id`.

1. Navegue até o recurso de Application Insights no portal do Azure.
2. Clique em **Pesquisar**.
3. Digite `ai.snapshot.id` na caixa de texto de pesquisa e pressione Enter.

![Pesquisar telemetria com uma ID de instantâneo no portal](./media/snapshot-debugger/search-snapshot-portal.png)

Se essa pesquisa não retornar nenhum resultado, nenhum instantâneo foi relatado para Application Insights para seu aplicativo no intervalo de tempo selecionado.

Para procurar uma ID de instantâneo específica dos logs do carregador, digite essa ID na caixa de pesquisa. Se você não encontrar a telemetria para um instantâneo que você sabe que foi carregado, siga estas etapas:

1. Verifique se você está olhando para a direita Application Insights recurso verificando a chave de instrumentação.

2. Usando o carimbo de data/hora do log do carregador, ajuste o filtro de intervalo de tempo da pesquisa para cobrir esse intervalo de tempo.

Se você ainda não vir uma exceção com essa ID de instantâneo, a telemetria de exceção não foi relatada para Application Insights. Essa situação pode acontecer se o aplicativo falhar depois de ter levado o instantâneo, mas antes de reportar a telemetria de exceção. Nesse caso, verifique os logs do serviço de aplicativo em `Diagnose and solve problems` para ver se houve reinicializações inesperadas ou exceções sem tratamento.

## <a name="edit-network-proxy-or-firewall-rules"></a>Editar regras de firewall ou proxy de rede

Se seu aplicativo se conectar à Internet por meio de um proxy ou um firewall, talvez seja necessário editar as regras para permitir que seu aplicativo se comunique com o serviço de Depurador de Instantâneos. Aqui está [uma lista de endereços IP e portas usadas pelo depurador de instantâneos](../../azure-monitor/app/ip-addresses.md#snapshot-debugger).
