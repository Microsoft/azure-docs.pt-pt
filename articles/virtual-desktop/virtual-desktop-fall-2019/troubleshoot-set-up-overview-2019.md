---
title: Windows Virtual Desktop (clássico) visão geral de resolução de problemas - Azure
description: Uma visão geral para problemas de resolução de problemas ao configurar um ambiente de inquilino virtual do Windows Desktop (clássico).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7b2220ffe92446338e1b9db9b329847d93830334
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88005436"
---
#  <a name="windows-virtual-desktop-classic-troubleshooting-overview-feedback-and-support"></a>Windows Virtual Desktop (clássico) resolução de problemas, feedback e suporte

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../troubleshoot-set-up-overview.md).

Este artigo fornece uma visão geral dos problemas que pode encontrar ao configurar um ambiente de inquilino virtual do Windows Desktop e fornece formas de resolver os problemas.

## <a name="provide-feedback"></a>Enviar comentários

Visite a [Comunidade Virtual desktop tech do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de desktop virtual do Windows com a equipa de produtos e membros da comunidade ativa.

## <a name="escalation-tracks"></a>Faixas de escalada

Utilize a seguinte tabela para identificar e resolver problemas que poderá encontrar ao configurar um ambiente de inquilino utilizando o cliente Remote Desktop. Uma vez configurado o seu inquilino, você pode usar o nosso novo [serviço de Diagnóstico](diagnostics-role-service-2019.md) para identificar problemas para cenários comuns.

>[!NOTE]
> Dispomos de um fórum tech community que pode visitar para discutir os seus problemas com a equipa de produtos e membros da comunidade ativa. Visite a [Comunidade de Tecnologia virtual de Desktop do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para iniciar uma discussão.

| **Problema**                                                            | **Solução Sugerida**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Criação de um inquilino virtual do Windows Desktop                                                    | Se houver uma falha do Azure, [abra um pedido de apoio ao Azure;](https://azure.microsoft.com/support/create-ticket/) caso [contrário, abra um pedido de suporte Azure](https://azure.microsoft.com/support/create-ticket/), selecione **Windows Virtual Desktop** para o serviço, selecione **Implementação** para o tipo de problema e, em seguida, selecione **Problemas criando um inquilino virtual do Windows** para o subtipo do problema.|
| Aceder a modelos de Marketplace no portal Azure       | Se houver uma falha do Azure, abra um pedido de [apoio ao Azure.](https://azure.microsoft.com/support/create-ticket/) <br> <br> Os modelos de ambiente de trabalho virtual Azure Marketplace Windows estão disponíveis gratuitamente.|
| Aceder aos modelos do Gestor de Recursos Azure do GitHub                                  | Consulte a [secção de VMs do Anfitrião VMs](troubleshoot-set-up-issues-2019.md#creating-windows-virtual-desktop-session-host-vms) do [Insumotário e anfitrião](troubleshoot-set-up-issues-2019.md)da criação de piscinas . Se o problema ainda não estiver resolvido, contacte a equipa de apoio do [GitHub.](https://github.com/contact) <br> <br> Se o erro ocorrer após o acesso ao modelo no GitHub, contacte o [Suporte Azure](https://azure.microsoft.com/support/create-ticket/).|
| Sessão anfitrião piscina Azure Virtual Network (VNET) e definições de Rota Expresso               | [Abra um pedido de suporte Azure](https://azure.microsoft.com/support/create-ticket/)e, em seguida, selecione o serviço apropriado (na categoria Networking). |
| Sessão anfitrião pool Virtual Machine (VM) criação quando modelos de Gestor de Recursos Azure fornecidos com Windows Virtual Desktop não estão sendo usados | [Abra um pedido de suporte Azure](https://azure.microsoft.com/support/create-ticket/)e, em seguida, selecione **Máquina Virtual executando o Windows** para o serviço. <br> <br> Para problemas com os modelos do Gestor de Recursos Azure que são fornecidos com o Windows Virtual Desktop, consulte a secção de inquilinos virtual do Windows Desktop do Inquilino e a criação de [piscinas de anfitrião.](troubleshoot-set-up-issues-2019.md) |
| Gestão do ambiente de acolhimento de mesa virtual do Windows a partir do portal Azure    | [Abra um pedido de apoio Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Para problemas de gestão ao utilizar serviços de ambiente de trabalho remoto/Windows Virtual Desktop PowerShell, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md) ou abra um pedido de suporte [Azure](https://azure.microsoft.com/support/create-ticket/), selecione **Windows Virtual Desktop** para o serviço, selecione **Configuração e gestão** para o tipo de problema, selecione **Problemas configurar o inquilino usando PowerShell** para o subtipo de problema. |
| Gestão da configuração virtual do Windows desktop ligada a piscinas de anfitriões e grupos de aplicações (grupos de aplicações)      | Consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md), ou abra um pedido de suporte [Azure](https://azure.microsoft.com/support/create-ticket/), selecione **Windows Virtual Desktop** para o serviço e, em seguida, selecione o tipo de problema apropriado.|
| Implantação e gestão de contentores de perfil FSLogix | Consulte [o guia de resolução de problemas para produtos FSLogix](/fslogix/fslogix-trouble-shooting-ht/) e, se isso não resolver o problema, abra um pedido de suporte [Azure](https://azure.microsoft.com/support/create-ticket/), selecione **Windows Virtual Desktop** para o serviço, selecione **FSLogix** para o tipo de problema e, em seguida, selecione o subtipo de problema apropriado. |
| Avaria de clientes de desktop remoto no início                                                 | Consulte [a resolução de problemas do cliente Remote Desktop](../troubleshoot-client.md) e se isso não resolver o problema, abra um pedido de suporte  [Azure](https://azure.microsoft.com/support/create-ticket/), selecione Windows Virtual **Desktop** para o serviço e, em seguida, selecione **clientes remote desktop** para o tipo de problema.  <br> <br> Se for um problema de rede, os seus utilizadores precisam de contactar o administrador da rede. |
| Conectado, mas sem alimentação                                                                 | A resolução de problemas utilizando o [Utilizador liga-se, mas nada é apresentado (sem feed)](troubleshoot-service-connection-2019.md#user-connects-but-nothing-is-displayed-no-feed) das [ligações](troubleshoot-service-connection-2019.md)de serviço de ambiente de trabalho virtual do Windows . <br> <br> Se os seus utilizadores tiverem sido designados para um grupo de aplicações,  [abra um pedido de suporte Azure](https://azure.microsoft.com/support/create-ticket/), selecione o Windows Virtual **Desktop** para o serviço e, em seguida, selecione **Clientes de Ambiente de Trabalho Remoto** para o tipo de problema. |
| Problemas de descoberta de alimentos devido à rede                                            | Os seus utilizadores precisam de contactar o administrador da rede. |
| Conectar clientes                                                                    | Consulte [as ligações de serviço de ambiente de trabalho virtual do Windows](troubleshoot-service-connection-2019.md) e, se isso não resolver o seu problema, consulte a [configuração da máquina virtual do anfitrião da Sessão](troubleshoot-vm-configuration-2019.md). |
| Capacidade de resposta de aplicações remotas ou ambiente de trabalho                                      | Se as questões estiverem ligadas a uma aplicação ou produto específico, contacte a equipa responsável por esse produto. |
| Mensagens de licenciamento ou erros                                                          | Se as questões estiverem ligadas a uma aplicação ou produto específico, contacte a equipa responsável por esse produto. |
| Problemas ao utilizar ferramentas de ambiente de trabalho virtual do Windows no GitHub (modelos de Gestor de Recursos Azure, ferramenta de diagnóstico, ferramenta de gestão) | Consulte [os modelos do Gestor de Recursos Azure para serviços de ambiente de trabalho remoto](https://github.com/Azure/RDS-Templates/blob/master/README.md) para reportar problemas. |

## <a name="next-steps"></a>Passos seguintes

- Para resolver problemas enquanto cria um inquilino e piscina de hospedagem em um ambiente de desktop virtual windows, consulte [o Inquilino e a criação de piscina de anfitrião.](troubleshoot-set-up-issues-2019.md)
- Para resolver problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração da máquina virtual do anfitrião da Sessão](troubleshoot-vm-configuration-2019.md).
- Para resolver problemas com as ligações do cliente virtual do Windows Desktop, consulte [as ligações do serviço de desktop virtual do Windows](troubleshoot-service-connection-2019.md).
- Para resolver problemas com clientes de ambiente de trabalho remoto, consulte [Troubleshoot o cliente Remote Desktop](../troubleshoot-client.md)
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md).
- Para saber mais sobre o serviço, consulte o [ambiente de ambiente de trabalho virtual do Windows.](environment-setup-2019.md)
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações de modelos do Gestor de Recursos de Resolução de Problemas](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para conhecer as ações de auditoria, consulte [as operações de Auditoria com o Gestor de Recursos.](../../azure-resource-manager/management/view-activity-logs.md)
- Para obter ações para determinar erros durante a implementação, consulte [as operações de implantação da visualização](../../azure-resource-manager/templates/deployment-history.md).
