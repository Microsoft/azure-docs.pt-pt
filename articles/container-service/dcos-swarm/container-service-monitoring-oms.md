---
title: (DEPRECIADO) Monitor Azure DC/OS cluster - Gestão de Operações
description: Monitorize um cluster de serviço de contentores Azure DC/OS com Log Analytics.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277327"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(DEPRECIADO) Monitorize um cluster de serviço de contentores Azure DC/OS com Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

O Log Analytics é a solução de gestão de TI baseada na nuvem da Microsoft que o ajuda a gerir e proteger as suas infraestruturas no local e na nuvem.A Solução de Contentores é uma solução no Log Analytics, que o ajuda a visualizar o inventário, desempenho e registos do contentor num único local. Você pode auditar, resolver contentores de problemas visualizando os troncos em localização centralizada, e encontrar recipiente supérno consumindo excesso em um hospedeiro.

![](media/container-service-monitoring-oms/image1.png)

Para mais informações sobre a Solução de Contentores, consulte o [Registo de Solução](../../azure-monitor/insights/containers.md)de Contentores Analytics .

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Configuração de Log Analytics do universo DC/OS


Este artigo pressupõe que criou um DC/OS e implementou aplicações simples de contentores web no cluster.

### <a name="pre-requisite"></a>Pré-requisito
- [Subscrição](https://azure.microsoft.com/free/) do Microsoft Azure - Pode obter uma subscrição gratuitamente.  
- Configuração do espaço de trabalho de Análise de Log Analytics - ver "Passo 3" abaixo
- [DC/OS CLI](https://docs.mesosphere.com/1.12/cli) instalado.

1. No painel de instrumentos DC/OS, clique no Universo e procure por 'OMS' como mostrado abaixo.

   >[!NOTE]
   >O OMS é agora referido como Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Clique em **Instalar**. Verá um pop-up com as informações da versão e um pacote de **instalação** ou botão **de instalação avançada.** Quando clicar em **Instalação Avançada**, o que o leva à página de propriedades de configuração específica seleções **OMS.**

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Aqui, será-lhe pedido `wsid` que introduza o ID `wskey` (o id do espaço de trabalho Log Analytics) e (a chave principal para o ID do espaço de trabalho). Para obter `wsid` `wskey` os dois e precisa <https://mms.microsoft.com>criar uma conta em .
   Siga os passos para criar uma conta. Uma vez feito a criação da `wsid` conta, precisa de obter as `wskey` suas e clicando em **Definições**, em **seguida, Fontes Conectadas**, e depois em **Servidores Linux**, como mostrado abaixo.

   ![](media/container-service-monitoring-oms/image5.png)

4. Selecione o número de casos que deseja e clique no botão 'Rever e Instalar'. Normalmente, você vai querer ter o número de casos igual ao número de VM que você tem no seu cluster de agente. O agente Log Analytics para o Linux instala como recipientes individuais em cada VM que pretende recolher informações para monitorização e registo de informações.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Configuração de um simples dashboard Log Analytics

Depois de ter instalado o agente Log Analytics para o Linux nos VMs, o próximo passo é configurar o painel de instrumentos Log Analytics. Pode configurar o painel através do portal Azure.

### <a name="azure-portal"></a>Portal do Azure 

Inscreva-se no portal <https://portal.microsoft.com/>Azure em . Vá ao **Marketplace,** **selecione Monitoring + gestão** e clique **em Ver Tudo**. Em `containers` seguida, digite em busca. Verá "contentores" nos resultados da pesquisa. Selecione **Recipientes** e clique em **Criar**.

![](media/container-service-monitoring-oms/image9.png)

Assim que clicar em **Criar,** irá pedir-lhe o seu espaço de trabalho. Selecione o seu espaço de trabalho ou se não tiver um, crie um novo espaço de trabalho.

![](media/container-service-monitoring-oms/image10.PNG)

Depois de selecionar o seu espaço de trabalho, clique em **Criar**.

![](media/container-service-monitoring-oms/image11.png)

Para mais informações sobre a Solução de Recipientes de Log Analytics, consulte o [Registo de Solução](../../azure-monitor/insights/containers.md)de Contentores Analytics .

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Como escalar o agente Log Analytics com ACS DC/OS 

Caso necessite de ter instalado o agente Log Analytics aquém da contagem real do nó ou se estiver a escalonar a `msoms` escala virtual da máquina, adicionando mais VM, pode fazê-lo escalando o serviço.

Você pode ir à Maratona ou ao separador DC/OS UI Services e aumentar a sua contagem de nós.

![](media/container-service-monitoring-oms/image12.PNG)

Isto irá implantar-se em outros nós que ainda não tenham implantado o agente Log Analytics.

## <a name="uninstall-ms-oms"></a>Desinstalar MS OMS

Para desinstalar mS OMS insira o seguinte comando:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Deixe-nos saber!!!
O que funciona? O que falta? O que mais precisa para que isto seja útil para si? Informe-nos na <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Passos seguintes

 Agora que criou o Log Analytics para monitorizar os seus contentores, consulte o seu painel de instrumentos de[contentores](../../azure-monitor/insights/containers.md).
