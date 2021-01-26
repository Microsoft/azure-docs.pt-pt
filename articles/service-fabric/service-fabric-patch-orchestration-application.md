---
title: Corrigir o sistema operativo Windows no seu cluster de Tecido de Serviço
description: Este artigo discute como automatizar o patching do sistema operativo num cluster de tecido de serviço utilizando a Aplicação de Orquestração de Patch.
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
ms.openlocfilehash: d64c6383b9a83b759dd8368a4e3e0f1847b5ee16
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791228"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Corrigir o sistema operativo Windows no seu cluster de Tecido de Serviço

> 
> [!IMPORTANT]
> A partir de 30 de abril de 2019, a versão 1.2** da Aplicação de Orquestração de Patch já não é suportada. Certifique-se de atualizar para a versão mais recente.

> [!NOTE]
> Obter [atualizações automáticas de imagem de SO no seu conjunto de escala de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) é a melhor prática para manter o seu sistema operativo remendado em Azure. Escala de máquina virtual Conjunto de atualizações automáticas de imagem DE SO exigirão prata ou maior durabilidade num conjunto de escala.
>

 Patch Orchestration Application (POA) é um invólucro em torno do serviço Azure Service Fabric Repair Manager, que permite o agendamento de patchs de sistemas operativos não-Azure. O POA não é necessário para clusters hospedados não-Azure, mas a instalação de patch de agendamento por domínio de atualização é necessária para remendar os anfitriões do cluster de tecido de serviço sem incorrer em tempo de inatividade.

POA é uma aplicação de Tecido de Serviço que automatiza a correção do sistema operativo num cluster de Tecido de Serviço sem incorrer em tempo de inatividade.

A POA fornece as seguintes características:

- **Instalação automática de atualização do sistema operativo**. As atualizações do sistema operativo são automaticamente descarregadas e instaladas. Os nós de cluster são reiniciados conforme necessário sem incorrer em tempo de insaluada do cluster.

- **Patching consciente do cluster e integração na saúde.** Enquanto o POA está a aplicar atualizações, monitoriza a saúde dos nós do cluster. Os nós de cluster são atualizados um nó de cada vez ou um domínio de atualização de cada vez. Se a saúde do cluster diminuir devido ao processo de remendos, a remendação é interrompida para evitar agravar o problema.

## <a name="internal-details-of-poa"></a>Detalhes internos do POA

A POA é composta pelos seguintes subcomponentes:

- **Serviço coordenador**: Este serviço imponente é responsável por:
    - Coordenar o trabalho de Atualização do Windows em todo o cluster.
    - Armazenar o resultado das operações concluídas no Windows Update.

- **Serviço de Agente de Nó**: Este serviço apátrida funciona em todos os nós de cluster de tecido de serviço. O serviço é responsável por:
    - Bootstrapping o Agente de Nó NTService.
    - Monitorização do Agente de Nó NTService.

- **Node Agent NTService**: Este serviço Windows NT funciona com um privilégio de nível superior (SYSTEM). Em contrapartida, o Serviço de Agente de Nó e o Serviço de Coordenador funcionam com um privilégio de nível inferior (SERVIÇO DE REDE). O serviço é responsável pela realização dos seguintes trabalhos do Windows Update em todos os nós do cluster:
    - Desativando as atualizações automáticas do Windows no nó.
    - Descarregar e instalar atualizações do Windows de acordo com a política que o utilizador forneceu.
    - Reiniciar a instalação de atualizações pós-Windows da máquina.
    - A carregar os resultados das atualizações do Windows para o Serviço de Coordenadores.
    - Reportar relatórios de saúde se uma operação falhou depois de esgotar todas as retrações.

> [!NOTE]
> O POA utiliza o serviço Service Fabric Repair Manager para desativar ou ativar o nó e efetuar verificações de saúde. A tarefa de reparação criada pelo POA acompanha o progresso do Windows Update para cada nó.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> A versão mínima exigida .NET Framework é 4.6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Ativar o serviço de Gestor de Reparação (se já não estiver em funcionamento)

O POA exige que o serviço de Gestor de Reparação seja ativado no cluster.

#### <a name="azure-clusters"></a>Aglomerados azuis

Os clusters Azure no nível de durabilidade prateada têm o serviço de Gestor de Reparação ativado por padrão. Os clusters Azure no nível de durabilidade dourada podem ou não ter o serviço de Repair Manager habilitado, dependendo da altura em que esses clusters foram criados. Os aglomerados Azure no nível de durabilidade de bronze, por padrão, não têm o serviço de Reparação habilitado. Se o serviço já estiver ativado, pode vê-lo a funcionar na secção de serviços de sistema no Service Fabric Explorer.

##### <a name="the-azure-portal"></a>O portal do Azure
Pode ativar o Gestor de Reparação a partir do portal Azure quando configurar o cluster. Ao configurar o cluster, selecione a opção **Incluir Gestor de Reparação** **nas funcionalidades Add-on**.

![Imagem de ativar o Gestor de Reparação a partir do portal Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>O modelo de implementação do Gestor de Recursos Azure
Em alternativa, pode utilizar o modelo de implementação do [Gestor de Recursos Azure](./service-fabric-cluster-creation-via-arm.md) para permitir o serviço de Gestor de Reparação em novos clusters de tecidos de serviço existentes. Obtenha o modelo para o cluster que pretende implementar. Pode utilizar os modelos de amostra ou criar um modelo de modelo de implementação personalizado do Gestor de Recursos Azure. 

Para ativar o serviço de Gestor de Reparação utilizando o modelo de implementação do [Gestor de Recursos Azure,](./service-fabric-cluster-creation-via-arm.md)faça o seguinte:

1. Verifique se `apiVersion` está definido para *a pré-visualização 2017-07-01* para o recurso *Microsoft.ServiceFabric/clusters.* Se for diferente, tem de atualizar `apiVersion` para *2017-07-01-pré-visualização* ou posterior:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Ative o serviço de Gestor de Reparação adicionando a seguinte `addonFeatures` secção após a `fabricSettings` secção:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Depois de atualizar o seu modelo de cluster com estas alterações, aplique-as e deixe terminar a atualização. Agora pode ver o serviço de Gerente de Reparação a funcionar no seu cluster. Chama-se *tecido:/System/RepairManagerService* na secção de serviços de sistema no Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Aglomerados autónomos no local

Para ativar o serviço de Gestor de Reparação num cluster de tecido de serviço novo ou existente, pode utilizar as [definições de Configuração para o cluster Windows autónomo](./service-fabric-cluster-manifest.md).

Para ativar o serviço de Gestor de Reparação:

1. Verifique se `apiVersion` nas [configurações gerais do cluster](./service-fabric-cluster-manifest.md#general-cluster-configurations) estão definidas para *04-2017* ou mais tarde, como mostrado aqui:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Ative o serviço de Gestor de Reparação adicionando a seguinte `addonFeatures` secção após a `fabricSettings` secção, como mostrado aqui:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Atualize o manifesto do cluster com estas alterações utilizando o manifesto de cluster [atualizado, crie um novo cluster](./service-fabric-cluster-creation-for-windows-server.md) ou [atualize a configuração do cluster](./service-fabric-cluster-upgrade-windows-server.md). 

   Depois de o cluster estar a funcionar com um manifesto de cluster atualizado, pode ver o serviço de Gestor de Reparação a funcionar no seu cluster. Chama-se *tecido:/System/RepairManagerService,* e está na secção de serviços de sistema no Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Configurar atualizações do Windows para todos os nós

As atualizações automáticas do Windows podem levar à perda de disponibilidade, uma vez que vários nós de cluster podem reiniciar ao mesmo tempo. O POA, por padrão, tenta desativar as atualizações automáticas do Windows em cada nó de cluster. No entanto, se as definições forem geridas por um administrador ou por uma Política de Grupo, recomendamos que se estabeleça explicitamente a política de Atualização do Windows para "Notificar antes do Download".

## <a name="download-the-application-package"></a>Descarregue o pacote de aplicações

Para descarregar o pacote de aplicações, aceda à página de lançamento da [Aplicação de Orquestração de Patch](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) no GitHub.

## <a name="configure-poa-behavior"></a>Configurar o comportamento do POA

Pode configurar o comportamento do POA para satisfazer as suas necessidades. Sobrepõe os valores predefinidos ao passar no parâmetro de aplicação enquanto está a criar ou atualizar a aplicação. Pode fornecer parâmetros de aplicação especificando `ApplicationParameter` para os `Start-ServiceFabricApplicationUpgrade` `New-ServiceFabricApplication` cmdlets ou cmdlets.

| Parâmetro        | Tipo                          | Detalhes |
|:-|-|-|
|MaxResultsToCache    |Longo                              | O número máximo de resultados da Atualização do Windows que deve ser em cache. <br><br>O valor padrão é 3000, assumindo que: <br> &nbsp;&nbsp;- O número de nós é de 20. <br> &nbsp;&nbsp;- O número de atualizações para um nó por mês é de 5. <br> &nbsp;&nbsp;- O número de resultados por operação pode ser de 10. <br> &nbsp;&nbsp;- Os resultados dos últimos três meses devem ser guardados. |
|TaskApprovalPolicy   |Enumeração <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy indica a política que deve ser utilizada pelo Serviço de Coordenador para instalar atualizações do Windows em todos os nós do cluster de Tecido de Serviço.<br><br>Os valores permitidos são: <br>*NodeWise*: As atualizações do Windows são instaladas um nó de cada vez. <br> *UpgradeDomainWise*: As atualizações do Windows são instaladas um domínio de atualização de cada vez. (No máximo, todos os nós pertencentes a um domínio de atualização podem ir para uma atualização do Windows.)<br><br> Para ajudar a decidir qual a política mais adequada para o seu cluster, consulte a secção [FAQ.](#frequently-asked-questions)
|LogsDiskQuotaInMB   |Longo  <br> (Predefinição: *1024)*               | O tamanho máximo dos registos de aplicações de orquestração de remendos em MB, que podem ser persistidos localmente em nós.
| RIO WUQuery               | string<br>(Predefinido: *IsInstalled=0*)                | Consulta para obter atualizações do Windows. Para mais informações, consulte [WuQuery.](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search)
| InstalarWindowsOSOnlyUpdates | *Booleano* <br> (predefinição: falso)                 | Utilize esta bandeira para controlar quais as atualizações que devem ser descarregadas e instaladas. São permitidos valores a seguir <br>verdadeiro - Instala apenas atualizações do sistema operativo Windows.<br>falso - Instala todas as atualizações disponíveis na máquina.          |
| WUOperationTimeOutInMinutes | int <br>(Predefinição: *90)*                   | Especifica o tempo limite para qualquer operação de Atualização do Windows (procurar ou descarregar ou instalar). Se a operação não estiver concluída dentro do intervalo especificado, será abortada.       |
| WURescheduleCount     | int <br> (Predefinição: *5*)                  | O número máximo de vezes que o serviço reage a atualização do Windows se uma operação falhar persistentemente.          |
| WURescheduleTimeInMinutes | int <br>(Predefinição: *30)* | O intervalo em que o serviço reagenda as atualizações do Windows se a falha persistir. |
| WuFrequency           | Cadeia separada por vírgula (Padrão: *Semanal, quarta-feira, 7:00:00)*     | A frequência para instalar atualizações do Windows. O formato e os valores possíveis são: <br>- Mensalmente, DD, HH:MM:SS (exemplo: *Mensal, 5, 12:22:32*). Os valores permitidos para _DD_ de campo (dia) são números de 1 a 28 e _último_. <br>- Semanal, Dia, HH:MM:SS (exemplo: *Semanal, terça-feira, 12:22:32)*  <br>- Diariamente, HH:MM:SS (exemplo: *Diariamente, 12:22:32)*  <br>- Semana, Dia, HH:MM:SS (exemplo: *2, sexta-feira, 21:00:00* indica 21:00 UTC na sexta-feira da 2ª semana de cada mês) <br>- *Nenhum* indica que as atualizações do Windows não devem ser feitas.  <br><br> Os tempos estão na UTC.|
| AcceptWindowsUpdateEula | Booleano <br>(Predefinição: *verdadeiro)* | Ao definir esta bandeira, a aplicação aceita o Contrato de Licença End-User para atualização do Windows em nome do proprietário da máquina.              |

> [!TIP]
> Se pretender que as atualizações do Windows ocorram imediatamente, `WUFrequency` desative-se em relação ao tempo de implementação da aplicação. Por exemplo, suponha que você tem um cluster de teste de cinco nós e planeie implementar a app por volta das 17:00 UTC. Se assumir que a atualização ou implantação da aplicação demora no máximo 30 minutos, desaline a WUFrequency como *Daily, 17:30:00*.

## <a name="deploy-poa"></a>Implementar POA

1. Termine todos os passos pré-requisitos para preparar o cluster.
1. Implemente poa como qualquer outra aplicação de Tecido de Serviço. Para implementá-lo utilizando o PowerShell, consulte [implementar e remover aplicações utilizando o PowerShell](./service-fabric-deploy-remove-applications.md).
1. Para configurar a aplicação no momento da colocação, passe o `ApplicationParameter` `New-ServiceFabricApplication` cmdlet. Para sua conveniência, fornecemos o script Deploy.ps1 juntamente com a aplicação. Para usar o script:

    - Ligue-se a um cluster de tecido de serviço `Connect-ServiceFabricCluster` utilizando.
    - Execute o script PowerShell Deploy.ps1 com o `ApplicationParameter` valor apropriado.

> [!NOTE]
> Mantenha o script e a pasta de aplicação *PatchOrchestrationApplicação* no mesmo diretório.

## <a name="upgrade-poa"></a>Atualizar POA

Para atualizar a sua versão POA utilizando o PowerShell, siga as instruções na [atualização da aplicação Service Fabric utilizando o PowerShell](./service-fabric-application-upgrade-tutorial-powershell.md).

## <a name="remove-poa"></a>Remover POA

Para remover a aplicação, siga as instruções em [Implementar e remova as aplicações utilizando o PowerShell](./service-fabric-deploy-remove-applications.md).

Para sua conveniência, fornecemos o Undeploy.ps1 script juntamente com a aplicação. Para usar o script:

  - Ligue-se a um cluster de tecido de serviço ```Connect-ServiceFabricCluster``` utilizando.
  - Execute o Undeploy.ps1 de script PowerShell.

> [!NOTE]
> Mantenha o script e a pasta de aplicação *PatchOrchestrationApplicação* no mesmo diretório.

## <a name="view-the-windows-update-results"></a>Ver os resultados da Atualização do Windows

O POA expõe as APIs do REST para exibir os resultados históricos aos utilizadores. Aqui está um exemplo do resultado JSON:

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
OperaçãoResult | 0 - Bem sucedido<br> 1 - Bem sucedido com erros<br> 2 - Falhado<br> 3 - Abortado<br> 4 - Abortado com timeout | Indica o resultado do funcionamento global, que normalmente envolve a instalação de uma ou mais atualizações.
Resultados Código | O mesmo que a OperaçãoResult | Este campo indica o resultado da operação de instalação para uma atualização individual.
Tipo de Operação | 1 - Instalação<br> 0 - Pesquisar e Descarregar| Por predefinição, a instalação é a única OperaçãoType que é mostrada nos resultados.
WindowsUpdateQuery | O padrão é "IsInstalled=0" | A consulta de Atualização do Windows que foi usada para procurar atualizações. Para mais informações, consulte [WuQuery.](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search)
RebootRequired | verdadeiro - reboot foi necessário<br> falso - reboot não era necessário | Indica se foi necessário reiniciar a instalação de atualizações.
OperaçãoStartTime | DateTime | Indica a hora em que a operação (Descarregamento/Instalação) começou.
Operação Tempo | DateTime | Indica a hora em que a operação (Download/Instalação) foi concluída.
Rio HResult | 0 - Sucesso<br> outros - falha| Indica o motivo da falha da atualização do Windows com atualizaçãoID "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Se ainda não houver atualização, o resultado JSON está vazio.

Inscreva-se no cluster para consultar os resultados do Windows Update. Descubra o endereço IP de réplica para o endereço principal do Serviço de Coordenador e abra o seguinte URL do navegador: http:// &lt; REPLICA-IP &gt; : &lt; ApplicationPort &gt; /PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

O ponto final DO REST para o Serviço de Coordenador tem uma porta dinâmica. Para verificar o URL exato, consulte o Explorador de Tecidos de Serviço. Por exemplo, os resultados estão disponíveis em *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* .

![Imagem do ponto final REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Se o proxy invertido estiver ativado no cluster, também pode aceder ao URL de fora do cluster.

O ponto final que precisa de atingir é *http:// &lt; SERVERURL &gt; : &lt; REVERSEPROXYPORT &gt; /PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

Para ativar o representante inverso no cluster, siga as instruções em [Reverse Proxy in Azure Service Fabric](./service-fabric-reverseproxy.md). 

> 
> [!WARNING]
> Após configurar o proxy inverso, todos os microserviços do cluster que expõem um ponto final HTTP são endereçados de fora do cluster.

## <a name="diagnostics-and-health-events"></a>Diagnósticos e eventos de saúde

Esta secção discute como depurar ou diagnosticar problemas com atualizações de patch através de aglomerados de POA em Service Fabric.

> [!NOTE]
> Para obter muitas das seguintes melhorias chamadas, auto-diagnóstico, você deve ter a versão POA 1.4.0 ou posteriormente instalada.

O Agente de Nó NTService cria tarefas de [reparação](/dotnet/api/system.fabric.repair.repairtask) para instalar atualizações nos nós. Cada tarefa é então preparada pelo Serviço coordenador de acordo com a política de aprovação de tarefas. Finalmente, as tarefas preparadas são aprovadas pelo Gestor de Reparação, que não aprova qualquer tarefa se o cluster estiver num estado pouco saudável. 

Para ajudá-lo a entender como as atualizações prosseguem num nó, vamos passo a passo:

1. O NodeAgentNTService, em execução em todos os nós, procura atualizações disponíveis do Windows na hora programada. Se as atualizações estiverem disponíveis, descarrega-as no nó.

1. Após o download das atualizações, o Agente de Nó NTService cria uma tarefa de reparação correspondente para o nó com o nome *POS___ \<unique_id>*. Pode ver estas tarefas de reparação utilizando o cmdlet [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask) ou utilizando o SFX na secção de detalhes do nó. Após a criação da tarefa de reparação, move-se rapidamente para o estado [ *reclamado*](/dotnet/api/system.fabric.repair.repairtaskstate).

1. O Serviço de Coordenador procura periodicamente tarefas de reparação em estado *reclamado* e, em seguida, atualiza-as para o estado *de preparação* com base na TaskApprovalPolicy. Se a TaskApprovalPolicy estiver configurada como NodeWise, uma tarefa de reparação que corresponda a um nó só será preparada se não houver outra tarefa de reparação atualmente em *Preparação,* *Execução* ou *Restauro.*  

   Da mesma forma, no caso do UpgradeWise TaskApprovalPolicy, existem tarefas nos estados anteriores apenas para nós que pertencem ao mesmo domínio de atualização. Depois de uma tarefa de reparação ser transferida para o estado *de preparação,* o nó de tecido de serviço correspondente é [desativado](/powershell/module/servicefabric/disable-servicefabricnode) com a intenção definida para *reiniciar*.

   As versões POA 1.4.0 e posteriormente pós eventos com a propriedade ClusterPatchingStatus no CoordinatorService para exibir os nós que estão a ser remendados. As atualizações são instaladas no _poanode_0, como mostra a seguinte imagem:

    [![Imagem do estado de remendação do cluster](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Depois de desativado o nó, a tarefa de reparação é transferida para o estado *de execução.* 
   
   > [!NOTE]
   > Um nó que está preso no estado *de desativado* pode bloquear uma nova tarefa de reparação, que interrompe a operação de remendação no cluster.

1. Quando a tarefa de reparação estiver em estado *de execução,* a instalação do remendo nesse nó começa. Depois de instalado o patch, o nó pode ou não ser reiniciado, dependendo do patch. Em seguida, a tarefa de reparação é transferida para o estado *de restauração,* que reensifica o nó. A tarefa de reparação é então marcada como concluída.

   Nas versões POA 1.4.0 e posteriormente, pode encontrar o estado da atualização visualizando os eventos de saúde no NodeAgentService com a propriedade WUOperationStatus. \<NodeName> As secções destacadas nas seguintes imagens mostram o estado das atualizações do Windows nos poanode_0 *e* *poanode_2:*

   [![O Screenshot mostra a janela da consola do estado de funcionamento do Windows Update com poanode_0 realçada.](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![O Screenshot mostra a janela da consola do estado de funcionamento do Windows Update com poanode_1 realçada.](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Também pode obter os detalhes usando o PowerShell. Para tal, liga-se ao cluster e obtém o estado da tarefa de reparação utilizando [o Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask). 
   
   No exemplo seguinte, a tarefa "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" encontra-se no estado *de DownloadComplete.* Significa que as atualizações foram descarregadas no nó *poanode_2,* e a instalação será tentada quando a tarefa for para o estado *de Execução.*

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Se ainda houver mais problemas, inicie sessão na sua máquina virtual (VM) ou VMs e aprenda sobre eles utilizando registos de eventos windows. A tarefa de reparação anteriormente mencionada só pode existir nos seguintes subestuais executor:

      Estado do ExecutorSub | Descrição
    -- | -- 
      Nenhum=1 |  Implica que não houve uma operação em curso no nó. O Estado pode estar em transição.
      DownloadCompleted=2 | Implica que a operação de descarregamento foi concluída com sucesso, falha parcial ou falha.
      InstalaçãoAproved=3 | Implica que a operação de descarregamento foi concluída anteriormente e o Gestor de Reparação aprovou a instalação.
      InstalaçãoInProgress=4 | Corresponde ao estado de execução da tarefa de reparação.
      InstalaçãoCompleted=5 | Implica que a instalação foi concluída com sucesso, sucesso parcial ou falha.
      Reiniciar Requested=6 | Implica que a instalação do patch foi concluída e há uma ação de reinício pendente no nó.
      RestartNotNeeded=7 |  Implica que o reinício não foi necessário após a instalação do patch ter sido concluído.
      ReinícioCompletado=8 | Implica que o reinício foi concluído com sucesso.
      OperaçãoCompleted=9 | A operação do Windows Update foi concluída com sucesso.
      OperaçãoAborted=10 | Implica que a operação de Atualização do Windows foi abortada.

1. Nas versões POA 1.4.0 e posteriormente, quando uma tentativa de atualização de nó termina, um evento com a propriedade "WUOperationStatus-[NodeName]" é publicado no NodeAgentService para notificá-lo quando começar a próxima tentativa de descarregar e instalar as atualizações do Windows. Isto é exibido na seguinte imagem:

     [![O Screenshot mostra a janela da consola do estado de funcionamento do Windows Update com o NodeAgentService.](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Registos de diagnósticos

Os registos de aplicação de orquestração de remendos são recolhidos como parte dos registos de execução do Tecido de Serviço.

Pode capturar registos utilizando a ferramenta de diagnóstico ou o pipeline à sua escolha. O POA utiliza os seguintes IDs fixos do fornecedor para registar eventos através de [fonte de evento:](/dotnet/api/system.diagnostics.tracing.eventsource)

- e39b723c-590c-4090-abb0-11e3e616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Relatórios de saúde

A POA também publica relatórios de saúde contra o Serviço de Agente de Nó ou o Serviço de Coordenador nos seguintes cenários:

* O Agente de Nó NTService está em baixo

   Se o Agente De nó NTService estiver em baixo num nó, é gerado um relatório de saúde de nível de aviso contra o Serviço de Agente de Nó.

* O serviço de Gestor de Reparação não está ativado

   Se o serviço de Gestor de Reparação não for encontrado no cluster, é gerado um relatório de saúde de nível de aviso para o Serviço de Coordenador.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Por que vejo o meu cluster num estado de erro quando o POA está a funcionar?**

R: Durante o processo de instalação, o POA desativa ou reinicia os nós, o que pode resultar temporariamente num aglomerado pouco saudável.

Dependendo da política da aplicação, qualquer um dos nós pode descer durante uma operação de remendação *ou* todo o domínio de atualização pode ser desativado de uma só vez.

No final da instalação de atualizações do Windows, os nós são reenuáveis após o reinício.

No exemplo seguinte, o cluster foi para um estado de erro temporariamente porque dois nós estavam em baixo e a política MaxPercentageUnhealthyNodes foi violada. O erro é temporário até que a operação de remendação possa começar.

![Imagem de aglomerado insalubre](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Se o problema persistir, consulte a secção de resolução de problemas.

**P: O que posso fazer se o POA estiver em estado de alerta?**

R: Verifique se um relatório de saúde publicado contra a aplicação indica a causa principal. Normalmente, o aviso contém detalhes do problema. Se o problema for transitório, espera-se que a aplicação recupere automaticamente.

**P: O que posso fazer se o meu cluster não estiver saudável e precisar de fazer uma atualização urgente do sistema operativo?**

R: O POA não instala atualizações enquanto o cluster não estiver saudável. Tente trazer o seu cluster para um estado saudável para desbloquear o fluxo de trabalho do POA.

**P: Devo definir a TaskApprovalPolicy como "NodeWise" ou "UpgradeDomainWise" para o meu cluster?**

R: A definição "UpgradeDomainWise" acelera a reparação global do cluster remendando paralelamente todos os nós que pertencem a um domínio de atualização. Durante o processo, os nódes que pertencem a todo um domínio de atualização estão indisponíveis (em estado [ *de desativação).*](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabled)

Em contraste, a definição "NodeWise" remenda apenas um nó de cada vez, o que implicaria que a remendação geral do cluster poderia demorar mais tempo. No entanto, apenas um nó no máximo estaria indisponível (em estado *de desativação)* durante o processo de remendação.

Se o seu cluster pode tolerar executar um número N-1 de domínios de atualização durante o ciclo de remendação (onde N é o número total de domínios de atualização no seu cluster) pode definir a política como "UpgradeDomainWise". Caso contrário, coloque-o em "NodeWise".

**P: Quanto tempo leva para remendar um nó?**

R: Remendar um nó pode demorar de minutos (por exemplo, atualizações de [definição do Windows Defender)](https://www.microsoft.com/en-us/wdsi/definitions)a horas (por exemplo, [atualizações cumulativas do Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). O tempo que é necessário para remendar um nó depende principalmente de: 
 - O tamanho das atualizações.
 - O número de atualizações, que têm de ser aplicadas numa janela de remendos.
 - O tempo que demora a instalar as atualizações, reinicie o nó (se necessário) e termine os passos de instalação pós-reinicialização.
 - O desempenho do VM ou da máquina e as condições de rede.

**P: Quanto tempo demora a remendar um aglomerado inteiro?**

R: O tempo necessário para remendar um cluster inteiro depende de:

- O tempo necessário para remendar um nó.

- A política do Serviço de Coordenador. A política padrão, "NodeWise", resulta na remendação de apenas um nó de cada vez, uma abordagem mais lenta do que usar "UpgradeDomainWise". 

   Por exemplo: Se um nó demorar ~1 hora a ser remendado, remendar um cluster de 20 nós (mesmo tipo de nós) com 5 domínios de atualização, cada um contendo 4 nós, requer:
    - Para "NodeWise": ~20 horas.
    - Para "UpgradeDomainWise": ~5 horas.

- A carga de aglomerado. Cada operação de remendação requer a recolocar a carga de trabalho do cliente para outros nós disponíveis no cluster. Um nó que está a ser remendado estaria em estado [ *de desativação*](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabling) durante este tempo. Se o cluster estiver a funcionar perto da carga máxima, o processo de desativação demoraria mais tempo. Por conseguinte, o processo global de remendação pode parecer lento em condições tão stressadas.

- Falhas de saúde do cluster durante a remendação. Qualquer [degradação](/dotnet/api/system.fabric.health.healthstate#System_Fabric_Health_HealthState_Error) na [saúde do cluster](./service-fabric-health-introduction.md) interromperia o processo de remendos. Esta questão acrescentaria ao tempo global necessário para remendar todo o cluster.

**P: Por que vejo algumas atualizações nos resultados do Windows Update que são obtidos através da REST API, mas não sob o histórico de Atualização do Windows na máquina?**

R: Algumas atualizações do produto aparecem apenas no seu próprio histórico de atualização ou correção. Por exemplo, as atualizações do Windows Defender podem ou não ser apresentadas no histórico de Atualização do Windows no Windows Server 2016.

**P: O POA pode ser usado para remendar o meu cluster dev (cluster de um nó)?**

A: Não, o POA não pode ser usado para remendar um aglomerado de nó. Esta limitação é por design, porque [os serviços de sistema Service Fabric](./service-fabric-technical-overview.md#system-services) ou outras aplicações de clientes incorreriam em tempo de inatividade. Por conseguinte, os trabalhos de reparação de remendos nunca seriam aprovados pelo Gestor de Reparação.

**P: Como remendo os nós de cluster no Linux?**

R: Para saber sobre a orquestração de atualizações no Linux, consulte [a escala de máquina virtual Azure definir upgrades automáticos de imagem de SO](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

**P: Porque é que o ciclo de atualização está a demorar tanto tempo?**

R: Consultar o resultado JSON, introduzir o ciclo de atualização para todos os nós e, em seguida, pode tentar descobrir o tempo despendido pela instalação de atualização em cada nó utilizando a OperationStartTime e a OperationTime (OperationCompletionTime). 

Se houver uma grande janela de tempo em que não esteja a ser efetuada nenhuma atualização, o cluster pode estar em estado de erro e, consequentemente, o Gestor de Reparação não pode aprovar quaisquer tarefas de reparação de POA. Se a instalação da atualização estiver a demorar muito tempo em qualquer nó, esse nó pode não ter sido atualizado durante algum tempo. Muitas atualizações podem estar pendentes de instalação, o que pode resultar em atrasos. 

Também pode ser possível que o remendos de nó está bloqueado porque está preso no estado de *desativação.* Isto geralmente acontece porque desativar o nó pode levar a situações de quórum ou perda de dados.

**P: Por que o nó deve ser desativado quando o POA está a remendá-lo?**

R: POA desativa o nó com uma intenção *restart,* que para ou realoca todos os serviços de Tecido de Serviço que estão a funcionar no nó. O POA faz isto para garantir que as aplicações não acabem por usar uma mistura de DLLs novos e antigos, pelo que recomendamos não remendar um nó sem desativá-lo.

**P: Qual é o número máximo de nós que pode ser atualizado utilizando POA?**

R: O POA utiliza o Service Fabric Repair Manager para criar tarefas de reparação de nós para atualizações. No entanto, não podem estar presentes mais de 250 tarefas de reparação ao mesmo tempo. Atualmente, o POA cria tarefas de reparação para cada nó ao mesmo tempo, para que o POA não possa atualizar mais de 250 nós num cluster. 

## <a name="disclaimers"></a>Exclusões de Responsabilidade

- A POA aceita o Contrato de Licença End-User para a Atualização do Windows em nome do utilizador. Opcionalmente, a definição pode ser desligada na configuração da aplicação.

- O POA recolhe telemetria para acompanhar o uso e o desempenho. A telemetria da aplicação segue a definição da definição de telemetria do tecido de serviço (que está ligado por predefinição).

## <a name="troubleshooting"></a>Resolução de Problemas

Esta secção fornece possíveis soluções de resolução de problemas para problemas com os nós de remendação.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Um nó não está voltando ao estado

* O nó pode estar preso no estado *de desativação* porque:

  - Está pendente uma verificação de segurança. Para remediar esta situação, certifique-se de que existem nóns suficientes num estado saudável.

* O nó pode estar preso no estado *de deficientes* porque:

  - Foi desativado manualmente.
  - Foi incapacitado por causa de um trabalho de infraestrutura Azure em curso.
  - Foi temporariamente desativado pela AP para remendar o nó.

* O nó pode estar preso num estado baixo porque:

  - Foi colocado num estado baixo manualmente.
  - Está a ser reiniciado (que pode ser desencadeado pela AP).
  - Tem um VM ou máquina defeituoso, ou está com problemas de conectividade de rede.

### <a name="updates-were-skipped-on-some-nodes"></a>As atualizações foram ignoradas em alguns nos acenos

O POA tenta instalar uma atualização do Windows de acordo com a política de reagendamento. O serviço tenta recuperar o nó e saltar a atualização de acordo com a política de aplicação.

Neste caso, é gerado um relatório de saúde de nível de alerta contra o Serviço de Agente de Nó. O resultado do Windows Update também contém o motivo possível para a falha.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>A saúde do cluster vai para o erro enquanto a atualização está a ser instalada

Uma atualização defeituosa do Windows pode diminuir a saúde de uma aplicação ou cluster num determinado nó ou domínio de atualização. O POA interrompe qualquer operação subsequente do Windows Update até que o cluster volte a ficar saudável.

Um administrador deve intervir e determinar por que a aplicação ou cluster tornou-se insalubre por causa do Windows Update.

## <a name="poa-release-notes"></a>Notas de lançamento do POA

>[!NOTE]
> Para as versões POA 1.4.0 e posterior, pode encontrar notas de lançamento e lançamentos na página de lançamento da [Aplicação de Orquestração de Patch](https://github.com/microsoft/Service-Fabric-POA/releases/) no GitHub.

### <a name="version-110"></a>Versão 1.1.0
- Divulgação pública

### <a name="version-111"></a>Versão 1.1.1
- Corrigiu um bug no SetupEntryPoint do NodeAgentService que impediu a instalação do NodeAgentNTService.

### <a name="version-120"></a>Versão 1.2.0

- As correções de bugs em torno do sistema reiniciam o fluxo de trabalho.
- Correção de bugs na criação de tarefas de RM devido à qual a verificação de saúde durante a preparação das tarefas de reparação não estava a acontecer como esperado.
- Alterou o modo de arranque para o serviço do Windows POANodeSvc de automóvel para automóvel atrasado.

### <a name="version-121"></a>Versão 1.2.1

- Correção de insetos no fluxo de trabalho de escala de cluster. Introduziu a lógica de recolha de lixo para tarefas de reparação de POA pertencentes a nós inexistentes.

### <a name="version-122"></a>Versão 1.2.2

- Correções de erros diversos.
- Os binários estão assinados.
- Adicionado link sfpkg para a aplicação.

### <a name="version-130"></a>Versão 1.3.0

- Configurar instalarWindowsOSOnlyUpdates para falsos agora instala todas as atualizações disponíveis.
- Alterou a lógica de desativar atualizações automáticas. Isto corrige um bug onde as atualizações automáticas não estavam a ser desativadas no Server 2016 e acima.
- Restrição de colocação parametrizada para ambos os microserviços de POA para casos de utilização avançada.

### <a name="version-131"></a>Versão 1.3.1
- Correção de regressão onde o POA 1.3.0 não funcionará no Windows Server 2012 R2 ou mais cedo devido a uma falha na desativação de atualizações automáticas. 
- A correção de erros onde a configuração InstallWindowsOSOnlyUpdates é sempre escolhida como Verdadeira.
- Alterar o valor predefinido de InstallWindowsOSOnlyUpdates para Falso.

### <a name="version-132"></a>Versão 1.3.2
- Corrigir um problema que afeta o ciclo de vida de remendação num nó, se houver nós com um nome que seja um subconjunto do nome do nó atual. Para tais nós, é possível que o remendar tenha sido perdido ou um reboot está pendente.
