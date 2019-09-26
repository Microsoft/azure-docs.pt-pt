---
title: Aplicativo de orquestração de patch do Azure Service Fabric | Microsoft Docs
description: Aplicativo para automatizar a aplicação de patch do sistema operacional em um Cluster Service Fabric.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: 2aa2dd8373a9568478a02691ca5e6a43e80cd408
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71289412"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Corrigir o sistema operacional Windows no Cluster Service Fabric

> 
> [!IMPORTANT]
> A versão do aplicativo 1,2. * está saindo do suporte em 30 de abril de 2019. Atualize para a versão mais recente.


[Conjunto de dimensionamento de máquinas virtuais do Azure as atualizações automáticas de imagem do sistema](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) operacional são a melhor prática para manter seus sistemas operacionais corrigidos no Azure, e o POA (aplicativo de orquestração de patch) é um wrapper em relação ao serviço de sistemas de reparo do Service fabrics que habilita o agendamento de patch de so baseado em configuração para clusters não hospedados no Azure. O POA não é necessário para clusters não hospedados no Azure, mas o agendamento de instalação de patch por domínios de atualização é necessário para patches de hosts de Service Fabric clusters sem tempo de inatividade.

O POA é um aplicativo de Service Fabric do Azure que automatiza a aplicação de patch do sistema operacional em um Cluster Service Fabric sem tempo de inatividade.

O aplicativo de orquestração de patch fornece os seguintes recursos:

- **Instalação automática de atualização do sistema operacional**. As atualizações do sistema operacional são baixadas e instaladas automaticamente. Nós de cluster são reinicializados conforme necessário sem tempo de inatividade do cluster.

- **Aplicação de patch e integração de integridade com reconhecimento de cluster**. Ao aplicar atualizações, o aplicativo de orquestração de patch monitora a integridade dos nós do cluster. Nós de cluster são atualizados em um nó por vez ou em um domínio de atualização por vez. Se a integridade do cluster falhar devido ao processo de aplicação de patch, a aplicação de patches será interrompida para evitar o agravar do problema.

## <a name="internal-details-of-the-app"></a>Detalhes internos do aplicativo

O aplicativo de orquestração de patch é composto pelos seguintes subcomponentes:

- **Serviço de coordenador**: Este serviço com estado é responsável por:
    - Coordenar o trabalho de Windows Update em todo o cluster.
    - Armazenando o resultado das operações de Windows Update concluídas.
- **Serviço de agente de nó**: Esse serviço sem estado é executado em todos os nós de Cluster Service Fabric. O serviço é responsável por:
    - Inicialização do NTService do agente de nó.
    - Monitorando o NTService do agente do nó.
- **NTService do agente do nó**: Esse serviço do Windows NT é executado em um privilégio de nível superior (sistema). Por outro lado, o serviço de agente de nó e o serviço de coordenador são executados em um privilégio de nível inferior (serviço de rede). O serviço é responsável por executar os seguintes trabalhos de Windows Update em todos os nós de cluster:
    - Desabilitando o Windows Update automático no nó.
    - Baixar e instalar Windows Update de acordo com a política que o usuário forneceu.
    - Reiniciar o computador post Windows Update instalação.
    - Carregando os resultados das atualizações do Windows para o serviço de coordenador.
    - Relatórios de integridade de relatórios no caso de uma operação falhar depois de esgotar todas as tentativas.

> [!NOTE]
> O aplicativo de orquestração de patch usa o serviço de sistema Service Fabric reparar Gerenciador para desabilitar ou habilitar o nó e realizar verificações de integridade. A tarefa de reparo criada pelo aplicativo de orquestração de patch rastreia o Windows Update andamento de cada nó.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> A versão mínima do .NET Framework necessária é 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Habilitar o serviço do Gerenciador de reparo (se ele ainda não estiver em execução)

O aplicativo de orquestração de patch requer que o serviço do sistema do Gerenciador de reparo esteja habilitado no cluster.

#### <a name="azure-clusters"></a>Clusters do Azure

Os clusters do Azure na camada de durabilidade prateada têm o serviço do Gerenciador de reparo habilitado por padrão. Os clusters do Azure na camada de durabilidade Gold podem ou não ter o serviço do Gerenciador de reparo habilitado, dependendo de quando esses clusters foram criados. Os clusters do Azure na camada de durabilidade bronze, por padrão, não têm o serviço do Gerenciador de reparo habilitado. Se o serviço já estiver habilitado, você poderá vê-lo em execução na seção serviços do sistema no Service Fabric Explorer.

##### <a name="azure-portal"></a>Portal do Azure
Você pode habilitar o Gerenciador de reparo de portal do Azure no momento da configuração do cluster. Selecione a opção **incluir Gerenciador de reparos** em **recursos de complemento** no momento da configuração do cluster.
![Imagem de habilitação de Gerenciador de Reparos de portal do Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Modelo de implantação de Azure Resource Manager
Como alternativa, você pode usar o [modelo de implantação Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para habilitar o serviço do Gerenciador de reparo em clusters de Service Fabric novos e existentes. Obtenha o modelo para o cluster que você deseja implantar. Você pode usar os modelos de exemplo ou criar um modelo de modelo de implantação de Azure Resource Manager personalizado. 

Para habilitar o serviço do Gerenciador de reparo usando o [modelo de modelo de implantação Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Primeiro, verifique se `apiversion` o está definido `2017-07-01-preview` como para `Microsoft.ServiceFabric/clusters` o recurso. Se for diferente, você precisará atualizar o `apiVersion` para o valor `2017-07-01-preview` ou superior:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora, habilite o serviço do Gerenciador de reparo `addonFeatures` adicionando a seguinte `fabricSettings` seção após a seção:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Depois de atualizar o modelo de cluster com essas alterações, aplique-as e permita que a atualização seja concluída. Agora você pode ver o serviço do sistema do Gerenciador de reparo em execução no cluster. Ele é chamado `fabric:/System/RepairManagerService` na seção serviços do sistema no Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Clusters locais autônomos

Você pode usar as [definições de configuração para o cluster autônomo do Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) para habilitar o serviço do Gerenciador de reparo no cluster de Service Fabric novo e existente.

Para habilitar o serviço do Gerenciador de reparo:

1. Primeiro, verifique se `apiversion` as [configurações gerais do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) estão definidas `04-2017` para ou superior:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Agora habilite o serviço do Gerenciador de reparo `addonFeatures` adicionando a seguinte `fabricSettings` seção após a seção, conforme mostrado abaixo:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Atualize o manifesto do cluster com essas alterações, usando o manifesto de cluster atualizado [crie um novo cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) ou [atualize a configuração do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). Depois que o cluster estiver sendo executado com o manifesto de cluster atualizado, agora você poderá ver o serviço do sistema do Gerenciador de reparo em `fabric:/System/RepairManagerService`execução no cluster, que é chamado, na seção serviços do sistema no Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Configurar atualizações do Windows para todos os nós

As atualizações automáticas do Windows podem levar à perda de disponibilidade porque vários nós de cluster podem ser reiniciados ao mesmo tempo. O aplicativo de orquestração de patch, por padrão, tenta desabilitar o Windows Update automático em cada nó de cluster. No entanto, se as configurações forem gerenciadas por um administrador ou Política de Grupo, recomendamos definir a política de Windows Update como "notificar antes do download" explicitamente.

## <a name="download-the-app-package"></a>Baixar o pacote do aplicativo

Para baixar o pacote de aplicativos, visite a [página](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) de versão do GitHub do aplicativo de orquestração de patch.

## <a name="configure-the-app"></a>Configurar o aplicativo

O comportamento do aplicativo de orquestração de patch pode ser configurado para atender às suas necessidades. Substitua os valores padrão passando o parâmetro do aplicativo durante a criação ou atualização do aplicativo. Os parâmetros do aplicativo podem ser fornecidos `ApplicationParameter` especificando para `Start-ServiceFabricApplicationUpgrade` os `New-ServiceFabricApplication` cmdlets ou.

|**Parâmetro**        |**Tipo**                          | **Detalhes**|
|:-|-|-|
|MaxResultsToCache    |Longo                              | Número máximo de resultados de Windows Update, que devem ser armazenados em cache. <br>O valor padrão é 3000 supondo que: <br> -Número de nós é 20. <br> -Número de atualizações acontecendo em um nó por mês é cinco. <br> -O número de resultados por operação pode ser 10. <br> -Os resultados dos últimos três meses devem ser armazenados. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |A taskapprovalpolicy indica a política a ser usada pelo serviço de coordenador para instalar atualizações do Windows entre os nós de Cluster Service Fabric.<br>                         Valores permitidos são: <br>                                                           <b>NodeWise</b>. O Windows Update é instalado em um nó por vez. <br>                                                           <b>UpgradeDomainWise</b>. O Windows Update é instalado em um domínio de atualização por vez. (No máximo, todos os nós que pertencem a um domínio de atualização podem ir para Windows Update.)<br> Consulte a seção de [perguntas frequentes](#frequently-asked-questions) sobre como decidir qual é a melhor política adequada para seu cluster.
|LogsDiskQuotaInMB   |Longo  <br> Os 1024)               |Tamanho máximo dos logs do aplicativo de orquestração de patch em MB, que pode ser persistido localmente em nós.
| WUQuery               | Cadeia de caracteres<br>Os "IsInstalled=0")                | Consulte para obter atualizações do Windows. Para obter mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Booleano <br> (padrão: false)                 | Use esse sinalizador para controlar quais atualizações devem ser baixadas e instaladas. Os valores a seguir são permitidos <br>true – instala apenas as atualizações do sistema operacional Windows.<br>false – instala todas as atualizações disponíveis no computador.          |
| WUOperationTimeOutInMinutes | Int <br>Os 90)                   | Especifica o tempo limite para qualquer operação de Windows Update (Pesquisar ou baixar ou instalar). Se a operação não for concluída dentro do tempo limite especificado, ela será anulada.       |
| WURescheduleCount     | Int <br> Os 5)                  | O número máximo de vezes que o serviço reagendará o Windows Update no caso de uma operação falhar de forma persistente.          |
| WURescheduleTimeInMinutes | Int <br>Os 30) | O intervalo no qual o serviço reagendará o Windows Update caso a falha persista. |
| WUFrequency           | Cadeia de caracteres separada por vírgulas (padrão: "Semanalmente, quarta-feira, 7:00:00")     | A frequência para instalar o Windows Update. O formato e os valores possíveis são: <br>-Mensal, DD, HH: MM: SS, por exemplo, mensal, 5, 12:22:32.<br>Os valores permitidos para o campo DD (Day) são números entre o intervalo de 1-28 e o "último". <br> -Semanalmente, dia, HH: MM: SS, por exemplo, semanal, terça-feira, 12:22:32.  <br> -Diário, HH: MM: SS, por exemplo, diário, 12:22:32.  <br> -None indica que Windows Update não deve ser feito.  <br><br> Observe que os horários estão em UTC.|
| AcceptWindowsUpdateEula | Booleano <br>(Padrão: true) | Ao definir esse sinalizador, o aplicativo aceita o contrato de licença de usuário final para Windows Update em nome do proprietário do computador.              |

> [!TIP]
> Se você quiser que Windows Update aconteça imediatamente, defina `WUFrequency` em relação ao tempo de implantação do aplicativo. Por exemplo, suponha que você tenha um cluster de teste de cinco nós e planeje implantar o aplicativo em cerca de 5:00 PM UTC. Se você assumir que a atualização ou a implantação do aplicativo leva 30 minutos no máximo, defina o WUFrequency como "Daily, 17:30:00"

## <a name="deploy-the-app"></a>Implementar a aplicação

1. Conclua todas as etapas de pré-requisito para preparar o cluster.
2. Implante o aplicativo de orquestração de patch como qualquer outro aplicativo Service Fabric. Você pode implantar o aplicativo usando o PowerShell. Siga as etapas em [implantar e remover aplicativos usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Para configurar o aplicativo no momento da implantação, passe o `ApplicationParameter` para o `New-ServiceFabricApplication` cmdlet. Para sua conveniência, fornecemos o script Deploy. ps1 junto com o aplicativo. Para usar o script:

    - Conecte-se a um Cluster Service Fabric `Connect-ServiceFabricCluster`usando.
    - Execute o script do PowerShell Deploy. ps1 com o `ApplicationParameter` valor apropriado.

> [!NOTE]
> Mantenha o script e a pasta do aplicativo PatchOrchestrationApplication no mesmo diretório.

## <a name="upgrade-the-app"></a>Atualizar o aplicativo

Para atualizar um aplicativo de orquestração de patch existente usando o PowerShell, siga as etapas em [Service Fabric atualização de aplicativo usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Remover o aplicativo

Para remover o aplicativo, siga as etapas em [implantar e remover aplicativos usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Para sua conveniência, fornecemos o script undeploy. ps1 junto com o aplicativo. Para usar o script:

  - Conecte-se a um Cluster Service Fabric ```Connect-ServiceFabricCluster```usando.

  - Execute o script do PowerShell undeploy. ps1.

> [!NOTE]
> Mantenha o script e a pasta do aplicativo PatchOrchestrationApplication no mesmo diretório.

## <a name="view-the-windows-update-results"></a>Exibir os resultados de Windows Update

O aplicativo de orquestração de patch expõe as APIs REST para exibir os resultados históricos para o usuário. Um exemplo do resultado JSON:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

Os campos do JSON são descritos abaixo.

Campo | Valores | Detalhes
-- | -- | --
OperationResult | 0-êxito<br> 1-êxito com erros<br> 2-com falha<br> 3-anulado<br> 4-anulado com tempo limite | Indica o resultado da operação geral (normalmente envolvendo a instalação de uma ou mais atualizações).
resultCode | O mesmo que OperationResult | Esse campo indica o resultado da operação de instalação para uma atualização individual.
OperationType | 1-instalação<br> 0-Pesquisar e baixar.| A instalação é o único OperationType que seria mostrado nos resultados por padrão.
WindowsUpdateQuery | O padrão é "IsInstalled = 0" |Consulta do Windows Update que foi usada para Pesquisar atualizações. Para obter mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | true-a reinicialização foi necessária<br> false-a reinicialização não foi necessária | Indica se a reinicialização foi necessária para concluir a instalação das atualizações.
OperationStartTime | DateTime | Indica a hora em que a operação (download/instalação) foi iniciada.
Operationtime | DateTime | Indica a hora em que a operação (download/instalação) foi concluída.
Resultado | 0-êxito<br> outro-falha| Indica o motivo da falha da atualização do Windows com a UpdateId "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Se nenhuma atualização for agendada ainda, o resultado JSON estará vazio.

Entre no cluster para consultar Windows Update resultados. Em seguida, descubra o endereço de réplica para o primário do serviço de coordenador e pressione a URL do navegador: http://&lt;réplica-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/ GetWindowsUpdateResults.

O ponto de extremidade REST para o serviço de coordenador tem uma porta dinâmica. Para verificar a URL exata, consulte a Service Fabric Explorer. Por exemplo, os resultados estão disponíveis em `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Imagem do ponto de extremidade REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Se o proxy reverso estiver habilitado no cluster, você também poderá acessar a URL fora do cluster.
O ponto de extremidade que precisa ser atingido é&lt;http://&gt;ServerURL&lt;:&gt;REVERSEPROXYPORT/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Para habilitar o proxy reverso no cluster, siga as etapas em [proxy reverso no Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Depois que o proxy reverso é configurado, todos os micro serviços no cluster que expõem um ponto de extremidade HTTP são endereçáveis de fora do cluster.

## <a name="diagnosticshealth-events"></a>Eventos de diagnóstico/integridade

A seção a seguir fala sobre como depurar/diagnosticar problemas com atualizações de patch por meio do aplicativo de orquestração de patch em clusters Service Fabric.

> [!NOTE]
> Você deve ter a versão v 1.4.0 do POA instalada para obter muitas das versões abaixo, denominadas melhorias de diagnóstico automático.

O NodeAgentNTService cria [tarefas de reparo](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) para instalar atualizações nos nós. Cada tarefa é preparada por CoordinatorService de acordo com a política de aprovação de tarefa. As tarefas preparadas são, por fim, aprovadas por Gerenciador de Reparos que não aprovarão nenhuma tarefa se o cluster estiver em estado não íntegro. Vamos passar passo a passo para entender como as atualizações procedem em um nó.

1. NodeAgentNTService, em execução em cada nó, procura Windows Update disponíveis no horário agendado. Se as atualizações estiverem disponíveis, elas continuarão e serão baixadas no nó.
2. Depois que as atualizações forem baixadas, o NodeAgentNTService, criará a tarefa de reparo correspondente para o nó com o nome POS___ < unique_id >. É possível exibir essas tarefas de reparo usando o cmdlet [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) ou no SFX na seção detalhes do nó. Depois que a tarefa de reparo for criada, o passará rapidamente para o [estado declarado](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).
3. O serviço de coordenador, procura periodicamente por tarefas de reparo no estado declarado e prossegue e atualiza-as para preparar o estado com base no A taskapprovalpolicy. Se a A taskapprovalpolicy estiver configurada para ser NodeWiseda, uma tarefa de reparo correspondente a um nó será preparada somente se não houver outra tarefa de reparo no estado de preparação/aprovado/em execução/restauração. Da mesma forma, no caso do UpgradeWise A taskapprovalpolicy, ele é garantido em qualquer ponto há tarefas nos Estados acima somente para os nós que pertencem ao mesmo domínio de atualização. Depois que uma tarefa de reparo for movida para o estado de preparação, o nó de Service Fabric correspondente será [desabilitado](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) com intenção como "reiniciar".

   POA (v 1.4.0 e superior) posta eventos com a propriedade "ClusterPatchingStatus" no CoordinaterService para exibir os nós que estão sendo corrigidos. A imagem abaixo mostra que as atualizações estão sendo instaladas no _poanode_0:

    [![Imagem do status de aplicação de patch do cluster](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

4. Quando o nó estiver desabilitado, a tarefa de reparo será movida para o estado de execução.
   
   >[!NOTE]
   > Um nó preso em um estado desabilitado pode bloquear uma nova tarefa de reparo, o que interromperá a operação de aplicação de patch no cluster.

5. Depois que a tarefa de reparo estiver em estado de execução, a instalação do patch nesse nó será iniciada. Aqui, depois que o patch é instalado, o nó pode ou não ser reiniciado, dependendo do patch. Poste que a tarefa de reparo foi movida para o estado de restauração, o que permite novamente o nó e, em seguida, é marcado como concluído.

   Em v 1.4.0 e versões superiores do aplicativo, o status da atualização pode ser encontrado examinando os eventos de integridade em NodeAgentService com a propriedade "WUOperationStatus-[NodeName]". As seções realçadas nas imagens abaixo mostram o status do Windows Update no nó ' poanode_0 ' e ' poanode_2 ':

   [![Imagem do status da operação do Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Imagem do status da operação do Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Também é possível obter os detalhes usando o PowerShell, conectando-se ao cluster e buscando o estado da tarefa de reparo usando [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). Como o exemplo abaixo mostra que a tarefa "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" está no estado DownloadComplete. Isso significa que as atualizações foram baixadas no nó "poanode_2" e a instalação será tentada quando a tarefa for movida para o estado de execução.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Se ainda houver mais informações a serem encontradas, entre em VM/VMs específicas para saber mais sobre o problema usando os logs de eventos do Windows. A tarefa de reparo mencionada acima só pode ter estes subcaminhos de executor:

      ExecutorSubState | Detalhe
    -- | -- 
      None=1 |  Implica que não havia uma operação em andamento no nó. Possíveis transições de estado.
      DownloadCompleted=2 | Implica que a operação de download foi concluída com êxito, falha parcial ou falha.
      InstallationApproved=3 | Implica que a operação de download foi concluída anteriormente e Gerenciador de Reparos aprovou a instalação.
      InstallationInProgress=4 | Corresponde ao estado de execução da tarefa de reparo.
      InstallationCompleted = 5 | Implica que a instalação foi concluída com êxito, êxito parcial ou falha.
      RestartRequested=6 | Implica que a instalação do patch foi concluída e que há uma ação de reinicialização pendente no nó.
      RestartNotNeeded=7 |  Implica que a reinicialização não foi necessária após a conclusão da instalação do patch.
      RestartCompleted = 8 | Implica que a reinicialização foi concluída com êxito.
      OperationCompleted=9 | A operação do Windows Update foi concluída com êxito.
      OperationAborted=10 | Implica que a operação do Windows Update foi anulada.

6. Em v 1.4.0 e superior do aplicativo, quando a tentativa de atualização em um nó é concluída, um evento com a propriedade "WUOperationStatus-[NodeName]" é Postado no NodeAgentService para notificar quando a próxima tentativa será baixar e instalar a atualização, iniciar. Consulte a imagem abaixo:

     [![Imagem do status da operação do Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostic-logs"></a>Registos de diagnósticos

Os logs do aplicativo de orquestração de patch são coletados como parte dos logs do Service Fabric Runtime.

Caso você queira capturar logs por meio da ferramenta de diagnóstico/pipeline de sua escolha. O aplicativo de orquestração de patch usa as IDs de provedor fixas a seguir para registrar eventos via [origem do evento](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Relatórios de integridade

O aplicativo de orquestração de patch também publica relatórios de integridade no serviço de coordenador ou no serviço de agente de nó nos seguintes casos:

#### <a name="the-node-agent-ntservice-is-down"></a>O NTService do agente do nó está inoperante

Se o NTService do agente do nó estiver inoperante em um nó, um relatório de integridade no nível de aviso será gerado em relação ao serviço do agente do nó.

#### <a name="the-repair-manager-service-is-not-enabled"></a>O serviço do Gerenciador de reparo não está habilitado

Se o serviço do Gerenciador de reparo não for encontrado no cluster, um relatório de integridade no nível de aviso será gerado para o serviço de coordenador.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

P. **Por que vejo o meu cluster em um estado de erro quando o aplicativo de orquestração de patch está em execução?**

R. Durante o processo de instalação, o aplicativo de orquestração de patch desabilita ou reinicia os nós, o que pode resultar temporariamente na integridade do cluster ficar inativo.

Com base na política para o aplicativo, um nó pode ficar inativo durante uma operação de aplicação de patch *ou* um domínio de atualização inteiro pode ficar inoperante simultaneamente.

Ao final da instalação do Windows Update, os nós são reabilitados após a reinicialização.

No exemplo a seguir, o cluster passou para um estado de erro temporariamente porque dois nós estavam inativos e a política de MaxPercentageUnhealthyNodes foi violada. O erro é temporário até que a operação de aplicação de patch esteja em andamento.

![Imagem de cluster não íntegro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Se o problema persistir, consulte a seção solução de problemas.

P. **O aplicativo de orquestração de patch está em estado de aviso**

R. Verifique se um relatório de integridade Postado no aplicativo é a causa raiz. Normalmente, o aviso contém detalhes do problema. Se o problema for transitório, espera-se que o aplicativo se recupere automaticamente a partir desse Estado.

P. **O que posso fazer se o cluster não estiver íntegro e precisar fazer uma atualização urgente do sistema operacional?**

R. O aplicativo de orquestração de patch não instala atualizações enquanto o cluster não está íntegro. Tente colocar o cluster em um estado íntegro para desbloquear o fluxo de trabalho do aplicativo de orquestração de patch.

P. **Devo definir A taskapprovalpolicy como ' NodeWise ' ou ' UpgradeDomainWise ' para o meu cluster?**

R. ' UpgradeDomainWise ' torna a aplicação de patch geral do cluster mais rápida por meio da aplicação de patch em todos os nós que pertencem a um domínio de atualização em paralelo. Isso significa que os nós que pertencem a um domínio de atualização inteiro não estarão disponíveis (no estado [desabilitado](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) ) durante o processo de aplicação de patch.

Em contraste, a política ' NodeWise ' corrige apenas um nó por vez, isso implica que a aplicação geral de patches do cluster levaria mais tempo. No entanto, no máximo, apenas um nó estaria indisponível (no estado [desabilitado](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) ) durante o processo de aplicação de patch.

Se o cluster puder tolerar a execução em N-1 número de domínios de atualização durante o ciclo de aplicação de patch (em que N é o número total de domínios de atualização no cluster), você poderá definir a política como ' UpgradeDomainWise ', caso contrário, defini-la como ' NodeWise '.

P. **Quanto tempo leva para corrigir um nó?**

R. A aplicação de patch em um nó pode levar minutos (por exemplo: [Atualizações de definições do Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)) a horas (por exemplo: [Atualizações cumulativas do Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). O tempo necessário para corrigir um nó depende principalmente de 
 - O tamanho das atualizações
 - Número de atualizações, que devem ser aplicadas em uma janela de aplicação de patch
 - Tempo necessário para instalar as atualizações, reinicialize o nó (se necessário) e conclua as etapas de instalação após a reinicialização.
 - Desempenho de condições de VM/máquina e rede.

P. **Quanto tempo leva para corrigir um cluster inteiro?**

R. O tempo necessário para corrigir um cluster inteiro depende dos seguintes fatores:

- Tempo necessário para aplicar patch em um nó.
- A política do serviço de coordenador. -A política padrão, `NodeWise`, resulta na aplicação de patches em apenas um nó por vez, o que seria mais `UpgradeDomainWise`lento do que. Por exemplo: Se um nó levar cerca de 1 hora para ser corrigido, a fim de corrigir um cluster de 20 nós (mesmo tipo de nó) com 5 domínios de atualização, cada um contendo 4 nós.
    - Deve levar cerca de 20 horas para aplicar patch ao cluster inteiro, se a política for`NodeWise`
    - Deve levar cerca de 5 horas se a política for`UpgradeDomainWise`
- Carregamento de cluster-cada operação de patch requer a realocação da carga de trabalho do cliente para outros nós disponíveis no cluster. O patch de saída do nó estaria no estado [desabilitando](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) durante esse tempo. Se o cluster estiver executando perto da carga de pico, o processo de desabilitação levaria mais tempo. Portanto, o processo geral de aplicação de patch pode parecer lento em tais condições sob medida.
- Falhas de integridade do cluster durante a aplicação de patch-qualquer [degradação](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) na [integridade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) interromperia o processo de aplicação de patch. Isso adicionaria ao tempo geral necessário para aplicar o patch a todo o cluster.

P. **Por que vejo algumas atualizações em Windows Update resultados obtidos por meio da API REST, mas não sob o histórico de Windows Update no computador?**

R. Algumas atualizações do produto só apareceriam em seu respectivo histórico de atualização/patch. Por exemplo, as atualizações do Windows Defender podem ou não ser exibidas no histórico de Windows Update no Windows Server 2016.

P. **O aplicativo de orquestração de patch pode ser usado para corrigir meu cluster de desenvolvimento (cluster de um nó)?**

R. Não, o aplicativo de orquestração de patch não pode ser usado para corrigir o cluster de um nó. Essa limitação é por design, pois os [Serviços do sistema do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) ou qualquer aplicativo de cliente enfrentará o tempo de inatividade e, portanto, qualquer trabalho de reparo para aplicação de patch nunca será aprovado pelo Gerenciador de reparos.

P. **Como fazer nós de cluster de patch no Linux?**

R. Confira [conjunto de dimensionamento de máquinas virtuais do Azure atualizações automáticas de imagem do sistema operacional](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) para orquestrar atualizações no Linux.

P.**por que o ciclo de atualização está demorando tanto tempo?**

R. Consulte o resultado JSON e, em seguida, percorra a entrada do ciclo de atualização para todos os nós e, em seguida, você pode tentar descobrir o tempo gasto pela instalação da atualização em todos os nós usando OperationStartTime e Operationtime (OperationCompletionTime). Se houver uma janela de tempo grande na qual nenhuma atualização estava acontecendo, poderia ser porque o cluster estava em estado de erro e porque esse Gerenciador de reparo não aprovou nenhuma outra tarefa de reparo POA. Se a instalação da atualização demorou muito em qualquer nó, pode ser possível que o nó não tenha sido atualizado a partir de um longo tempo e muita atualização tenha sido uma instalação pendente, o que levou tempo. Também pode haver um caso em que a aplicação de patch em um nó seja bloqueada devido ao nó estar preso na desabilitação do estado, o que geralmente acontece porque a desabilitação do nó pode levar a situações de perda de quorum/dados.

P. **Por que é necessário desabilitar o nó quando o POA estiver aplicando patches nele?**

R. O aplicativo de orquestração de patch desabilita o nó com a intenção de "reiniciar", que interrompe/realoca todos os serviços do Service Fabric em execução no nó. Isso é feito para garantir que os aplicativos não acabem usando uma combinação de DLLs novas e antigas, portanto, não é recomendável corrigir um nó sem desabilitá-lo.

## <a name="disclaimers"></a>Exclusões de Responsabilidade

- O aplicativo de orquestração de patch aceita o contrato de licença de usuário final de Windows Update em nome do usuário. Opcionalmente, a configuração pode ser desativada na configuração do aplicativo.

- O aplicativo de orquestração de patch coleta a telemetria para acompanhar o uso e o desempenho. A telemetria do aplicativo segue a configuração da configuração de telemetria do Service Fabric Runtime (que está ativada por padrão).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="a-node-is-not-coming-back-to-up-state"></a>Um nó não está voltando para o estado ativo

**O nó pode estar preso em um estado de desabilitação porque**:

Uma verificação de segurança está pendente. Para corrigir essa situação, certifique-se de que nós suficientes estejam disponíveis em um estado íntegro.

**O nó pode estar preso em um estado desabilitado porque**:

- O nó foi desabilitado manualmente.
- O nó foi desabilitado devido a um trabalho de infraestrutura do Azure em andamento.
- O nó foi desabilitado temporariamente pelo aplicativo de orquestração de patch para corrigir o nó.

**O nó pode estar preso em um estado inativo porque**:

- O nó foi colocado em um estado inativo manualmente.
- O nó está passando por uma reinicialização (que pode ser disparada pelo aplicativo de orquestração de patch).
- O nó está inativo devido a problemas de conectividade de rede ou máquina virtual com falha.

### <a name="updates-were-skipped-on-some-nodes"></a>As atualizações foram ignoradas em alguns nós

O aplicativo de orquestração de patch tenta instalar uma atualização do Windows de acordo com a política de reagendamento. O serviço tenta recuperar o nó e ignorar a atualização de acordo com a política de aplicativo.

Nesse caso, um relatório de integridade no nível de aviso é gerado em relação ao serviço do agente do nó. O resultado de Windows Update também contém o motivo possível para a falha.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>A integridade do cluster vai para erro durante a instalação da atualização

Um Windows Update com falha pode reduzir a integridade de um aplicativo ou cluster em um nó ou domínio de atualização específico. O aplicativo de orquestração de patch descontinuará qualquer operação de Windows Update subsequente até que o cluster esteja íntegro novamente.

Um administrador deve intervir e determinar por que o aplicativo ou o cluster se tornou não íntegro devido a Windows Update.

## <a name="release-notes"></a>Notas de Versão

>[!NOTE]
> A partir da versão 1.4.0, notas de versão e versões podem ser encontradas na [página](https://github.com/microsoft/Service-Fabric-POA/releases/)de versão do github.

### <a name="version-110"></a>Versão 1.1.0
- Versão pública

### <a name="version-111"></a>Versão 1.1.1
- Correção de um bug em SetupEntryPoint de NodeAgentService que impediu a instalação do NodeAgentNTService.

### <a name="version-120"></a>Versão 1.2.0

- Correções de bugs em relação ao fluxo de trabalho de reinicialização
- Correção de bug na criação de tarefas do RM devido a qual verificação de integridade durante a preparação de tarefas de reparo não estava ocorrendo conforme o esperado.
- O modo de inicialização do serviço Windows POANodeSvc foi alterado de automático para atrasado-automático.

### <a name="version-121"></a>Versão 1.2.1

- Correção de bug no fluxo de trabalho de redução do cluster. Introduziu a lógica de coleta de lixo para POA tarefas de reparo que pertencem a nós inexistentes.

### <a name="version-122"></a>1\.2.2 da versão

- Correções de bugs diversas.
- Os binários agora estão assinados.
- Adicionado o link sfpkg para o aplicativo.

### <a name="version-130"></a>Versão 1.3.0

- Definir InstallWindowsOSOnlyUpdates como false agora instala todas as atualizações disponíveis.
- Alteração da lógica de desabilitar atualizações automáticas. Isso corrige um bug em que as atualizações automáticas não estão sendo desabilitadas no servidor 2016 e acima.
- Restrição de posicionamento com parâmetros para ambos os microserviços de POA para casos de uso avançados.

### <a name="version-131"></a>1\.3.1 da versão
- Corrigindo a regressão em que POA 1.3.0 não funcionará no Windows Server 2012 R2 ou inferior devido a uma falha na desabilitação das atualizações automáticas. 
- Corrigindo o bug em que a configuração InstallWindowsOSOnlyUpdates é sempre escolhida como verdadeira.
- Alterando o valor padrão de InstallWindowsOSOnlyUpdates para false.

### <a name="version-132"></a>1\.3.2 da versão
- Correção de um problema que afetava o ciclo de vida de aplicação de patch em um nó, caso haja nós com o nome, que é o subconjunto do nome do nó atual. Para esses nós, é possível que a aplicação de patch seja perdida ou a reinicialização esteja pendente. 
