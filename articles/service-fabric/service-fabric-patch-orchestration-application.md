---
title: Remenda o sistema operativo Windows no seu cluster De Tecido de Serviço
description: Este artigo discute como automatizar o sistema operativo patching em um cluster de Tecido de Serviço usando patch orchestration Application.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: 857a4da0b24d600ecc572933af578e2e8faf501a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80366322"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Remenda o sistema operativo Windows no seu cluster De Tecido de Serviço

> 
> [!IMPORTANT]
> A partir de 30 de abril de 2019, a versão 1.2.* da Patch Orchestration Application já não é suportada. Certifique-se de fazer upgrade para a versão mais recente.

> [!NOTE]
> Obter [atualizações automáticas](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) de imagem de OS no seu conjunto de escala de máquina virtual é a melhor prática para manter o seu sistema operativo remendado em Azure. As atualizações automáticas de imagem baseadas em oso baseados em máquinas virtuais exigirão uma durabilidade prateada ou maior num conjunto de escala.
>

 Patch Orchestration Application (POA) é um invólucro em torno do serviço Azure Service Fabric Repair Manager, que permite o agendamento de patchs de sistema soro baseado na configuração para clusters não-Azure hospedados. O POA não é necessário para clusters não-Azure hospedados, mas a instalação de patch de agendamento por domínio de atualização é necessária para remendar os anfitriões do cluster Service Fabric sem incorrer em tempo de inatividade.

PoA é uma aplicação de Tecido de Serviço que automatiza o sistema operativo patching em um cluster de Tecido de Serviço sem incorrer em tempo de inatividade.

A POA fornece as seguintes funcionalidades:

- **Instalação automática**de atualização do sistema operativo. As atualizações do sistema operativo são automaticamente descarregadas e instaladas. Os nós do cluster são reiniciados conforme necessário sem incorrer no tempo de inatividade do cluster.

- **Patching consciente do cluster e integração da saúde.** Enquanto a POA está a aplicar atualizações, monitoriza a saúde dos nós do cluster. Os nós do cluster são atualizados um nó de cada vez ou um domínio de atualização de cada vez. Se a saúde do cluster diminuir devido ao processo de remendos, a correção é interrompida para evitar agravar o problema.

## <a name="internal-details-of-poa"></a>Detalhes internos da POA

O POA é composto pelos seguintes subcomponentes:

- **Serviço coordenador**: Este serviço imponente é responsável por:
    - Coordenar o trabalho de Atualização do Windows em todo o cluster.
    - Armazenar o resultado de operações completas do Windows Update.

- **Serviço de Agente de Nó**: Este serviço apátrida funciona em todos os nós de cluster de tecido de serviço. O serviço é responsável por:
    - A amarrar o Agente ntservice do nó.
    - Monitorização do Agente ntservice do nó.

- **Agente do nó NTService**: Este serviço Windows NT funciona com um privilégio de nível mais elevado (SYSTEM). Em contrapartida, o Serviço de Agente do Nó e o Serviço de Coordenador funcionam com um privilégio de nível inferior (SERVIÇO DE REDE). O serviço é responsável pela realização dos seguintes trabalhos de Atualização do Windows em todos os nós do cluster:
    - Desativar as atualizações automáticas do Windows no nó.
    - Descarregar e instalar as atualizações do Windows de acordo com a política que o utilizador forneceu.
    - Reiniciar a instalação de atualizações pós-Windows da máquina.
    - Envio dos resultados das atualizações do Windows para o Serviço de Coordenador.
    - Reportar relatórios de saúde se uma operação falhou depois de esgotar todas as tentativas.

> [!NOTE]
> O POA utiliza o serviço Service Fabric Repair Manager para desativar ou ativar o nó e efetuar verificações de saúde. A tarefa de reparação criada por POA acompanha o progresso do Windows Update para cada nó.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> A versão mínima .NET Framework necessária é de 4.6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Ativar o serviço DeReser Manager (se ainda não estiver a funcionar)

A POA requer que o serviço de Reparação seja ativado no cluster.

#### <a name="azure-clusters"></a>Aglomerados azure

Os clusters Azure no nível de durabilidade prateada têm o serviço Dereparo Gestor ativado por padrão. Os clusters azure no nível de durabilidade do ouro podem ou não ter o serviço de Reparação ativo, dependendo de quando esses clusters foram criados. Os clusters Azure no nível de durabilidade do bronze, por padrão, não têm o serviço Dereparador ativo. Se o serviço já estiver ativado, pode vê-lo a funcionar na secção de serviços de sistema no Service Fabric Explorer.

##### <a name="the-azure-portal"></a>O portal do Azure
Pode ativar o Reparador a partir do portal Azure quando configurar o cluster. Quando estiver a configurar o cluster, selecione a opção Incluir O Gestor de **Reparação** em **funcionalidades add-on**.

![Imagem de habilitar gerente de reparação do portal Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>O modelo de implementação do Gestor de Recursos Azure
Em alternativa, pode utilizar o modelo de implementação do Gestor de [Recursos Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para ativar o serviço DeRerepair Manager em novos e já existentes clusters de Tecido de Serviço. Obtenha o modelo para o cluster que pretende implementar. Pode utilizar os modelos de amostra ou criar um modelo de modelo de implementação personalizado do Gestor de Recursos Azure. 

Para ativar o serviço do Gestor de Reparação utilizando o modelo de implementação do Gestor de [Recursos Azure,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)faça o seguinte:

1. Verifique se `apiVersion` está definido para *2017-07-01-pré-visualização* para o recurso *Microsoft.ServiceFabric/clusters.* Se for diferente, precisa de `apiVersion` atualizar para *2017-07-01-pré-visualização* ou mais tarde:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Ativar o serviço do `addonFeatures` Gestor de `fabricSettings` Reparação adicionando a seguinte secção após a secção:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Depois de atualizar o seu modelo de cluster com estas alterações, aplique-as e deixe a atualização terminar. Agora pode ver o serviço de Reparação a funcionar no seu cluster. Chama-se *tecido:/System/RepairManagerService* na secção de serviços do sistema no Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Aglomerados autónomos no local

Para ativar o serviço Repair Manager num novo ou existente cluster de tecido de serviço, pode utilizar as definições de [Configuração para o cluster Windows autónomo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Para ativar o serviço DeRerepair Manager:

1. Verifique se `apiVersion` as [configurações gerais](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) do cluster estão definidas para *04-2017* ou posteriormente, como mostrado aqui:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Ativar o serviço Do `addonFeatures` Gestor de `fabricSettings` Reparação adicionando a seguinte secção após a secção, como mostrado aqui:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Atualize o manifesto do cluster com estas alterações utilizando o manifesto de cluster [atualizado, criar um novo cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) ou atualizar a [configuração do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). 

   Depois de o cluster estar a funcionar com um manifesto de cluster atualizado, pode ver o serviço Do Gestor de Reparação a funcionar no seu cluster. Chama-se *tecido:/Sistema/RepairManagerService,* e está na secção de serviços do sistema no Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Configure atualizações do Windows para todos os nós

As atualizações automáticas do Windows podem levar à perda de disponibilidade, uma vez que vários nós de cluster podem reiniciar ao mesmo tempo. O POA, por padrão, tenta desativar as atualizações automáticas do Windows em cada nó de cluster. No entanto, se as definições forem geridas por um administrador ou por uma Política de Grupo, recomendamos que a definição da política de Atualização do Windows "notifique antes de descarregar" explicitamente.

## <a name="download-the-application-package"></a>Descarregue o pacote de candidaturas

Para descarregar o pacote de aplicações, vá à página de lançamento da Aplicação de [Orquestração de Patch](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) no GitHub.

## <a name="configure-poa-behavior"></a>Configurar o comportamento da POA

Pode configurar o comportamento da POA para satisfazer as suas necessidades. Sobrepor-se aos valores predefinidos ao passar o parâmetro da aplicação enquanto está a criar ou atualizar a aplicação. Pode fornecer parâmetros de `ApplicationParameter` aplicação `Start-ServiceFabricApplicationUpgrade` `New-ServiceFabricApplication` especificando os cmdlets ou cmdlets.

| Parâmetro        | Tipo                          | Detalhes |
|:-|-|-|
|MaxResultsTocache    |Longo                              | O número máximo de resultados do Windows Update que deve ser colocado em cache. <br><br>O valor padrão é 3000, assumindo que: <br> &nbsp;&nbsp;- O número de nós é de 20. <br> &nbsp;&nbsp;- O número de atualizações para um nó por mês é de 5. <br> &nbsp;&nbsp;- O número de resultados por operação pode ser de 10. <br> &nbsp;&nbsp;- Os resultados dos últimos três meses devem ser armazenados. |
|Política de Desaprovação de Tarefas   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy indica a política que deve ser utilizada pelo Serviço de Coordenador para instalar atualizações do Windows nos nós do cluster do Tecido de Serviço.<br><br>Os valores permitidos são: <br>*NodeWise*: As atualizações do Windows são instaladas com um nó de cada vez. <br> *UpgradeDomainWise*: As atualizações do Windows são instaladas num domínio de atualização de cada vez. (No máximo, todos os nós pertencentes a um domínio de atualização podem optar por uma atualização do Windows.)<br><br> Para ajudar a decidir qual a política mais adequada para o seu cluster, consulte a secção [DE PERGUNTAS FREQUENTES.](#frequently-asked-questions)
|LogsDiskQuotaInMB   |Longo  <br> (Predefinido: *1024)*               | O tamanho máximo dos registos de aplicativos de orquestração de patch em MB, que pode ser pernoia do local em nós.
| WUQuery               | string<br>(Predefinido: *IsInstalled=0*)                | Consulta para obter atualizações do Windows. Para mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstalarWindowsOSOnlyUpdates | *Booleano* <br> (predefinição: falso)                 | Utilize esta bandeira para controlar quais as atualizações que devem ser descarregadas e instaladas. Os seguintes valores são permitidos <br>verdade - Instala apenas atualizações do sistema operativo Windows.<br>falso - Instala todas as atualizações disponíveis na máquina.          |
| WUOperationTimeTimeInMinutes | int <br>(Predefinido: *90)*                   | Especifica o prazo limite para qualquer operação de Atualização do Windows (pesquisar ou descarregar ou instalar). Se a operação não estiver concluída dentro do prazo previsto, é abortada.       |
| WURescheduleCount     | int <br> (Predefinido: *5*)                  | O número máximo de vezes que o serviço remarca a atualização do Windows se uma operação falhar persistentemente.          |
| WUReScheduleTimeInMinutes | int <br>(Predefinido: *30)* | O intervalo em que o serviço remarca as atualizações do Windows caso persista falha. |
| WUFrequency           | Corda separada com vírina (Padrão: *Semanal, quarta-feira, 7:00:00*)     | A frequência para instalar atualizações do Windows. O formato e os valores possíveis são: <br>&nbsp;&nbsp;- Mensal: DD, HH:MM:SS (por exemplo, *Mensal, 5,12:22:32*)<br>Os valores permitidos para o campo DD (dia) são números de 1 a 28 e "último". <br> &nbsp;&nbsp;- Semanal, DIA, HH:MM:SS (por exemplo, *Semanal, terça-feira, 12:22:32*)  <br> &nbsp;&nbsp;- Diariamente, HH:MM:SS (por exemplo, *Diariamente, 12:22:32*)  <br> &nbsp;&nbsp;-  *Nenhuma* indica que as atualizações do Windows não devem ser feitas.  <br><br> Os tempos estão na UTC.|
| AceitarWindowsUpdateEula | Booleano <br>(Padrão: *verdadeiro)* | Ao definir esta bandeira, a aplicação aceita o Contrato de Licença de Utilizador Final para Atualização do Windows em nome do proprietário da máquina.              |

> [!TIP]
> Se pretender que as atualizações do `WUFrequency` Windows ocorram imediatamente, detetete em relação ao tempo de implementação da aplicação. Por exemplo, suponha que você tem um cluster de teste de cinco nós e planeie implementar a app por volta das 17:00 UTC. Se assumir que a atualização ou implementação da aplicação demora no máximo 30 minutos, desempente a WUFrequency como *Diariamente, 17:30:00*.

## <a name="deploy-poa"></a>Implementar POA

1. Termine todos os passos pré-requisitos para preparar o cluster.
1. Implementar POA como qualquer outra app de Tecido de Serviço. Para implantá-lo utilizando o PowerShell, consulte [a implementação e a remoção de aplicações utilizando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
1. Para configurar a aplicação no momento `ApplicationParameter` da `New-ServiceFabricApplication` implantação, passe a cmdlet para o cmdlet. Para sua conveniência, fornecemos o script Deploy.ps1 juntamente com a aplicação. Para utilizar o guião:

    - Ligue-se a um `Connect-ServiceFabricCluster`cluster de tecido de serviço utilizando .
    - Execute o script PowerShell Deploy.ps1 com o valor adequado. `ApplicationParameter`

> [!NOTE]
> Mantenha o script e a pasta de aplicação *PatchOrchestrationApplication* no mesmo diretório.

## <a name="upgrade-poa"></a>Upgrade POA

Para atualizar a sua versão POA utilizando o PowerShell, siga as instruções na atualização da [aplicação Service Fabric utilizando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-poa"></a>Remover POA

Para remover a aplicação, siga as instruções em ['Enviar' e remova as aplicações utilizando powerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Para sua conveniência, fornecemos o script Undeploy.ps1 juntamente com a aplicação. Para utilizar o guião:

  - Ligue-se a um ```Connect-ServiceFabricCluster```cluster de tecido de serviço utilizando .
  - Execute o script PowerShell Undeploy.ps1.

> [!NOTE]
> Mantenha o script e a pasta de aplicação *PatchOrchestrationApplication* no mesmo diretório.

## <a name="view-the-windows-update-results"></a>Ver os resultados da Atualização do Windows

A POA expõe as APIs rest para exibir os resultados históricos aos utilizadores. Aqui está um exemplo do resultado JSON:

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

Os campos JSON são descritos na tabela seguinte:

Campo | Valores | Detalhes
-- | -- | --
OperaçãoResult | 0 - Bem sucedido<br> 1 - Sucesso com Erros<br> 2 - Falhado<br> 3 - Abortado<br> 4 - Abortado com timeout | Indica o resultado do funcionamento global, que normalmente envolve a instalação de uma ou mais atualizações.
Código de Resultados | O mesmo que a Operação Result | Este campo indica o resultado da operação de instalação para uma atualização individual.
OperaçãoTipo | 1 - Instalação<br> 0 - Pesquisar e Descarregar| Por predefinição, a instalação é o único OperationType que é mostrado nos resultados.
WindowsUpdateQuery | Predefinido é "IsInstalled=0" | A consulta do Windows Update que foi usada para procurar atualizações. Para mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
Reiniciar Necessário | verdadeiro - reiniciar foi necessário<br> falso - reiniciar não era necessário | Indica se foi necessário um reboot para completar a instalação de atualizações.
Operação Início | DateTime | Indica a hora em que começou a operação (Download/Instalação).
Operação Tempo | DateTime | Indica a hora em que a operação (Download/Instalação) foi concluída.
HResult | 0 - Sucesso<br> outro - falha| Indica o motivo da falha da atualização do Windows com o atualização "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Se ainda não houver atualização, o resultado jSON está vazio.

Inscreva-se no cluster para consultar os resultados do Windows Update. Descubra o endereço IP da réplica para o endereço principal do Serviço coordenador&lt;e abra&gt;&lt;o&gt;seguinte URL do navegador: http:// REPLICA-IP : ApplicationPort /PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

O ponto final do REST para o Serviço de Coordenador tem uma porta dinâmica. Para verificar o URL exato, consulte o Service Fabric Explorer. Por exemplo, os resultados estão disponíveis em *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults*.

![Imagem do ponto final do REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Se o proxy inverso estiver ativado no cluster, também pode aceder ao URL de fora do cluster.

O ponto final que precisa de atingir é *http://&lt;&gt;SERVERURL :&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

Para ativar o proxy inverso no cluster, siga as instruções em [proxy inverso em Tecido de Serviço Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Após a configuração do proxy invertido, todos os microserviços do cluster que expõem um ponto final HTTP são endereçados de fora do cluster.

## <a name="diagnostics-and-health-events"></a>Diagnósticos e eventos de saúde

Esta secção discute como depurar ou diagnosticar problemas com atualizações de patch através de POA em clusters de Tecido de Serviço.

> [!NOTE]
> Para obter muitas das seguintes melhorias de autodiagnóstico, deverá ter a versão POA 1.4.0 ou posteriormente instalada.

O Agente nó NTService cria tarefas de [reparação](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) para instalar atualizações nos nódosos. Cada tarefa é então preparada pelo Serviço de Coordenador estoirar de acordo com a política de aprovação de tarefas. Finalmente, as tarefas preparadas são aprovadas pelo Reparo Manager, que não aprova qualquer tarefa se o cluster estiver em estado de saúde. 

Para ajudá-lo a entender como as atualizações prosseguem num nó, vamos passo a passo:

1. NodeAgentNTService, em execução em todos os nós, procura as atualizações disponíveis do Windows na hora programada. Se as atualizações estiverem disponíveis, descarrega-as no nó.

1. Após o download das atualizações, o Agente nó NTService cria uma tarefa de reparação correspondente para o nó com o nome *POS___\<unique_id>*. Pode ver estas tarefas de reparação utilizando o cmdlet [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) ou utilizando o SFX na secção de detalhes do nó. Após a criação da tarefa de reparação, passa rapidamente para o estado [ *reclamado.* ](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet)

1. O Serviço de Coordenador procura periodicamente tarefas de reparação em estado *reclamado* e, em seguida, atualiza-as para *preparar* o estado com base na Política de Reprovação de Tarefas. Se a Política de Reparação de Tarefas estiver configurada para ser nonodeWise, uma tarefa de reparação que corresponde a um nó só está preparada se nenhuma outra tarefa de reparação estiver atualmente em *Preparação,* *Aprovada,* *Execução*ou *Restaurar* o estado. 

   Da mesma forma, no caso da ActualizaWise TaskApprovalPolicy, existem tarefas nos estados anteriores apenas para nós que pertencem ao mesmo domínio de atualização. Depois de uma tarefa de reparação ser transferida para o estado *de preparação,* o nó de tecido de serviço correspondente é [desativado](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) com a intenção de *reiniciar*.

   As versões POA 1.4.0 e posteriormente publicar eventos com a propriedade ClusterPatchingStatus no CoordinatorService para exibir os nós que estão sendo remendados. As atualizações são instaladas em _poanode_0, como mostra a seguinte imagem:

    [![Imagem do estado de remendo de cluster](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Depois de o nó ser desativado, a tarefa de reparação é transferida para o estado *de execução.* 
   
   > [!NOTE]
   > Um nó que está preso no estado *de deficientes* pode bloquear uma nova tarefa de reparação, que interrompe a operação de remendo no cluster.

1. Quando a tarefa de reparação estiver em estado *de execução,* inicia-se a instalação do patch no nó. Após a instalação do patch, o nó pode ou não ser reiniciado, dependendo do patch. Em seguida, a tarefa de reparação é transferida para o estado *de Restauro,* que reativa o nó. A tarefa de reparação é então marcada como concluída.

   Nas versões POA 1.4.0 e posteriormente, pode encontrar o estado da atualização visualizando\<os eventos de saúde no NodeAgentService com a propriedade WUOperationStatus- NodeName>. As secções em destaque nas seguintes imagens mostram o estado das atualizações do Windows nos nódosos *poanode_0* e *poanode_2:*

   [![Imagem do estado de funcionamento do Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Imagem do estado de funcionamento do Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Também pode obter os detalhes utilizando o PowerShell. Para isso, ligue-se ao cluster e obtenha o estado da tarefa de reparação utilizando a [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). 
   
   No exemplo seguinte, a tarefa "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" está no estado *downloadComplete.* Significa que as atualizações foram descarregadas no nó *poanode_2,* e a instalação será tentada quando a tarefa passar para o estado *de Execução.*

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Se ainda houver mais problemas, inicie sessão na sua máquina virtual (VM) ou VMs e aprenda sobre eles utilizando registos de eventos do Windows. A tarefa de reparação anteriormente mencionada só pode existir nas seguintes sub-propriedades executoras:

      ExecutorSubState | Descrição
    -- | -- 
      Nenhum=1 |  Implica que não houve uma operação em curso no nó. O Estado pode estar em transição.
      DownloadCompleted=2 | Implica que a operação de descarregamento foi concluída com sucesso, falha parcial ou falha.
      InstalaçõesAprovadas=3 | Implica que a operação de descarregamento foi concluída mais cedo e o Gestor de Reparação aprovou a instalação.
      InstalaçõesInProgress=4 | Corresponde ao estado de execução da tarefa de reparação.
      InstalaçãoConcluída=5 | Implica que a instalação foi concluída com sucesso, sucesso parcial ou falha.
      ReiniciarSolicitado=6 | Implica que a instalação do patch foi concluída e há uma ação pendente de reinício no nó.
      RestartNotNeeded=7 |  Implica que o reinício não foi necessário após a instalação do patch ter sido concluída.
      RestartCompleted=8 | Implica que o recomeço foi concluído com sucesso.
      OperaçãoCompleted=9 | A operação Windows Update foi concluída com sucesso.
      OperaçãoAbortado=10 | Implica que a operação Windows Update foi abortada.

1. Nas versões POA 1.4.0 e mais tarde, quando uma tentativa de atualização de nó termina, um evento com a propriedade "WUOperationStatus-[NodeName]" é publicado no NodeAgentService para notificá-lo quando a próxima tentativa de descarregar e instalar as atualizações do Windows começará. Isto é apresentado na seguinte imagem:

     [![Imagem do estado de funcionamento do Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Registos de diagnósticos

Os registos de aplicações de orquestração de patch são recolhidos como parte dos registos de tempo de execução do Tecido de Serviço.

Pode capturar registos utilizando a ferramenta de diagnóstico ou o oleoduto à sua escolha. A POA utiliza as seguintes IDs fixas para registar eventos através da [fonte do evento:](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Relatórios de saúde

A POA também publica relatórios de saúde contra o Serviço de Agente do Nó ou o Serviço de Coordenador nos seguintes cenários:

* O Agente ntservice do Nó está em baixo.

   Se o Agente ntservice do Nó estiver em baixo num nó, um relatório de saúde de nível de aviso é gerado contra o Serviço de Agente do Nó.

* O serviço DeReparo Não está ativado

   Se o serviço de Reparação não for encontrado no cluster, é gerado um relatório de saúde de nível de alerta para o Serviço de Coordenador.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Por que vejo o meu cluster num estado de erro quando a POA está a funcionar?**

R: Durante o processo de instalação, o POA desativa ou reinicia os nós, o que pode resultar temporariamente num cluster pouco saudável.

Dependendo da política da aplicação, qualquer um dos nós pode descer durante uma operação de remendo *ou* todo um domínio de atualização pode descer tudo de uma vez.

No final da instalação de atualizações do Windows, os nós são reativados após o reinício.

No exemplo seguinte, o cluster foi para um estado de erro temporariamente porque dois nós estavam em baixo e a política MaxPercentageUnhealthyNodes foi violada. O erro é temporário até que a operação de remendo possa começar.

![Imagem de aglomerado pouco saudável](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Se o problema persistir, consulte a secção de resolução de problemas.

**P: O que posso fazer se a POA estiver em estado de alerta?**

R: Verifique se um relatório de saúde publicado contra a aplicação indica a causa principal. Normalmente, o aviso contém detalhes do problema. Se o problema for transitório, espera-se que a aplicação recupere automaticamente.

**P: O que posso fazer se o meu cluster não for saudável e precisar de fazer uma atualização urgente do sistema operativo?**

R: O POA não instala atualizações enquanto o cluster não é saudável. Tente trazer o seu cluster para um estado saudável para desbloquear o fluxo de trabalho da AP.

**P: Devo definir a Política de Reprovação de Tarefas como "NodeWise" ou "UpgradeDomainWise" para o meu cluster?**

R: A configuração "UpgradeDomainWise" acelera a reparação global do cluster remendando paralelamente todos os nós que pertencem a um domínio de atualização. Durante o processo, os nós que pertencem a todo um domínio de atualização não estão disponíveis (em estado [ *de desativação).* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)

Em contraste, a definição "NodeWise" remende apenas um nó de cada vez, o que implicaria que a correção geral do cluster poderia demorar mais tempo. No entanto, apenas um nó no máximo estaria indisponível (em estado *de desativação)* durante o processo de remendo.

Se o seu cluster puder tolerar correr num número N-1 de domínios de atualização durante o ciclo de remendo (onde N é o número total de domínios de atualização no seu cluster) pode definir a política como "UpgradeDomainWise". Caso contrário, coloque-o em "NodeWise".

**P: Quanto tempo leva para remendar um nó?**

Remendar um nó pode demorar de minutos (por exemplo, atualizações de [definição do Windows Defender)](https://www.microsoft.com/en-us/wdsi/definitions)a horas (por exemplo, [atualizações cumulativas do Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). O tempo necessário para remendar um nó depende principalmente de: 
 - O tamanho das atualizações.
 - O número de atualizações, que têm de ser aplicadas numa janela de remendos.
 - O tempo que demora a instalar as atualizações, reiniciar o nó (se necessário) e terminar os passos de instalação pós-reinicialização.
 - O desempenho do VM ou da máquina e as condições de rede.

**P: Quanto tempo demora a remendar um cluster inteiro?**

R: O tempo necessário para remendar um cluster inteiro depende de:

- O tempo necessário para remendar um nó.

- A política do Serviço coordenador. A política padrão, "NodeWise", resulta em remendar apenas um nó de cada vez, uma abordagem mais lenta do que usar "UpgradeDomainWise". 

   Por exemplo: Se um nó demorar ~1 hora a ser remendado, remendar um cluster de 20 nós (mesmo tipo de nós) com 5 domínios de atualização, cada um contendo 4 nós, requer:
    - Para "NodeWise": ~20 horas.
    - Para "UpgradeDomainWise": ~5 horas.

- A carga do cluster. Cada operação de remendo requer a deslocalização da carga de trabalho do cliente para outros nós disponíveis no cluster. Um nó que está a ser remendado seria em [ *estado de desativação* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) durante este tempo. Se o cluster estiver a funcionar perto da carga máxima, o processo de desativação demoraria mais tempo. Por conseguinte, o processo global de correção pode parecer lento em condições tão tensas.

- Falhas de saúde do cluster durante o remendo. Qualquer [degradação](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) na [saúde do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) interromperia o processo de remendo. Esta questão acrescentaria ao tempo global necessário para remendar todo o cluster.

**P: Por que vejo algumas atualizações nos resultados do Windows Update que são obtidas através da API REST, mas não no histórico de Atualização do Windows na máquina?**

R: Algumas atualizações do produto aparecem apenas no seu próprio histórico de atualização ou patch. Por exemplo, as atualizações do Windows Defender podem ou não ser exibidas no histórico de Atualizações do Windows no Windows Server 2016.

**P: Pode a POA ser usada para remendar o meu aglomerado de v (cluster de um nó)?**

R: Não, poa não pode ser usado para remendar um aglomerado de um nó. Esta limitação é por design, porque [os serviços](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) de sistema service Fabric ou outras aplicações de clientes incorreriam em tempo de inatividade. Por conseguinte, os trabalhos de reparação nunca seriam aprovados pelo Reparo.

**P: Como remendo nós de cluster em Linux?**

R: Para aprender sobre a orquestração de atualizações no Linux, consulte a [escala de imagem automática da escala de máquinas do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

**P: Porque é que o ciclo de atualização está a demorar tanto tempo?**

R: Consulta para o resultado JSON, introduza o ciclo de atualização para todos os nós e, em seguida, pode tentar descobrir o tempo deavida pela instalação de atualização em cada nó utilizando a OperationStartTime e OperationTime (OperationCompletionTime). 

Se houver uma grande janela de tempo em que não está a ocorrer nenhuma atualização, o cluster pode estar em estado de erro e, consequentemente, o Gestor de Reparação não pode aprovar nenhuma tarefa de reparação de POA. Se a instalação da atualização estiver a demorar muito tempo em qualquer nó, esse nó pode não ter sido atualizado há algum tempo. Muitas atualizações podem estar pendentes de instalação, o que pode resultar em atrasos. 

Também pode ser possível que o remendo do nó esteja bloqueado porque está preso no estado *de Desativação.* Isto geralmente acontece porque desativar o nó pode levar a situações de quórum ou perda de dados.

**P: Por que o nó deve ser desativado quando o POA está a remendá-lo?**

R: POA desativa o nó com uma intenção *restart,* que para ou reafecta todos os serviços de Tecido de Serviço que estão a funcionar no nó. O POA faz isto para garantir que as aplicações não acabem por usar uma mistura de DLLs novos e antigos, por isso recomendamos que não remende um nó sem o desativar.

**P: Qual é o número máximo de nós que podem ser atualizados através da utilização de POA?**

R: PoA usa Service Fabric Repair Manager para criar tarefas de reparação para os nódosos para atualizações. No entanto, não podem estar presentes ao mesmo tempo mais de 250 tarefas de reparação. Atualmente, a POA cria tarefas de reparação para cada nó ao mesmo tempo, para que a POA não possa atualizar mais de 250 nós num cluster. 

## <a name="disclaimers"></a>Exclusões de Responsabilidade

- A POA aceita o Contrato de Licença de Utilizador Final para a Atualização do Windows em nome do utilizador. Opcionalmente, a definição pode ser desligada na configuração da aplicação.

- A POA recolhe telemetria para acompanhar o uso e o desempenho. A telemetria da aplicação segue a definição da definição de telemetria do tecido de serviço (que está em predefinição).

## <a name="troubleshooting"></a>Resolução de problemas

Esta secção fornece possíveis soluções de resolução de problemas para problemas com nós de remendos.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Um nó não vai voltar para o estado de cima

* O nó pode estar preso no estado *de desativação* porque:

  - Uma verificação de segurança está pendente. Para remediar esta situação, certifique-se de que os nós suficientes estão disponíveis num estado saudável.

* O nó pode estar preso no estado *de deficientes* porque:

  - Foi desativado manualmente.
  - Foi desativado por causa de um trabalho de infraestrutura Azure em curso.
  - Foi desativado temporariamente pela POA para remendar o nó.

* O nó pode estar preso num estado baixo porque:

  - Foi colocado em baixo manualmente.
  - Está a ser reiniciado (que pode ser desencadeado pela POA).
  - Tem um VM ou uma máquina defeituoso, ou está a ter problemas de conectividade de rede.

### <a name="updates-were-skipped-on-some-nodes"></a>As atualizações foram ignoradas em alguns nódosos

O POA tenta instalar uma atualização do Windows de acordo com a política de reagendamento. O serviço tenta recuperar o nó e saltar a atualização de acordo com a política de aplicação.

Neste caso, é gerado um relatório de saúde ao nível de alerta contra o Serviço de Agente do Nó. O resultado do Windows Update também contém a possível razão para a falha.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>A saúde do cluster vai para o erro enquanto a atualização está a ser instalada

Uma atualização defeituosa do Windows pode reduzir a saúde de uma aplicação ou cluster num determinado nó ou domínio de atualização. O POA suspende qualquer operação subsequente do Windows Update até que o cluster esteja saudável novamente.

Um administrador deve intervir e determinar por que razão a aplicação ou cluster tornou-se insalubre por causa do Windows Update.

## <a name="poa-release-notes"></a>Notas de lançamento do POA

>[!NOTE]
> Para versões POA 1.4.0 e mais tarde, pode encontrar notas de lançamento e lançamentos na página de lançamento da [Patch Orchestration Application](https://github.com/microsoft/Service-Fabric-POA/releases/) no GitHub.

### <a name="version-110"></a>Versão 1.1.0
- Lançamento público

### <a name="version-111"></a>Versão 1.1.1
- Fixou um bug no SetupEntryPoint do NodeAgentService que impediu a instalação do NodeAgentNTService.

### <a name="version-120"></a>Versão 1.2.0

- Correções de bugs em torno do fluxo de trabalho de reinício do sistema.
- Correção de bugs na criação de tarefas RM devido ao qual o exame de saúde durante a preparação das tarefas de reparação não estava a acontecer como esperado.
- Alterou o modo de arranque para o serviço Windows POANodeSvc de auto para retardado.auto.

### <a name="version-121"></a>Versão 1.2.1

- Correção de insetos no fluxo de trabalho de escala de cluster. Introduziu a lógica de recolha de lixo para tarefas de reparação de POA pertencentes a nódosos inexistentes.

### <a name="version-122"></a>Versão 1.2.2

- Correções de insetos diversos.
- Os binários estão agora assinados.
- Ligação de sfpkg adicionada para a aplicação.

### <a name="version-130"></a>Versão 1.3.0

- Configurar a instalaçãoDoOnlyUpdates para falsos instala agora todas as atualizações disponíveis.
- Mudou a lógica de desativar atualizações automáticas. Isto corrige um bug onde as atualizações automáticas não estavam a ser desativadas no Servidor 2016 ou acima.
- Restrição de colocação parametrizada para ambos os microserviços de POA para casos de utilização avançada.

### <a name="version-131"></a>Versão 1.3.1
- Fixação de regressão onde POA 1.3.0 não funcionará no Windows Server 2012 R2 ou mais cedo devido a uma falha na desativação de atualizações automáticas. 
- A fixação do bug onde a configuração installWindowsOSOnlyUpdates é sempre escolhida como True.
- Alterar o valor padrão das InstalaçõesWindowsOsOnlyUpdates para False.

### <a name="version-132"></a>Versão 1.3.2
- Corrigir um problema que afeta o ciclo de vida de remendos num nó, se houver nós com um nome que seja um subconjunto do nome atual do nó. Para tais nós, é possível que o remendo tenha sido perdido ou um reboot esteja pendente.
