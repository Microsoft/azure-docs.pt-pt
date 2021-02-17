---
title: Resolução de problemas Azure Arc ativou problemas de extensão VM dos servidores
description: Este artigo diz como resolver problemas e resolver problemas com extensões Azure VM que surgem com servidores ativados pelo Azure Arc.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 799e5c8ec00a894c6a54c64494edd8f259faf2dc
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584673"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>Problemas arc ativado servidores problemas de extensão VM

Este artigo fornece informações sobre resolução de problemas e resolução de problemas que podem ocorrer enquanto tenta implementar ou remover extensões VM do Azure nos servidores ativados pelo Arc. Para obter informações [gerais, consulte Gerir e utilizar extensões Azure VM](./manage-vm-extensions.md).

## <a name="general-troubleshooting"></a>Resolução geral de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure.

As seguintes etapas de resolução de problemas aplicam-se a todas as extensões de VM.

1. Para verificar o registo do agente Convidado, consulte a atividade quando a sua extensão estava a ser disponibilizada `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` para o Windows e para o Linux sob `/var/lib/GuestConfig/ext_mgr_logs` .

2. Consulte os registos de extensão para obter mais detalhes para o `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` Windows. A saída de extensão é registada num ficheiro para cada extensão instalada no Linux sob `/var/lib/GuestConfig/extension_logs` .

3. Verifique se secções de resolução de problemas de documentação específica de extensão para códigos de erro, problemas conhecidos, etc. Informações adicionais de resolução de problemas para cada extensão podem ser encontradas na secção **de resolução de problemas e suporte** na visão geral para a extensão. Isto inclui a descrição dos códigos de erro escritos no registo. Os artigos de extensão estão ligados na [tabela de extensões.](manage-vm-extensions.md#extensions)

4. Olhe para os registos do sistema. Verifique outras operações que possam ter interferido com a extensão, como uma instalação de longa duração de outra aplicação que exigia acesso exclusivo ao gestor de pacotes.

## <a name="troubleshooting-specific-extension-scenarios"></a>Resolução de problemas de cenários específicos de extensão

### <a name="vm-insights"></a>VM Insights

- Ao ativar o VM Insights para um servidor ativado pelo Arco Azure, instala o agente Dependency and Log Analytics. Numa máquina lenta ou numa máquina lenta com uma ligação de rede lenta, é possível ver intervalos de tempo durante o processo de instalação. A Microsoft está a tomar medidas para resolver esta questão no agente 'Máquina Conectada' para ajudar a melhorar esta condição. Entretanto, uma nova provisóriação da instalação pode ter êxito.

### <a name="log-analytics-agent-for-linux"></a>Agente de Log Analytics para Linux

- A versão 1.13.9 (versão de extensão correspondente é 1.13.15) não está a marcar corretamente os dados carregados com o ID de recursos do servidor ativado pelo Arco Azure. Embora os registos estejam a ser enviados para o serviço, quando tenta visualizar os dados do servidor ativado selecionado após a seleção **de Registos** ou **Insights,** nenhum dado é devolvido. Pode ver os seus dados executando consultas a partir de Registos monitores Azure ou do Azure Monitor para VMs, que são telescópios para o espaço de trabalho.

- Algumas distribuições não são atualmente suportadas pelo agente Log Analytics para o Linux. O agente requer dependências adicionais para ser instalado, incluindo Python 2. Reveja [aqui](../../azure-monitor/agents/agents-overview.md#supported-operating-systems)a matriz de suporte e os pré-requisitos.

- O código de erro 52 na mensagem de estado indica uma dependência em falta. Verifique a saída e os registos para obter mais informações sobre a dependência que falta.

- Se uma instalação falhar, reveja a **secção de resolução de problemas e suporte** na visão geral da extensão. Na maioria dos casos, existe um código de erro incluído na mensagem de estado. Para o agente Log Analytics para o Linux, as mensagens de estado são explicadas [aqui,](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support)juntamente com informações gerais de resolução de problemas para esta extensão VM.

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema aqui ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

- Obtenha respostas de especialistas da Azure através do [Microsoft Q&A](/answers/topics/azure-arc.html).

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) , a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga a comunidade Azure a respostas, suporte e especialistas.

- Arquive um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **Apoio**.
