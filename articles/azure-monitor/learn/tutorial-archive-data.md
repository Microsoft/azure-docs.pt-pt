---
title: Arquivar os dados de métricas e de registos do Azure com o Armazenamento do Azure
description: Registo de arquivo e dados métricos gerados pelos recursos do Azure para uma conta de armazenamento.
author: johnkemnetz
services: azure-monitor
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.subservice: metrics
ms.openlocfilehash: 3ed00b1c68c41bc392b09c97dd47c9cdb8fa890d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77661730"
---
# <a name="archive-azure-metric-and-log-data-using-azure-storage"></a>Arquivar os dados de métricas e de registos do Azure com o Armazenamento do Azure

Várias camadas do seu ambiente do Azure produzem dados de registos e de métricas que podem ser arquivados numa conta de Armazenamento do Azure. Poderá arquivá-los para preservar o histórico de dados de monitorização ao longo do tempo num arquivo não pesquisável e económico depois de o período de retenção.

- As métricas da plataforma do Azure Monitor são mantidas durante 93 dias.
- Os registos de diagnóstico de recursos só são apresentados se forem encaminhados para o Log Analytics, onde têm um período de retenção configurável com um mínimo de 30 dias.
- As entradas de registo de atividades são mantidas durante 90 dias.  

Este tutorial mostra o processo de configuração do seu ambiente do Azure para arquivar dados numa conta de armazenamento.

> [!div class="checklist"]
> * Criar uma conta de armazenamento para armazenar os dados de monitorização
> * Encaminha registos da subscrição para a mesma
> * Encaminhar dados de recursos para a mesma
> * Encaminhar dados de máquinas virtuais (SO convidado) para a mesma
> * Ver os dados de monitorização na mesma
> * Apague os seus recursos

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Criar uma conta do Storage

Primeiro, tem de configurar uma conta de armazenamento na qual os dados de monitorização serão arquivados. Para tal, [siga os passos aqui](../../storage/common/storage-account-create.md).

## <a name="route-subscription-logs-to-the-storage-account"></a>Encaminhar registos da subscrição para a conta de armazenamento

Agora, está pronto para começar a configurar o ambiente do Azure para encaminhar os dados de monitorização para uma conta de armazenamento. Primeiro, vamos configurar os dados ao nível da subscrição (contidos no Registo de Atividades do Azure) para serem encaminhados para a conta de armazenamento. O [**Registo de Atividades do Azure**](../../azure-monitor/platform/platform-logs-overview.md) disponibiliza um histórico de eventos ao nível da subscrição no Azure. Pode navegar no mesmo no portal do Azure para determinar *quem* criou, atualizou ou eliminou *que* recursos e *quando*.

1. Clique no botão **Monitorizar**, na lista de navegação esquerda, e em **Registo de Atividades**.

   ![Secção Registo de Atividades](media/tutorial-archive-data/activity-log-home.png)

2. Na secção Registo de Atividades que é apresentada, clique no botão **Exportar**.

3. Na secção **Exportar registo de atividades** que aparece, selecione a caixa para **Exportar para uma conta de armazenamento** e clique em **Selecionar uma conta de armazenamento.**

   ![Exportar o Registo de Atividades](media/tutorial-archive-data/activity-log-export.png)

4. Na secção que aparece, utilize o menu pendente **Conta de armazenamento** para selecionar o nome da conta de armazenamento que criou no passo anterior, **Criar uma conta de armazenamento**, e clique em **OK**.

   ![Escolher uma conta de armazenamento](media/tutorial-archive-data/activity-log-storage.png)

5. Defina o controlo de deslize **Retenção (dias)** para 30. Este controlo de deslize define um número de dias durante os quais os dados de monitorização vão ser retidos na conta de armazenamento. O Azure Monitor elimina automaticamente os dados mais antigos em comparação com o número de dias especificado. A retenção de zero dias armazena os dados indefinidamente.

6. Clique em **Guardar** e feche esta secção.

Os dados de monitorização da sua subscrição estão agora a ser encaminhados para a conta de armazenamento.

## <a name="route-resource-data-to-the-storage-account"></a>Encaminhar os dados de recursos para a conta de armazenamento

Agora configuramos dados ao nível de recursos (métricas de recursos e registos de recursos) para serem encaminhados para a conta de armazenamento, configurando **as definições**de diagnóstico de recursos .

1. Clique no botão **Monitorizar**, na lista de navegação esquerda, e em **Definições de Diagnóstico**. Aqui, pode ver uma lista de todos os recursos na sua subscrição que produzem dados de monitorização através do Azure Monitor. Se a lista não mostrar nenhum recurso, pode [criar uma aplicação lógica](../../logic-apps/quickstart-create-first-logic-app-workflow.md) antes de avançar, para que tenha um recurso no qual possa configurar uma definição de diagnósticos.

2. Clique num recurso na lista e clique em **Ativar os diagnósticos**.

   ![Ativar os diagnósticos](media/tutorial-archive-data/diagnostic-settings-turn-on.png)

   Contudo, se já existir uma definição configurada, verá as definições existentes e um botão para **Adicionar a definição de diagnóstico**. Clique neste botão.

   As definições de diagnóstico de recursos são definições *dos* dados de monitorização que devem ser encaminhados a partir de um determinado recurso e para *onde* é que devem ser encaminhados.

3. Na secção que aparece, dê um **nome** à definição e selecione a caixa para **Arquivar numa conta de armazenamento**.

   ![Secção Definições de diagnóstico](media/tutorial-archive-data/diagnostic-settings-home.png)

4. Clique no botão **Configurar**, em **Arquivo numa conta de armazenamento** e selecione a conta de armazenamento que criou na secção anterior. Clique em **OK**.

   ![Conta de armazenamento com definições de diagnóstico](media/tutorial-archive-data/diagnostic-settings-storage.png)

5. Selecione todas as caixas em **Registo** e **Métrica**. Dependendo do tipo de recurso, poderá ter apenas uma destas opções. Estas caixas de verificação controlam quais as categorias de dados de registos e de métricas que estão disponíveis para esse tipo de recurso que são enviadas para o destino que selecionou, neste caso, uma conta de armazenamento.

   ![Categorias das definições de diagnóstico](media/tutorial-archive-data/diagnostic-settings-categories.png)

6. Defina o controlo de deslize **Retenção (dias)** para 30. Este controlo de deslize define um número de dias durante os quais os dados de monitorização vão ser retidos na conta de armazenamento. O Azure Monitor elimina automaticamente os dados mais antigos em comparação com o número de dias especificado. A retenção de zero dias armazena os dados indefinidamente.

7. Clique em **Guardar**.

Os dados de monitorização do seu recurso estão agora a ser encaminhados para a conta de armazenamento.

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: a métrica “Mensagens Recebidas” num hub do Hub de Eventos pode ser explorada e representada ao nível da linha. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
>

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>Encaminhar dados de máquinas virtuais (SO convidado) para a conta de armazenamento

1. Se ainda não tiver uma máquina virtual na sua subscrição, [crie uma](../../virtual-machines/windows/quick-create-portal.md).

2. Na lista de navegação esquerda no portal, clique em **Máquinas Virtuais**.

3. Na lista de máquinas virtuais que é apresentada, clique na máquina virtual que criou.

4. Na secção que aparece, clique em **Definições de Diagnóstico**, no painel de navegação esquerda. Esta secção permite-lhe configurar a extensão de monitorização pronta a utilizar do Azure Monitor na sua máquina virtual e encaminhar os dados que estão a ser produzidos pelo Windows ou Linux para uma conta de armazenamento.

   ![Navegar para as definições de diagnóstico](media/tutorial-archive-data/guest-navigation.png)

5. Clique em **Ativar a monitorização ao nível do convidado** na secção que aparece.

   ![Ativar as definições de diagnóstico](media/tutorial-archive-data/guest-enable.png)

6. Assim que a definição de diagnóstico for guardada corretamente, o separador **Descrição Geral** mostra uma lista dos dados que estão a ser recolhidos e onde estão a ser armazenados. Clique na secção **Contadores de desempenho** para rever o conjunto de contadores do Windows que estão a ser recolhidos.

   ![Definições de contadores de desempenho](media/tutorial-archive-data/guest-perf-counters.png)

7. Clique no separador **Registos** e selecione as caixas de verificação para os registos ao nível de **Informações** sobre os registos de Aplicações e do Sistema.

   ![Definições de registos](media/tutorial-archive-data/guest-logs.png)

8. Clique no separador **Agente** e, em **Conta de armazenamento**, clique no nome da conta de armazenamento mostrada.

   ![Atualizar a conta de armazenamento](media/tutorial-archive-data/guest-storage-home.png)

9. Na secção que aparece, escolha a conta de armazenamento que criou no passo **Criar uma conta de armazenamento** anterior.

10. Clique em **Guardar**.

Os dados de monitorização das suas máquinas virtuais estão agora a ser encaminhados para a conta de armazenamento.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>Ver os dados de monitorização na conta de armazenamento

> [!WARNING]
> O formato dos dados de registo na conta de armazenamento vai ser alterado para Linhas de JSON a 1 de novembro de 2018. [Leia este artigo para obter uma descrição do impacto e saber como atualizar a sua ferramenta para trabalhar com o novo formato.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md)
>
>

Se tiver seguido os passos anteriores, os dados começaram a fluir para a sua conta de armazenamento.

1. Para alguns tipos de dados, como, por exemplo, do Registo de Atividades, tem de haver alguma atividade que gere um evento na conta de armazenamento. Para gerar atividade no Registo de Atividades, siga [estas instruções](./../../azure-monitor/platform/quick-audit-notify-action-subscription.md). Poderá ter de aguardar até cinco minutos para que o evento apareça na conta de armazenamento.

2. No portal, navegue para a secção **Contas de Armazenamento**, que estão disponíveis na barra de navegação esquerda.

3. Identifique a conta de armazenamento que criou na secção anterior e clique na mesma.

4. Clique em **Blobs**, clique no contentor com o nome **insights-operational-logs** e, por fim, no contentor com o nome **name=default**. Este é o contentor no qual o seu Registo de Atividades está contido. Os dados de monitorização são divididos em contentores por ID de recurso (apenas o ID de subscrição do Registo de Atividades) e, depois, pela data e a hora. O formato completo destes blobs é:

   insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json

5. Clique nos contentores de ID de recurso, data e hora para navegar para o ficheiro PT1H.json. Clique no ficheiro PT1H.json e clique em **Transferir**. Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro da hora especificada no URL do blob (por exemplo, h=12). Durante a hora presente, os eventos são acrescentados ao ficheiro PT1H.json à medida que ocorrem. O valor de minuto (m=00) é sempre 00, uma vez que o registo de eventos é divididos em blobs individuais por hora.

   Pode ver agora o evento JSON que foi armazenado na conta de armazenamento. Para os registos de recursos, o formato para as bolhas é:

   insights-logs-{log category name}/resourceId=/{resource ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json

6. Os dados de monitorização do SO convidado são armazenados em tabelas. navegue de volta para a conta de armazenamento inicial e clique em **Tabelas**. Existem tabelas para métricas, contadores de desempenho e registos de eventos.

Pode agora definir com êxito o arquivamento dos dados de monitorização numa conta de armazenamento.

## <a name="clean-up-resources"></a>Limpar recursos

1. Navegue de volta para a secção **Exportar Registo de Atividades** do passo **Encaminhar registos de subscrição para a conta de armazenamento** anterior e clique em **Repor**.

2. Navegue para a secção **Definições de Diagnóstico**, clique no recurso no qual criou uma definição de diagnóstico no passo **Encaminhar os dados de recursos para a conta de armazenamento** anterior, localize a definição criou, clique no botão **Editar definição** e clique em **Eliminar**.

3. Navegue para a secção **Definições de diagnóstico** na máquina virtual que configurou no passo **Encaminhar os dados da máquina virtual (SO convidado) para a conta de armazenamento** anterior e, no separador ** Agente**, clique em **Remover** (abaixo da secção **Remover agente de Diagnóstico do Azure**).

4. Navegue para a conta de armazenamento que criou no passo **Criar uma conta de armazenamento** anterior e clique em **Eliminar conta de armazenamento**. Introduza o nome da conta e clique em **Eliminar**.

5. Se tiver criado uma máquina virtual ou uma aplicação lógica nos passos anteriores, elimine-as também.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a configurar os dados de monitorização do seu ambiente do Azure (subscrição, recursos e SO convidado) para serem arquivados numa conta de armazenamento.


> [!div class="checklist"]
> * Criar uma conta de armazenamento para armazenar os dados de monitorização
> * Encaminha registos da subscrição para a mesma
> * Encaminhar dados de recursos para a mesma
> * Encaminhar dados de máquinas virtuais (SO convidado) para a mesma
> * Ver os dados de monitorização na mesma
> * Apague os seus recursos

Para tirar mais proveito dos seus dados e obter informações adicionais, envie-os também para o Log Analytics.

> [!div class="nextstepaction"]
> [Começar com Log Analytics](../../azure-monitor/log-query/log-query-overview.md)
