---
title: Utilize o portal para ativar o Ambiente de Trabalho Remoto para uma Função
titleSuffix: Azure Cloud Services
description: Como configurar a sua aplicação de serviço em nuvem azul para permitir ligações remotas de ambiente de trabalho
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: d65f4b55be317234c10a0e90cfe413d9e38a6a90
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79247465"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Ativar a ligação remota de ambiente de trabalho para uma função nos serviços de nuvem azure

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

O Remote Desktop permite-lhe aceder ao ambiente de trabalho de uma função em funcionamento no Azure. Pode utilizar uma ligação Remote Desktop para resolver problemas e diagnosticar problemas com a sua aplicação enquanto esta estiver em execução.

Pode ativar uma ligação remota de ambiente de trabalho durante o desenvolvimento, incluindo os módulos de ambiente de trabalho remoto na definição de serviço ou pode optar por ativar o Ambiente de Trabalho Remoto através da extensão remota do ambiente de trabalho. A abordagem preferida é utilizar a extensão Remote Desktop, uma vez que pode ativar o Remote Desktop mesmo depois de a aplicação ser implementada sem ter de reimplantar a sua aplicação.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Configure o ambiente de trabalho remoto do portal Azure

O portal Azure utiliza a abordagem de extensão remota do ambiente de trabalho para que possa ativar o Ambiente de Trabalho Remoto mesmo depois de a aplicação ser implementada. As definições **do Ambiente de Trabalho Remoto** para o seu serviço na nuvem permitem ativar o Ambiente de Trabalho Remoto, alterar a conta de Administrador local utilizada para ligar às máquinas virtuais, o certificado utilizado na autenticação e definir a data de validade.

1. Clique em **Serviços cloud,** selecione o nome do serviço cloud e, em seguida, selecione **Remote Desktop**.

    ![Serviços de nuvem ambiente de trabalho remoto](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Escolha se pretende ativar o Remote Desktop para uma função individual ou para todas as funções e, em seguida, altere o valor do comutador para **Ativado**.

3. Preencha os campos necessários para o nome do utilizador, senha, caducidade e certificado.

    ![Serviços de nuvem ambiente de trabalho remoto](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Todas as instâncias de função serão reiniciadas quando ativar primeiro o Remote Desktop e selecionar **OK** (marca de verificação). Para evitar um reboot, o certificado utilizado para encriptar a palavra-passe deve ser instalado na função. Para evitar o reinício, [faça o upload de um certificado para o serviço de nuvem](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) e, em seguida, volte a este diálogo.

4. Em **Funções,** selecione o papel que pretende atualizar ou selecionar **All** para todas as funções.

5. Quando terminar as atualizações de configuração, selecione **Guardar**. Levará alguns momentos até que as suas instâncias de papel estejam prontas para receber ligações.

## <a name="remote-into-role-instances"></a>Remota em instâncias de papéis

Uma vez ativado o Ambiente de Trabalho Remoto nas funções, pode iniciar uma ligação diretamente a partir do portal Azure:

1. Clique em **Instâncias** para abrir as definições **de Instâncias.**
2. Selecione uma instância de função que tenha o Remote Desktop configurado.
3. Clique em **Connect** para descarregar um ficheiro RDP para a instância de funções.

    ![Serviços de nuvem ambiente de trabalho remoto](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Clique em **Abrir** e, em seguida, **ligar** para iniciar a ligação Remote Desktop.

>[!NOTE]
> Se o seu serviço na nuvem estiver sentado atrás de um NSG, poderá ter de criar regras que permitam o tráfego nos portos **3389** e **20000**.  O Ambiente de Trabalho Remoto utiliza a porta **3389**.  As instâncias do Serviço cloud são equilibradas em carga, por isso não pode controlar diretamente a que instância ligar.  Os agentes *RemoteForwarder* e *RemoteAccess* gerem o tráfego rdp e permitem ao cliente enviar um cookie RDP e especificar uma instância individual para se conectar.  Os agentes *RemoteForwarder* e *RemoteAccess* exigem que a porta **20000*** esteja aberta, que pode ser bloqueada se tiver um NSG.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)
