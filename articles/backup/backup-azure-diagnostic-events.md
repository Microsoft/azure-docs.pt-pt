---
title: Usando configurações de diagnóstico para cofres dos serviços de recuperação
description: Um artigo que descreve como usar os eventos de diagnóstico novos e antigos para o backup do Azure
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 875adb82aeeb56b378a84ca01e716c7189abc64f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281106"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Utilizar Definições de Diagnóstico para os Cofres dos Serviços de Recuperação

O backup do Azure envia eventos de diagnóstico que podem ser coletados e usados para fins de análise, alertas e relatórios. 

Você pode definir as configurações de diagnóstico para um cofre dos serviços de recuperação por meio do portal do Azure, navegando até o cofre e clicando no item de menu **configurações de diagnóstico** . Clicar em **+ Adicionar configuração de diagnóstico** permite que você envie um ou mais eventos de diagnóstico para uma conta de armazenamento, Hub de eventos ou um espaço de trabalho de log Analytics (la).

![Folha configurações de diagnóstico](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Eventos de diagnóstico disponíveis para usuários de backup do Azure

O backup do Azure fornece os seguintes eventos de diagnóstico, cada um dos quais fornece dados detalhados sobre um conjunto específico de artefatos relacionados ao backup:
* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage 

[Modelo de dados para eventos de diagnóstico de backup do Azure](https://aka.ms/diagnosticsdatamodel)

Os dados desses eventos podem ser enviados para uma conta de armazenamento, LA espaço de trabalho ou um hub de eventos. Se você estiver enviando esses dados para um espaço de trabalho LA, precisará selecionar a opção de alternância **específica do recurso** na tela de **configuração de diagnóstico** (veja mais informações nas seções abaixo).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Usando configurações de diagnóstico com Log Analytics (LA)

Alinhando com o roteiro de Log Analytics do Azure, o backup do Azure agora permite que você envie dados de diagnóstico de cofre para tabelas de LA dedicada para backup. Eles são chamados de [tabelas específicas de recursos](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

Para enviar os dados de diagnóstico do cofre para a LA:
1.  Navegue até seu cofre e clique em **configurações de diagnóstico**. Clique em **+ Adicionar configuração de diagnóstico**.
2.  Dê um nome para a configuração de diagnóstico.
3.  Marque a caixa **Enviar para log Analytics** e selecione um espaço de trabalho log Analytics.
4.  Selecione **recurso específico** na alternância e verifique os seis eventos- **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupProtectedInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**e **AddonAzureBackupStorage**a seguir.
5.  Clique em **Guardar**.

![Modo específico do recurso](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Depois que os dados fluem para o espaço de trabalho LA, as tabelas dedicadas para cada um desses eventos são criadas no seu espaço de trabalho. Você pode consultar qualquer uma dessas tabelas diretamente e também executar junções ou uniões entre essas tabelas, se necessário.

> [!IMPORTANT]
> Os seis eventos, especificamente, CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy e AddonAzureBackupStorage, têm suporte **apenas** no modo específico do recurso. **Observe que, se você tentar enviar dados para esses seis eventos no modo de Diagnóstico do Azure, nenhum dado fluirá para o espaço de trabalho LA.**

## <a name="legacy-event"></a>Evento herdado

Tradicionalmente, todos os dados de diagnóstico relacionados ao backup de um cofre estão contidos em um único evento chamado ' AzureBackupReport '. Os seis eventos descritos acima são, em essência, uma decomposição de todos os dados contidos em AzureBackupReport. 

Atualmente, continuamos a dar suporte ao evento AzureBackupReport para compatibilidade com versões anteriores, nos casos em que os usuários têm consultas personalizadas existentes sobre esse evento, por exemplo, alertas de log personalizados, visualizações personalizadas etc. No entanto, é recomendável escolher os novos eventos para todas as novas configurações de diagnóstico no cofre, já que isso torna os dados muito mais fáceis de trabalhar em consultas de log, fornece melhor capacidade de descoberta de esquemas e sua estrutura, melhora o desempenho em ambas as ingestãos latência e tempos de consulta. O suporte ao uso do modo de Diagnóstico do Azure, eventualmente, será dividido e, portanto, a escolha dos novos eventos pode ajudá-lo a evitar migrações complexas em uma data posterior.

Você pode optar por criar configurações de diagnóstico separadas para AzureBackupReport e os seis novos eventos, até migrar todas as suas consultas personalizadas para usar dados das novas tabelas. A imagem abaixo mostra um exemplo de um cofre com duas configurações de diagnóstico. A primeira configuração, chamada **Setting1** , envia dados do evento AzureBackupReport para um espaço de trabalho la no modo AzureDiagnostics. A segunda configuração, chamada **Setting2** , envia dados dos seis novos eventos de backup do Azure para um espaço de trabalho de la no modo específico do recurso.

![Duas configurações](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> O evento AzureBackupReport tem suporte **apenas** no modo diagnóstico do Azure. **Observe que, se você tentar enviar dados para esse evento no modo específico de recurso, nenhum dado fluirá para o espaço de trabalho LA.**

> [!NOTE]
> A alternância para Diagnóstico do Azure/recurso específico será exibida somente se o usuário marcar **Enviar para log Analytics**. Para enviar dados para uma conta de armazenamento ou um hub de eventos, um usuário pode simplesmente selecionar o destino necessário e verificar qualquer um dos eventos desejados, sem nenhuma entrada adicional. Novamente, é recomendável não escolher o evento herdado AzureBackupReport, no futuro.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Usuários enviando Azure Site Recovery eventos para Log Analytics (LA)

Os eventos de backup e Azure Site Recovery do Azure são enviados do mesmo cofre dos serviços de recuperação. Como Azure Site Recovery atualmente não está integrado em tabelas específicas de recursos, os usuários que desejam enviar Azure Site Recovery eventos para LA são direcionados para usar **somente** o modo de diagnóstico do Azure (consulte a imagem abaixo). **Escolher o modo específico de recurso para os eventos de Azure site Recovery impedirá que os dados necessários sejam enviados ao espaço de trabalho la**.

![Eventos de Site Recovery](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Para resumir as nuances acima:

* Se você já tiver o diagnóstico de LA configurado com Diagnóstico do Azure e tiver escrito consultas personalizadas sobre ele, mantenha essa configuração **intacta**até migrar suas consultas para usar dados dos novos eventos.
* Se você também quiser integrar novas tabelas (conforme recomendado), crie uma **nova** configuração de diagnóstico, escolha **recurso específico** e selecione os seis novos eventos, conforme especificado acima.
* Se você estiver atualmente enviando Azure Site Recovery eventos para LA, **não escolha o** modo específico do recurso para esses eventos, caso contrário, os dados desses eventos não fluirão para o espaço de trabalho da la. Em vez disso, crie uma **configuração de diagnóstico adicional**, selecione **diagnóstico do Azure**e escolha os eventos de Azure site Recovery relevantes.

A imagem abaixo mostra um exemplo de um usuário que tem três configurações de diagnóstico para um cofre. A primeira configuração, chamada **Setting1** , envia dados do evento AzureBackupReport para um espaço de trabalho la no modo AzureDiagnostics. A segunda configuração, chamada **Setting2** , envia dados dos seis novos eventos de backup do Azure para um espaço de trabalho de la no modo específico do recurso. A terceira configuração, chamada **Setting3**, envia dados dos eventos Azure site Recovery para um espaço de trabalho la no modo diagnóstico do Azure.

![Três configurações](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Passos Seguintes

[Aprenda o modelo de dados de Log Analytics para os eventos de diagnóstico](https://aka.ms/diagnosticsdatamodel)
