---
title: Windows área de Trabalho Virtual resolução de problemas de visão geral, comentários e suporte - Azure
description: Visão geral para resolução de problemas ao configurar um ambiente de inquilino de área de Trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 43f940c3c5b0fcf06fb5aa9be268f85db19fc8a6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077664"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Descrição geral da resolução de problemas, comentários e suporte

Este artigo fornece uma descrição geral dos problemas que pode ser encontrados ao configurar um ambiente de inquilino de área de Trabalho Virtual do Windows e fornece formas de resolver os problemas.

## <a name="provide-feedback"></a>Enviar comentários

Estamos atualmente não estão a demorar incidentes de suporte, enquanto a área de Trabalho Virtual do Windows está em pré-visualização. Visite o [Comunidade tecnológica da área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de área de Trabalho Virtual do Windows com a equipe do produto e membros da Comunidade de Active Directory.

## <a name="escalation-tracks"></a>Faixas de escalonamento

Utilize a seguinte tabela para identificar e resolver problemas que poderá encontrar ao configurar um ambiente de inquilino com o cliente de ambiente de trabalho remoto. Assim que o seu inquilino do configurado, pode utilizar a nova [serviço de diagnóstico](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) para identificar problemas para cenários comuns.

>[!NOTE]
>Estamos atualmente não estão a demorar incidentes de suporte, enquanto a área de Trabalho Virtual do Windows está em pré-visualização. Sempre que nos Referimos ao suporte da área de Trabalho Virtual do Windows, aceda ao nosso fórum da Comunidade tecnológica agora. Visite o [Comunidade tecnológica da área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir problemas com a equipe do produto e membros da Comunidade de Active Directory. Se precisar de resolver um problema de suporte, incluem o ID de atividade e o período de tempo aproximado para quando o problema ocorreu.

| **Problema**                                                            | **Solução de sugestão**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Criação de um inquilino                                                    | Se houver uma falha do Azure, contacte [suporte do Azure](https://azure.microsoft.com/support/options/); caso contrário, contacte **suporte remoto Desktop Windows/serviços de área de Trabalho Virtual**.|
| Ao aceder ao Marketplace modelos no portal do Azure       | Se houver uma falha do Azure, contacte [suporte do Azure](https://azure.microsoft.com/support/options/). <br> <br> Modelos de área de Trabalho Virtual do Windows Marketplace do Azure estão disponíveis gratuitamente.|
| Aceder a partir do GitHub de modelos Azure Resource Manager                                  | Consulte a secção "Criar Windows Virtual anfitrião de sessões de ambiente de trabalho VMs" [inquilino e o host a criação do agrupamento](troubleshoot-set-up-issues.md). Se o problema é ainda não resolvido, entre em contato com o [equipa de suporte do GitHub](https://github.com/contact). <br> <br> Se o erro ocorrer depois do modelo no GitHub, contacte [suporte do Azure](https://azure.microsoft.com/support/options/).|
| Sessão anfitrião rede Virtual do Azure (VNET) e o Express Route definições do conjunto               | Contacte **suporte do Azure (redes)** . |
| Conjunto de host de sessão criação de Máquina Virtual (VM) quando não estão a ser utilizados modelos Azure Resource Manager fornecidos com a área de Trabalho Virtual do Windows | Contacte **suporte do Azure (computação)** . <br> <br> Para problemas com os modelos Azure Resource Manager que são fornecidos com a área de Trabalho Virtual de Windows, consulte a secção de inquilino de criação de Desktop Virtual do Windows de [inquilino e o host a criação do agrupamento](troubleshoot-set-up-issues.md). |
| Gerir o ambiente de host de sessão de área de Trabalho Virtual do Windows a partir do portal do Azure    | Contacte **suporte do Azure**. <br> <br> Para problemas de gestão ao utilizar o ambiente de trabalho serviços/Windows Virtual Desktop PowerShell remoto, consulte [Windows PowerShell de ambiente de Trabalho Virtual](troubleshoot-powershell.md) ou contacte o **equipa de suporte de ambiente de trabalho serviços/Windows Virtual ambiente de trabalho remoto** . |
| Gerenciamento de configuração de área de Trabalho Virtual do Windows vinculados aos conjuntos de anfitrião e os grupos de aplicações (grupos de aplicações)      | Ver [Windows PowerShell de ambiente de Trabalho Virtual](troubleshoot-powershell.md), ou entre em contato com o **equipa de suporte de ambiente de trabalho serviços/Windows Virtual ambiente de trabalho remoto**. <br> <br> Se problemas estão associados à interface do usuário gráfica de exemplo (GUI), entrar em contacto com a Comunidade do Yammer.|
| Funcionamento inadequado de clientes de ambiente de trabalho remoto no início                                                 | Ver [ligações de cliente de ambiente de trabalho remoto](troubleshoot-client-connection.md) e, se isso não resolver o problema, contacte **equipa de suporte de ambiente de trabalho serviços/Windows Virtual ambiente de trabalho remoto**.  <br> <br> Se for um problema de rede, os utilizadores precisam para contactar o administrador de rede. |
| Ligado mas não feed                                                                 | Resolver problemas com o "utilizador se liga, mas não será apresentado nada (sem feed)" seção [ligações de cliente de ambiente de trabalho remoto](troubleshoot-client-connection.md). <br> <br> Se os utilizadores tenham sido atribuídos a um grupo de aplicações, escalar para o **equipa de suporte de ambiente de trabalho serviços/Windows Virtual ambiente de trabalho remoto**. |
| Problemas de deteção devido a rede de feed                                            | Os utilizadores precisam para contactar o administrador de rede. |
| Ligação de clientes                                                                    | Ver [ligações de cliente de ambiente de trabalho remoto](troubleshoot-client-connection.md) e se de que não resolve o problema, consulte o artigo [configuração de máquina virtual do anfitrião de sessão](troubleshoot-vm-configuration.md). |
| Capacidade de resposta de aplicativos remotos ou ambiente de trabalho                                      | Se os problemas estão associados a uma aplicação específica ou um produto, contacte a equipa responsável por esse produto. |
| Mensagens de licenciamento ou de erros                                                          | Se os problemas estão associados a uma aplicação específica ou um produto, contacte a equipa responsável por esse produto. |

## <a name="next-steps"></a>Passos Seguintes

- Para resolver problemas ao criar um conjunto de inquilino e o anfitrião num ambiente de área de Trabalho Virtual do Windows, consulte [inquilino e o host a criação do agrupamento](troubleshoot-set-up-issues.md).
- Para resolver problemas ao configurar uma máquina virtual (VM) na área de Trabalho Virtual do Windows, consulte [configuração de máquina virtual do anfitrião de sessão](troubleshoot-vm-configuration.md).
- Para resolver problemas com ligações de cliente de área de Trabalho Virtual do Windows, consulte [ligações de cliente de ambiente de trabalho remoto](troubleshoot-client-connection.md).
- Para resolver problemas ao utilizar o PowerShell com a área de Trabalho Virtual do Windows, consulte [Windows PowerShell de ambiente de Trabalho Virtual](troubleshoot-powershell.md).
- Para saber mais sobre o serviço de pré-visualização, veja [ambiente de pré-visualização do Windows Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para seguir um tutorial de resolução de problemas, consulte [Tutorial: Resolver problemas de implementações de modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Para saber mais sobre a auditoria de ações, veja [auditar operações com o Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber mais sobre as ações para determinar os erros durante a implementação, veja [ver as operações de implementação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).