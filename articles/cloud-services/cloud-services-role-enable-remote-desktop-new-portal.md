---
title: Utilize o portal para ativar o Ambiente de Trabalho Remoto para uma Função
description: Como configurar a sua aplicação de serviço em nuvem azul para permitir ligações remotas de ambiente de trabalho
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8fa0d3c0e29c53e6fe9cb32ddf02168686be1efe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743258"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic"></a>Ativar a conexão de ambiente de trabalho remoto para uma função em serviços de nuvem Azure (clássico)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

O Ambiente de Trabalho Remoto permite-lhe aceder ao ambiente de trabalho de uma função em execução no Azure. Pode utilizar uma ligação remote desktop para resolver problemas e diagnosticar problemas com a sua aplicação enquanto está em funcionamento.

Pode ativar uma ligação de ambiente de trabalho remoto na sua função durante o desenvolvimento, incluindo os módulos de ambiente de trabalho remoto na definição de serviço ou pode optar por ativar o Ambiente de Trabalho Remoto através da Extensão de Ambiente de Trabalho Remoto. A abordagem preferida é utilizar a extensão Remote Desktop, uma vez que pode ativar o Remote Desktop mesmo depois de a aplicação ser implementada sem ter de recolocar a sua aplicação.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Configurar o Ambiente de Trabalho Remoto a partir do portal Azure

O portal Azure utiliza a abordagem Remote Desktop Extension para que possa ativar o Ambiente de Trabalho Remoto mesmo depois de a aplicação ser implementada. As definições **de Ambiente de Trabalho Remoto** para o seu serviço na nuvem permitem-lhe ativar o Ambiente de Trabalho Remoto, alterar a conta de Administrador local utilizada para ligar às máquinas virtuais, o certificado utilizado na autenticação e definir a data de validade.

1. Clique **em Cloud Services**, selecione o nome do serviço na nuvem e, em seguida, selecione Remote **Desktop**.

    ![imagem mostra cloud serviços remotos desktop](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Escolha se deseja ativar o Ambiente de Trabalho Remoto para uma função individual ou para todas as funções e, em seguida, altere o valor do comutador para **Ativado**.

3. Preencha os campos necessários para o nome de utilizador, palavra-passe, caducidade e certificado.

    ![Ambiente de trabalho remoto dos serviços em nuvem](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Todas as instâncias de função serão reiniciadas quando ativar pela primeira vez o Ambiente de Trabalho Remoto e selecione **OK** (marcação de verificação). Para evitar um reboot, o certificado utilizado para encriptar a palavra-passe deve ser instalado na função. Para evitar um reinício, [faça o upload de um certificado para o serviço na nuvem](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) e, em seguida, volte a este diálogo.

4. Em **Roles**, selecione a função que pretende atualizar ou selecione **All** para todas as funções.

5. Quando terminar as atualizações de configuração, **selecione Guardar**. Levará alguns momentos até que as suas instâncias de papel estejam prontas para receber ligações.

## <a name="remote-into-role-instances"></a>Remoto em instâncias de papel

Uma vez ativado o Ambiente de Trabalho Remoto nas funções, pode iniciar uma ligação diretamente a partir do portal Azure:

1. Clique **em Instâncias** para abrir as definições **de Instâncias.**
2. Selecione uma instância de função que tenha o Ambiente de Trabalho Remoto configurado.
3. Clique **em Connect** para descarregar um ficheiro RDP para a instância de função.

    ![Imagem de ambiente de trabalho remoto de serviços na nuvem](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Clique **em Abrir** e, em seguida, **Conecte-se** para iniciar a ligação remote desktop.

>[!NOTE]
> Se o seu serviço de nuvem estiver sentado atrás de um NSG, poderá ter de criar regras que permitam o tráfego nas portas **3389** e **20000**.  O Ambiente de Trabalho Remoto utiliza a porta **3389**.  As instâncias do Serviço de Cloud são equilibradas em carga, por isso não é possível controlar diretamente a que instância se ligar.  Os agentes *RemoteForwarder* e *RemoteAccess* gerem o tráfego RDP e permitem ao cliente enviar um cookie RDP e especificar uma instância individual para se ligar.  Os agentes *RemoteForwarder* e *RemoteAccess* exigem que a porta **2000*** esteja aberta, o que pode ser bloqueado se tiver um NSG.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)
