---
title: Corrigir o sistema operacional Windows no cluster de Service Fabric | Microsoft Docs
description: Este artigo discute como automatizar a aplicação de patch do sistema operacional em um Cluster Service Fabric usando o aplicativo de orquestração de patch.
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
ms.openlocfilehash: d1ff58611278f02d74f064f0536e5f6f77195fb2
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828871"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Corrigir o sistema operacional Windows no Cluster Service Fabric

> 
> [!IMPORTANT]
> A partir de 30 de abril de 2019, o aplicativo de orquestração de patch versão 1,2. * não é mais suportado. Certifique-se de atualizar para a versão mais recente.


Obter [atualizações automáticas de imagem do sistema operacional em seu conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) é a melhor prática para manter seu sistema operacional corrigido no Azure. O POA (aplicativo de orquestração de patch) é um wrapper em todo o serviço de Gerenciador de Reparos Service Fabric do Azure, que permite o agendamento de patches de so baseado em configuração para clusters não hospedados no Azure. O POA não é necessário para clusters não hospedados no Azure, mas o agendamento de instalação de patch por domínio de atualização é necessário para patches Service Fabric hosts de cluster sem incorrer em tempo de inatividade.

O POA é um aplicativo Service Fabric que automatiza a aplicação de patches do sistema operacional em um Cluster Service Fabric sem incorrer em tempo de inatividade.

O POA fornece os seguintes recursos:

- **Instalação automática de atualização do sistema operacional**. As atualizações do sistema operacional são baixadas e instaladas automaticamente. Nós de cluster são reinicializados conforme necessário sem incorrer em tempo de inatividade do cluster.

- **Aplicação de patch e integração de integridade com reconhecimento de cluster**. Embora o POA esteja aplicando atualizações, ele monitora a integridade dos nós do cluster. Nós de cluster são atualizados em um nó por vez ou em um domínio de atualização por vez. Se a integridade do cluster falhar devido ao processo de aplicação de patch, a aplicação de patches será interrompida para evitar o agravar do problema.

## <a name="internal-details-of-poa"></a>Detalhes internos de POA

O POA é composto pelos seguintes subcomponentes:

- **Serviço de coordenador**: Este serviço com estado é responsável por:
    - Coordenar o trabalho de Windows Update em todo o cluster.
    - Armazenando o resultado das operações de Windows Update concluídas.

- **Serviço de agente de nó**: Esse serviço sem estado é executado em todos os nós de Cluster Service Fabric. O serviço é responsável por:
    - Inicialização do NTService do agente de nó.
    - Monitorando o NTService do agente do nó.

- **NTService do agente do nó**: Esse serviço do Windows NT é executado em um privilégio de nível superior (sistema). Por outro lado, o serviço de agente de nó e o serviço de coordenador são executados em um privilégio de nível inferior (serviço de rede). O serviço é responsável por executar os seguintes trabalhos de Windows Update em todos os nós de cluster:
    - Desabilitando atualizações automáticas do Windows no nó.
    - Baixar e instalar atualizações do Windows de acordo com a política que o usuário forneceu.
    - Reiniciando a instalação de atualizações do post-Windows do computador.
    - Carregando os resultados das atualizações do Windows para o serviço de coordenador.
    - Relatórios de integridade de relatórios se uma operação tiver falhado depois de esgotar todas as novas tentativas.

> [!NOTE]
> O POA usa o serviço de Gerenciador de Reparos Service Fabric para desabilitar ou habilitar o nó e realizar verificações de integridade. A tarefa de reparo criada pelo POA rastreia o Windows Update andamento de cada nó.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> A versão de .NET Framework mínima necessária é 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Habilitar o serviço de Gerenciador de Reparos (se ele ainda não estiver em execução)

POA exige que o serviço de Gerenciador de Reparos seja habilitado no cluster.

#### <a name="azure-clusters"></a>Clusters do Azure

Os clusters do Azure na camada de durabilidade prateada têm o serviço Gerenciador de Reparos habilitado por padrão. Os clusters do Azure na camada de durabilidade Gold podem ou não ter o serviço Gerenciador de Reparos habilitado, dependendo de quando esses clusters foram criados. Os clusters do Azure na camada de durabilidade bronze, por padrão, não têm o serviço Gerenciador de Reparos habilitado. Se o serviço já estiver habilitado, você poderá vê-lo em execução na seção serviços do sistema em Service Fabric Explorer.

##### <a name="the-azure-portal"></a>O portal do Azure
Você pode habilitar Gerenciador de Reparos do portal do Azure ao configurar o cluster. Quando você estiver configurando o cluster, selecione a opção **incluir Gerenciador de reparos** em **recursos de complemento**.

![Imagem de habilitação de Gerenciador de Reparos do portal do Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>O modelo de implantação Azure Resource Manager
Como alternativa, você pode usar o [modelo de implantação Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para habilitar o serviço de Gerenciador de reparos em clusters de Service Fabric novos e existentes. Obtenha o modelo para o cluster que você deseja implantar. Você pode usar os modelos de exemplo ou criar um modelo de modelo de implantação de Azure Resource Manager personalizado. 

Para habilitar o serviço de Gerenciador de Reparos usando o [modelo de modelo de implantação Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm), faça o seguinte:

1. Verifique se `apiVersion` está definido como *2017-07-01-Preview* para o recurso *Microsoft. perfabric/clusters* . Se for diferente, você precisará atualizar `apiVersion` para *2017-07-01-Preview* ou posterior:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Habilite o serviço de Gerenciador de Reparos adicionando a seguinte seção `addonFeatures` após a seção `fabricSettings`:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Depois de atualizar o modelo de cluster com essas alterações, aplique-as e permita que a atualização seja concluída. Agora você pode ver o serviço Gerenciador de Reparos em execução no cluster. Ele é chamado de *Fabric:/System/RepairManagerService* na seção serviços do sistema em Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Clusters locais autônomos

Para habilitar o serviço de Gerenciador de Reparos em um cluster de Service Fabric novo ou existente, você pode usar as [definições de configuração para o cluster autônomo do Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Para habilitar o serviço de Gerenciador de Reparos:

1. Verifique se o `apiVersion` em [configurações gerais de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) está definido como *04-2017* ou posterior, conforme mostrado aqui:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Habilite o serviço de Gerenciador de Reparos adicionando a seguinte seção `addonFeatures` após a seção `fabricSettings`, conforme mostrado aqui:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Atualize o manifesto do cluster com essas alterações usando o manifesto de cluster atualizado [crie um novo cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) ou [atualize a configuração do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). 

   Depois que o cluster estiver em execução com um manifesto de cluster atualizado, você poderá ver o serviço de Gerenciador de Reparos em execução no cluster. Ele é chamado de *Fabric:/System/RepairManagerService*e está na seção serviços do sistema em Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Configurar atualizações do Windows para todos os nós

As atualizações automáticas do Windows podem levar à perda de disponibilidade, pois vários nós de cluster podem ser reiniciados ao mesmo tempo. O POA, por padrão, tenta desabilitar as atualizações automáticas do Windows em cada nó de cluster. No entanto, se as configurações forem gerenciadas por um administrador ou um Política de Grupo, recomendamos definir a política de Windows Update como "notificar antes do download" explicitamente.

## <a name="download-the-application-package"></a>Baixar o pacote de aplicativos

Para baixar o pacote de aplicativos, vá para a [página de lançamento do aplicativo de orquestração de patch](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) no github.

## <a name="configure-poa-behavior"></a>Configurar o comportamento de POA

Você pode configurar o comportamento do POA para atender às suas necessidades. Substitua os valores padrão passando o parâmetro Application enquanto você estiver criando ou atualizando o aplicativo. Você pode fornecer parâmetros de aplicativo especificando `ApplicationParameter` para os cmdlets `Start-ServiceFabricApplicationUpgrade` ou `New-ServiceFabricApplication`.

| Parâmetro        | Type                          | Detalhes |
|:-|-|-|
|MaxResultsToCache    |Longo                              | O número máximo de resultados de Windows Update que devem ser armazenados em cache. <br><br>O valor padrão é 3000, supondo que: <br> &nbsp; @ no__t-1-o número de nós é 20. <br> &nbsp; @ no__t-1-o número de atualizações para um nó por mês é 5. <br> &nbsp; @ no__t-1-o número de resultados por operação pode ser 10. <br> &nbsp; @ no__t-1-os resultados dos últimos três meses devem ser armazenados. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |A taskapprovalpolicy indica a política a ser usada pelo serviço de coordenador para instalar atualizações do Windows entre os nós de Cluster Service Fabric.<br><br>Os valores permitidos são: <br>*NodeWise*: As atualizações do Windows são instaladas em um nó por vez. <br> *UpgradeDomainWise*: As atualizações do Windows são instaladas em um domínio de atualização por vez. (No máximo, todos os nós que pertencem a um domínio de atualização podem ir para um Windows Update.)<br><br> Para ajudar a decidir qual política é mais adequada para seu cluster, consulte a seção [perguntas frequentes](#frequently-asked-questions) .
|LogsDiskQuotaInMB   |Longo  <br> Os *1024*)               | O tamanho máximo dos logs do aplicativo de orquestração de patch em MB, que pode ser persistido localmente em nós.
| WUQuery               | Cadeia de caracteres<br>Os *IsInstalled = 0*)                | Consulte para obter atualizações do Windows. Para obter mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | *valor booleano* <br> (padrão: false)                 | Use esse sinalizador para controlar quais atualizações devem ser baixadas e instaladas. Os valores a seguir são permitidos <br>true – instala apenas as atualizações do sistema operacional Windows.<br>false – instala todas as atualizações disponíveis no computador.          |
| WUOperationTimeOutInMinutes | Int <br>Os *90*)                   | Especifica o tempo limite para qualquer operação de Windows Update (Pesquisar ou baixar ou instalar). Se a operação não for concluída dentro do tempo limite especificado, ela será anulada.       |
| WURescheduleCount     | Int <br> Os *5*)                  | O número máximo de vezes que o serviço reagendará o Windows Update se uma operação falhar de forma persistente.          |
| WURescheduleTimeInMinutes | Int <br>Os *30*) | O intervalo no qual o serviço reagendará as atualizações do Windows se a falha persistir. |
| WUFrequency           | Cadeia de caracteres separada por vírgulas (padrão: *Semanalmente, quarta-feira, 7:00:00*)     | A frequência de instalação das atualizações do Windows. O formato e os valores possíveis são: <br>&nbsp; @ no__t-1-mensalmente: DD, HH: MM: SS (por exemplo, *mensal, 5, 12:22:32*)<br>Os valores permitidos para o campo DD (Day) são números de 1 a 28 e "Last". <br> &nbsp; @ no__t-1-Weekly, DAY, HH: MM: SS (por exemplo, *Weekly, terça-feira, 12:22:32*)  <br> &nbsp; @ no__t-1-Daily, HH: MM: SS (por exemplo, *Daily, 12:22:32*)  <br> &nbsp; @ no__t-1 @ no__t-2*None* indica que as atualizações do Windows não devem ser feitas.  <br><br> Os horários estão em UTC.|
| AcceptWindowsUpdateEula | Booleano <br>(Padrão: *true*) | Ao definir esse sinalizador, o aplicativo aceita o contrato de licença de usuário final para Windows Update em nome do proprietário do computador.              |

> [!TIP]
> Se você quiser que as atualizações do Windows ocorram imediatamente, defina `WUFrequency` em relação ao tempo de implantação do aplicativo. Por exemplo, suponha que você tenha um cluster de teste de cinco nós e planeje implantar o aplicativo em cerca de 5:00 PM UTC. Se você assumir que a atualização ou a implantação do aplicativo levará 30 minutos no máximo, defina o WUFrequency como *diário, 17:30:00*.

## <a name="deploy-poa"></a>Implantar POA

1. Conclua todas as etapas de pré-requisito para preparar o cluster.
1. Implante POA como qualquer outro aplicativo Service Fabric. Para implantá-lo usando o PowerShell, consulte [implantar e remover aplicativos usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
1. Para configurar o aplicativo no momento da implantação, passe o `ApplicationParameter` para o `New-ServiceFabricApplication` cmdlet. Para sua conveniência, fornecemos o script Deploy. ps1 junto com o aplicativo. Para usar o script:

    - Conecte-se a um Cluster Service Fabric `Connect-ServiceFabricCluster`usando.
    - Execute o script do PowerShell Deploy. ps1 com o `ApplicationParameter` valor apropriado.

> [!NOTE]
> Mantenha o script e a pasta do aplicativo *PatchOrchestrationApplication* no mesmo diretório.

## <a name="upgrade-poa"></a>Atualizar POA

Para atualizar sua versão do POA usando o PowerShell, siga as instruções em [Service Fabric atualização do aplicativo usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-poa"></a>Remover POA

Para remover o aplicativo, siga as instruções em [implantar e remover aplicativos usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Para sua conveniência, fornecemos o script undeploy. ps1 junto com o aplicativo. Para usar o script:

  - Conecte-se a um Cluster Service Fabric ```Connect-ServiceFabricCluster```usando.
  - Execute o script do PowerShell undeploy. ps1.

> [!NOTE]
> Mantenha o script e a pasta do aplicativo *PatchOrchestrationApplication* no mesmo diretório.

## <a name="view-the-windows-update-results"></a>Exibir os resultados de Windows Update

O POA expõe as APIs REST para exibir os resultados históricos para os usuários. Aqui está um exemplo do resultado JSON:

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

Os campos JSON são descritos na tabela a seguir:

Campo | Valores | Detalhes
-- | -- | --
OperationResult | 0-êxito<br> 1-êxito com erros<br> 2-com falha<br> 3-anulado<br> 4-anulado com tempo limite | Indica o resultado da operação geral, que normalmente envolve a instalação de uma ou mais atualizações.
resultCode | O mesmo que OperationResult | Esse campo indica o resultado da operação de instalação para uma atualização individual.
OperationType | 1-instalação<br> 0-Pesquisar e baixar| Por padrão, a instalação é o único OperationType mostrado nos resultados.
WindowsUpdateQuery | O padrão é "IsInstalled = 0" | A consulta Windows Update que foi usada para Pesquisar atualizações. Para obter mais informações, consulte [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
RebootRequired | true-a reinicialização foi necessária<br> false-a reinicialização não foi necessária | Indica se uma reinicialização foi necessária para concluir a instalação das atualizações.
OperationStartTime | DateTime | Indica a hora em que a operação (download/instalação) foi iniciada.
Operationtime | DateTime | Indica a hora em que a operação (download/instalação) foi concluída.
Resultado | 0-êxito<br> outro-falha| Indica o motivo da falha da atualização do Windows com a UpdateId "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Se nenhuma atualização for agendada ainda, o resultado JSON estará vazio.

Entre no cluster para consultar Windows Update resultados. Descubra o endereço IP de réplica para o endereço principal do serviço de coordenador e abra a seguinte URL no navegador: http://&lt;REPLICA-IP @ no__t-1: &lt;ApplicationPort @ no__t-3/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

O ponto de extremidade REST para o serviço de coordenador tem uma porta dinâmica. Para verificar a URL exata, consulte Service Fabric Explorer. Por exemplo, os resultados estão disponíveis em *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* .

![Imagem do ponto de extremidade REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Se o proxy reverso estiver habilitado no cluster, você também poderá acessar a URL de fora do cluster.

O ponto de extremidade que você precisa atingir é *http://&lt;SERVERURL @ no__t-2: &lt;REVERSEPROXYPORT @ no__t-4/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

Para habilitar o proxy reverso no cluster, siga as instruções em [proxy reverso no Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Depois que o proxy reverso é configurado, todos os microserviços no cluster que expõem um ponto de extremidade HTTP são endereçáveis de fora do cluster.

## <a name="diagnostics-and-health-events"></a>Diagnóstico e eventos de integridade

Esta seção discute como depurar ou diagnosticar problemas com atualizações de patch por meio do POA em clusters Service Fabric.

> [!NOTE]
> Para obter muitos dos seguintes aprimoramentos de diagnóstico, desconhecidos, você deve ter o POA versão 1.4.0 ou posterior instalado.

O NTService do agente de nó cria [tarefas de reparo](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) para instalar atualizações nos nós. Cada tarefa é preparada pelo serviço de coordenador de acordo com a política de aprovação de tarefa. Por fim, as tarefas preparadas são aprovadas por Gerenciador de Reparos, que não aprova nenhuma tarefa se o cluster estiver em um estado não íntegro. 

Para ajudá-lo a entender como as atualizações procedem em um nó, vamos passar passo a passo:

1. NodeAgentNTService, em execução em cada nó, procura atualizações disponíveis do Windows no horário agendado. Se as atualizações estiverem disponíveis, elas serão baixadas no nó.

1. Depois que as atualizações forem baixadas, o NTService do agente de nó criará uma tarefa de reparo correspondente para o nó com o nome *POS___ @ no__t-1unique_id >* . Você pode exibir essas tarefas de reparo usando o cmdlet [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) ou usando o SFX na seção detalhes do nó. Depois que a tarefa de reparo for criada, ela passará rapidamente para o [estado *declarado* ](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).

1. Periodicamente, o serviço de coordenador procura tarefas de reparo no estado *declarado* e, em seguida, atualiza-as para *preparando* o estado com base em a taskapprovalpolicy. Se A taskapprovalpolicy estiver configurado para ser NodeWise, uma tarefa de reparo que corresponde a um nó será preparada somente se nenhuma outra tarefa de reparo estiver atualmente em *preparação*, *aprovado*, em *execução*ou em estado de *restauração* . 

   Da mesma forma, no caso de UpgradeWise A taskapprovalpolicy, há tarefas nos Estados anteriores somente para os nós que pertencem ao mesmo domínio de atualização. Depois que uma tarefa de reparo for movida para o estado de *preparação* , o nó de Service Fabric correspondente será [desabilitado](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) com a intenção definida para *reiniciar*.

   POA versões 1.4.0 e posteriores postam eventos com a propriedade ClusterPatchingStatus em CoordinatorService para exibir os nós que estão sendo corrigidos. As atualizações são instaladas em _poanode_0, conforme mostrado na imagem a seguir:

    [![Image do status de aplicação de patch do cluster](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Depois que o nó é desabilitado, a tarefa de reparo é movida para o estado de *execução* . 
   
   > [!NOTE]
   > Um nó que está preso no estado *desabilitado* pode bloquear uma nova tarefa de reparo, o que interrompe a operação de aplicação de patch no cluster.

1. Quando a tarefa de reparo estiver em estado de *execução* , a instalação do patch nesse nó será iniciada. Depois que o patch é instalado, o nó pode ou não ser reiniciado, dependendo do patch. Em seguida, a tarefa de reparo é movida para o estado de *restauração* , que reabilita o nó. A tarefa de reparo é marcada como concluída.

   No POA versões 1.4.0 e posteriores, você pode encontrar o status da atualização exibindo os eventos de integridade no NodeAgentService com a propriedade WUOperationStatus-\<NodeName >. As seções realçadas nas imagens a seguir mostram o status das atualizações do Windows nos nós *poanode_0* e *poanode_2*:

   [![Image do status da operação de Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Image do status da operação de Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Você também pode obter os detalhes usando o PowerShell. Para fazer isso, você se conecta ao cluster e busca o estado da tarefa de reparo usando [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). 
   
   No exemplo a seguir, a tarefa "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" está no estado *DownloadComplete* . Isso significa que as atualizações foram baixadas no nó *poanode_2* e a instalação será tentada quando a tarefa for movida para o estado de *execução* .

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Se mais problemas ainda estiverem localizados, entre na VM (máquina virtual) ou nas VMs e saiba mais sobre eles usando os logs de eventos do Windows. A tarefa de reparo mencionada anteriormente pode existir somente nos seguintes subestados do executor:

      ExecutorSubState | Descrição
    -- | -- 
      None=1 |  Implica que não havia uma operação em andamento no nó. O estado pode estar em transição.
      DownloadCompleted=2 | Implica que a operação de download foi concluída com êxito, falha parcial ou falha.
      InstallationApproved=3 | Implica que a operação de download foi concluída anteriormente e Gerenciador de Reparos aprovou a instalação.
      InstallationInProgress=4 | Corresponde ao estado de execução da tarefa de reparo.
      InstallationCompleted = 5 | Implica que a instalação foi concluída com êxito, êxito parcial ou falha.
      RestartRequested=6 | Implica que a instalação do patch foi concluída e que há uma ação de reinicialização pendente no nó.
      RestartNotNeeded=7 |  Implica que a reinicialização não era necessária após a conclusão da instalação do patch.
      RestartCompleted = 8 | Implica que a reinicialização foi concluída com êxito.
      OperationCompleted=9 | A operação de Windows Update foi concluída com êxito.
      OperationAborted=10 | Implica que a operação de Windows Update foi anulada.

1. No POA versões 1.4.0 e posteriores, quando uma tentativa de atualização de nó é concluída, um evento com a propriedade "WUOperationStatus-[NodeName]" é Postado no NodeAgentService para notificá-lo quando a próxima tentativa de baixar e instalar as atualizações do Windows começar. Isso é exibido na imagem a seguir:

     [![Image do status da operação de Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Registos de diagnósticos

Os logs do aplicativo de orquestração de patch são coletados como parte dos logs do Service Fabric Runtime.

Você pode capturar logs usando a ferramenta de diagnóstico ou pipeline de sua escolha. O POA usa as seguintes IDs de provedor fixas para registrar eventos por meio da [origem do evento](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1):

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Relatórios de integridade

O POA também publica relatórios de integridade no serviço de agente de nó ou no serviço de coordenador nos seguintes cenários:

* O NTService do agente do nó está inoperante

   Se o NTService do agente do nó estiver inoperante em um nó, um relatório de integridade no nível de aviso será gerado em relação ao serviço do agente do nó.

* O serviço de Gerenciador de Reparos não está habilitado

   Se o serviço de Gerenciador de Reparos não for encontrado no cluster, um relatório de integridade no nível de aviso será gerado para o serviço de coordenador.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Por que vejo o meu cluster em um estado de erro quando POA está em execução?**

R: Durante o processo de instalação, o POA desabilita ou reinicia os nós, o que pode resultar temporariamente em um cluster não íntegro.

Dependendo da política do aplicativo, um nó pode ficar inativo durante uma operação de aplicação de patch *ou* um domínio de atualização inteiro pode ficar inativo de uma só vez.

Ao final da instalação das atualizações do Windows, os nós são reabilitados após a reinicialização.

No exemplo a seguir, o cluster passou para um estado de erro temporariamente porque dois nós estavam inativos e a política de MaxPercentageUnhealthyNodes foi violada. O erro é temporário até que a operação de aplicação de patch possa começar.

![Imagem de cluster não íntegro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Se o problema persistir, consulte a seção solução de problemas.

**P: O que posso fazer se POA estiver em um estado de aviso?**

R: Verifique se um relatório de integridade Postado no aplicativo indica a causa raiz. Normalmente, o aviso contém detalhes do problema. Se o problema for transitório, espera-se que o aplicativo se recupere automaticamente.

**P: O que posso fazer se o cluster não estiver íntegro e precisar fazer uma atualização urgente do sistema operacional?**

R: O POA não instala atualizações enquanto o cluster não está íntegro. Tente colocar o cluster em um estado íntegro para desbloquear o fluxo de trabalho do POA.

**P: Devo definir A taskapprovalpolicy como "NodeWise" ou "UpgradeDomainWise" para o meu cluster?**

R: A configuração "UpgradeDomainWise" acelera o reparo geral do cluster ao aplicar patches em paralelo a todos os nós que pertencem a um domínio de atualização. Durante o processo, os nós que pertencem a um domínio de atualização inteiro ficam indisponíveis (no [estado *desabilitado* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)).

Por outro lado, a configuração "NodeWise" corrige apenas um nó por vez, o que significa que a aplicação geral de patches do cluster pode demorar mais. No entanto, apenas um nó no máximo não estará disponível (no estado *desabilitado* ) durante o processo de aplicação de patch.

Se o cluster puder tolerar a execução em um número N-1 de domínios de atualização durante o ciclo de aplicação de patch (em que N é o número total de domínios de atualização no cluster), você poderá definir a política como "UpgradeDomainWise". Caso contrário, defina-o como "NodeWise".

**P: Quanto tempo leva para corrigir um nó?**

R: A aplicação de patch de um nó pode levar de minutos (por exemplo, [atualizações de definições do Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)) a horas (por exemplo, [atualizações cumulativas do Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). O tempo necessário para corrigir um nó depende principalmente de: 
 - O tamanho das atualizações.
 - O número de atualizações, que devem ser aplicadas em uma janela de aplicação de patch.
 - O tempo que leva para instalar as atualizações, reinicialize o nó (se necessário) e conclua as etapas de instalação após a reinicialização.
 - O desempenho da VM, da máquina e das condições de rede.

**P: Quanto tempo leva para corrigir um cluster inteiro?**

R: O tempo necessário para corrigir um cluster inteiro depende de:

- O tempo necessário para aplicar patch a um nó.

- A política do serviço de coordenador. A política padrão, "NodeWise", resulta na aplicação de patch em apenas um nó por vez, uma abordagem que é mais lenta do que usar "UpgradeDomainWise". 

   Por exemplo: Se um nó levar cerca de 1 hora para ser corrigido, aplicar patches em um cluster de 20 nós (mesmo tipo de nós) com 5 domínios de atualização, cada um contendo 4 nós, exigirá:
    - Para "NodeWise": ~ 20 horas.
    - Para "UpgradeDomainWise": ~ 5 horas.

- O carregamento do cluster. Cada operação de aplicação de patch requer a realocação da carga de trabalho do cliente para outros nós disponíveis no cluster. Um nó que está sendo corrigido estaria no estado de [ *desabilitação* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) durante esse tempo. Se o cluster estiver executando perto da carga de pico, o processo de desabilitação levará mais tempo. Portanto, o processo geral de aplicação de patch pode parecer lento em tais condições sob medida.

- Falhas de integridade do cluster durante a aplicação de patch. Qualquer [degradação](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) na [integridade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) interromperia o processo de aplicação de patch. Esse problema seria adicionado ao tempo geral necessário para aplicar o patch a todo o cluster.

**P: Por que vejo algumas atualizações na Windows Update resultados obtidos por meio da API REST, mas não sob o histórico de Windows Update no computador?**

R: Algumas atualizações do produto aparecem apenas em seu próprio histórico de atualização ou patch. Por exemplo, as atualizações do Windows Defender podem ou não ser exibidas no histórico de Windows Update no Windows Server 2016.

**P: POA pode ser usado para corrigir meu cluster de desenvolvimento (cluster de um nó)?**

R: Não, o POA não pode ser usado para corrigir um cluster de um nó. Essa limitação é por design, porque [Service Fabric serviços do sistema](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) ou outros aplicativos de cliente incorrem em tempo de inatividade. Portanto, os trabalhos de reparo de patch nunca seriam aprovados pelo Gerenciador de Reparos.

**P: Como fazer nós de cluster de patch no Linux?**

R: Para saber mais sobre como orquestrar atualizações no Linux, confira [conjunto de dimensionamento de máquinas virtuais do Azure atualizações automáticas de imagem do sistema operacional](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

**P: Por que o ciclo de atualização está demorando tanto tempo?**

R: Consulte o resultado JSON, insira o ciclo de atualização de todos os nós e, em seguida, você pode tentar descobrir o tempo gasto pela instalação da atualização em cada nó usando OperationStartTime e Operationtime (OperationCompletionTime). 

Se houver uma janela de tempo grande na qual nenhuma atualização está ocorrendo, o cluster poderá estar em um estado de erro e, consequentemente, Gerenciador de Reparos não poderá aprovar nenhuma tarefa de reparo POA. Se a instalação da atualização estiver demorando muito em qualquer nó, esse nó poderá não ter sido atualizado há algum tempo. Muitas atualizações podem ter uma instalação pendente, o que pode resultar em atrasos. 

Também pode ser possível que a aplicação de patches de nó seja bloqueada porque está presa no estado de *desabilitação* . Isso geralmente acontece porque a desabilitação do nó pode levar a situações de perda de dados ou de quorum.

**P: Por que o nó deve ser desabilitado quando o POA estiver aplicando patches nele?**

R: POA desabilita o nó com uma intenção de *reinicialização* , que interrompe ou realoca todos os serviços Service Fabric que estão em execução no nó. O POA faz isso para garantir que os aplicativos não acabem usando uma combinação de DLLs novas e antigas, portanto, é recomendável não aplicar patches em um nó sem desabilitá-lo.

## <a name="disclaimers"></a>Exclusões de Responsabilidade

- POA aceita o contrato de licença de usuário final para Windows Update em nome do usuário. Opcionalmente, a configuração pode ser desativada na configuração do aplicativo.

- O POA coleta a telemetria para acompanhar o uso e o desempenho. A telemetria do aplicativo segue a configuração da configuração de telemetria do Service Fabric Runtime (que está ativada por padrão).

## <a name="troubleshooting"></a>Resolução de problemas

Esta seção fornece soluções de solução de problemas possíveis para problemas com nós de aplicação de patches.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Um nó não está voltando para o estado ativo

* O nó pode estar paralisado ao *desabilitar* o estado porque:

  - Uma verificação de segurança está pendente. Para corrigir essa situação, certifique-se de que nós suficientes estejam disponíveis em um estado íntegro.

* O nó pode estar paralisado no estado *desabilitado* porque:

  - Ele foi desabilitado manualmente.
  - Ele foi desabilitado devido a um trabalho de infraestrutura do Azure em andamento.
  - Ele foi desabilitado temporariamente pelo POA para corrigir o nó.

* O nó pode estar preso em um estado inativo porque:

  - Ele foi colocado em um estado inativo manualmente.
  - Ele está passando por uma reinicialização (que pode ser disparada por POA).
  - Ele tem uma VM ou máquina com defeito, ou está tendo problemas de conectividade de rede.

### <a name="updates-were-skipped-on-some-nodes"></a>As atualizações foram ignoradas em alguns nós

O POA tenta instalar uma atualização do Windows de acordo com a política de reagendamento. O serviço tenta recuperar o nó e ignorar a atualização de acordo com a política de aplicativo.

Nesse caso, um relatório de integridade no nível de aviso é gerado em relação ao serviço do agente do nó. O resultado de Windows Update também contém o motivo possível para a falha.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>A integridade do cluster vai para o erro enquanto a atualização está sendo instalada

Um Windows Update com falha pode reduzir a integridade de um aplicativo ou cluster em um nó ou domínio de atualização específico. POA descontinuará qualquer operação de Windows Update subsequente até que o cluster esteja íntegro novamente.

Um administrador deve intervir e determinar por que o aplicativo ou o cluster se tornou não íntegro devido à Windows Update.

## <a name="poa-release-notes"></a>Notas de versão do POA

>[!NOTE]
> Para POA versões 1.4.0 e posteriores, você pode encontrar notas de versão e versões na [página de lançamento do aplicativo de orquestração de patch](https://github.com/microsoft/Service-Fabric-POA/releases/) no github.

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
- Corrigindo a regressão em que POA 1.3.0 não funcionará no Windows Server 2012 R2 ou anterior devido a uma falha ao desabilitar as atualizações automáticas. 
- Corrigindo o bug em que a configuração InstallWindowsOSOnlyUpdates é sempre escolhida como verdadeira.
- Alterando o valor padrão de InstallWindowsOSOnlyUpdates para false.

### <a name="version-132"></a>1\.3.2 da versão
- Corrigir um problema que afeta o ciclo de vida da aplicação de patch em um nó, se houver nós com um nome que seja um subconjunto do nome do nó atual. Para esses nós, é possível que os patches tenham sido perdidos ou uma reinicialização esteja pendente.