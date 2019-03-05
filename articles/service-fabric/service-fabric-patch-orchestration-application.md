---
title: Aplicação de orquestração do patch de Service Fabric do Azure | Documentos da Microsoft
description: Aplicativo para automatizar a aplicação de patches de sistema operativo no cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: timlt
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: 1f6ce2e51255ddf43b6c123585866117d76f8d0b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313655"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Corrigir o sistema operativo do Windows no seu cluster do Service Fabric

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

[Atualizações automáticas da imagem de SO do conjunto de dimensionamento de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) é a melhor prática para manter seus sistemas de operativos corrigidos no Azure e o Patch Orchestration Application (POA) é um wrapper em torno do serviço de sistemas de RepairManager de recursos de infraestrutura do serviço Isso permite a configuração baseada o agendamento de patch de SO para clusters de alojado não pertencente ao Azure. POA não é necessária para clusters de alojado não pertencente ao Azure, mas agendamento da instalação de patch por domínios de atualização, é necessário para corrigir o Service Fabric clusters anfitriões sem tempo de inatividade.

POA é uma aplicação do Azure Service Fabric que automatiza o sistema operativo, aplicação de patches no cluster do Service Fabric sem tempo de inatividade.

A aplicação de orquestração do patch fornece as seguintes funcionalidades:

- **Instalação de atualização do sistema operativo automático**. Atualizações do sistema operativo são automaticamente transferidas e instaladas. Nós de cluster são reiniciados conforme necessário, sem tempo de inatividade do cluster.

- **Integração de aplicação de patches e o estado de funcionamento com reconhecimento de cluster**. Ao aplicar as atualizações, a aplicação de orquestração do patch monitoriza o estado de funcionamento de nós do cluster. Nós de cluster são atualizado um nó num período de tempo ou de um domínio de atualização de cada vez. Se o estado de funcionamento do cluster ficar inativa devido ao processo de aplicação de patches, aplicação de patches é parada para impedir a aggravating o problema.

## <a name="internal-details-of-the-app"></a>Detalhes internos da aplicação

A aplicação de orquestração do patch é composta pelos subcomponentes seguintes:

- **Serviço de coordenador**: Este serviço com monitorização de estado é responsável por:
    - Coordenar a tarefa de atualização do Windows em todo o cluster.
    - Armazenando o resultado de operações de atualização do Windows concluídas.
- **Serviço de agente de nó**: Este serviço sem estado é executado em todos os nós de cluster do Service Fabric. O serviço é responsável por:
    - O NTService de agente de nó de inicialização.
    - Monitorizar o NTService de agente de nó.
- **Nó agente NTService**: Este serviço do Windows NT é executado num nível mais alto privilégio (sistema). Por outro lado, o serviço de agente de nó e o serviço Coordenador de ser executados num nível inferior de privilégio (serviço de rede). O serviço é responsável por executar as seguintes tarefas do Windows Update em todos os nós de cluster:
    - A desativar atualização automática do Windows no nó.
    - Transferir e instalar a atualização do Windows, de acordo com a política do utilizador forneceu.
    - A reiniciar a postagem de máquina instalação da atualização do Windows.
    - A carregar os resultados das atualizações do Windows para o serviço de coordenador.
    - Os relatórios de estado de geração de relatórios no caso de uma operação falhou depois de esgotar todas as tentativas.

> [!NOTE]
> A aplicação de orquestração do patch utiliza o serviço de sistema do Service Fabric reparação manager para desativar ou ativar o nó e executar verificações do Estado de funcionamento. A tarefa de reparação criada pela aplicação de orquestração do patch controla o progresso da atualização do Windows para cada nó.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> Versão mínima do .NET framework necessária seja a 4.6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Ativar o serviço de Gestor de reparação (se não tiver o já)

A aplicação de orquestração do patch exige que o serviço de sistema do Gestor de reparação para ser ativada no cluster.

#### <a name="azure-clusters"></a>Clusters do Azure

Os clusters do Azure no escalão de durabilidade silver têm o serviço de Gestor de reparação ativado por predefinição. Clusters do Azure no escalão de durabilidade gold poderão ou poderão não ter o serviço de Gestor de reparação ativado, dependendo de quando esses grupos foram criados. Clusters do Azure no escalão de durabilidade de bronze, por predefinição, não têm o serviço de Gestor de reparação ativado. Se o serviço já está ativado, pode ver que o mesmo está na seção de serviços de sistema no Service Fabric Explorer.

##### <a name="azure-portal"></a>Portal do Azure
Pode ativar o Gestor de reparação a partir do portal do Azure no momento da configuração de cluster. Selecione **incluem o Gestor de reparação** opção sob **funcionalidades de suplemento** no momento da configuração de cluster.
![Imagem de ativar o Gestor de reparação a partir do portal do Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Modelo de implementação do Azure Resource Manager
Em alternativa, pode utilizar o [modelo de implementação Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para ativar o serviço de Gestor de reparação em clusters do Service Fabric novos e existentes. Obter o modelo para o cluster que pretende implementar. Pode utilizar os modelos de exemplo ou criar um modelo de modelo de implementação personalizado do Azure Resource Manager. 

Para ativar o Gestor de reparação serviço utilizando [modelo de modelo de implementação Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Verifique que o primeiro o `apiversion` está definido como `2017-07-01-preview` para o `Microsoft.ServiceFabric/clusters` recursos. Se for diferente, então tem de atualizar o `apiVersion` para o valor `2017-07-01-preview` ou superior:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora de ativar o serviço de Gestor de reparação adicionando o seguinte `addonFeatures` secção após o `fabricSettings` secção:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Depois de atualizar o modelo de cluster com estas alterações, aplicá-las e deixar que a atualização a concluir. Agora, pode ver o serviço de sistema do reparo manager em execução no seu cluster. Ele é chamado `fabric:/System/RepairManagerService` na seção de serviços de sistema no Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Clusters de locais de autónomos

Pode utilizar o [definições de configuração para cluster autónomo do Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) para ativar o serviço de Gestor de reparação no cluster do Service Fabric novo e existente.

Para ativar o serviço de Gestor de reparação:

1. Verifique que o primeiro o `apiversion` no [configurações de cluster gerais](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) está definido como `04-2017` ou superior:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Ativar agora o serviço de Gestor de reparação adicionando o seguinte `addonFeatures` secção após o `fabricSettings` secção conforme mostrado abaixo:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Atualizar o manifesto do cluster com essas alterações, usando o manifesto do cluster atualizado [criar um novo cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) ou [atualizar a configuração de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). Depois do cluster está em execução com o manifesto do cluster atualizado, pode ver agora o serviço de sistema do reparo manager em execução no seu cluster, o que é chamado `fabric:/System/RepairManagerService`, em sistema dos serviços de seção no Explorador do Service Fabric.

### <a name="configure-windows-updates-for-all-nodes"></a>Configurar atualizações do Windows para todos os nós

As atualizações automáticas do Windows pode levar à perda de disponibilidade, uma vez que vários nós de cluster podem reiniciar ao mesmo tempo. A aplicação de orquestração do patch, por predefinição, tenta desativar a atualização automática do Windows em cada nó de cluster. No entanto, se as definições são geridas por um administrador ou a política de grupo, recomendamos definir a política de atualização do Windows para "Notificar antes de baixar" explicitamente.

## <a name="download-the-app-package"></a>Transferir o pacote de aplicação

Aplicativo, juntamente com os scripts de instalação pode ser transferido a partir [link de arquivo](https://go.microsoft.com/fwlink/?linkid=869566).

Aplicação em formato de sfpkg pode ser transferida a partir [sfpkg ligação](https://aka.ms/POA/POA.sfpkg). Isso é útil para [do Azure Resource Manager com base em implantação de aplicativos](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Configurar a aplicação

O comportamento da aplicação de orquestração do patch pode ser configurado para atender às suas necessidades. Substitua os valores predefinidos ao transmitir o parâmetro de aplicação durante a criação de aplicação ou atualização. Podem ser fornecidos parâmetros de aplicação, especificando `ApplicationParameter` para o `Start-ServiceFabricApplicationUpgrade` ou `New-ServiceFabricApplication` cmdlets.

|**Parâmetro**        |**Tipo**                          | **Detalhes**|
|:-|-|-|
|MaxResultsToCache    |Longo                              | Número máximo de resultados de atualização do Windows, que devem ser colocados em cache. <br>Valor predefinido é 3000 assumindo a: <br> -Número de nós é 20. <br> -Número de atualizações a acontecer num nó por mês é cinco. <br> -Número de resultados por operação pode ser 10. <br> -Devem ser armazenados resultados para os últimos três meses. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy indica a política que está a ser utilizado pelo serviço de coordenador para instalar atualizações do Windows em todos os nós de cluster do Service Fabric.<br>                         Valores permitidos são: <br>                                                           <b>NodeWise</b>. Atualização do Windows é instalado um nó por vez. <br>                                                           <b>UpgradeDomainWise</b>. Atualização do Windows está instalado um domínio de atualização de cada vez. (No máximo, podem ir todos os nós que pertencem a um domínio de atualização para o Windows Update.)<br> Consulte a [FAQ](#frequently-asked-questions) secção sobre como decidir o que é melhor se adequam política para o seu cluster.
|LogsDiskQuotaInMB   |Longo  <br> (Predefinição: 1024)               |Tamanho máximo da aplicação de orquestração do patch os logs em MB, que pode ser mantido localmente em nós.
| WUQuery               | string<br>(Predefinição: "IsInstalled=0")                | Consulta para obter atualizações do Windows. Para obter mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Booleano <br> (predefinição: FALSO)                 | Utilize este sinalizador para controlar quais atualizações devem ser baixadas e instaladas. São permitidos valores seguintes <br>TRUE - instala apenas atualizações de sistema operativo do Windows.<br>FALSE – instala todas as atualizações disponíveis na máquina.          |
| WUOperationTimeOutInMinutes | Int <br>(Predefinição: 90)                   | Especifica o tempo limite para qualquer operação de atualização do Windows (pesquisa ou download ou instalação). Se não é possível concluir a operação no tempo limite especificado, é abortada.       |
| WURescheduleCount     | Int <br> (Predefinição: 5)                  | O número máximo de vezes que o serviço reagenda o Windows update no caso de uma operação falha de forma permanente.          |
| WURescheduleTimeInMinutes | Int <br>(Predefinição: 30) | O intervalo no qual o serviço reagenda a atualização do Windows no caso de falha persistir. |
| WUFrequency           | Cadeia de caracteres separados por vírgulas (predefinição: "Semanais, quarta-feira, 7:00:00")     | A frequência para instalar a atualização do Windows. Os valores de formato e possíveis são: <br>-Mensais, DD, hh: mm:, por exemplo, mensalmente, 5, 12: 22:32.<br>Permissão de valores de campo DD (dia) são números entre o intervalo de 1 a 28 e "last". <br> -Semanal, dia, hh: mm:, para o exemplo, semanalmente, Terça-feira, 12:22:32.  <br> -Diárias, hh: mm:, por exemplo, diariamente, 12:22:32.  <br> -None indica que o Windows Update não deve ser feito.  <br><br> Tenha em atenção que os tempos são indicados em UTC.|
| AcceptWindowsUpdateEula | Booleano <br>(Predefinição: VERDADEIRO) | Ao definir este sinalizador, o aplicativo aceite o contrato de licença de utilizador final para o Windows Update em nome do proprietário da máquina.              |

> [!TIP]
> Se pretender que o Windows Update para imediatamente, defina `WUFrequency` em relação ao tempo de implementação de aplicação. Por exemplo, suponha que tem um cluster de teste de cinco nós e planeja implantar o aplicativo ao cerca as 17:00 UTC. Se for suposto que a atualização da aplicação ou implementação demora 30 minutos no máximo, definir o WUFrequency como "Diariamente, 17:30:00"

## <a name="deploy-the-app"></a>Implementar a aplicação

1. Conclua todos os passos de pré-requisitos para preparar o cluster.
2. Implemente a aplicação de orquestração do patch, como qualquer outra aplicação do Service Fabric. Pode implementar a aplicação com o PowerShell. Siga os passos em [implementar e remover aplicações com o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Para configurar a aplicação no momento da implementação, de transmitir o `ApplicationParameter` para o `New-ServiceFabricApplication` cmdlet. Para sua conveniência, fornecemos o script Deploy.ps1 juntamente com a aplicação. Para utilizar o script:

    - Ligar ao cluster do Service Fabric com `Connect-ServiceFabricCluster`.
    - Execute o script do PowerShell Deploy.ps1 com apropriado `ApplicationParameter` valor.

> [!NOTE]
> Manter o script e a pasta de aplicativo PatchOrchestrationApplication no mesmo diretório.

## <a name="upgrade-the-app"></a>Atualizar a aplicação

Para atualizar uma aplicação de orquestração do patch existente com o PowerShell, siga os passos em [atualização da aplicação de Service Fabric com o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Remover a aplicação

Para remover a aplicação, siga os passos em [implementar e remover aplicações com o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Para sua conveniência, fornecemos o script Undeploy.ps1 juntamente com a aplicação. Para utilizar o script:

  - Ligar ao cluster do Service Fabric com ```Connect-ServiceFabricCluster```.

  - Execute o script de PowerShell Undeploy.ps1.

> [!NOTE]
> Manter o script e a pasta de aplicativo PatchOrchestrationApplication no mesmo diretório.

## <a name="view-the-windows-update-results"></a>Ver os resultados de atualização do Windows

A aplicação de orquestração do patch expõe as APIs REST para exibir os resultados históricos para o usuário. Um exemplo do resultado JSON:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2017-05-21T11:46:52.1953713Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0
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

Campos do JSON são descritos abaixo.

Campo | Valores | Detalhes
-- | -- | --
OperationResult | 0 - foi concluída com êxito<br> 1 - foi concluída com êxito com erros<br> 2 - falha<br> 3 - abortada<br> 4 - abortada com tempo limite | Indica o resultado da operação geral (em geral envolvendo a instalação de atualizações de um ou mais).
ResultCode | Mesmo que operationresult ainda | Este campo indica o resultado da operação de instalação para a atualização individual.
OperationType | 1 - instalação<br> 0 - pesquisa e a transferência.| A instalação é a única OperationType que poderia ser apresentada nos resultados, por predefinição.
WindowsUpdateQuery | A predefinição é "IsInstalled = 0" |Windows atualizar a consulta que foi utilizada para procurar atualizações. Para obter mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | VERDADEIRO - reinício foi necessário<br> FALSO - não foi necessário reiniciar | Indica se o reinício foi necessário para concluir a instalação de atualizações.

Se nenhuma atualização está agendada ainda, o resultado JSON está vazio.

Inicie sessão no cluster para consultar a atualização do Windows resultados. Em seguida, Descubra o endereço de réplica para as primárias do serviço de coordenador e clica na URL do navegador: http://&lt;IP de RÉPLICA&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1 / GetWindowsUpdateResults.

O ponto de final REST para o serviço de coordenador tem uma porta dinâmica. Para verificar o URL exato, consulte o Service Fabric Explorer. Por exemplo, os resultados estão disponíveis em `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Imagem do ponto final REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Se o proxy inverso estiver ativado no cluster, pode acessar o URL de fora do cluster também.
O ponto final que tem de ser atingido é http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Para ativar o proxy inverso no cluster, siga os passos em [proxy no Azure Service Fabric inverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Após a configuração do proxy inverso, todos os microsserviços no cluster que expõem um ponto de extremidade HTTP são endereçáveis a partir de fora do cluster.

## <a name="diagnosticshealth-events"></a>Eventos de diagnóstico/estado de funcionamento

### <a name="diagnostic-logs"></a>Registos de diagnósticos

Registos de aplicações de orquestração do patch são recolhidos como parte dos registos de tempo de execução do Service Fabric.

Caso queira capturar os registos por meio da ferramenta de diagnóstico/pipeline à sua escolha. Aplicação de orquestração do patch utiliza abaixo fornecedor fixo IDs para registar eventos através de [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Relatórios de estado de funcionamento

A aplicação de orquestração do patch também publica relatórios de estado de funcionamento contra o serviço de coordenador ou o serviço de agente de nó nos seguintes casos:

#### <a name="a-windows-update-operation-failed"></a>Uma falha na operação de atualização do Windows

Se uma operação de atualização do Windows falhar num nó, é gerado um relatório de estado de funcionamento para o serviço de agente de nó. Detalhes do relatório de estado de funcionamento contém o nome do nó problemático.

Após a aplicação de patches é concluída com êxito no nó problemático, o relatório é automaticamente limpo.

#### <a name="the-node-agent-ntservice-is-down"></a>O NTService de agente de nó está inativo

Se o NTService de agente de nó estiver desativado num nó, é gerado um relatório de estado de funcionamento do nível de aviso para o serviço de agente de nó.

#### <a name="the-repair-manager-service-is-not-enabled"></a>O serviço de Gestor de reparação não está ativado

Se o serviço de Gestor de reparação não foi encontrado no cluster, é gerado um relatório de estado de funcionamento do nível de aviso para o serviço de coordenador.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

P. **Por que motivo é que vejo o meu cluster em estado de erro quando a aplicação de orquestração do patch está em execução?**

R. Durante o processo de instalação, a aplicação de orquestração do patch desativa ou reinicia os nós, que temporariamente podem resultar em estado de funcionamento do cluster na direção para baixo.

Com base na política para o aplicativo, seja um nó pode descer durante uma operação de aplicação de patches *ou* um todo domínio de atualização pode ser reduzidos em simultâneo.

No final da instalação do Windows Update, os nós são reenabled publicar o reinício.

No exemplo a seguir, o cluster correu para um Estado de erro temporariamente porque dois nós estavam inativa e a política de MaxPercentageUnhealthyNodes foi violada. O erro é temporário até que a operação de correção está em curso.

![Imagem do cluster de mau estado de funcionamento](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Se o problema persistir, consulte a secção de resolução de problemas.

P. **Aplicação de orquestração do patch está num Estado de aviso**

R. Verifique se um relatório de estado de funcionamento lançado contra a aplicação é a causa raiz. Normalmente, o aviso contém detalhes do problema. Se o problema é transitório, espera-se a aplicação para recuperar automaticamente de neste estado.

P. **O que devo fazer se o meu cluster está em mau estado de funcionamento e o que preciso fazer uma atualização do sistema de operativo urgente?**

R. A aplicação de orquestração do patch não instala atualizações enquanto o cluster está em mau estado de funcionamento. Tente colocar o seu cluster para um bom estado de funcionamento para desbloquear o fluxo de trabalho de aplicação do patch orchestration.

P. **Deve i definida TaskApprovalPolicy como 'NodeWise' ou 'UpgradeDomainWise' para o meu cluster?**

R. "UpgradeDomainWise" faz com que o cluster geral, aplicação de patches mais rapidamente ao corrigir todos os nós que pertencem a um domínio de atualização em paralelo. Isso significa que nós que pertencem a um domínio de atualização completo seria indisponíveis (no [desativado](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) Estado) durante o processo de aplicação de patches.

Por outro lado "NodeWise" política patches apenas um nó por vez, isso implica a aplicação de patches de cluster geral exigiria mais tempo. No entanto, no máximo, apenas um nó estaria indisponível (no [desativado](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) Estado) durante o processo de aplicação de patches.

Se o seu cluster pode tolerar em execução num número de n-1 de domínios de atualização durante o ciclo (em que N é o número total de domínios de atualização no seu cluster), a aplicação de patches, em seguida, pode definir a política como "UpgradeDomainWise", caso contrário, defini-lo como 'NodeWise'.

P. **Quanto tempo isso take para corrigir um nó?**

R. Aplicação de patches de um nó pode demorar minutos (por exemplo: [Atualizações de definições do Windows Defender](https://www.microsoft.com/wdsi/definitions)) para horas (por exemplo: [As atualizações cumulativas do Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Tempo necessário para corrigir um nó depende principalmente no 
 - O tamanho de atualizações
 - Número de atualizações, que têm de ser aplicados a uma janela da aplicação de patches
 - Tempo necessário para instalar as atualizações, reiniciar o nó (se necessário) e concluir os passos de instalação de pós-reinício.
 - Desempenho das condições VM/máquina e de rede.

P. **Quanto tempo demora a aplicar o patch num cluster completo?**

R. O tempo necessário para aplicar o patch num cluster completo depende dos seguintes fatores:

- Tempo necessário para corrigir um nó.
- A política do serviço de coordenador. -A política predefinida, `NodeWise`, resulta na aplicação de patches apenas um nó de cada vez, que seria mais lento do que `UpgradeDomainWise`. Por exemplo: Se um nó demora ~ 1 hora para ser corrigido, para corrigir um 20 (mesmo tipo de nós) de cluster com 5 domínios de atualização, cada um contendo de 4 nós.
    - Deve demorar cerca de 20 horas para corrigir o todo o cluster, se estiver política `NodeWise`
    - Deve demorar cerca de 5 horas se estiver política `UpgradeDomainWise`
- Carga de cluster - cada operação de aplicação de patches requer a alteração da localização a carga de trabalho do cliente para outros nós disponíveis no cluster. Nó passando por patch estaria [desabilitar](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) estado durante este período. Se o cluster está em execução perto de pico de carga, o processo de desabilitação seria necessário mais tempo. Por conseguinte, o processo geral de aplicação de patches pode aparecer lento em tais condições stressed.
- Falhas de estado de funcionamento do cluster durante a aplicação de patches - qualquer [degradação](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) na [estado de funcionamento do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) seria interromper o processo de aplicação de patches. Isto seria adicionar o tempo geral necessário para corrigir o todo o cluster.

P. **Por que vejo algumas atualizações nos resultados da atualização do Windows obtidos através da REST API, mas não está sob o histórico de atualização do Windows na máquina?**

R. Algumas atualizações de produto apenas apareceria no respetivo histórico de atualização/correção respectivos. Por exemplo, atualizações do Windows Defender podem ou não podem aparecer no histórico de atualização do Windows no Windows Server 2016.

P. **Aplicação de Patch Orchestration pode ser utilizada para corrigir o meu cluster de desenvolvimento (um nó de cluster)?**

R. Não, não podem servir-se aplicações de orquestração do Patch para o cluster de um nó de patch. Esta limitação é por design, como [serviços do sistema de recursos de infraestrutura do serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) ou todas as aplicações cliente enfrentarão o tempo de inatividade e, por conseguinte, qualquer tarefa de reparação para aplicação de patches nunca seria obter aprovada pelo Gestor de reparação.

## <a name="disclaimers"></a>Exclusões de Responsabilidade

- A aplicação de orquestração do patch aceita o contrato de licença pelo utilizador final do Windows Update em nome do utilizador. Opcionalmente, a definição pode ser desativada na configuração da aplicação.

- A aplicação de orquestração do patch recolhe telemetria para controlar a utilização e desempenho. Telemetria da aplicação segue a definição de configuração de telemetria do runtime do Service Fabric (o que está ativada por predefinição).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="a-node-is-not-coming-back-to-up-state"></a>Um nó não voltará ao estado de segurança

**O nó poderão estar bloqueado num Estado de Desativação porque**:

Uma verificação de segurança está pendente. Para solucionar esta situação, certifique-se de que estão disponíveis suficientes nós em bom estado.

**O nó poderão estar bloqueado num estado desativado porque**:

- O nó foi desativado manualmente.
- O nó foi desativado devido a uma tarefa de infraestrutura do Azure em curso.
- O nó foi desativado temporariamente pela aplicação de orquestração do patch para corrigir o nó.

**O nó poderão estar bloqueado num estado para baixo, porque**:

- O nó foi colocado num estado para baixo manualmente.
- O nó está a reiniciar (que pode ser acionada com a aplicação de orquestração do patch).
- O nó está pendente devido a uma VM com falhas ou problemas de conectividade de rede ou de máquina.

### <a name="updates-were-skipped-on-some-nodes"></a>As atualizações foram ignoradas em alguns nós

A aplicação de orquestração do patch tenta instalar uma atualização do Windows, de acordo com a política rescheduling. O serviço tentará recuperar o nó e ignorar a atualização, de acordo com a política de aplicação.

Nesse caso, é gerado um relatório de estado de funcionamento do nível de aviso para o serviço de agente de nó. O resultado para o Windows Update também contém o motivo possível da falha.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>O estado de funcionamento do cluster vai para o erro, enquanto a atualização é instalada

Uma atualização do Windows com falhas pode prejudicar o estado de funcionamento de uma aplicação ou o cluster num determinado nó ou o domínio de atualização. A aplicação de orquestração do patch discontinues qualquer operação subseqüente do Windows Update até que o cluster está em bom estado novamente.

Um administrador deve intervir e determinar por que a aplicação ou o cluster se tornou mau estado de funcionamento devido a atualização do Windows.

## <a name="release-notes"></a>Notas de Versão

### <a name="version-110"></a>Versão 1.1.0
- Lançamento público

### <a name="version-111"></a>Versão 1.1.1
- Foi corrigido um erro no SetupEntryPoint de NodeAgentService que impediu a instalação de NodeAgentNTService.

### <a name="version-120"></a>Versão 1.2.0

- Correções de erros em todo o sistema reiniciar o fluxo de trabalho.
- Correção de erro na criação de tarefas de RM devido à qual estado de funcionamento da verificação durante a preparação de tarefas de reparo não estava acontecendo conforme esperado.
- Alterar o modo de inicialização de windows service POANodeSvc de automática para automático atrasado.

### <a name="version-121"></a>Versão 1.2.1

- Correção de erro no fluxo de trabalho de redução vertical de cluster. Introduziu a lógica de coleta de lixo para tarefas de reparo POA que pertencem a nós não existente.

### <a name="version-122"></a>Versão 1.2.2

- Diversas correções de bugs.
- Binários agora sessão iniciados.
- Adicionar ligação sfpkg para a aplicação.

### <a name="version-130"></a>Versão 1.3.0

- Configurando InstallWindowsOSOnlyUpdates como false agora instala todas as atualizações disponíveis.
- Alterar a lógica de desabilitar as atualizações automáticas. Isso corrige um bug em que as atualizações automáticas não foram desativadas no Server 2016 e acima.
- Restrição de posicionamento parametrizadas para ambos os microsserviços de POA para usecases avançadas.

### <a name="version-131"></a>Versão 1.3.1
- Corrigir regressão onde POA 1.3.0 não funcionará no Windows Server 2012 R2 ou inferior devido uma falha na desativação de atualizações automáticas. 
- Corrigir erro em que a configuração InstallWindowsOSOnlyUpdates sempre é escolhida como verdadeiro.
- Alterar o valor predefinido de InstallWindowsOSOnlyUpdates como False.

### <a name="version-132"></a>Versão 1.3.2
- Corrigir um problema que afetado o ciclo de vida de aplicação de patches num nó, caso haja nós com o nome que é o subconjunto do nome do nó atual. Para esses nós, sua possível, aplicação de patches está em falta ou o reinício está pendente. 
