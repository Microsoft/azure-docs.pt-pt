---
title: Ativar a ligação de ambiente de trabalho remoto para uma função nos serviços Cloud do Azure | Documentos da Microsoft
description: Como configurar a aplicação do serviço cloud do azure para permitir ligações de ambiente de trabalho remotas
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: 73ea1d64-1529-4d72-b58e-f6c10499e6bb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: 0c36dc5fb6b2754fc93a02e29d8d8ae74df36da7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963265"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Ativar a ligação de ambiente de trabalho remoto para uma função nos serviços Cloud do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Ambiente de trabalho remoto permite-lhe aceder a área de trabalho de uma função em execução no Azure. Pode utilizar uma ligação de ambiente de trabalho remoto para resolver problemas e diagnosticar problemas com a sua aplicação durante a execução.

Pode ativar uma ligação de ambiente de trabalho remoto na sua função durante o desenvolvimento, incluindo os módulos de ambiente de trabalho remoto na definição do serviço ou pode optar por ativar o ambiente de trabalho remoto através da extensão de ambiente de trabalho remoto. A abordagem preferida é usar a extensão de ambiente de trabalho remoto, como pode ativar o ambiente de trabalho remoto, mesmo depois da aplicação é implementada sem ter de voltar a implementar a sua aplicação.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Configurar o ambiente de trabalho remoto a partir do portal do Azure

O portal do Azure utiliza a abordagem de extensão de ambiente de trabalho remoto para que possa permitir o ambiente de trabalho remoto, mesmo depois da aplicação é implementada. O **ambiente de trabalho remoto** definições do serviço em nuvem permite-lhe ativar o ambiente de trabalho remoto, alterar a conta de administrador local utilizada para ligar às máquinas virtuais, o certificado utilizado na autenticação e defina a expiração data.

1. Clique em **serviços Cloud**, selecione o nome do serviço em nuvem e, em seguida, selecione **ambiente de trabalho remoto**.

    ![Ambiente de trabalho remoto do serviços de cloud](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Escolha se pretende ativar o ambiente de trabalho remoto para uma função individual ou para todas as funções, em seguida, altere o valor do comutador para **ativado**.

3. Preencha os campos necessários para o nome de utilizador, palavra-passe, expiração e certificado.

    ![Ambiente de trabalho remoto do serviços de cloud](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Todas as instâncias de função serão reiniciadas quando ativar o ambiente de trabalho remoto e selecionar primeiro **OK** (marca de verificação). Para evitar um reinício, o certificado utilizado para encriptar a palavra-passe tem de estar instalado na função. Para evitar um reinício [carregar um certificado para o serviço cloud](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) e, em seguida, voltar para esta caixa de diálogo.

4. Na **funções**, selecione a função que pretende atualizar ou selecione **todos os** para todas as funções.

5. Quando concluir as atualizações de configuração, selecione **guardar**. Irá demorar alguns minutos antes das instâncias de função estão prontas para ligações de receção.

## <a name="remote-into-role-instances"></a>Remoto em instâncias de função

Assim que o ambiente de trabalho remoto está ativado nas funções, pode iniciar uma ligação diretamente a partir do portal do Azure:

1. Clique em **instâncias** para abrir o **instâncias** definições.
2. Selecione uma instância de função que tem o ambiente de trabalho remoto configuradas.
3. Clique em **Connect** para transferir um ficheiro RDP para a instância de função.

    ![Ambiente de trabalho remoto do serviços de cloud](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Clique em **aberto** e, em seguida **Connect** para iniciar a ligação de ambiente de trabalho remoto.

>[!NOTE]
> Se estiver junto do seu serviço cloud por trás de um NSG, poderá ter de criar regras que permitam o tráfego nas portas **3389** e **20000**.  Ambiente de trabalho remoto utiliza a porta **3389**.  As instâncias de serviço cloud são carga balanceada, então diretamente não é possível controlar qual a instância para ligar ao.  O *RemoteForwarder* e *RemoteAccess* agentes gerir o tráfego RDP e permitir que o cliente enviar um cookie RDP e especifique uma instância individual para ligar a.  O *RemoteForwarder* e *RemoteAccess* agentes exigem essa porta **20000*** é aberto, que pode ser bloqueado se tiver um NSG.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)
