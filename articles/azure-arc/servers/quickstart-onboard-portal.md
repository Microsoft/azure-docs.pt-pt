---
title: Início rápido – conectar computadores ao Azure usando o Azure ARC para servidores-Portal
description: Neste início rápido, você aprende a conectar computadores ao Azure usando o Azure ARC para servidores do portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: automação do Azure, DSC, PowerShell, configuração de estado desejado, gerenciamento de atualizações, controle de alterações, inventário, runbooks, Python, gráfico, híbrido, integrado
ms.date: 08/25/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: b014f6015b3e13a603cf3893062bd0463eb110ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501989"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>Início rápido: conectar computadores ao Azure usando o Azure ARC para servidores-Portal

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Revise os clientes com suporte e a configuração de rede necessária na [visão geral do arco do Azure para servidores](overview.md).

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>Gerar o script de instalação do agente usando o portal do Azure

1. Iniciar [https://aka.ms/hybridmachineportal] [aka_hybridmachineportal]
1. Clique em **+ Adicionar**
1. Siga o assistente para concluir
1. A última página tem um script gerado que você pode copiar (ou baixar).

O script deve ser executado no computador de destino que você deseja conectar. Ele baixa o agente, instala-o e conecta o computador como uma única operação.

Nos servidores não Azure que você deseja gerenciar:

1. Fazer logon no servidor (usando o SSH, RDP ou comunicação remota do PowerShell)
1. Iniciar um shell: bash no Linux, PowerShell como administrador no Windows
1. Cole o script do portal e execute-o no servidor a ser conectado ao Azure.
1. A autenticação padrão para a integração de um servidor individual é *interativa* usando o dispositivo login do Azure. Ao executar o script, você verá uma mensagem semelhante a:

  ```none
  To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B3V3NLWRF to authenticate.
  ```
  
   Abra um navegador e insira o código para autenticar. O navegador não precisa estar em execução no servidor que você está integrando, podendo estar em outro computador, como seu laptop.

1. Se você quiser autenticar de forma não interativa, siga as etapas em [criar uma entidade de serviço](quickstart-onboard-powershell.md#create-a-service-principal-for-onboarding-at-scale) e modificar o script gerado no Portal.

> [!NOTE]
> Se você estiver usando o Internet Explorer no servidor pela primeira vez para fazer logon, ocorrerá um erro. Você pode apenas reabrir o navegador e fazer isso novamente.

## <a name="execute-the-script-on-target-nodes"></a>Executar o script em nós de destino

Faça logon em cada nó e execute o script gerado no Portal. Depois que o script for concluído com êxito, vá para o portal do Azure Verifique se o servidor foi conectado com êxito.

![Integração bem-sucedida](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Limpeza

Para desconectar um computador do arco do Azure para servidores, você precisa executar duas etapas.

1. Selecione o computador no [portal](https://aka.ms/hybridmachineportal), clique nas reticências (`...`) e selecione **excluir**.
1. Desinstale o agente do computador.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Atribuir uma política a computadores conectados](../../governance/policy/assign-policy-portal.md)