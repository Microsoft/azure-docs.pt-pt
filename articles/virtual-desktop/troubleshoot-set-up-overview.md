---
title: Visão geral de resolução de problemas do Windows Virtual Desktop - Azure
description: Uma visão geral para problemas de resolução de problemas enquanto configura um ambiente de ambiente de trabalho virtual do Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4d4bdc4fa15f634b36f12a650b70b9ffd89b40e0
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539117"
---
# <a name="troubleshooting-overview-feedback-and-support-for-windows-virtual-desktop"></a>Visão geral de resolução de problemas, feedback e suporte para Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md).

Este artigo fornece uma visão geral dos problemas que pode encontrar ao configurar um ambiente de desktop virtual do Windows e fornece formas de resolver os problemas.

## <a name="report-issues"></a>Comunicar problemas

Para reportar problemas ou sugerir funcionalidades para o Windows Virtual Desktop com integração do Azure Resource Manager, visite a [Comunidade virtual de Desktop Tech do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop). Você pode usar a Comunidade Tecnológica para discutir as melhores práticas ou sugerir e votar em novas funcionalidades.

Quando fizer uma publicação a pedir ajuda ou propor uma nova funcionalidade, certifique-se de descrever o seu tópico com o maior detalhe possível. Informações detalhadas podem ajudar outros utilizadores a responder à sua pergunta ou a compreender a funcionalidade a que está a propor um voto.

## <a name="escalation-tracks"></a>Faixas de escalada

Antes de fazer qualquer outra coisa, certifique-se de verificar a página de [estado do Azure](https://status.azure.com/status) e [a Azure Service Health](https://azure.microsoft.com/features/service-health/) para se certificar de que o seu serviço Azure está a funcionar corretamente.

Utilize a seguinte tabela para identificar e resolver problemas que poderá encontrar ao configurar um ambiente utilizando o cliente remote desktop. Uma vez configurado o seu ambiente, você pode usar o nosso novo [serviço de Diagnóstico](diagnostics-role-service.md) para identificar problemas para cenários comuns.

| **Emissão**                                                            | **Solução sugerida**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Sessão anfitrião piscina Azure Virtual Network (VNET) e definições de Rota Expresso               | [Abra um pedido de suporte Azure](https://azure.microsoft.com/support/create-ticket/)e, em seguida, selecione o serviço apropriado (na categoria Networking). |
| Sessão anfitrião pool Virtual Machine (VM) criação quando modelos de Gestor de Recursos Azure fornecidos com Windows Virtual Desktop não estão sendo usados | [Abra um pedido de suporte Azure](https://azure.microsoft.com/support/create-ticket/)e, em seguida, selecione **Windows Virtual Desktop** para o serviço. <br> <br> Para problemas com os modelos do Gestor de Recursos Azure que são fornecidos com o Windows Virtual Desktop, consulte a secção de erros do modelo do Gestor de Recursos Azure da [criação](troubleshoot-set-up-issues.md)da piscina host . |
| Gestão do ambiente de acolhimento de mesa virtual do Windows a partir do portal Azure    | [Abra um pedido de apoio Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Para problemas de gestão quando utilizar serviços de ambiente de trabalho remoto/Windows Virtual Desktop PowerShell, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) ou abra um pedido de suporte [Azure](https://azure.microsoft.com/support/create-ticket/), selecione **Windows Virtual Desktop** para o serviço, selecione **Configuração e gestão** para o tipo de problema, selecione **Issues configurar ambiente utilizando PowerShell** para o subtipo de problema. |
| Gestão da configuração virtual do Windows desktop ligada a piscinas de anfitriões e grupos de aplicações (grupos de aplicações)      | Consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md), ou abra um pedido de suporte [Azure](https://azure.microsoft.com/support/create-ticket/), selecione **Windows Virtual Desktop** para o serviço e, em seguida, selecione o tipo de problema apropriado.|
| Implantação e gestão de contentores de perfil FSLogix | Consulte [o guia de resolução de problemas para produtos FSLogix](/fslogix/fslogix-trouble-shooting-ht/) e, se isso não resolver o problema, abra um pedido de suporte [Azure](https://azure.microsoft.com/support/create-ticket/), selecione **Windows Virtual Desktop** para o serviço, selecione **FSLogix** para o tipo de problema e, em seguida, selecione o subtipo de problema apropriado. |
| Avaria de clientes de desktop remoto no início                                                 | Consulte [a resolução de problemas do cliente Remote Desktop](troubleshoot-client.md) e se isso não resolver o problema, abra um pedido de suporte  [Azure](https://azure.microsoft.com/support/create-ticket/), selecione Windows Virtual **Desktop** para o serviço e, em seguida, selecione **clientes remote desktop** para o tipo de problema.  <br> <br> Se for um problema de rede, os seus utilizadores precisam de contactar o administrador da rede. |
| Conectado, mas sem alimentação                                                                 | A resolução de problemas utilizando o [Utilizador liga-se, mas nada é apresentado (sem feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) das [ligações](troubleshoot-service-connection.md)de serviço de ambiente de trabalho virtual do Windows . <br> <br> Se os seus utilizadores tiverem sido designados para um grupo de aplicações,  [abra um pedido de suporte Azure](https://azure.microsoft.com/support/create-ticket/), selecione o Windows Virtual **Desktop** para o serviço e, em seguida, selecione **Clientes de Ambiente de Trabalho Remoto** para o tipo de problema. |
| Problemas de descoberta de alimentos devido à rede                                            | Os seus utilizadores precisam de contactar o administrador da rede. |
| Conectar clientes                                                                    | Consulte [as ligações de serviço de ambiente de trabalho virtual do Windows](troubleshoot-service-connection.md) e, se isso não resolver o seu problema, consulte a [configuração da máquina virtual do anfitrião da Sessão](troubleshoot-vm-configuration.md). |
| Capacidade de resposta de aplicações remotas ou ambiente de trabalho                                      | Se as questões estiverem ligadas a uma aplicação ou produto específico, contacte a equipa responsável por esse produto. |
| Mensagens de licenciamento ou erros                                                          | Se as questões estiverem ligadas a uma aplicação ou produto específico, contacte a equipa responsável por esse produto. |
| Problemas com métodos ou ferramentas de autenticação de terceiros | Verifique se o seu fornecedor de terceiros suporta cenários de Ambiente de Trabalho Virtual do Windows e aborde-os relativamente a quaisquer problemas conhecidos. |
| Problemas utilizando o Log Analytics para o Windows Virtual Desktop | Para problemas com o esquema de diagnóstico, abra um pedido de [apoio ao Azure](https://azure.microsoft.com/support/create-ticket/).<br><br>Para consultas, visualização ou outros problemas no Log Analytics, selecione o tipo de problema apropriado em Log Analytics. |
| Problemas com aplicações M365 | Contacte o centro de administração M365 com uma das opções de ajuda do [centro de administração M365](/microsoft-365/admin/contact-support-for-business-products/). |

## <a name="next-steps"></a>Passos seguintes

- Para resolver problemas enquanto cria uma piscina de anfitriões num ambiente de ambiente de trabalho virtual do Windows, consulte [a criação de piscinas hospedeiras.](troubleshoot-set-up-issues.md)
- Para resolver problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração da máquina virtual do anfitrião da Sessão](troubleshoot-vm-configuration.md).
- Para resolver problemas relacionados com o agente virtual do Windows desktop ou conectividade de sessão, consulte [problemas comuns](troubleshoot-agent.md)de Computador de Secretária virtual do Windows .
- Para resolver problemas com as ligações do cliente virtual do Windows Desktop, consulte [as ligações do serviço de desktop virtual do Windows](troubleshoot-service-connection.md).
- Para resolver problemas com clientes de ambiente de trabalho remoto, consulte [Troubleshoot o cliente Remote Desktop](troubleshoot-client.md)
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte o [ambiente de ambiente de trabalho virtual do Windows.](environment-setup.md)
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações de modelos do Gestor de Recursos de Resolução de Problemas](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para conhecer as ações de auditoria, consulte [as operações de Auditoria com o Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md)
- Para obter ações para determinar erros durante a implementação, consulte [as operações de implantação da visualização](../azure-resource-manager/templates/deployment-history.md).
