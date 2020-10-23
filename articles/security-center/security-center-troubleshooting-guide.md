---
title: Guia de Resolução de Problemas do Centro de Segurança do Azure | Microsoft Docs
description: Este guia destina-se a profissionais de TI, analistas de segurança e administradores de nuvem que precisam de resolver problemas relacionados com o Centro de Segurança Azure.
services: security-center
author: v-miegge
manager: dcscontentpm
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 6646b8a563cfe156a23b47011a769c6df015a286
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340347"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Guia de Resolução de Problemas do Centro de Segurança do Azure

Este guia destina-se a profissionais de tecnologias da informação (TI), analistas de segurança de informações e administradores da cloud cujas organizações utilizam o Centro de Segurança do Azure e que precisam de resolver problemas relacionados com o Centro de Segurança.

O Security Center utiliza o agente Log Analytics para recolher e armazenar dados. Veja [Azure Security Center Platform Migration](./security-center-enable-data-collection.md) (Migração da Plataforma do Centro de Segurança do Azure) para saber mais. A informação neste artigo representa a funcionalidade do Centro de Segurança após a transição para o agente Log Analytics.

## <a name="troubleshooting-guide"></a>Guia de resolução de problemas

Este guia explica como resolver problemas relacionados com o Centro de Segurança.

Tipos de alerta:

* Análise Comportamental de Máquinas Virtuais (VMBA)
* Análise de Rede
* SqL Database e Azure Synapse Analytics (anteriormente SQL Data Warehouse) Análise
* Informações Contextuais

Consoante os tipos de alerta, os clientes podem obter as informações necessárias para investigar o alerta através dos seguintes recursos:

* Registos de segurança no visualizador de eventos da Máquina Virtual (VM) no Windows
* AuditD no Linux
* Os registos de atividades do Azure e ativar registos de diagnóstico no recurso de ataque.

Os clientes podem partilhar feedback na descrição e relevância do alerta. Aceda ao alerta, selecione o botão **Isto foi útil?**, selecione o motivo e introduza um comentário para explicar o feedback. Nós monitorizamos consistentemente este canal de feedback para melhorar os nossos alertas.

## <a name="audit-log"></a>Registo de auditoria

A maior parte da resolução de problemas feita no Centro de Segurança será realizada ao consultar primeiro os [Registos de Auditoria](../azure-monitor/platform/platform-logs-overview.md) quanto ao componente que falhou. Os registos de auditoria permitem-lhe determinar:

* As operações que foram executadas
* Quem iniciou a operação
* Quando ocorreu a operação
* O estado da operação
* Os valores de outras propriedades que possam ajudá-lo a procurar a operação

O registo de auditoria contém todas as operações de escrita (PUT, POST, DELETE) efetuadas nos seus recursos, no entanto, não inclui as operações de leitura (GET).

## <a name="log-analytics-agent"></a>Agente do Log Analytics

O Security Center utiliza o agente Log Analytics – este é o mesmo agente utilizado pelo serviço Azure Monitor – para recolher dados de segurança das suas máquinas virtuais Azure. Assim que a recolha de dados estiver ativa e o agente estiver corretamente instalado no computador de destino, o processo abaixo deve estar em execução:

* HealthService.exe

Se abrir a consola de gestão de serviços (services.msc), também verá o serviço de agente Log Analytics a funcionar como mostrado abaixo:

![Serviços](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Para ver qual a versão do agente que tem, abra o Gestor de **Tarefas,** no separador **Processos,** localize o Serviço de **Agente De Registos,** clique com o botão direito e clique em **Propriedades**. No separador **Detalhes**, procure a versão do ficheiro, conforme mostrado abaixo:

![Ficheiro](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)

## <a name="log-analytics-agent-installation-scenarios"></a>Cenários de instalação do agente Log Analytics

Existem dois cenários de instalação que podem produzir resultados diferentes ao instalar o agente Log Analytics no seu computador. Os cenários suportados são:

* **Agente instalado automaticamente pelo Centro de Segurança**: neste cenário poderá ver os alertas em ambos os locais, Centro de Segurança e pesquisa de registos. Receberá notificações por e-mail para o endereço de e-mail configurado na política de segurança para a subscrição a que pertence o recurso.

* **Agente instalado manualmente num VM localizado em Azure**: neste cenário, se estiver a utilizar agentes descarregados e instalados manualmente antes de fevereiro de 2017, só poderá visualizar os alertas no portal do Centro de Segurança se filtrar a subscrição a que pertence o espaço de trabalho. Se filtrar na subscrição a que pertence o recurso, não verá quaisquer alertas. Receberá notificações por e-mail para o endereço de e-mail configurado na política de segurança para a subscrição a que pertence o espaço de trabalho.

> [!NOTE]
> Para evitar o comportamento explicado no segundo cenário, certifique-se de que transfere a versão mais recente do agente.

## <a name="monitoring-agent-health-issues"></a>Monitorizar problemas de estado de funcionamento do agente <a name="mon-agent"></a>

O **estado da monitorização** define o motivo pelo qual o Centro de Segurança não consegue monitorizar com êxito VMs e computadores inicializados para aprovisionamento automático. A tabela seguinte mostra os valores, as descrições e os passos de resolução do **Estado de monitorização**.

| Estado de monitorização | Descrição | Passos de resolução |
|---|---|---|
| Instalação de agente pendente | A instalação do agente Log Analytics ainda está em funcionamento.  A instalação pode demorar algumas horas. | Aguarde até que a instalação automática esteja concluída. |
| Estado de energia desligado | A VM está parada.  O agente Log Analytics só pode ser instalado num VM que está em funcionamento. | Reinicie a VM. |
| Agente da VM do Azure inválido ou em falta | O agente Log Analytics ainda não está instalado.  Para que o Centro de Segurança instale a extensão, é necessário um agente válido da VM do Azure. | Instale, reinstale ou atualize o agente da VM do Azure na VM. |
| O estado da VM não está pronto para instalação  | O agente Log Analytics ainda não está instalado porque o VM não está pronto para a instalação. A VM não está pronta para instalação devido a um problema do agente da VM ou do aprovisionamento da VM. | Verifique o estado da sua VM. Volte a **Máquinas Virtuais** no portal e selecione a VM para obter informações de estado. |
|A instalação falhou – erro geral | O agente Log Analytics foi instalado, mas falhou devido a um erro. | [Instale a extensão manualmente](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) ou desinstale a extensão, para que o Centro de Segurança tente instalá-la novamente. |
| A instalação falhou - o agente local já está instalado | A instalação do agente Log Analytics falhou. O Centro de Segurança identificou um agente local (Log Analytics ou Gestor de Operações do Centro de Sistema) já instalado no VM. Para evitar uma configuração de vários homing, onde o VM está a reportar a dois espaços de trabalho separados, a instalação do agente Log Analytics parou. | Existem duas formas de resolver isto: [instalar manualmente a extensão](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) e ligá-la à área de trabalho pretendida. Ou então definir a sua área de trabalho pretendida como a área de trabalho predefinida e ativar o aprovisionamento automático do agente.  Veja [ativar o aprovisionamento automático](security-center-enable-data-collection.md). |
| O agente não consegue ligar à área de trabalho | Log Analytics agente instalado mas falhou devido à conectividade da rede.  Verifique se tem acesso à Internet ou se foi configurado um proxy HTTP válido para o agente. | Veja requisitos de rede do agente de monitorização. |
| Agente ligado a área de trabalho em falta ou desconhecida | O Centro de Segurança identificou que o agente Log Analytics instalado no VM está ligado a um espaço de trabalho ao qual não tem acesso. | Isto pode acontecer em dois casos. A área de trabalho foi eliminada e já não existe. Reinstale o agente com a área de trabalho correta ou desinstale o agente e permita que o Centro de Segurança conclua a instalação de aprovisionamento automática. O segundo caso é quando a área de trabalho faz parte de uma subscrição para a qual o Centro de Segurança não tem permissões. O Centro de Segurança requer subscrições para permitir que o Fornecedor de Recursos de Segurança da Microsoft aceda às mesmas. Para ativar, registe a subscrição no Fornecedor de Recursos de Segurança da Microsoft. Isto pode ser feito por API, PowerShell, portal ou filtrando a subscrição no dashboard **Descrição Geral** do Centro de Segurança. Veja [Fornecedores e tipos de recursos](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) para obter mais informações. |
| O agente não responde ou tem o ID em falta | O Centro de Segurança não consegue obter dados de segurança analisados a partir da VM, apesar de o agente estar instalado. | O agente não está a comunicar quaisquer dados, incluindo o heartbeat. O agente pode estar danificado ou algo está a bloquear o tráfego. Ou, o agente está a reportar dados, mas falta-lhe uma identificação de recurso Azure, por isso é impossível comparar os dados com o VM Azure. Para resolver problemas, consulte o [Guia de Resolução de Problemas do Agente de Análise de Registos para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal). Para resolver problemas do Windows, Veja [Resolução de Problemas das Máquinas Virtuais Windows](../virtual-machines/extensions/oms-windows.md#troubleshoot-and-support). |
| Agente não instalado | A recolha de dados está desativada. | Ligue a recolha de dados na política de segurança ou instale manualmente o agente Log Analytics. |

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>Requisitos de resolução de problemas de rede do agente de monitorização <a name="mon-network-req"></a>

Para os agentes se ligarem e registarem no Centro de Segurança, precisam de ter acesso aos recursos de rede, incluindo os números de porta e os URLs de domínio.

* Para servidores proxy, tem de assegurar que os recursos do servidor proxy apropriados estão configurados nas definições do agente. Leia este artigo para obter mais informações sobre [como alterar as definições de proxy](../azure-monitor/platform/agent-windows.md).
* Para as firewalls que restringem o acesso à Internet, tem de configurar a firewall para permitir o acesso ao Log Analytics. Não é necessária nenhuma ação nas definições do agente.

A tabela seguinte mostra os recursos necessários para a comunicação.

| Recursos do Agente | Portas | Inspeção de HTTPS direto |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Yes |
| *.oms.opinsights.azure.com | 443 | Yes |
| *.blob.core.windows.net | 443 | Yes |
| *.azure-automation.net | 443 | Yes |

Se encontrar problemas de integração com o agente, certifique-se de que lê o artigo [How to troubleshoot Operations Management Suite onboarding issues](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) (Como resolver problemas de integração do Operations Management Suite).

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>A resolução de problemas do endpoint protection não está a funcionar corretamente

O agente convidado é o processo principal de tudo o que a extensão do [Microsoft Antimalware](../security/fundamentals/antimalware.md) faz. Quando o processo do agente convidado falha, o Microsoft Antimalware que é executado como um processo subordinado do agente convidado também poderá falhar.  Em cenários como os que são recomendados para verificar as seguintes opções:

* Se a VM de destino for uma imagem personalizada e o criador da VM nunca tiver instalado o agente convidado.
* Se o destino for uma VM do Linux em vez de uma VM do Windows, a instalação da versão do Windows da extensão de antimalware numa VM do Linux irá falhar. O agente convidado do Linux tem requisitos específicos em termos da versão do SO e pacotes necessários, e se esses requisitos não forem cumpridos, o agente da VM também não irá funcionar.
* Se a VM foi criada com uma versão antiga do agente convidado. Caso tenha sido, deve ter em atenção que alguns agentes antigos não conseguirão atualizar de forma automática para a versão mais recente e isto pode levar a este problema. Utilize sempre a versão mais recente do agente convidado se criar as suas próprias imagens.
* Algum software de administração de terceiros pode desativar o agente convidado ou bloquear o acesso a determinadas localizações de ficheiros. Se tiver instalado software de terceiros na VM, certifique-se de que o agente está na lista de exclusão.
* Determinadas definições da firewall ou do Grupo de Segurança de Rede (NSG) podem bloquear o tráfego de rede para e a partir do agente convidado.
* Determinada Lista de Controlo de Acesso (ACL) poderá impedir o acesso ao disco.
* A falta de espaço em disco pode impedir o agente convidado de funcionar corretamente.

A Interface de Utilizador de Antimalware da Microsoft está desativada por predefinição, leia [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment (Ativar a Interface de Utilizador de Antimalware da Microsoft nas VMs do Azure Resource Manager Depois da Implementação)](/archive/blogs/azuresecurity/enabling-microsoft-antimalware-user-interface-post-deployment) para obter mais informações sobre como ativá-lo se for necessário.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Resolução de problemas ao carregar o dashboard

Se ocorrerem problemas ao carregar o dashboard do Centro de Segurança, certifique-se de que o utilizador que regista a subscrição no Centro de Segurança (ou seja, o primeiro utilizador que abrir o Centro de Segurança com a subscrição) e o utilizador que gostaria de ativar a recolha de dados são o *Proprietário* ou o *Contribuinte* na subscrição. A partir desse momento os utilizadores com *Leitor* na subscrição também podem ver o dashboard/alertas/recomendação/política.

## <a name="contacting-microsoft-support"></a>Contactar o Suporte da Microsoft

Alguns problemas podem ser identificados usando as diretrizes fornecidas neste artigo, outros que você também pode encontrar documentado no Centro de Segurança público [Microsoft Q&página A](/answers/topics/azure-security-center.html). No entanto, se precisar de mais resolução de problemas, pode abrir um novo pedido de suporte utilizando **o portal Azure,** como mostra abaixo:

![Suporte da Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Ver também

Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Guia de Planeamento e Operações do Centro de Segurança Azure](security-center-planning-and-operations-guide.md) — Aprenda a planear e a compreender as considerações de design para adotar o Centro de Segurança Azure.
* [Monitorização de saúde de segurança no Azure Security Center](security-center-monitoring.md) — Saiba como monitorizar a saúde dos seus recursos Azure
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança
* [Compreender os alertas de segurança no Centro de Segurança do Azure](./security-center-alerts-overview.md)
* [Tutorial: responder a incidentes de segurança](tutorial-security-incident.md)
* [Alerts Validation in Azure Security Center](security-center-alert-validation.md) (Validação de alertas no Centro de Segurança do Azure)
* [Email Notifications in Azure Security Center](security-center-provide-security-contact-details.md) (Notificações por e-mail no Centro de Segurança do Azure)
* [Lidar com Incidentes de Segurança no Centro de Segurança do Azure](security-center-incident.md)
* [Capacidades de deteção do Centro de Segurança do Azure](./security-center-alerts-overview.md)
* [Monitorizar soluções de parceiros com o Azure Security Center](./security-center-partner-integration.md) — Saiba como monitorizar o estado de saúde das soluções do seu parceiro.
* [Azure Security Center FAQ](faq-general.md) — Encontre perguntas frequentes sobre a utilização do serviço
* [Blogue de Segurança do Azure](/archive/blogs/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure