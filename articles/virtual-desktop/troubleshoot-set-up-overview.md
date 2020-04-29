---
title: Visão geral de resolução de problemas do Windows Virtual Desktop - Azure
description: Uma visão geral para problemas de resolução de problemas ao mesmo tempo que cria um ambiente de inquilino sinuoso virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b9dfb2ee7e74f94bfc3e6d2c679cb7da3d6bc66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127399"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Descrição geral da resolução de problemas, comentários e suporte

Este artigo fornece uma visão geral dos problemas que pode encontrar ao configurar um ambiente de inquilino seletiva virtual do Windows e fornece formas de resolver os problemas.

## <a name="provide-feedback"></a>Enviar comentários

Visite o [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de desktop virtual windows com a equipa de produtos e membros ativos da comunidade.

## <a name="escalation-tracks"></a>Faixas de escalada

Utilize a tabela seguinte para identificar e resolver problemas que possa encontrar ao configurar um ambiente de inquilino utilizando o cliente Remote Desktop. Assim que o seu inquilino estiver configurado, pode usar o nosso novo serviço de [Diagnóstico](diagnostics-role-service.md) para identificar questões para cenários comuns.

>[!NOTE]
> Temos um fórum tech community que pode visitar para discutir os seus problemas com a equipa de produtos e membros ativos da comunidade. Visite o [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para iniciar uma discussão.

| **Edição**                                                            | **Solução Sugerida**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Criar um inquilino de ambiente de trabalho virtual Windows                                                    | Se houver uma paragem do Azure, abra um pedido de [apoio azure;](https://azure.microsoft.com/support/create-ticket/) De outra forma, abra um pedido de [suporte Azure,](https://azure.microsoft.com/support/create-ticket/)selecione **O Ambiente de Trabalho Virtual do Windows** para o serviço, selecione **Implementação** para o tipo de problema, em seguida, selecione **Problemas criando um inquilino de ambiente** de trabalho virtual Windows para o subtipo de problemas.|
| Aceder aos modelos do Marketplace no portal Azure       | Se houver uma paragem no Azure, abra um pedido de [apoio azure.](https://azure.microsoft.com/support/create-ticket/) <br> <br> Os modelos de ambiente de trabalho virtual Do Azure Marketplace Windows Estão disponíveis gratuitamente.|
| Aceder aos modelos do Gestor de Recursos Azure do GitHub                                  | Consulte a secção de [VMs de anfitriões de VMs](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms) do [Anfitrião e da criação](troubleshoot-set-up-issues.md)de piscinas do Windows Virtual . Se o problema ainda não estiver resolvido, contacte a equipa de apoio do [GitHub.](https://github.com/contact) <br> <br> Se o erro ocorrer depois de aceder ao modelo no GitHub, contacte o [Suporte Azure](https://azure.microsoft.com/support/create-ticket/).|
| Piscina de anfitriões de sessão Azure Rede Virtual (VNET) e definições de Rota Expresso               | Abra um pedido de [apoio Azure](https://azure.microsoft.com/support/create-ticket/)e, em seguida, selecione o serviço adequado (na categoria Networking). |
| Sessão de hospedante virtual machine (VM) criação quando modelos de Gestor de Recursos Azure fornecidos com Windows Virtual Desktop não estão sendo usados | [Abra um pedido](https://azure.microsoft.com/support/create-ticket/)de suporte Azure e, em seguida, selecione **Máquina Virtual executando o Windows** para o serviço. <br> <br> Para problemas com os modelos do Gestor de Recursos Azure que são fornecidos com o Windows Virtual Desktop, consulte a criação da secção de inquilinos virtual do Windows Desktop do Tenant e a criação de [pool anfitrião.](troubleshoot-set-up-issues.md) |
| Gerir o ambiente de anfitriões da sessão de desktop virtual do Windows do portal Azure    | Abra um pedido de [apoio Azure.](https://azure.microsoft.com/support/create-ticket/) <br> <br> Para problemas de gestão ao utilizar serviços de ambiente de trabalho remotos/Windows Virtual Desktop PowerShell, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) ou abra um pedido de suporte [Azure, selecione](https://azure.microsoft.com/support/create-ticket/)o Windows Virtual **Desktop** para o serviço, selecione **Configuração e gestão** para o tipo de problema, e selecione **Problemas configurando o inquilino usando powerShell** para o subtipo de problemas. |
| Gerir a configuração do Windows Virtual Desktop ligada a piscinas de anfitriões e grupos de aplicações (grupos de aplicações)      | Consulte o [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md), ou abra um pedido de [suporte Azure,](https://azure.microsoft.com/support/create-ticket/)selecione **O Ambiente de Trabalho Virtual do Windows** para o serviço e, em seguida, selecione o tipo de problema apropriado.|
| Implantação e gestão de recipientes de perfil FSLogix | Consulte o guia de [resolução de problemas para produtos FSLogix](/fslogix/fslogix-trouble-shooting-ht/) e se isso não resolver o problema, abra um pedido de [suporte Azure](https://azure.microsoft.com/support/create-ticket/), selecione **Windows Virtual Desktop** para o serviço, selecione **FSLogix** para o tipo de problema e, em seguida, selecione o subtipo de problema apropriado. |
| Avaria dos clientes de ambiente supor remoto no início                                                 | Consulte [o Troubleshoot do cliente Remote Desktop](troubleshoot-client.md) e se isso não resolver o problema, abra um pedido de suporte [Azure](https://azure.microsoft.com/support/create-ticket/), selecione **Windows Virtual Desktop** para o serviço e, em seguida, selecione clientes **remote Desktop** para o tipo de problema.  <br> <br> Se for um problema de rede, os seus utilizadores precisam de contactar o administrador da rede. |
| Conectado, mas sem alimentação                                                                 | Problemas utilizando o [Utilizador, mas nada é exibido (sem feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) das [ligações](troubleshoot-service-connection.md)de serviço de ambiente de trabalho virtual do Windows . <br> <br> Se os seus utilizadores tiverem sido atribuídos a um grupo de aplicações, abra um pedido de [suporte Azure](https://azure.microsoft.com/support/create-ticket/), selecione **O Ambiente de Trabalho Virtual do Windows** para o serviço e, em seguida, selecione Remote Desktop **Clients** para o tipo de problema. |
| Problemas de descoberta de alimentos devido à rede                                            | Os seus utilizadores precisam de contactar o administrador da rede. |
| Conectar clientes                                                                    | Consulte [as ligações](troubleshoot-service-connection.md) de serviço do Windows Virtual Desktop e se isso não resolver o seu problema, consulte a [configuração da máquina virtual do anfitrião](troubleshoot-vm-configuration.md)da sessão . |
| Capacidade de resposta de aplicações remotas ou ambiente de trabalho                                      | Se os problemas estiverem ligados a uma aplicação ou produto específico, contacte a equipa responsável por esse produto. |
| Licenciamento de mensagens ou erros                                                          | Se os problemas estiverem ligados a uma aplicação ou produto específico, contacte a equipa responsável por esse produto. |
| Problemas ao utilizar ferramentas de ambiente de trabalho virtual do Windows no GitHub (modelos do Gestor de Recursos Azure, ferramenta de diagnóstico, ferramenta de gestão) | Consulte os modelos do Gestor de [Recursos Azure para serviços de ambiente](https://github.com/Azure/RDS-Templates/blob/master/README.md) de trabalho remotos para reportar problemas. |

## <a name="next-steps"></a>Passos seguintes

- Para resolver problemas ao criar um inquilino e uma piscina de hospedas num ambiente de ambiente de trabalho virtual windows, consulte [tenant e host pool creation](troubleshoot-set-up-issues.md).
- Para resolver problemas ao configurar uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração virtual](troubleshoot-vm-configuration.md)do anfitrião da sessão .
- Para resolver problemas com as ligações do cliente do Windows Virtual Desktop, consulte [as ligações](troubleshoot-service-connection.md)de serviço do Windows Virtual Desktop .
- Para resolver problemas com clientes do Desktop Remoto, consulte [Troubleshoot o cliente Remote Desktop](troubleshoot-client.md)
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte o ambiente de ambiente de [trabalho virtual do Windows](environment-setup.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)do modelo de gestor de recursos de resolução de problemas .
- Para conhecer as ações de auditoria, consulte operações de [auditoria com o Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md)
- Para aprender sobre ações para determinar erros durante a implementação, consulte as operações de [implantação do View](../azure-resource-manager/templates/deployment-history.md).
