---
title: (PRETERIDO) Monitorizar o cluster do DC/OS do Azure - gestão de operações
description: Monitorize um cluster de DC/OS do Azure Container Service com o Log Analytics.
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 290141136672729060f5156d645c47ac303fa0c3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110975"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(PRETERIDO) Monitorizar um cluster DC/OS do Azure Container Service com o Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics é uma solução da Microsoft com base na cloud IT management que ajuda a gerenciar e proteger no local e a infraestrutura na cloud. Solução de contentor é uma solução no Log Analytics, que lhe permite ver o inventário de contentor, desempenho e registos numa única localização. Pode de auditoria, resolver problemas relacionados com contentores visualizando os registos de localização centralizada e encontrar ruidosos consumir em excesso contentor num anfitrião.

![](media/container-service-monitoring-oms/image1.png)

Para obter mais informações sobre solução de contentor, consulte a [contentor solução Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Configurar o Log Analytics do DC/OS universe


Este artigo pressupõe que tiver configurado um DC/OS e implementar aplicações de contentor web simples no cluster.

### <a name="pre-requisite"></a>Pré-requisito
- [Subscrição do Microsoft Azure](https://azure.microsoft.com/free/) -podem obter uma subscrição gratuita.  
- Configuração da área de trabalho de análise de registo - consulte "Step 3" abaixo
- [DC/OS CLI](https://docs.mesosphere.com/1.12/cli) instalado.

1. No dashboard do DC/OS, clique no universo e procure "OMS", conforme mostrado abaixo.

   >[!NOTE]
   >OMS agora é referido como o Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Clique em **Instalar**. Verá um pop-up com as informações de versão e um **Instalar pacote** ou **instalação avançada** botão. Quando clica em **instalação avançada**, que leva ao **propriedades de configuração específicas do OMS** página.

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Aqui, será solicitado a inserir o `wsid` (o ID de área de trabalho do Log Analytics) e `wskey` (a chave primária para o ID de área de trabalho). Para obter ambos `wsid` e `wskey` tem de criar uma conta no <https://mms.microsoft.com>.
   Siga os passos para criar uma conta. Quando tiver terminado criar a conta, tem de obter seu `wsid` e `wskey` ao clicar em **definições**, em seguida, **origens ligadas**e, em seguida, **servidores Linux**, conforme mostrado abaixo.

   ![](media/container-service-monitoring-oms/image5.png)

4. Selecione o número de instâncias que pretende e clique no botão "Rever e instalar". Normalmente, deverá ter o número de instâncias iguais ao número de VMS que tem no seu cluster do agente. Agente do log Analytics para Linux é instalado como contentores individuais em cada VM que quer recolher informações de monitorização e informações de registo.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Como configurar um dashboard do Log Analytics simples

Depois de instalar o agente do Log Analytics para Linux nas VMs, a próxima etapa é configurar o dashboard do Log Analytics. Pode configurar o dashboard através do portal do Azure.

### <a name="azure-portal"></a>Portal do Azure 

Inicie sessão portal do Azure em <https://portal.microsoft.com/>. Aceda a **Marketplace**, selecione **monitorização + gestão** e clique em **ver todos os**. Em seguida, escreva `containers` na pesquisa. Verá "contentores" nos resultados da pesquisa. Selecione **contentores** e clique em **criar**.

![](media/container-service-monitoring-oms/image9.png)

Assim que clicar em **criar**, ele irá pedir-lhe para sua área de trabalho. Selecione a sua área de trabalho ou se não tiver uma, crie uma nova área de trabalho.

![](media/container-service-monitoring-oms/image10.PNG)

Depois de selecionar a área de trabalho, clique em **criar**.

![](media/container-service-monitoring-oms/image11.png)

Para obter mais informações sobre a solução de contentor do Log Analytics, consulte a [contentor solução Log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Como dimensionar o agente do Log Analytics com o ACS DC/OS 

No caso de tem de ter instalado o agente do Log Analytics sem a contagem real do nó ou é aumente verticalmente o conjunto ao adicionar mais de VM de dimensionamento de máquina virtual, pode fazê-lo ao dimensionar o `msoms` serviço.

Pode ir para o Marathon ou o separador de serviços de interface do Usuário do DC/OS e dimensionar a sua contagem de nós.

![](media/container-service-monitoring-oms/image12.PNG)

Isto irá implementar para os outros nós que ainda não implantaram o agente Log Analytics.

## <a name="uninstall-ms-oms"></a>Desinstalar o MS OMS

Para desinstalar o MS OMS, introduza o seguinte comando:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Fale conosco!!!
O que funciona? O que está faltando? O que mais precisa para que isso seja útil para? Fale na <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Passos Seguintes

 Agora que configurou o Log Analytics para monitorizar os seus contentores,[ver o seu dashboard de contentor](../../azure-monitor/insights/containers.md).
