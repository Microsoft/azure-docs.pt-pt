---
title: PRETERIDO Monitorar cluster de DC/so do Azure-gerenciamento de operações
description: Monitore um cluster DC/OS do serviço de contêiner do Azure com o Log Analytics.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277327"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>PRETERIDO Monitorar um cluster DC/OS do serviço de contêiner do Azure com o Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics é a solução de gerenciamento de ti baseada em nuvem da Microsoft que ajuda você a gerenciar e proteger sua infraestrutura local e na nuvem. A solução de contêiner é uma solução no Log Analytics, que ajuda a exibir o inventário de contêiner, o desempenho e os logs em um único local. Você pode auditar, solucionar problemas de contêineres exibindo os logs no local centralizado e encontrar um excesso de consumo excessivo de contêiner em um host.

![](media/container-service-monitoring-oms/image1.png)

Para obter mais informações sobre a solução de contêiner, consulte a [solução de contêiner log Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Configurando Log Analytics do universo DC/OS


Este artigo pressupõe que você configurou um DC/so e implantou aplicativos de contêiner da Web simples no cluster.

### <a name="pre-requisite"></a>Pré-requisito
- [Assinatura Microsoft Azure](https://azure.microsoft.com/free/) -você pode obter uma assinatura gratuitamente.  
- Instalação do Log Analytics Workspace-consulte a "etapa 3" abaixo
- [CLI do DC/os](https://docs.mesosphere.com/1.12/cli) instalada.

1. No painel do DC/so, clique em universo e procure ' OMS ', conforme mostrado abaixo.

   >[!NOTE]
   >OMS agora é referido como o Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Clique em **Instalar**. Você verá um pop-up com as informações de versão e um botão **instalar pacote** ou **instalação avançada** . Quando você clica em **instalação avançada**, o leva para a página de **Propriedades de configuração específica do OMS** .

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Aqui, você será solicitado a inserir o `wsid` (a ID do espaço de trabalho Log Analytics) e `wskey` (a chave primária para a ID do espaço de trabalho). Para obter `wsid` e `wskey` você precisa criar uma conta em <https://mms.microsoft.com>.
   Siga as etapas para criar uma conta. Quando terminar de criar a conta, você precisará obter o `wsid` e `wskey` clicando em **configurações**, em **fontes conectadas**e em **servidores Linux**, conforme mostrado abaixo.

   ![](media/container-service-monitoring-oms/image5.png)

4. Selecione o número de instâncias que você deseja e clique no botão ' revisar e instalar '. Normalmente, você desejará ter o número de instâncias igual ao número de VMs que você tem em seu cluster de agente. Log Analytics agente para Linux é instalado como contêineres individuais em cada VM para a qual deseja coletar informações para monitoramento e registro em log de informações.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Configurando um painel simples de Log Analytics

Depois de instalar o agente de Log Analytics para Linux nas VMs, a próxima etapa é configurar o painel de Log Analytics. Você pode configurar o painel por meio de portal do Azure.

### <a name="azure-portal"></a>Portal do Azure 

Entre em portal do Azure em <https://portal.microsoft.com/>. Vá para o **Marketplace**, selecione **monitoramento + gerenciamento** e clique em **ver tudo**. Em seguida, digite `containers` na pesquisa. Você verá "contêineres" nos resultados da pesquisa. Selecione **contêineres** e clique em **criar**.

![](media/container-service-monitoring-oms/image9.png)

Depois de clicar em **criar**, ele solicitará seu espaço de trabalho. Selecione seu espaço de trabalho ou, se você não tiver um, crie um novo espaço de trabalho.

![](media/container-service-monitoring-oms/image10.PNG)

Depois de selecionar seu espaço de trabalho, clique em **criar**.

![](media/container-service-monitoring-oms/image11.png)

Para obter mais informações sobre a solução de contêiner de Log Analytics, consulte a [solução de contêiner log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Como dimensionar Log Analytics agente com o ACS DC/OS 

No caso de você precisar ter instalado Log Analytics agente da contagem de nós real ou se estiver expandindo o conjunto de dimensionamento de máquinas virtuais adicionando mais VM, você pode fazer isso dimensionando o serviço de `msoms`.

Você pode ir para Marathon ou a guia serviços de interface do usuário do DC/so e escalar verticalmente sua contagem de nós.

![](media/container-service-monitoring-oms/image12.PNG)

Isso será implantado em outros nós que ainda não implantaram o agente de Log Analytics.

## <a name="uninstall-ms-oms"></a>Desinstalar o MS OMS

Para desinstalar o MS OMS, insira o seguinte comando:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Avise-nos!!!
O que funciona? O que está faltando? O que mais você precisa para isso é útil para você? Informe-nos em <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Passos seguintes

 Agora que você configurou Log Analytics para monitorar seus contêineres,[consulte o painel do contêiner](../../azure-monitor/insights/containers.md).
