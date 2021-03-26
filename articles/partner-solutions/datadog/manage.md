---
title: Gerir um recurso Datadog - Soluções parceiras Azure
description: Este artigo descreve a gestão de um recurso Datadog no portal Azure. Como configurar um único senting, eliminar uma organização Confluent e obter apoio.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 04aef540bc134e5ec307be6a232ce47f0923e528
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046356"
---
# <a name="manage-the-datadog-resource"></a>Gerir o recurso Datadog

Este artigo mostra como gerir as definições para a sua integração Azure com o Datadog.

## <a name="resource-overview"></a>Visão geral do recurso

Para ver detalhes do seu recurso Datadog, **selecione Overview** no painel esquerdo.

:::image type="content" source="media/manage/resource-overview.png" alt-text="Visão geral do recurso datadog" border="true" lightbox="media/manage/resource-overview.png":::

Os detalhes incluem:

- Nome do grupo de recursos
- Localização/Região
- Subscrição
- Etiquetas
- Link único de inscrição para a organização Datadog
- Oferta/plano datadog
- Termo de faturação

Também fornece links para dashboards, registos e mapas de anfitriões do Datadog.

O ecrã geral fornece um resumo dos recursos que enviam registos e métricas para o Datadog.

- Tipo de recurso – Tipo de recurso Azure.
- Recursos totais – Contar todos os recursos para o tipo de recurso.
- Recursos de envio de registos – Contagem de recursos enviando registos para Datadog através da integração.
- Recursos enviando métricas – Contagem de recursos enviando métricas para Datadog através da integração.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>Reconfigurar regras para métricas e registos

Para alterar as regras de configuração para métricas e registos, selecione **Métricas e Troncos** no painel esquerdo.

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="Modifique a configuração de registos e métricas para o recurso Datadog." border="true":::

Para obter mais informações, consulte [métricas e registos](create.md#configure-metrics-and-logs)de configuração .

## <a name="view-monitored-resources"></a>Ver recursos monitorizados

Para ver a lista de recursos que emitem registos para o Datadog, selecione **Recursos Monitorizados** no painel esquerdo.

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="Ver recursos monitorizados por Datadog" border="true":::

Pode filtrar a lista de recursos por tipo de recurso, nome do grupo de recursos, localização e se o recurso está a enviar registos e métricas.

Os **registos da** coluna para Datadog indicam se o recurso está a enviar registos para o Datadog. Se o recurso não estiver a enviar registos, este campo indica porque é que os registos não estão a ser enviados para o Datadog. As razões podem ser:

- O recurso não suporta o envio de registos. Apenas tipos de recursos com categorias de registo de monitorização podem ser configurados para enviar registos para Datadog.
- Limite de cinco configurações de diagnóstico alcançadas. Cada recurso Azure pode ter um máximo de cinco configurações de diagnóstico. Para obter mais informações, consulte [as definições de diagnóstico](../../azure-monitor/essentials/diagnostic-settings.md).
- Error. O recurso está configurado para enviar registos para Datadog, mas está bloqueado por um erro.
- Registos não configurados. Apenas os recursos Azure que têm as etiquetas de recursos apropriadas são configurados para enviar registos para Datadog.
- Região não apoiada. O recurso Azure está numa região que não suporta atualmente o envio de registos para o Datadog.
- Agente datadog não configurado. Máquinas virtuais sem o agente Datadog instalado não emitem registos para o Datadog.

## <a name="api-keys"></a>Chaves de API

Para ver a lista de chaves API para o seu recurso Datadog, selecione as **Chaves** no painel esquerdo. Vê informações sobre as chaves.

:::image type="content" source="media/manage/keys.png" alt-text="Chaves da API para a organização Datadog." border="true":::

O portal Azure fornece uma visão apenas de leitura das chaves API. Para gerir as teclas, selecione a ligação do portal Datadog. Depois de escoar as alterações no portal Datadog, refresque a visão do portal Azure.

A integração Azure Datadog proporciona-lhe a capacidade de instalar o agente Datadog numa máquina virtual ou serviço de aplicações. Se uma chave predefinida não for selecionada, a instalação do agente Datadog falha.

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>Monitorizar máquinas virtuais com o agente do Datadog

Pode instalar agentes datadog em máquinas virtuais como uma extensão. Aceda ao **agente de máquinas Virtual** sob as configurações do Datadog org no painel esquerdo. Este ecrã mostra a lista de todas as máquinas virtuais na subscrição.

Para cada máquina virtual, são apresentados os seguintes dados:

- Nome de recurso – Nome da máquina virtual
- Estado do recurso – Se a máquina virtual está parada ou em funcionamento. O agente Datadog só pode ser instalado em máquinas virtuais que estejam em funcionamento. Se a máquina virtual for parada, a instalação do agente Datadog será desativada.
- Versão agente - Número da versão do agente Datadog.
- Estado do agente - Se o agente Datadog está a funcionar na máquina virtual.
- Integrações habilitados – As principais métricas que estão a ser recolhidas pelo agente Datadog.
- Método de Instalação – A ferramenta específica utilizada para instalar o agente Datadog. Por exemplo, Chef ou Script.
- Envio de registos - Se o agente Datadog está a enviar registos para o Datadog.

Selecione a máquina virtual para instalar o agente Datadog ligado. Selecione **o agente de instalação**.

O portal solicita a confirmação de que pretende instalar o agente com a chave predefinida. Selecione **OK** para iniciar a instalação. O Azure mostra o estado de **instalação** até que o agente seja instalado e a provisionado.

Após a instalação do agente Datadog, o estado muda para Instalado.

Para ver se o agente Datadog foi instalado, selecione a máquina virtual e navegue para a janela Extensões.

Pode desinstalar agentes datadog numa máquina virtual indo para **o agente de máquina virtual.** Selecione a máquina virtual e **o agente Desinstalar**.

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>Monitor App Services usando o agente Datadog como uma extensão

Pode instalar agentes datadog nos serviços de aplicações como uma extensão. Aceda à **extensão do Serviço de Aplicações** no painel esquerdo. Este ecrã mostra a lista de todos os serviços de aplicações na subscrição.

Para cada serviço de aplicações, são apresentados os seguintes elementos de dados:

- Nome de recurso – Nome da máquina virtual.
- Estado dos Recursos – Se o serviço de aplicação está parado ou em funcionamento. O agente Datadog só pode ser instalado em serviços de aplicações que estão em execução. Se o serviço de aplicações for interrompido, a instalação do agente Datadog é desativada.
- Plano de serviço de aplicações – O plano específico configurado para o serviço de aplicações.
- Versão agente - Número da versão do agente Datadog.

Para instalar o agente Datadog, selecione o serviço de aplicação e **instale extensão.** O mais recente agente datadog está instalado no serviço de aplicações como uma extensão.

O portal confirma que pretende instalar o agente Datadog. Além disso, as definições de aplicação para o serviço de aplicações específicas são atualizadas com a chave padrão. O serviço de aplicações é reiniciado após a instalação do agente Datadog.

Selecione **OK** para iniciar o processo de instalação do agente Datadog. O portal mostra o estado de **instalação** até que o agente seja instalado. Após a instalação do agente Datadog, o estado muda para Instalado.

Para desinstalar os agentes do Datadog no serviço de aplicações, aceda à **Extensão do Serviço de Aplicações.** Selecione o serviço de aplicações e **desinstale a extensão**

## <a name="reconfigure-single-sign-on"></a>Reconfigurar o único sinal

Se pretender reconfigurar o único sinal de sação, selecione **'Único' no** painel esquerdo.

Para estabelecer um único sinal de inscrição através do diretório Azure Ative, selecione **Ative single sign-on através do Azure Ative Directory**.

O portal recupera a aplicação datadog apropriada do Azure Ative Directory. A aplicação provém do nome da aplicação da empresa que selecionou ao configurar a integração. Selecione o nome da aplicação Datadog como mostrado abaixo:
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="Reconfigure a aplicação de inscrição única." border="true":::
 
## <a name="disable-or-enable-integration"></a>Desativar ou permitir a integração

Pode parar de enviar registos e métricas de Azure para Datadog. Continuará a ser cobrado por outros serviços datadog que não estejam relacionados com métricas e registos de monitorização.

Para desativar a integração do Azure com o Datadog, vá ao **Overview**. **Selecione Disable** e **OK**.
 
:::image type="content" source="media/manage/disable.png" alt-text="Desative o recurso Datadog." border="true":::

Para permitir a integração do Azure com o Datadog, vá ao **Overview**. **Selecione Ative** e **OK**. Selecionar **Ativar** recupera qualquer configuração anterior para métricas e registos. A configuração determina quais os recursos Azure que emitem métricas e registos para Datadog. Após completar o passo, métricas e registos são enviados para Datadog.
 
:::image type="content" source="media/manage/enable.png" alt-text="Ativar o recurso Datadog." border="true":::

## <a name="delete-datadog-resource"></a>Eliminar recurso Datadog

Vá ao **Resumo** no painel esquerdo e selecione **Delete**. Confirme que deseja eliminar o recurso Datadog. Selecione **Eliminar**.

:::image type="content" source="media/manage/delete.png" alt-text="Eliminar recurso Datadog" border="true":::

Se apenas um recurso datadog for mapeado para uma organização datadog, os registos e métricas já não são enviados para o Datadog. Todas as paragens de faturação para Datadog através do Azure Marketplace.

Se mais de um recurso datadog for mapeado para a organização Datadog, a eliminação do recurso Datadog apenas deixa de enviar registos e métricas para esse recurso Datadog. Como a organização Datadog está ligada a outros recursos Azure, a faturação continua através do Azure Marketplace.

## <a name="getting-support"></a>Obter suporte

Para contactar o suporte sobre a integração do Azure Datadog, selecione **Novo Pedido de Suporte** no painel esquerdo. Selecione o link para o portal Datadog.

:::image type="content" source="media/manage/support-request.png" alt-text="Criar um novo pedido de suporte do Datadog" border="true":::

## <a name="next-steps"></a>Passos seguintes

Para obter ajuda na resolução de problemas, consulte [soluções datadog de resolução de problemas.](troubleshoot.md)