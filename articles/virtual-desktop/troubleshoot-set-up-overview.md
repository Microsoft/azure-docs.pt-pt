---
title: Visão geral, comentários e suporte da solução de problemas de área de trabalho virtual do Windows – Azure
description: Uma visão geral para solucionar problemas durante a configuração de um ambiente de locatário de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: e652c8a67db686159a2c3fd48e2ea65888bcc10d
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816348"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Descrição geral da resolução de problemas, comentários e suporte

Este artigo fornece uma visão geral dos problemas que você pode encontrar ao configurar um ambiente de locatário de área de trabalho virtual do Windows e fornece maneiras de resolver os problemas.

## <a name="provide-feedback"></a>Enviar comentários

No momento, não estamos adotando casos de suporte enquanto a área de trabalho virtual do Windows está em versão prévia. Visite a [comunidade técnica de área de trabalho virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de área de trabalho virtual do Windows com a equipe de produto e os membros ativos da Comunidade.

## <a name="escalation-tracks"></a>Faixas de escalonamento

Use a tabela a seguir para identificar e resolver os problemas que você pode encontrar ao configurar um ambiente de locatário usando o Área de Trabalho Remota Client. Depois de configurar o locatário, você pode usar nosso novo [serviço de diagnóstico](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) para identificar problemas em cenários comuns.

>[!NOTE]
>No momento, não estamos adotando casos de suporte enquanto a área de trabalho virtual do Windows está em versão prévia. Sempre que nos referirmos ao suporte de área de trabalho virtual do Windows, acesse nosso fórum da comunidade técnica por enquanto. Visite a [comunidade técnica de área de trabalho virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir problemas com a equipe de produto e os membros ativos da Comunidade. Se você precisar resolver um problema de suporte, inclua a ID da atividade e o intervalo de tempo aproximado para quando o problema ocorreu.

| **Lo**                                                            | **Solução sugerida**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Criando um locatário                                                    | Se houver uma interrupção do Azure, entre em contato com o [suporte do Azure](https://azure.microsoft.com/support/options/); caso contrário, contate o **suporte de área de trabalho Virtual serviços de área de trabalho remota/Windows**.|
| Acessando modelos do Marketplace no portal do Azure       | Se houver uma interrupção do Azure, entre em contato com o [suporte do Azure](https://azure.microsoft.com/support/options/). <br> <br> Os modelos de área de trabalho virtual do Windows do Azure Marketplace estão disponíveis gratuitamente.|
| Acessando modelos de Azure Resource Manager do GitHub                                  | Consulte a seção "criando VMs do host de sessão de área de trabalho virtual do Windows" do [locatário e da criação do pool](troubleshoot-set-up-issues.md)de hosts. Se o problema ainda não for resolvido, entre em contato com a [equipe de suporte do GitHub](https://github.com/contact). <br> <br> Se o erro ocorrer depois de acessar o modelo no GitHub, entre em contato com o [suporte do Azure](https://azure.microsoft.com/support/options/).|
| Configurações de rede virtual (VNET) do pool de hosts da sessão e de rota expressa               | Contate o **suporte do Azure (rede)** . |
| Criação de VM (máquina virtual) do pool de hosts da sessão quando Azure Resource Manager modelos fornecidos com a área de trabalho virtual do Windows não estão sendo usados | Contate o **suporte do Azure (computação)** . <br> <br> Para problemas com os modelos de Azure Resource Manager que são fornecidos com a área de trabalho virtual do Windows, consulte a seção criando locatário da área de trabalho virtual do Windows do [locatário e criação do pool](troubleshoot-set-up-issues.md)de hosts. |
| Gerenciando o ambiente de host de sessão de área de trabalho virtual do Windows no portal do Azure    | Contate o **suporte do Azure**. <br> <br> Para problemas de gerenciamento ao usar o Serviços de Área de Trabalho Remota/Windows Virtual Desktop PowerShell, consulte [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) ou entre em contato com a **equipe de suporte do serviços de área de trabalho remota/área de trabalho virtual**do Windows. |
| Gerenciando a configuração da área de trabalho virtual do Windows vinculada a pools de hosts e grupos de aplicativos      | Consulte [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)ou entre em contato com a **equipe de suporte do serviços de área de trabalho remota/área de trabalho virtual**do Windows. <br> <br> Se os problemas estiverem vinculados à GUI (interface gráfica do usuário) de exemplo, entre em contato com a Comunidade do Yammer.|
| Problemas de clientes de área de trabalho remota em iniciar                                                 | Consulte [área de trabalho remota conexões de cliente](troubleshoot-client-connection.md) e, se isso não resolver o problema, entre em contato com a **equipe de suporte do serviços de área de trabalho remota/área de trabalho virtual do Windows**.  <br> <br> Se for um problema de rede, os usuários precisarão entrar em contato com o administrador da rede. |
| Conectado, mas sem feed                                                                 | Solucionar problemas usando a seção "o usuário se conecta, mas nada é exibido (sem feed)" de [área de trabalho remota conexões de cliente](troubleshoot-client-connection.md). <br> <br> Se os usuários tiverem sido atribuídos a um grupo de aplicativos, encaminhe para a **equipe de suporte do serviços de área de trabalho remota/área de trabalho virtual do Windows**. |
| Problemas de descoberta de feed devido à rede                                            | Os usuários precisam entrar em contato com o administrador da rede. |
| Conectando clientes                                                                    | Consulte [área de trabalho remota conexões de cliente](troubleshoot-client-connection.md) e, se isso não resolver o problema, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md). |
| Capacidade de resposta de aplicativos remotos ou área de trabalho                                      | Se os problemas estiverem vinculados a um aplicativo ou produto específico, entre em contato com a equipe responsável pelo produto. |
| Mensagens de licenciamento ou erros                                                          | Se os problemas estiverem vinculados a um aplicativo ou produto específico, entre em contato com a equipe responsável pelo produto. |

## <a name="next-steps"></a>Passos seguintes

- Para solucionar problemas ao criar um pool de locatários e de host em um ambiente de área de trabalho virtual do Windows, confira [criação de locatário e pool](troubleshoot-set-up-issues.md)de hosts.
- Para solucionar problemas durante a configuração de uma VM (máquina virtual) na área de trabalho virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de cliente de área de trabalho virtual do Windows, consulte [área de trabalho remota conexões de cliente](troubleshoot-client-connection.md).
- Para solucionar problemas ao usar o PowerShell com a área de trabalho virtual do Windows, consulte [PowerShell da área de trabalho virtual do Windows](troubleshoot-powershell.md).
- Para saber mais sobre o serviço de visualização, consulte [ambiente do Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para percorrer um tutorial de solução de problemas [, consulte o tutorial: Solucionar problemas de implantações](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)de modelo do Resource Manager.
- Para saber mais sobre ações de auditoria, consulte [operações de auditoria com o Gerenciador de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber mais sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).