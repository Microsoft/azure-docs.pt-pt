---
title: Visão geral de resolução de problemas do Windows Virtual Desktop - Azure
description: Uma visão geral para problemas de resolução de problemas ao mesmo tempo que cria um ambiente de inquilino sinuoso virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 196c4f692b425d2fc6592888ba89f2fa6dafd3de
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612508"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Descrição geral da resolução de problemas, comentários e suporte

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Este artigo fornece uma visão geral dos problemas que pode encontrar ao configurar um ambiente de inquilino seletiva virtual do Windows e fornece formas de resolver os problemas.

## <a name="report-issues-during-public-preview"></a>Reportar problemas durante a pré-visualização pública

Para relatar problemas ou sugerir funcionalidades durante a pré-visualização pública para o lançamento da primavera de 2020, visite o [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop). Você pode usar a Comunidade Tecnológica para discutir as melhores práticas ou sugerir e votar em novas funcionalidades. Quando reportar um problema relacionado com o lançamento da pré-visualização pública, certifique-se de rotular o tipo de problema como **Atualização da primavera de 2020 (Pré-visualização)**.

Quando fizer uma publicação pedindo ajuda ou propor uma nova funcionalidade, certifique-se de descrever o seu tópico com o máximo de detalhes possível. Informações detalhadas podem ajudar outros utilizadores a responder à sua pergunta ou entender a funcionalidade em que está a propor um voto.

## <a name="escalation-tracks"></a>Faixas de escalada

Antes de fazer qualquer outra coisa, certifique-se de verificar a página de [estado do Azure](https://status.azure.com/status) e a [Azure Service Health](https://azure.microsoft.com/features/service-health/) para se certificar de que o seu serviço Azure está a funcionar corretamente.

Utilize a tabela seguinte para identificar e resolver problemas que possa encontrar ao configurar um ambiente de inquilino utilizando o cliente Remote Desktop. Assim que o seu inquilino estiver configurado, pode usar o nosso novo serviço de [Diagnóstico](diagnostics-role-service.md) para identificar questões para cenários comuns.

| **Edição**                                                            | **Solução Sugerida**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Aceder aos modelos do Marketplace no portal Azure       | Os modelos de ambiente de trabalho virtual Do Azure Marketplace Windows Estão disponíveis gratuitamente.|
| Piscina de anfitriões de sessão Azure Rede Virtual (VNET) e definições de Rota Expresso               | Abra um pedido de [apoio Azure](https://azure.microsoft.com/support/create-ticket/)e, em seguida, selecione o serviço adequado (na categoria Networking). |
| Sessão de hospedante virtual machine (VM) criação quando modelos de Gestor de Recursos Azure fornecidos com Windows Virtual Desktop não estão sendo usados | [Abra um pedido](https://azure.microsoft.com/support/create-ticket/)de suporte Azure e, em seguida, selecione **Máquina Virtual executando o Windows** para o serviço. <br> <br> Para problemas com os modelos do Gestor de Recursos Azure que são fornecidos com o Windows Virtual Desktop, consulte a criação da secção de inquilinos do Ambiente do Windows Virtual Desktop e a criação de [pool anfitrião.](troubleshoot-set-up-issues.md) |
| Gerir o ambiente de anfitriões da sessão de desktop virtual do Windows do portal Azure    | Abra um pedido de [apoio Azure.](https://azure.microsoft.com/support/create-ticket/) <br> <br> Para problemas de gestão ao utilizar serviços de ambiente de trabalho remotos/Windows Virtual Desktop PowerShell, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) ou abra um pedido de suporte [Azure, selecione](https://azure.microsoft.com/support/create-ticket/)o Windows Virtual **Desktop** para o serviço, selecione **Configuração e gestão** para o tipo de problema, e selecione **Problemas configurando o inquilino usando powerShell** para o subtipo de problemas. |
| Gerir a configuração do Windows Virtual Desktop ligada a piscinas de anfitriões e grupos de aplicações (grupos de aplicações)      | Consulte o [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md), ou abra um pedido de [suporte Azure,](https://azure.microsoft.com/support/create-ticket/)selecione **O Ambiente de Trabalho Virtual do Windows** para o serviço e, em seguida, selecione o tipo de problema apropriado.|
| Implantação e gestão de recipientes de perfil FSLogix | Consulte o guia de [resolução de problemas para produtos FSLogix](/fslogix/fslogix-trouble-shooting-ht/) e se isso não resolver o problema, abra um pedido de [suporte Azure](https://azure.microsoft.com/support/create-ticket/), selecione **Windows Virtual Desktop** para o serviço, selecione **FSLogix** para o tipo de problema e, em seguida, selecione o subtipo de problema apropriado. |
| Avaria dos clientes de ambiente supor remoto no início                                                 | Consulte [o Troubleshoot do cliente Remote Desktop](troubleshoot-client.md) e se isso não resolver o problema, abra um pedido de suporte [Azure](https://azure.microsoft.com/support/create-ticket/), selecione **Windows Virtual Desktop** para o serviço e, em seguida, selecione clientes **remote Desktop** para o tipo de problema.  <br> <br> Se for um problema de rede, os seus utilizadores precisam de contactar o administrador da rede. |
| Conectado, mas sem alimentação                                                                 | Problemas utilizando o [Utilizador, mas nada é exibido (sem feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) das [ligações](troubleshoot-service-connection.md)de serviço de ambiente de trabalho virtual do Windows . <br> <br> Se os seus utilizadores tiverem sido atribuídos a um grupo de aplicações, abra um pedido de [suporte Azure](https://azure.microsoft.com/support/create-ticket/), selecione **O Ambiente de Trabalho Virtual do Windows** para o serviço e, em seguida, selecione Remote Desktop **Clients** para o tipo de problema. |
| Problemas de descoberta de alimentos devido à rede                                            | Os seus utilizadores precisam de contactar o administrador da rede. |
| Conectar clientes                                                                    | Consulte [as ligações](troubleshoot-service-connection.md) de serviço do Windows Virtual Desktop e se isso não resolver o seu problema, consulte a [configuração da máquina virtual do anfitrião](troubleshoot-vm-configuration.md)da sessão . |
| Capacidade de resposta de aplicações remotas ou ambiente de trabalho                                      | Se os problemas estiverem ligados a uma aplicação ou produto específico, contacte a equipa responsável por esse produto. |
| Licenciamento de mensagens ou erros                                                          | Se os problemas estiverem ligados a uma aplicação ou produto específico, contacte a equipa responsável por esse produto. |
| Problemas com métodos de autenticação de terceiros | Verifique se o seu fornecedor de terceiros suporta cenários do Windows Virtual Desktop e aborde-os em relação a quaisquer problemas conhecidos. |
| Problemas que usam o Log Analytics para o Windows Virtual Desktop | Para problemas com o esquema de diagnóstico, abra um pedido de [apoio Azure.](https://azure.microsoft.com/support/create-ticket/)<br><br>Para consultas, visualização ou outros problemas no Log Analytics, selecione o tipo de problema apropriado no Log Analytics. |

## <a name="next-steps"></a>Passos seguintes

- Para resolver problemas ao criar uma piscina de anfitriões num ambiente de ambiente de trabalho virtual windows, consulte ambiente e criação de [piscina seletiva.](troubleshoot-set-up-issues.md)
- Para resolver problemas ao configurar uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração virtual](troubleshoot-vm-configuration.md)do anfitrião da sessão .
- Para resolver problemas com as ligações do cliente do Windows Virtual Desktop, consulte [as ligações](troubleshoot-service-connection.md)de serviço do Windows Virtual Desktop .
- Para resolver problemas com clientes do Desktop Remoto, consulte [Troubleshoot o cliente Remote Desktop](troubleshoot-client.md)
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte o ambiente de ambiente de [trabalho virtual do Windows](environment-setup.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)do modelo de gestor de recursos de resolução de problemas .
- Para conhecer as ações de auditoria, consulte operações de [auditoria com o Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md)
- Para aprender sobre ações para determinar erros durante a implementação, consulte as operações de [implantação do View](../azure-resource-manager/templates/deployment-history.md).
