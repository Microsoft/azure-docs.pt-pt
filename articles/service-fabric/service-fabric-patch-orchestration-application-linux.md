---
title: Aplicação de orquestração do Service Fabric patch do Azure para linux | Documentos da Microsoft
description: Aplicativo para automatizar a aplicação de patches de sistema operativo num cluster do Service Fabric do Linux.
services: service-fabric
documentationcenter: .net
author: novino
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/22/2018
ms.author: nachandr
ms.openlocfilehash: 537450dbc386a94fa5c2e0d9334435dce041a32f
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057647"
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>Corrigir o sistema operativo do Linux no seu cluster do Service Fabric

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

A aplicação de orquestração de patch é uma aplicação do Azure Service Fabric que automatiza o sistema operativo, aplicação de patches no cluster do Service Fabric sem tempo de inatividade.

A aplicação de orquestração do patch fornece as seguintes funcionalidades:

- **Instalação de atualização do sistema operativo automático**. Atualizações do sistema operativo são automaticamente transferidas e instaladas. Nós de cluster são reiniciados conforme necessário, sem tempo de inatividade do cluster.

- **Integração de aplicação de patches e o estado de funcionamento com reconhecimento de cluster**. Ao aplicar as atualizações, a aplicação de orquestração do patch monitoriza o estado de funcionamento de nós do cluster. Nós de cluster são atualizado um nó num período de tempo ou de um domínio de atualização de cada vez. Se o estado de funcionamento do cluster ficar inativa devido ao processo de aplicação de patches, aplicação de patches é parada para impedir a aggravating o problema.

## <a name="internal-details-of-the-app"></a>Detalhes internos da aplicação

A aplicação de orquestração do patch é composta pelos subcomponentes seguintes:

- **Serviço de coordenador**: Este serviço com monitorização de estado é responsável por:
    - Coordenar a tarefa de atualização de SO em todo o cluster.
    - Armazenando o resultado de operações de atualização de SO concluídos.
- **Serviço de agente de nó**: Este serviço sem estado é executado em todos os nós de cluster do Service Fabric. O serviço é responsável por:
    - O daemon de agente de nó no Linux de inicialização.
    - O serviço de daemon de monitorização.
- **O daemon de agente de nó**: Este serviço de daemon de Linux é executado num nível mais alto privilégio (raiz). Por outro lado, o serviço de agente de nó e o serviço Coordenador de ser executados num nível inferior de privilégio. O serviço é responsável por executar as seguintes tarefas de atualização em todos os nós de cluster:
    - Desativar a atualização de SO automática no nó.
    - Transferir e instalar a atualização do SO, de acordo com a política do utilizador forneceu.
    - A reiniciar a instalação da atualização de SO de postagem de máquina se for necessário.
    - A carregar os resultados das atualizações de SO para o serviço de coordenador.
    - Os relatórios de estado de geração de relatórios no caso de uma operação falhou depois de esgotar todas as tentativas.

> [!NOTE]
> A aplicação de orquestração do patch utiliza o serviço de sistema do Service Fabric reparação manager para desativar ou ativar o nó e executar verificações do Estado de funcionamento. A tarefa de reparação criada pela aplicação de orquestração do patch controla o progresso da atualização para cada nó.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Certifique-se de que as VMs do Azure estão a executar o Ubuntu 16.04
No momento da escrita neste documento, Ubuntu 16.04 (`Xenial Xerus`) é a única versão suportada.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-62x-and-above"></a>Certifique-se de que o cluster de linux do service fabric é a versão 6.2.x e acima

Patch orchestration aplicação linux utiliza determinadas funcionalidades de tempo de execução que só estão disponíveis na versão de runtime do service fabric 6.2.x e acima.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Ativar o serviço de Gestor de reparação (se não tiver o já)

A aplicação de orquestração do patch exige que o serviço de sistema do Gestor de reparação para ser ativada no cluster.

#### <a name="azure-clusters"></a>Clusters do Azure

Clusters do linux do Azure no silver e escalão de durabilidade gold ter o serviço de Gestor de reparação ativado por predefinição. Clusters do Azure no escalão de durabilidade de bronze, por predefinição, não têm o serviço de Gestor de reparação ativado. Se o serviço já está ativado, pode ver que o mesmo está na seção de serviços de sistema no Service Fabric Explorer.

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

Não são suportados clusters de Linux autónomo do Service Fabric no momento da escrita deste documento.

### <a name="disable-automatic-os-update-on-all-nodes"></a>Desativar atualização automática de SO em todos os nós

Atualizações automáticas do SO podem levar à perda de disponibilidade e ou alterar o comportamento dos aplicativos em execução. A aplicação de orquestração do patch, por predefinição, tenta desativar a atualização do SO automática em cada nó de cluster para impedir que esses cenários.
Para Ubuntu [autônoma – as atualizações](https://help.ubuntu.com/community/AutomaticSecurityUpdates) estão desativados por aplicação de orquestração do patch.

## <a name="download-the-app-package"></a>Transferir o pacote de aplicação

Aplicativo, juntamente com os scripts de instalação pode ser transferido a partir [link de arquivo](https://go.microsoft.com/fwlink/?linkid=867984).

Aplicação em formato de sfpkg pode ser transferida a partir [sfpkg ligação](https://aka.ms/POA/POA_v2.0.3.sfpkg). Isso é útil para [do Azure Resource Manager com base em implantação de aplicativos](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Configurar a aplicação

O comportamento da aplicação de orquestração do patch pode ser configurado para atender às suas necessidades. Substitua os valores predefinidos ao transmitir o parâmetro de aplicação durante a criação de aplicação ou atualização. Podem ser fornecidos parâmetros de aplicação, especificando `ApplicationParameter` para o `Start-ServiceFabricApplicationUpgrade` ou `New-ServiceFabricApplication` cmdlets.

|**Parâmetro**        |**Type**                          | **Detalhes**|
|:-|-|-|
|MaxResultsToCache    |Longo                              | Número máximo de resultados de atualização, que devem ser colocados em cache. <br>Valor predefinido é 3000 assumindo a: <br> -Número de nós é 20. <br> -Número de atualizações a acontecer num nó por mês é cinco. <br> -Número de resultados por operação pode ser 10. <br> -Devem ser armazenados resultados para os últimos três meses. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy indica a política que está a ser utilizado pelo serviço de coordenador para instalar atualizações em todos os nós de cluster do Service Fabric.<br>                         Valores permitidos são: <br>                                                           <b>NodeWise</b>. As atualizações são instalado um nó por vez. <br>                                                           <b>UpgradeDomainWise</b>. As atualizações são instalado um domínio de atualização de cada vez. (No máximo, podem ir todos os nós que pertencem a um domínio de atualização para atualização.)
| UpdateOperationTimeOutInMinutes | Int <br>(Predefinição: 180)                   | Especifica o tempo limite para qualquer operação de atualização (download ou instalação). Se não é possível concluir a operação no tempo limite especificado, é abortada.       |
| RescheduleCount      | Int <br> (Predefinição: 5)                  | O número máximo de vezes que o serviço reagenda o sistema operacional de atualização no caso de uma operação falha de forma permanente.          |
| RescheduleTimeInMinutes  | Int <br>(Predefinição: 30) | No caso de falha persistir de atualizar o intervalo no qual o serviço reagenda o sistema operacional. |
| UpdateFrequency           | Cadeia de caracteres separados por vírgulas (predefinição: "Semanais, quarta-feira, 7:00:00")     | A frequência para instalar atualizações do SO no cluster. Os valores de formato e possíveis são: <br>-Mensais, DD, hh: mm:, por exemplo, mensalmente, 5, 12:22:32. <br> -Semanal, dia, hh: mm:, para o exemplo, semanalmente, Terça-feira, 12:22:32.  <br> -Diárias, hh: mm:, por exemplo, diariamente, 12:22:32.  <br> -None indica que essa atualização não deve ser feita.  <br><br> Todos os tempos são em formato UTC.|
| UpdateClassification | Cadeia de caracteres separados por vírgulas (predefinição: "securityupdates") | Tipo de atualizações que devem ser instalados em nós do cluster. Os valores aceitáveis são securityupdates, todos os. <br> -securityupdates - terá de instalar apenas as atualizações de segurança <br> -todos os - instaladas todas as atualizações disponíveis do apt.|
| ApprovedPatches | Cadeia de caracteres separados por vírgulas (predefinição: "") | Esta é a lista de atualizações aprovadas que devem ser instalados em nós de cluster. A lista separada por vírgulas contém pacotes aprovados e a versão de destino, opcionalmente, pretendida.<br> for example: "apt-utils = 1.2.10ubuntu1, python3-jwt, apt-transport-https < 1.2.194, libsystemd0 >= 229-4ubuntu16" <br> O procedimento acima terá de instalar <br> -apt utils 1.2.10ubuntu1 de versão, se estiver disponível na cache de apt. Se essa versão específico não estiver disponível, em seguida, é não operacional. <br> -atualizações de python3 jwt para a versão mais recente disponível. Se o pacote não estiver presente, em seguida, é não operacional. <br> -atualizações de apt transporte https para a versão mais recente é inferior a 1.2.194. Se esta versão não estiver presente, em seguida, é não operacional. <br> -libsystemd0 atualizações para a versão mais recente é maior ou igual a 229 4ubuntu16. Se não existir uma versão desse tipo, em seguida, é não operacional.|
| RejectedPatches | Cadeia de caracteres separados por vírgulas (predefinição: "") | Esta é a lista de atualizações que não deve ser instalado em nós do cluster <br> Por exemplo: "bash, sudo" <br> Anterior filtra bash, sudo da receção de todas as atualizações. |


> [!TIP]
> Se pretender que a atualização do SO para imediatamente, definir `UpdateFrequency` em relação ao tempo de implementação de aplicação. Por exemplo, suponha que tem um cluster de teste de cinco nós e planeja implantar o aplicativo ao cerca as 17:00 UTC. Se for suposto que a atualização da aplicação ou implementação demora 30 minutos no máximo, definir o UpdateFrequency como "Diariamente, 17:30:00."

## <a name="deploy-the-app"></a>Implementar a aplicação

1. Prepare o cluster ao concluir todos os passos de pré-requisitos.
2. Implemente a aplicação de orquestração do patch, como qualquer outra aplicação do Service Fabric. Pode implementar a aplicação com o PowerShell ou CLI do Azure Service Fabric. Siga os passos em [implementar e remover aplicações com o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) ou [implementar a aplicação com a CLI do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)
3. Para configurar a aplicação no momento da implementação, de transmitir o `ApplicationParameter` para o `New-ServiceFabricApplication` cmdlet ou os scripts fornecidos. Para sua comodidade, o powershell (Deploy.ps1) e scripts de bash (Deploy.sh) são fornecidas, juntamente com a aplicação. Para utilizar o script:

    - Ligar a um cluster do Service Fabric.
    - Execute o script de implementar. Opcionalmente, transmita o parâmetro de aplicação para o script. Por exemplo:.\Deploy.ps1 - ApplicationParameter @{UpdateFrequency = "Diária, 11:00:00"} OR./Deploy.sh "{\"UpdateFrequency\":\"diariamente, 11:00:00\"}" 

> [!NOTE]
> Manter o script e a pasta de aplicativo PatchOrchestrationApplication no mesmo diretório.

## <a name="upgrade-the-app"></a>Atualizar a aplicação

Para atualizar uma aplicação de orquestração do patch existente, siga os passos em [atualização da aplicação de Service Fabric com o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) ou [atualização da aplicação de Service Fabric com a CLI do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)

## <a name="remove-the-app"></a>Remover a aplicação

Para remover a aplicação, siga os passos em [implementar e remover aplicações com o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) ou [remover uma aplicação através da CLI do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)

Para sua comodidade, o powershell (Undeploy.ps1) e scripts de bash (Undeploy.sh) são fornecidas, juntamente com a aplicação. Para utilizar o script:

  - Ligar a um cluster do Service Fabric.
  - Executar o script Undeploy.ps1 ou Undeploy.sh

> [!NOTE]
> Manter o script e a pasta de aplicativo PatchOrchestrationApplication no mesmo diretório.

## <a name="view-the-update-results"></a>Ver os resultados de atualização

A aplicação de orquestração do patch expõe as APIs REST para exibir os resultados históricos para o usuário. Segue-se um resultado de exemplo:
```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
```json
[ 
  { 
    "NodeName": "_bronze_0", 
    "UpdateOperationResults": [ 
      { 
        "OperationResult": "succeeded", 
        "NodeName": "_bronze_0", 
        "OperationTime": "2017-11-21T12:39:29.0435917Z", 
        "UpdateDetails": [ 
          { 
            "UpdateId": "linux-cloud-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-headers-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-image-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "python3-apport:amd64=2.20.1-0ubuntu2.13", 
            "ResultCode": "succeeded" 
          }, 
        ], 
        "OperationType": "installation", 
        "UpdateClassification": "securityupdates", 
        "UpdateFrequency": "Daily, 7:00:00", 
        "RebootRequired": true, 
        "ApprovedList": "", 
        "RejectedList": "" 
      } 
    ] 
  } 
] 
```

Campos do JSON são descritos da seguinte forma:

Campo | Valores | Detalhes
-- | -- | --
OperationResult | 0 - foi concluída com êxito<br> 1 - foi concluída com êxito com erros<br> 2 - falha<br> 3 - abortada<br> 4 - abortada com tempo limite | Indica o resultado da operação geral (em geral envolvendo a instalação de atualizações de um ou mais).
ResultCode | Mesmo que operationresult ainda | Este campo indica o resultado da operação de instalação para a atualização individual.
OperationType | 1 - instalação<br> 0 - pesquisa e a transferência.| A instalação é a única OperationType que poderia ser apresentada nos resultados, por predefinição.
UpdateClassification | A predefinição é "securityupdates" | Tipo de atualizações, que é instalado durante a operação de atualização
UpdateFrequency | A predefinição é "Semanais, quarta-feira, 7:00:00" | Frequência configurada para esta atualização de atualização.
RebootRequired | VERDADEIRO - reinício foi necessário<br> FALSO - não foi necessário reiniciar | Indica que um reinício foi necessário para concluir a instalação de atualizações.
ApprovedList | A predefinição é "" | Lista de patches aprovados para esta atualização
RejectedList | A predefinição é "" | Lista de patches rejeitados para esta atualização

Se nenhuma atualização está agendada ainda, o resultado JSON está vazio.

Inicie sessão no cluster para os resultados da atualização de consulta. Em seguida, Descubra o endereço de réplica para as primárias do serviço de coordenador e clica na URL do navegador: http://&lt;IP de RÉPLICA&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults .

O ponto de final REST para o serviço de coordenador tem uma porta dinâmica. Para verificar o URL exato, consulte o Service Fabric Explorer. Por exemplo, os resultados estão disponíveis em `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`.

![Imagem do ponto final REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>Eventos de diagnóstico/estado de funcionamento

### <a name="diagnostic-logs"></a>Registos de diagnósticos

Registos de aplicações de orquestração do patch são recolhidos como parte dos registos de tempo de execução do Service Fabric.

Caso queira capturar os registos por meio da ferramenta de diagnóstico/pipeline à sua escolha. Patch orchestration application utiliza os seguintes IDs de fornecedor fixo para registar eventos através de [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Relatórios de estado de funcionamento

A aplicação de orquestração do patch também publica relatórios de estado de funcionamento contra o serviço de coordenador ou o serviço de agente de nó nos seguintes casos:

#### <a name="an-update-operation-failed"></a>Uma operação de atualização falhou

Se uma operação de atualização falhar num nó, é gerado um relatório de estado de funcionamento para o serviço de agente de nó. Detalhes do relatório de estado de funcionamento contém o nome do nó problemático.

Após a aplicação de patches é concluída com êxito no nó problemático, o relatório é automaticamente limpo.

#### <a name="the-node-agent-daemon-service-is-down"></a>O serviço de Daemon de agente de nó está desativado

Se o serviço de Daemon de agente de nó estiver desativado num nó, é gerado um relatório de estado de funcionamento do nível de aviso para o serviço de agente de nó.

#### <a name="the-repair-manager-service-is-not-enabled"></a>O serviço de Gestor de reparação não está ativado

É gerado um relatório de estado de funcionamento do nível de aviso para o serviço de coordenador se o serviço de Gestor de reparação não foi encontrado no cluster.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

P. **Por que motivo é que vejo o meu cluster em estado de erro quando a aplicação de orquestração do patch está em execução?**

R. Durante o processo de instalação, a aplicação de orquestração do patch desativa ou reinicia nós. Esta operação temporariamente pode resultar em estado de funcionamento do cluster na direção para baixo.

Com base na política para o aplicativo, seja um nó pode descer durante uma operação de aplicação de patches *ou* um todo domínio de atualização pode ser reduzidos em simultâneo.

No final da instalação, os nós são reenabled publicar o reinício.

No exemplo a seguir, o cluster correu para um Estado de erro temporariamente porque dois nós estavam inativa e a política de MaxPercentageUnhealthyNodes foi violada. O erro é temporário até que a operação de correção está em curso.

![Imagem do cluster de mau estado de funcionamento](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Se o problema persistir, consulte a secção de resolução de problemas.

P. **Aplicação de orquestração do patch está num Estado de aviso**

R. Verifique se um relatório de estado de funcionamento lançado contra a aplicação é a causa raiz. Normalmente, o aviso contém detalhes do problema. Se o problema é transitório, espera-se a aplicação para recuperar automaticamente de neste estado.

P. **O que devo fazer se o meu cluster está em mau estado de funcionamento e o que preciso fazer uma atualização do sistema de operativo urgente?**

R. A aplicação de orquestração do patch não instala atualizações enquanto o cluster está em mau estado de funcionamento. Para desbloquear o fluxo de trabalho de aplicação do patch orchestration, traga o seu cluster para um bom estado de funcionamento.

P. **Por que a aplicação de patches em clusters demora tanto tempo para ser executado?**

R. O tempo necessário para a aplicação de orquestração do patch principalmente está dependente dos seguintes fatores:

- A política do serviço de coordenador. 
  - A política predefinida, `NodeWise`, resulta na aplicação de patches apenas um nó por vez. Especialmente se houver um cluster maior, recomendamos que utilize o `UpgradeDomainWise` política para obter a aplicação de patches mais rápido em cluster.
- O número de atualizações disponíveis para download e instalação. 
- O tempo médio necessário para transferir e instalar uma atualização, que não deve exceder os algumas horas.
- O desempenho da VM e rede de largura de banda.

P. **Como a aplicação de orquestração do patch faz decide quais atualizações são atualizações de segurança.**

R. Aplicação de orquestração do patch utiliza lógica específica de distro para determinar quais são as atualizações entre as atualizações disponíveis atualizações de segurança. Por exemplo: No ubuntu o aplicativo procura atualizações a partir de arquivos mortos $RELEASE-segurança, $RELEASE-atualizações ($RELEASE = xenial ou a versão de lançamento de base padrão do linux). 

 
P. **Como bloquear logon numa versão específica do pacote**

R. Utilize as definições de ApprovedPatches para bloquear os pacotes para uma versão específica. 


P. **O que acontece com as atualizações automáticas ativadas no Ubuntu?**

R. Assim que instalar a aplicação de orquestração do patch no seu cluster, autônoma – as atualizações no seu nó de cluster serão desativadas. Todo o fluxo de trabalho de atualização periódica poderia ser orientado pela aplicação de orquestração do patch.
Para que a consistência do ambiente de cluster, recomendamos que instale as atualizações por meio de apenas aplicação do patch orchestration. 
 
P. **Publicar atualização o patch é aplicado fazer de aplicação de orquestração a limpeza de pacotes não utilizados?**

R. Sim, a limpeza acontece como parte dos passos de pós-instalação. 

P. **Aplicação de Patch Orchestration pode ser utilizada para corrigir o meu cluster de desenvolvimento (um nó de cluster)?**

R. Não, não podem servir-se aplicações de orquestração do Patch para o cluster de um nó de patch. Esta limitação é por design, como [serviços do sistema de recursos de infraestrutura do serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) ou todas as aplicações cliente enfrentarão o tempo de inatividade e, por conseguinte, qualquer tarefa de reparação para aplicação de patches nunca seria obter aprovada pelo Gestor de reparação.

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

A aplicação de orquestração do patch tenta instalar uma atualização, de acordo com a política rescheduling. O serviço tentará recuperar o nó e ignorar a atualização, de acordo com a política de aplicação.

Nesse caso, é gerado um relatório de estado de funcionamento do nível de aviso para o serviço de agente de nó. O resultado para a atualização também contém o motivo possível da falha.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>O estado de funcionamento do cluster vai para o erro, enquanto a atualização é instalada

Uma atualização com falhas pode prejudicar o estado de funcionamento de uma aplicação ou o cluster num determinado nó ou o domínio de atualização. A aplicação de orquestração do patch discontinues quaisquer operações de atualização subseqüente até que o cluster está em bom estado novamente.

Um administrador deve intervir e determinar por que a aplicação ou o cluster se tornou mau estado de funcionamento devido a uma atualização previamente instalada.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

A aplicação de orquestração do patch recolhe telemetria para controlar a utilização e desempenho. Telemetria da aplicação segue a definição de configuração de telemetria do runtime do Service Fabric (o que está ativada por predefinição).

## <a name="release-notes"></a>Notas de Versão

### <a name="version-010"></a>Versão 0.1.0
- Versão de pré-visualização privada

### <a name="version-200"></a>Versão 2.0.0
- Lançamento público

### <a name="version-201"></a>Versão 2.0.1
- Recompilado a aplicação com o SDK mais recente do Service Fabric

### <a name="version-202"></a>Versão 2.0.2 
- Foi corrigido um problema com aviso do Estado de funcionamento introdução deixado para trás durante o reinício.

### <a name="version-203-latest"></a>Versão 2.0.3 (mais recente)
- Correção do problema em que a utilização da CPU do serviço de daemon de agente de nó atingido até 99% em Standard_D1_v2 VMs.
- Correção do problema que afetado a aplicação de patches cyle de vida num nó, caso haja nós com o nome que é o subconjunto do nome do nó atual. Para esses nós, sua possível, aplicação de patches está em falta ou o reinício está pendente.
- Foi corrigido um erro devido a que o daemon de agente de nó mantém falhar quando danificadas definições são transmitidas para o serviço.