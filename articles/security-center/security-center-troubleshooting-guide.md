---
title: Guia de Resolução de Problemas do Centro de Segurança do Azure | Microsoft Docs
description: Este documento ajuda a resolver problemas no Centro de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2018
ms.author: rkarlin
ms.openlocfilehash: 658f38e32c2680e7c538147154a004359e431027
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246751"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Guia de Resolução de Problemas do Centro de Segurança do Azure
Este guia destina-se a profissionais de tecnologias da informação (TI), analistas de segurança de informações e administradores da cloud cujas organizações utilizam o Centro de Segurança do Azure e que precisam de resolver problemas relacionados com o Centro de Segurança.

>[!NOTE]
>A partir do início de junho de 2017, o Centro de Segurança utiliza o Microsoft Monitoring Agent para recolher e armazenar dados. Veja [Azure Security Center Platform Migration](security-center-platform-migration.md) (Migração da Plataforma do Centro de Segurança do Azure) para saber mais. As informações neste artigo representam a funcionalidade do Centro de Segurança após a transição para o Agente de Monitorização da Microsoft.
>

## <a name="troubleshooting-guide"></a>Guia de resolução de problemas
Este guia explica como resolver problemas relacionados com o Centro de Segurança. A maior parte da resolução de problemas feita no Centro de Segurança será realizada ao consultar primeiro os [Registos de Auditoria](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) quanto ao componente que falhou. Os registos de auditoria permitem-lhe determinar:

* As operações que foram executadas
* Quem iniciou a operação
* Quando ocorreu a operação
* O estado da operação
* Os valores de outras propriedades que possam ajudá-lo a procurar a operação

O registo de auditoria contém todas as operações de escrita (PUT, POST, DELETE) efetuadas nos seus recursos, no entanto, não inclui as operações de leitura (GET).

## <a name="microsoft-monitoring-agent"></a>Agente de Monitorização da Microsoft
Centro de segurança utiliza o Microsoft Monitoring Agent – este é o mesmo agente utilizado pelo serviço do Azure Monitor – para recolher dados de segurança de máquinas virtuais do Azure. Assim que a recolha de dados estiver ativa e o agente estiver corretamente instalado no computador de destino, o processo abaixo deve estar em execução:

* HealthService.exe

Se abrir a consola de gestão de serviços (services.msc), também verá o serviço Agente de Monitorização da Microsoft em execução, conforme mostrado abaixo:

![Serviços](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Para ver a versão do agente que tem, abra o **Gestor de Tarefas**, no separador **Processos**, localize o serviço **Agente de Monitorização da Microsoft**, clique no mesmo com o botão direito do rato e clique em **Propriedades**. No separador **Detalhes**, procure a versão do ficheiro, conforme mostrado abaixo:

![Ficheiro](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)


## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Cenários de instalação do Agente de Monitorização da Microsoft
Existem dois cenários de instalação que podem produzir resultados diferentes ao instalar o Agente de Monitorização da Microsoft no seu computador. Os cenários suportados são:

* **Agente instalado automaticamente pelo Centro de Segurança**: neste cenário poderá ver os alertas em ambos os locais, Centro de Segurança e pesquisa de registos. Receberá notificações por e-mail no endereço de e-mail configurado na política de segurança para a subscrição a que pertence o recurso.
.
* **Agente instalado manualmente numa VM localizada no Azure**: neste cenário, se estiver a utilizar agentes transferidos e instalados manualmente antes de fevereiro de 2017, poderá ver os alertas no portal do Centro de Segurança apenas se filtrar pela subscrição a que pertence a área de trabalho. No caso de filtrar pela subscrição a que pertence o recurso, não poderá ver os alertas. Receberá notificações por e-mail no endereço de e-mail configurado na política de segurança para a subscrição a que pertence a área de trabalho.

>[!NOTE]
> Para evitar o comportamento explicado no segundo cenário, certifique-se de que transfere a versão mais recente do agente.
>

## Monitorizar problemas de estado de funcionamento do agente <a name="mon-agent"></a>
O **estado da monitorização** define o motivo pelo qual o Centro de Segurança não consegue monitorizar com êxito VMs e computadores inicializados para aprovisionamento automático. A tabela seguinte mostra os valores, as descrições e os passos de resolução do **Estado de monitorização**.

| Estado de monitorização | Descrição | Passos de resolução |
|---|---|---|
| Instalação de agente pendente | A instalação do Microsoft Monitoring Agent ainda está em execução.  A instalação pode demorar algumas horas. | Aguarde até que a instalação automática esteja concluída. |
| Estado de energia desligado | A VM está parada.  O Microsoft Monitoring Agent só pode ser instalado numa VM que esteja em execução. | Reinicie a VM. |
| Agente da VM do Azure inválido ou em falta | O Microsoft Monitoring Agent ainda não está instalado.  Para que o Centro de Segurança instale a extensão, é necessário um agente válido da VM do Azure. | Instale, reinstale ou atualize o agente da VM do Azure na VM. |
| O estado da VM não está pronto para instalação  | O Microsoft Monitoring Agent ainda não está instalado porque a VM não está pronta para instalação. A VM não está pronta para instalação devido a um problema do agente da VM ou do aprovisionamento da VM. | Verifique o estado da sua VM. Volte a **Máquinas Virtuais** no portal e selecione a VM para obter informações de estado. |
|A instalação falhou – erro geral | O Microsoft Monitoring Agent foi instalado mas falhou devido a um erro. | [Instale a extensão manualmente](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) ou desinstale a extensão, para que o Centro de Segurança tente instalá-la novamente. |
| A instalação falhou - o agente local já está instalado | A instalação do Microsoft Monitoring Agent falhou. Centro de segurança identificou um agente local (Log Analytics ou SCOM) já instalado na VM. Para evitar a configuração multi-homing, em que a VM está a comunicar com duas áreas de trabalho separadas, a instalação do Microsoft Monitoring Agent parou. | Existem duas formas de resolver isto: [instalar manualmente a extensão](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) e ligá-la à área de trabalho pretendida. Ou então definir a sua área de trabalho pretendida como a área de trabalho predefinida e ativar o aprovisionamento automático do agente.  Veja [ativar o aprovisionamento automático](security-center-enable-data-collection.md). |
| O agente não consegue ligar à área de trabalho | O Microsoft Monitoring Agent foi instalado, mas falhou devido a conectividade de rede.  Verifique se tem acesso à Internet ou se foi configurado um proxy HTTP válido para o agente. | Consulte os requisitos de rede do agente de monitorização. |
| Agente ligado a área de trabalho em falta ou desconhecida | O Centro de Segurança detetou que o Microsoft Monitoring Agent instalado na VM está ligado a uma área de trabalho à qual não tem acesso. | Isto pode acontecer em dois casos. A área de trabalho foi eliminada e já não existe. Reinstale o agente com a área de trabalho correta ou desinstale o agente e permita que o Centro de Segurança conclua a instalação de aprovisionamento automática. O segundo caso é quando a área de trabalho faz parte de uma subscrição para a qual o Centro de Segurança não tem permissões. O Centro de Segurança requer subscrições para permitir que o Fornecedor de Recursos de Segurança da Microsoft aceda às mesmas. Para ativar, registe a subscrição no Fornecedor de Recursos de Segurança da Microsoft. Isto pode ser feito por API, PowerShell, portal ou filtrando a subscrição no dashboard **Descrição Geral** do Centro de Segurança. Veja [Fornecedores e tipos de recursos](../azure-resource-manager/resource-manager-supported-services.md#azure-portal) para obter mais informações. |
| O agente não responde ou tem o ID em falta | O Centro de Segurança não consegue obter dados de segurança analisados a partir da VM, apesar de o agente estar instalado. | O agente não está a comunicar quaisquer dados, incluindo o heartbeat. O agente pode estar danificado ou algo está a bloquear o tráfego. Ou então o agente está a comunicar dados mas falta um ID de recurso do Azure, pelo que é impossível corresponder os dados à VM do Azure. Para resolver problemas relacionados com o Linux, veja [guia de resolução de problemas de mensagens em fila para o agente do Log Analytics para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal). Para resolver problemas do Windows, Veja [Resolução de Problemas das Máquinas Virtuais Windows](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines). |
| Agente não instalado | A recolha de dados está desativada. | Ative a recolha de dados na política de segurança ou instale manualmente o Microsoft Monitoring Agent. |


## Requisitos de resolução de problemas de rede do agente de monitorização <a name="mon-network-req"></a>
Para os agentes se ligarem e registarem no Centro de Segurança, precisam de ter acesso aos recursos de rede, incluindo os números de porta e os URLs de domínio.

- Para servidores proxy, tem de assegurar que os recursos do servidor proxy apropriados estão configurados nas definições do agente. Leia este artigo para obter mais informações sobre [como alterar as definições de proxy](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents).
- Para as firewalls que restringem o acesso à Internet, tem de configurar a firewall para permitir o acesso ao Log Analytics. Não é necessária nenhuma ação nas definições do agente.

A tabela seguinte mostra os recursos necessários para a comunicação.

| Recursos do Agente | Portas | Inspeção de HTTPS direto |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Sim |
| *.oms.opinsights.azure.com | 443 | Sim |
| *.blob.core.windows.net | 443 | Sim |
| *.azure-automation.net | 443 | Sim |

Se encontrar problemas de integração com o agente, certifique-se de que lê o artigo [How to troubleshoot Operations Management Suite onboarding issues](https://support.microsoft.com/en-us/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) (Como resolver problemas de integração do Operations Management Suite).


## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>A resolução de problemas do endpoint protection não está a funcionar corretamente

O agente convidado é o processo principal de tudo o que a extensão do [Microsoft Antimalware](../security/azure-security-antimalware.md) faz. Quando o processo do agente convidado falha, o Microsoft Antimalware que é executado como um processo subordinado do agente convidado também poderá falhar.  Em cenários como os que são recomendados para verificar as seguintes opções:

- Se a VM de destino for uma imagem personalizada e o criador da VM nunca tiver instalado o agente convidado.
- Se o destino for uma VM do Linux em vez de uma VM do Windows, a instalação da versão do Windows da extensão de antimalware numa VM do Linux irá falhar. O agente convidado do Linux tem requisitos específicos em termos da versão do SO e pacotes necessários, e se esses requisitos não forem cumpridos, o agente da VM também não irá funcionar.
- Se a VM foi criada com uma versão antiga do agente convidado. Caso tenha sido, deve ter em atenção que alguns agentes antigos não conseguirão atualizar de forma automática para a versão mais recente e isto pode levar a este problema. Utilize sempre a versão mais recente do agente convidado se criar as suas próprias imagens.
- Algum software de administração de terceiros pode desativar o agente convidado ou bloquear o acesso a determinadas localizações de ficheiros. Se tiver instalado software de terceiros na VM, certifique-se de que o agente está na lista de exclusão.
- Determinadas definições da firewall ou do Grupo de Segurança de Rede (NSG) podem bloquear o tráfego de rede para e a partir do agente convidado.
- Determinada Lista de Controlo de Acesso (ACL) poderá impedir o acesso ao disco.
- A falta de espaço em disco pode impedir o agente convidado de funcionar corretamente.

A Interface de Utilizador de Antimalware da Microsoft está desativada por predefinição, leia [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment (Ativar a Interface de Utilizador de Antimalware da Microsoft nas VMs do Azure Resource Manager Depois da Implementação)](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) para obter mais informações sobre como ativá-lo se for necessário.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Resolução de problemas ao carregar o dashboard

Se ocorrerem problemas ao carregar o dashboard do Centro de Segurança, certifique-se de que o utilizador que regista a subscrição no Centro de Segurança (ou seja, o primeiro utilizador que abrir o Centro de Segurança com a subscrição) e o utilizador que gostaria de ativar a recolha de dados são o *Proprietário* ou o *Contribuinte* na subscrição. A partir desse momento os utilizadores com *Leitor* na subscrição também podem ver o dashboard/alertas/recomendação/política.

## <a name="contacting-microsoft-support"></a>Contactar o Suporte da Microsoft
Alguns problemas podem ser identificados através das diretrizes fornecidas neste artigo, ao passo que outros estão também documentados no [Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter) público do Centro de Segurança. Contudo, se tiver de resolver mais problemas, pode abrir um novo pedido de suporte através do **portal do Azure**, conforme mostrado abaixo:

![Suporte da Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Consulte também
Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md) – Saiba como planear e compreender as considerações de conceção para adoção do Centro de Segurança do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure
