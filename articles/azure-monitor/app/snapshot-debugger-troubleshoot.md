---
title: Resolução de problemas Azure Application Insights Snapshot Debugger
description: Este artigo apresenta etapas e informações de resolução de problemas para ajudar os desenvolvedores a ativar e utilizar o Debugger Snapshot Debugger do Application Insights.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: bd83367ae073e03f03188cdf62cb60faaad7ac97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026458"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> Problemas de resolução de problemas que permitem insights de aplicação Debugger ou visualização de instantâneos
Se ativou o Application Insights Snapshot Debugger para a sua aplicação, mas não estiver a ver instantâneos para exceções, pode utilizar estas instruções para resolver problemas.

Pode haver muitas razões diferentes para que os instantâneos não sejam gerados. Pode começar por fazer o exame de saúde instantâneo para identificar algumas das possíveis causas comuns.

## <a name="make-sure-youre-using-the-appropriate-snapshot-debugger-endpoint"></a>Certifique-se de que está a utilizar o ponto final apropriado do Snapshot Debugger

Atualmente, as únicas regiões que necessitam de modificações no ponto final são [o Governo de Azure](../../azure-government/compare-azure-government-global-azure.md#application-insights) e [a Azure China.](/azure/china/resources-developer-guide)

Para o Serviço de Aplicações e aplicações que utilizam o SDK De Insights de Aplicação, tem de atualizar a cadeia de ligação utilizando as substituições suportadas para Snapshot Debugger, conforme definido abaixo:

|Propriedade de cadeia de conexão    | Nuvem do Governo dos EUA | Nuvem da China |   
|---------------|---------------------|-------------|
|Ponto SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Para obter mais informações sobre outras ligações, consulte a [documentação do Application Insights](./sdk-connection-string.md?tabs=net#connection-string-with-explicit-endpoint-overrides).

Para a Aplicação de Função, tem de atualizar a `host.json` utilização das sobreposições suportadas abaixo:

|Propriedade    | Nuvem do Governo dos EUA | Nuvem da China |   
|---------------|---------------------|-------------|
|AgenteEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Abaixo está um exemplo da `host.json` atualização com o ponto final do agente da Cloud do governo dos EUA:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

## <a name="use-the-snapshot-health-check"></a>Use o cheque de saúde instantâneo
Vários problemas comuns resultam em que o Open Debug Snapshot não aparecesse. Utilizando um Coletor Instantâneo desatualizado, por exemplo; atingindo o limite de carregamento diário; ou talvez a foto esteja apenas a demorar muito tempo a carregar. Utilize o Snapshot Health Check para resolver problemas comuns.

Há um link no painel de exceção da vista de traços de ponta a ponta que o leva ao Snapshot Health Check.

![Insira o cheque de saúde instantâneo](./media/snapshot-debugger/enter-snapshot-health-check.png)

A interface interativa e tipo chat procura problemas comuns e guia-o para os corrigir.

![Verificação de Saúde](./media/snapshot-debugger/healthcheck.png)

Se isso não resolver o problema, consulte os seguintes passos manuais de resolução de problemas.

## <a name="verify-the-instrumentation-key"></a>Verifique a chave de instrumentação

Certifique-se de que está a utilizar a chave de instrumentação correta na sua aplicação publicada. Normalmente, a chave de instrumentação é lida a partir do ficheiro ApplicationInsights.config. Verifique se o valor é o mesmo que a chave de instrumentação do recurso Application Insights que vê no portal.

## <a name="check-tlsssl-client-settings-aspnet"></a><a id="SSL"></a>Verifique as definições do cliente TLS/SSL (ASP.NET)

Se tiver uma aplicação ASP.NET que esteja hospedada no Azure App Service ou no IIS numa máquina virtual, a sua aplicação poderá não conseguir ligar-se ao serviço Snapshot Debugger devido a um protocolo de segurança SSL em falta.

[O ponto final Snapshot Debugger requer a versão 1.2 do TLS](snapshot-debugger-upgrade.md?toc=/azure/azure-monitor/toc.json). O conjunto de protocolos de segurança SSL é uma das peculiaridades ativadas pelo valor de base de metas httpRuntime na secção system.web de web.config. Se o targetFramework httpRuntime for 4.5.2 ou inferior, então o TLS 1.2 não está incluído por padrão.

> [!NOTE]
> O valor do targetFramework httpRuntime é independente do quadro-alvo utilizado na construção da sua aplicação.

Para verificar a definição, abra o ficheiro web.config e encontre a secção system.web. Certifique-se de que o `targetFramework` for está definido para `httpRuntime` 4.6 ou superior.

   ```xml
   <system.web>
      ...
      <httpRuntime targetFramework="4.7.2" />
      ...
   </system.web>
   ```

> [!NOTE]
> Modificar o valor do targetFramework httpRuntime altera as peculiaridades de tempo de execução aplicadas à sua aplicação e pode causar outras alterações de comportamento subtis. Certifique-se de testar a sua aplicação cuidadosamente depois de escoar esta alteração. Para obter uma lista completa de alterações de compatibilidade, consulte https://docs.microsoft.com/dotnet/framework/migration-guide/application-compatibility#retargeting-changes

> [!NOTE]
> Se o targetFramework for 4.7 ou superior, o Windows determina os protocolos disponíveis. No Azure App Service, o TLS 1.2 está disponível. No entanto, se estiver a utilizar a sua própria máquina virtual, poderá ter de ativar o TLS 1.2 no SISTEMA.

## <a name="preview-versions-of-net-core"></a>Versões de pré-visualização de .NET Core
Se estiver a utilizar uma versão de pré-visualização de .NET Core ou as referências da sua aplicação Application Insights SDK, direta ou indiretamente através de um conjunto dependente, siga as instruções para [Enable Snapshot Debugger para outros ambientes](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json).

## <a name="check-the-diagnostic-services-site-extension-status-page"></a>Consulte a página de estado do site dos serviços de diagnóstico
Se o Snapshot Debugger foi ativado através do painel de Insights de [Aplicação](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) no portal, foi ativado pela extensão do site dos Serviços de Diagnóstico.

> [!NOTE]
> A instalação sem código do Application Insights Snapshot Debugger segue a política de suporte .NET Core.
> Para obter mais informações sobre os tempos de funcionação suportados, consulte [a Política de Suporte do Núcleo .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Pode consultar a Página de Estado desta extensão indo para o seguinte url: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> O domínio da ligação 'Página de Estado' variará consoante a nuvem.
Este domínio será o mesmo que o site de gestão kudu para o Serviço de Aplicações.

Esta página de estado mostra o estado de instalação dos agentes profiler e Snapshot Collector. Se houve um erro inesperado, será exibido e mostrará como corrigi-lo.

Pode utilizar o site de gestão Kudu para o Serviço de Aplicações para obter o url base desta página de estado:
1. Abra a sua aplicação de Serviço de Aplicações no portal Azure.
2. Selecione **Ferramentas Avançadas** ou procure **por Kudu**.
3. Selecione **Go**.
4. Uma vez que você está no site de gestão Kudu, no URL, **apece o seguinte `/DiagnosticServices` e pressione a entrada**.
 Vai acabar assim: `https://<kudu-url>/DiagnosticServices`

Apresentará uma Página de Estado semelhante à seguinte: ![ Página de Estado dos Serviços de Diagnóstico](./media/diagnostic-services-site-extension/status-page.png)

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Upgrade para a versão mais recente do pacote NuGet
Com base na forma como o Snapshot Debugger foi ativado, consulte as seguintes opções:

* Se o Snapshot Debugger foi ativado através do [painel Debugger Application Insights no portal,](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)então a sua aplicação já deve estar a executar o mais recente pacote NuGet.

* Se o Snapshot Debugger foi ativado através da inclusão do pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet, utilize o NuGet Package Manager do Visual Studio para se certificar de que está a utilizar a versão mais recente do Microsoft.ApplicationInsights.SnapshotCollector.

Para obter as últimas atualizações e correções de erro [consulte as notas de lançamento](./snapshot-collector-release-notes.md).

## <a name="check-the-uploader-logs"></a>Verifique os registos do uploader

Após a criação de um instantâneo, um ficheiro de minidump (.dmp) é criado no disco. Um processo de uploader separado cria esse ficheiro minidump e envia-o, juntamente com quaisquer PDBs associados, para o armazenamento de Debugger Snapshot Debugger do Application Insights. Depois de a minidump ter sido carregada com sucesso, é eliminada do disco. Os ficheiros de registo do processo do uploader são mantidos no disco. Num ambiente de Serviço de Aplicações, pode encontrar estes `D:\Home\LogFiles` registos. Utilize o site de gestão Kudu para o Serviço de Aplicações para encontrar estes ficheiros de registo.

1. Abra a sua aplicação de Serviço de Aplicações no portal Azure.
2. Selecione **Ferramentas Avançadas** ou procure **por Kudu**.
3. Selecione **Go**.
4. Na caixa de lista de drop-down da **consola Debug,** selecione **CMD**.
5. Selecione **Ficheiros de Registo**.

Deve ver pelo menos um ficheiro com um nome que comece com `Uploader_` ou `SnapshotUploader_` e uma `.log` extensão. Selecione o ícone apropriado para descarregar quaisquer ficheiros de registo ou abri-los num browser.
O nome do ficheiro inclui um sufixo único que identifica a instância do Serviço de Aplicações. Se a sua instância do Serviço de Aplicações estiver hospedada em mais de uma máquina, existem ficheiros de registo separados para cada máquina. Quando o carregador deteta um novo ficheiro de minidump, é gravado no ficheiro de registo. Aqui está um exemplo de um instantâneo bem sucedido e upload:

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
> O exemplo acima é da versão 1.2.0 do pacote Microsoft.ApplicationInsights.SnapshotCollector NuGet. Em versões anteriores, o processo do uploader é chamado `MinidumpUploader.exe` e o registo é menos detalhado.

No exemplo anterior, a chave de instrumentação é `c12a605e73c44346a984e00000000000` . Este valor deve corresponder à chave de instrumentação da sua aplicação.
A minidump está associada a um instantâneo com o ID `139e411a23934dc0b9ea08a626db16c5` . Pode utilizar este ID mais tarde para localizar o registo de exceções associado no Application Insights Analytics.

O carregador verifica novos PDBs uma vez a cada 15 minutos. Eis um exemplo:

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

Para aplicações que _não estão_ hospedadas no Serviço de Aplicações, os registos do uploader estão na mesma pasta que as minidumps: `%TEMP%\Dumps\<ikey>` (onde `<ikey>` está a sua chave de instrumentação).

## <a name="troubleshooting-cloud-services"></a>Serviços de Cloud resolução de problemas
Nos Serviços Cloud, a pasta temporária padrão pode ser demasiado pequena para segurar os ficheiros de minidump, levando a instantâneos perdidos.

O espaço necessário depende do conjunto de trabalho total da sua aplicação e do número de instantâneos simultâneos.

O conjunto de trabalho de um papel web ASP.NET de 32 bits é tipicamente entre 200 MB e 500 MB. Permita pelo menos duas fotos simultâneas.

Por exemplo, se a sua aplicação utilizar 1 GB do conjunto de trabalho total, deve certificar-se de que existe pelo menos 2 GB de espaço em disco para armazenar instantâneos.

Siga estes passos para configurar o seu papel de Cloud Service com um recurso local dedicado para instantâneos.

1. Adicione um novo recurso local ao seu Serviço cloud editando o ficheiro de definição de Cloud Service (.csdef). O exemplo a seguir define um recurso chamado `SnapshotStore` com um tamanho de 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Modifique o código de arranque do seu papel para adicionar uma variável ambiental que aponta para o `SnapshotStore` recurso local. Para funções de trabalhador, o código deve ser adicionado ao método do seu `OnStart` papel:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Para as funções web (ASP.NET), o código deve ser adicionado ao método da sua aplicação `Application_Start` web:
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

3. Atualize o ficheiro de ApplicationInsights.config da sua função para anular a localização temporária da pasta utilizada por `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Sobrevando a pasta Shadow Copy

Quando o Snapshot Collector começa, tenta encontrar uma pasta no disco adequada para executar o processo do Uploader Snapshot. A pasta escolhida é conhecida como a pasta Shadow Copy.

O Snapshot Collector verifica alguns locais bem conhecidos, certificando-se de que tem permissões para copiar os binários Do Uploader Snapshot. São utilizadas as seguintes variáveis ambientais:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Se não for encontrada uma pasta adequada, o Snapshot Collector relata um erro que diz _"Não foi possível encontrar uma pasta de cópia de sombra adequada."_

Se a cópia falhar, o Snapshot Collector reporta um `ShadowCopyFailed` erro.

Se o carregador não puder ser lançado, o Snapshot Collector reporta um `UploaderCannotStartFromShadowCopy` erro. O corpo da mensagem contém frequentemente `System.UnauthorizedAccessException` . Este erro ocorre geralmente porque a aplicação está a decorrer sob uma conta com permissões reduzidas. A conta tem permissão para escrever para a pasta de cópia de sombra, mas não tem permissão para executar código.

Como estes erros costumam acontecer durante o arranque, normalmente serão seguidos por um `ExceptionDuringConnect` erro a dizer _"O Uploader não começou"._

Para contornar estes erros, pode especificar manualmente a pasta de cópia de sombras através da `ShadowCopyFolder` opção de configuração. Por exemplo, usando ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Ou, se estiver a utilizar appsettings.jscom uma aplicação .NET Core:

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

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Use a pesquisa de Insights de Aplicação para encontrar exceções com instantâneos

Quando um instantâneo é criado, a exceção de arremesso é marcada com uma identificação instantânea. Esse ID instantâneo é incluído como uma propriedade personalizada quando a exceção é reportada ao Application Insights. Utilizando **o Search** in Application Insights, pode encontrar todos os registos com a propriedade `ai.snapshot.id` personalizada.

1. Navegue pelo seu recurso Application Insights no portal Azure.
2. Selecione **Pesquisar**.
3. Digite `ai.snapshot.id` na caixa de texto de pesquisa e prima Enter.

![Pesquisa de telemetria com uma imagem instantânea no portal](./media/snapshot-debugger/search-snapshot-portal.png)

Se esta pesquisa não retornar nenhum resultado, então, não foram reportados instantâneos ao Application Insights no intervalo de tempo selecionado.

Para procurar um ID instantâneo específico a partir dos registos do Uploader, escreva esse ID na caixa de pesquisa. Se não encontrar registos para uma foto que sabe que foi carregada, siga estes passos:

1. Verifique duas vezes se está a analisar o recurso correto de Insights de Aplicação, verificando a chave de instrumentação.

2. Utilizando o relógio de tempo do registo do Uploader, ajuste o filtro de intervalo de tempo da procura para cobrir esse intervalo de tempo.

Se ainda não vê uma exceção com o ID do instantâneo, então o registo de exceções não foi reportado à Application Insights. Esta situação pode acontecer se a sua aplicação se despenhar depois de ter tirado a fotografia, mas antes de reportar o registo de exceção. Neste caso, verifique os registos do Serviço de Aplicações `Diagnose and solve problems` para ver se houve recomeços inesperados ou exceções não manipuladas.

## <a name="edit-network-proxy-or-firewall-rules"></a>Editar regras de procuração de rede ou firewall

Se a sua aplicação se ligar à Internet através de um proxy ou de uma firewall, poderá ter de atualizar as regras para comunicar com o serviço Snapshot Debugger.

Os IPs utilizados pela Application Insights Snapshot Debugger estão incluídos na etiqueta de serviço Azure Monitor. Para mais informações, consulte a [documentação de Etiquetas de Serviço.](../../virtual-network/service-tags-overview.md)