---
title: Resolução de problemas comum Azure Automanage erros de embarque
description: Erros comuns de auto-produção no embarque e como resolvê-los
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 6ee0164dd8243d30cf691350352757f2503e34c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862980"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Resolução de problemas erros comuns de auto-produção de bordo
A auto-produção pode não conseguir embarcar uma máquina no serviço. Este documento explica como resolver falhas de implementação, partilha algumas razões comuns pelas quais as implementações podem falhar e descreve potenciais próximos passos na mitigação.

## <a name="troubleshooting-deployment-failures"></a>Falhas de implantação de resolução de problemas
Embarcar uma máquina para auto-gestão resultará na criação de uma implementação do Azure Resource Manager. Para mais informações, consulte a implementação para mais detalhes sobre o porquê de ter falhado. Existem ligações com as implementações no desvio de detalhes de falha, na imagem abaixo.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Falha de auto-produção de detalhes flyout.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Verifique as implementações do grupo de recursos que contém o VM falhado
O voo de falha conterá uma ligação às implementações do grupo de recursos que contém a máquina que falhou no embarque. O flyout também conterá um nome prefixo que pode usar para filtrar implementações com. Clicar no link de implementação irá levá-lo à lâmina de implementação, onde pode então filtrar as implementações para ver as implementações de auto-produção na sua máquina. Se estiver a implantar em várias regiões, certifique-se de que clica na implantação na região correta.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Verifique as implementações da subscrição que contém o VM falhado
Se não vir nenhuma falha na implementação do grupo de recursos, então o seu próximo passo seria olhar para as implementações na sua subscrição contendo o VM que falhou no embarque. Clique nas Implementações para o link **de subscrição** no voo de saída de falha e no filtro utilizando o filtro **Automanage-DefaultResourceGroup.** Utilize o nome do grupo de recursos da lâmina de avaria para filtrar as implementações. O nome de implantação será sufixado com um nome de região. Se estiver a implantar em várias regiões, certifique-se de que clica na implantação na região correta.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Verifique as implementações numa subscrição ligada a um espaço de trabalho log analytics
Se não vir nenhuma falha no grupo de recursos ou subscrição que contenha o seu VM falhado, e se o seu VM falhado estiver ligado a um espaço de trabalho do Log Analytics numa subscrição diferente, então vá para a subscrição ligada ao seu espaço de trabalho Log Analytics e verifique se há implementações falhadas.

## <a name="common-deployment-errors"></a>Erros de implementação comuns

Erro |  Mitigação
:-----|:-------------|
Erro de permissões insuficientes da conta de auto-controlo | Este erro pode ocorrer se recentemente tiver mudado uma subscrição contendo uma nova Conta de Autoadministração para um novo inquilino. Estão [localizados aqui](./repair-automanage-account.md)passos para resolver este erro.
Região do espaço de trabalho não corresponde aos requisitos de mapeamento da região | A automanagem não foi capaz de embarcar na sua máquina porque o espaço de trabalho log Analytics ao qual a máquina está atualmente ligada não está mapeado para uma região de Automação suportada. Certifique-se de que o seu espaço de trabalho e conta de Automação do Log Analytics existentes estão localizados num [mapeamento de região suportada.](../automation/how-to/region-mappings.md)
"Acesso negado por causa da atribuição de negação com o nome 'Sistema negar atribuição criada por aplicação gerida'" | Foi criada uma [assinatura de negação](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) no seu recurso, o que impediu a Automanage de aceder ao seu recurso. Esta negação A assinatura pode ter sido criada por um [Plano](https://docs.microsoft.com/azure/governance/blueprints/concepts/resource-locking) ou por uma [Aplicação Gerida](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/overview).
"Informação sobre os OS: Nome='(nulo)",, ver='(nulo)', agente status='Not Ready'." | Certifique-se de que está a executar uma [versão de agente com suporte mínimo,](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)o agente está em execução[(Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) e [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)), e que o agente está atualizado[(Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) e [Windows).](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)
"Incapaz de determinar o SO para o nome VM OS:, ver . Por favor, verifique se o agente VM está em funcionamento, o estado atual está pronto." | Certifique-se de que está a executar uma [versão de agente com suporte mínimo,](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)o agente está em execução[(Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) e [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)), e que o agente está atualizado[(Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) e [Windows).](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)
"A VM reportou uma falha no processamento da extensão 'IaaSAntimalware'" | Certifique-se de que não tem outra oferta antimalware/antivírus já instalada no seu VM. Se isso falhar, contacte o suporte.
Espaço de trabalho ASC: A automanagem não suporta atualmente o serviço Log Analytics no _local._ | Verifique se o seu VM está localizado numa [região apoiada.](./automanage-virtual-machines.md#supported-regions)
A implementação do modelo falhou devido à violação de políticas. Consulte os detalhes para mais informações. | Existe uma política que impede a Automanagem de embarcar no seu VM. Verifique as políticas que são aplicadas à sua subscrição ou grupo de recursos que contenham o seu VM que pretende embarcar na Automanage.
"A atribuição falhou; não há informação adicional disponível" | Por favor, abra um caso com o suporte do Microsoft Azure.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre a Azure Automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Ativar a auto-produção de máquinas virtuais no portal Azure](quick-create-virtual-machines-portal.md)