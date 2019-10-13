---
title: Usando o portal, habilite Área de Trabalho Remota para uma função (serviços de nuvem do Azure)
description: Como configurar seu aplicativo de serviço de nuvem do Azure para permitir conexões de área de trabalho remota
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: 50447beabefefcaa723a26ed4388354b9590c36e
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298437"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Habilitar Conexão de Área de Trabalho Remota para uma função nos serviços de nuvem do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Área de Trabalho Remota permite que você acesse a área de trabalho de uma função em execução no Azure. Você pode usar uma conexão Área de Trabalho Remota para solucionar e diagnosticar problemas com seu aplicativo enquanto ele está em execução.

Você pode habilitar uma conexão Área de Trabalho Remota em sua função durante o desenvolvimento, incluindo os módulos Área de Trabalho Remota em sua definição de serviço ou pode optar por habilitar Área de Trabalho Remota por meio da extensão Área de Trabalho Remota. A abordagem preferida é usar a extensão Área de Trabalho Remota, pois você pode habilitar Área de Trabalho Remota mesmo depois que o aplicativo é implantado sem a necessidade de reimplantar o aplicativo.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Configurar Área de Trabalho Remota do portal do Azure

O portal do Azure usa a abordagem de extensão de Área de Trabalho Remota para que você possa habilitar Área de Trabalho Remota mesmo depois que o aplicativo for implantado. As configurações de **área de trabalho remota** para seu serviço de nuvem permitem que você habilite área de trabalho remota, altere a conta de administrador local usada para se conectar às máquinas virtuais, o certificado usado na autenticação e defina a data de validade.

1. Clique em **serviços de nuvem**, selecione o nome do serviço de nuvem e, em seguida, selecione **área de trabalho remota**.

    ![Área de trabalho remota dos serviços de nuvem](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Escolha se deseja habilitar Área de Trabalho Remota para uma função individual ou para todas as funções e, em seguida, altere o valor do alternador para **habilitado**.

3. Preencha os campos obrigatórios para nome de usuário, senha, expiração e certificado.

    ![Área de trabalho remota dos serviços de nuvem](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Todas as instâncias de função serão reiniciadas quando você habilitar o Área de Trabalho Remota pela primeira vez e selecionar **OK** (marca de seleção). Para evitar uma reinicialização, o certificado usado para criptografar a senha deve ser instalado na função. Para evitar uma reinicialização, [carregue um certificado para o serviço de nuvem](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) e, em seguida, retorne a esta caixa de diálogo.

4. Em **funções**, selecione a função que você deseja atualizar ou selecione **tudo** para todas as funções.

5. Ao concluir as atualizações de configuração, selecione **salvar**. Levará alguns minutos para que as instâncias de função estejam prontas para receber conexões.

## <a name="remote-into-role-instances"></a>Remoto em instâncias de função

Quando Área de Trabalho Remota estiver habilitado nas funções, você poderá iniciar uma conexão diretamente do portal do Azure:

1. Clique em **instâncias** para abrir as configurações de **instâncias** .
2. Selecione uma instância de função que tenha Área de Trabalho Remota configurada.
3. Clique em **conectar** para baixar um arquivo RDP para a instância de função.

    ![Área de trabalho remota dos serviços de nuvem](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Clique em **abrir** e **Conecte-se** para iniciar a conexão área de trabalho remota.

>[!NOTE]
> Se seu serviço de nuvem estiver atrás de um NSG, talvez seja necessário criar regras que permitam o tráfego nas portas **3389** e **20000**.  Área de Trabalho Remota usa a porta **3389**.  As instâncias de serviço de nuvem têm balanceamento de carga, portanto, não é possível controlar diretamente a instância à qual se conectar.  Os agentes *RemoteForwarder* e *RemoteAccess* gerenciam o tráfego RDP e permitem que o cliente envie um cookie RDP e especifique uma instância individual à qual se conectar.  Os agentes *RemoteForwarder* e *RemoteAccess* exigem que a porta **20000*** esteja aberta, o que poderá ser bloqueado se você tiver um NSG.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)
