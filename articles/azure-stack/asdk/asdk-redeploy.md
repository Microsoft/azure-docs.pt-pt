---
title: Voltar a implementar o Azure Stack Development Kit (ASDK) | Documentos da Microsoft
description: Neste artigo, irá aprender a reinstalar o ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 77942e24af847f7c8f9680ca793dacf8ba0be55f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112590"
---
# <a name="redeploy-the-asdk"></a>Reimplementar o ASDK
Neste artigo, irá aprender a implementar novamente o Azure Stack Development Kit (ASDK) num ambiente de não produção. Como atualizar o ASDK não é suportada, precisa completamente reimplantá-la para mover para uma versão mais recente. Também pode implementar novamente o ASDK em qualquer altura em que pretende iniciar do zero.

> [!IMPORTANT]
> Atualizar o ASDK para uma nova versão não é suportada. Terá de voltar a implementar o ASDK no computador de anfitrião do kit de desenvolvimento cada vez que pretende avaliar uma versão mais recente do Azure Stack.

## <a name="remove-azure-registration"></a>Remover o registo do Azure 
Se tiver registado anteriormente a instalação de ASDK com o Azure, deverá remover o recurso de Registro antes de reimplantar o ASDK. Volte a registar o ASDK para ativar a disponibilidade de itens no marketplace, quando implementar novamente o ASDK. Se não tiver registado anteriormente o ASDK com a sua subscrição do Azure, pode ignorar esta secção.

Para remover o recurso de registo, utilize o **Remove-AzsRegistration** cmdlet para anular o registo do Azure Stack. Em seguida, utilize o **Remove-AzureRMResourceGroup** cmdlet para eliminar o grupo de recursos do Azure Stack da sua subscrição do Azure:

1. Abra uma consola do PowerShell como administrador no computador que tenha acesso ao ponto final com privilégios. Para o ASDK, que é o computador de anfitrião do kit de desenvolvimento.

2. Execute os seguintes comandos do PowerShell para anular o registo de instalação do ASDK e eliminar a **azurestack** grupo de recursos da sua subscrição do Azure:

   ```Powershell    
   #Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Provide Azure subscription admin credentials
   Add-AzureRmAccount

   # Provide ASDK admin credentials
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

   # Unregister Azure Stack
   Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

   # Remove the Azure Stack resource group
   Remove-AzureRmResourceGroup -Name azurestack -Force
   ```

3. São-lhe pedido para iniciar sessão na sua subscrição do Azure e a instalação de ASDK local quando o script é executado.
4. Quando o script tiver concluído, deverá ver as mensagens semelhantes aos exemplos seguintes:

    ` De-Activating Azure Stack (this may take up to 10 minutes to complete).` ` Your environment is now unable to syndicate items and is no longer reporting usage data.`
    ` Remove registration resource from Azure...`
    ` "Deleting the resource..." on target "/subscriptions/<subscription information>"`
    ` ********** End Log: Remove-AzsRegistration ********* `



O Azure Stack agora deve ser anulado a sua subscrição do Azure com êxito. Além disso, o grupo de recursos azurestack, criado quando regista o ASDK com o Azure, também deve ser eliminado.

## <a name="deploy-the-asdk"></a>Implementar o ASDK
Para Reimplementar o Azure Stack, deve começar do zero conforme descrito abaixo. Os passos são diferentes consoante esteja ou não utilizou o script de instalador (asdk installer.ps1) do Azure Stack para instalar o ASDK.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Reimplementar o ASDK usando o script de instalador
1. No computador ASDK, abra uma consola elevada do PowerShell e navegue para o script de asdk installer.ps1 na **AzureStack_Installer** diretório localizado numa unidade de sistema não. Execute o script e clique em **reiniciar**.

   ![Execute o script de asdk installer.ps1](media/asdk-redeploy/1.png)

2. Selecione o sistema operacional base (não **do Azure Stack**) e clique em **próxima**.

   ![Reiniciar para o sistema operativo anfitrião](media/asdk-redeploy/2.png)

3. Depois de reinicia o anfitrião do kit de desenvolvimento para o sistema operacional base, inicie sessão como um administrador local. Localize e elimine o **C:\CloudBuilder.vhdx** ficheiro que foi utilizado como parte da implementação anterior. 

4. Repetir as mesmas etapas que realizou a primeira [implementar a ASDK](asdk-install.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Reimplementar o ASDK sem utilizar o instalador
Se não utilizou o script de asdk installer.ps1 para instalar o ASDK, tem de reconfigurar manualmente o computador de anfitrião do kit de desenvolvimento antes de reimplantar o ASDK.

1. Inicie o utilitário de configuração do sistema, executando **msconfig.exe** no computador ASDK. Sobre o **inicialização** separador, selecione o sistema operativo do computador anfitrião (não o Azure Stack), clique em **predefinir**e, em seguida, clique em **OK**. Clique em **reiniciar** quando lhe for pedido.

      ![Definir a configuração de arranque](media/asdk-redeploy/4.png)

2. Depois de reinicia o anfitrião do kit de desenvolvimento para o sistema operacional base, inicie sessão como um administrador local para o computador de anfitrião do kit de desenvolvimento. Localize e elimine o **C:\CloudBuilder.vhdx** ficheiro que foi utilizado como parte da implementação anterior. 

3. Repetir as mesmas etapas que realizou a primeira [implementar ASDK com o PowerShell](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Passos Seguintes
[Publicar tarefas de implementação de ASDK](asdk-post-deploy.md)




