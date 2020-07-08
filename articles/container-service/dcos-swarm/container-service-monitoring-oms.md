---
title: (PRECADO) Monitor Azure DC/OS cluster - Gestão de Operações
description: Monitorize um cluster de serviço de contentores Azure DC/OS com Log Analytics.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76277327"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(PRECADO) Monitorize um cluster de serviço de contentores Azure DC/OS com Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics é a solução de gestão de TI baseada na nuvem da Microsoft que o ajuda a gerir e proteger as suas infraestruturas de nuvem e instalações.A Solução de Contentores é uma solução no Log Analytics, que o ajuda a visualizar o inventário, desempenho e registos do contentor num único local. Você pode auditar, resolver os recipientes, visualizando os registos em localização centralizada, e encontrar um recipiente em excesso de consumo ruidoso num hospedeiro.

![](media/container-service-monitoring-oms/image1.png)

Para obter mais informações sobre a Solução de Contentores, consulte o [Registo de Solução de Contentores Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Criação de Log Analytics do universo DC/OS


Este artigo pressupõe que criou um DC/OS e implementou aplicações simples de recipientes web no cluster.

### <a name="pre-requisite"></a>Pré-requisito
- [Subscrição do Microsoft Azure](https://azure.microsoft.com/free/) - Pode obter uma subscrição gratuitamente.  
- Configuração do espaço de trabalho do Log Analytics - ver "Passo 3" abaixo
- [DC/OS CLI](https://docs.mesosphere.com/1.12/cli) instalado.

1. No painel DC/OS, clique no Universo e procure por 'OMS' como mostrado abaixo.

   >[!NOTE]
   >OMS é agora referido como Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Clique **em Instalar**. Verá um pop-up com as informações da versão e um **pacote de instalação** ou um botão **de instalação avançada.** Quando clicar em **Instalação Avançada,** o que o leva à página **de propriedades de configuração específicas da OMS.**

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Aqui, será solicitado que entre no `wsid` (ID do espaço de trabalho Log Analytics) e `wskey` (a chave primária para o ID do espaço de trabalho). Para obter os dois `wsid` e precisa criar uma conta em `wskey` <https://mms.microsoft.com> .
   Siga os passos para criar uma conta. Uma vez terminada a criação da conta, tem de obter a sua `wsid` e `wskey` clicando em **Definições**, em seguida, **Connected Sources**, e, em seguida, **Linux Servers**, como mostrado abaixo.

   ![](media/container-service-monitoring-oms/image5.png)

4. Selecione o número de casos que deseja e clique no botão 'Rever e instalar'. Normalmente, você vai querer ter o número de casos igual ao número de VM's que você tem no seu cluster de agentes. Log Analytics agent for Linux instala como recipientes individuais em cada VM que pretende recolher informações para monitorização e registo de informações.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Configuração de um simples dashboard Log Analytics

Uma vez instalado o agente Log Analytics para o Linux nos VMs, o próximo passo é configurar o painel De Registo Analytics. Pode configurar o painel através do portal Azure.

### <a name="azure-portal"></a>Portal do Azure 

Inscreva-se no portal Azure em <https://portal.microsoft.com/> . Vá ao **Marketplace,** **selecione Monitoring + management** e clique em Ver **Tudo.** Em seguida, `containers` digite em busca. Verá "contentores" nos resultados da pesquisa. Selecione **Recipientes** e clique em **Criar**.

![](media/container-service-monitoring-oms/image9.png)

Assim que clicar em **Criar,** irá pedir-lhe o seu espaço de trabalho. Selecione o seu espaço de trabalho ou se não tiver um, crie um novo espaço de trabalho.

![](media/container-service-monitoring-oms/image10.PNG)

Uma vez selecionado o seu espaço de trabalho, clique em **Criar**.

![](media/container-service-monitoring-oms/image11.png)

Para obter mais informações sobre a Solução de Contentores Log Analytics, consulte o Registo de [Solução de Contentores Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Como escalar o agente Log Analytics com ACS DC/OS 

Caso precise de ter instalado o agente Log Analytics antes da contagem real de nós ou está a escalonar a escala de máquina virtual definida adicionando mais VM, pode fazê-lo escalando o `msoms` serviço.

Você pode ir à Maratona ou ao separador Dc/OS UI Services e aumentar a sua contagem de nós.

![](media/container-service-monitoring-oms/image12.PNG)

Isto irá implantar-se em outros nós que ainda não tenham implantado o agente Log Analytics.

## <a name="uninstall-ms-oms"></a>Desinstalar MS OMS

Para desinstalar o MS OMS introduza o seguinte comando:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Avise-nos!!!
O que funciona? O que falta? O que mais precisa para que isto seja útil para si? Informe-nos na <a href="mailto:OMSContainers@microsoft.com">OMSContainers.</a>

## <a name="next-steps"></a>Próximos passos

 Agora que criou o Log Analytics para monitorizar os seus contentores,[consulte o painel de instrumentos do seu contentor.](../../azure-monitor/insights/containers.md)
