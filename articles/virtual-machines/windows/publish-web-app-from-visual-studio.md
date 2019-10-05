---
title: Publicar um aplicativo Web em uma VM do Azure por meio do Visual Studio
description: Publicar um aplicativo Web ASP.NET em uma máquina virtual do Azure por meio do Visual Studio
services: virtual-machines-windows
author: ghogen
manager: jillfra
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 6e5db9b4e46019aa386057d51d956ff11d90f498
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970868"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publicar um aplicativo Web ASP.NET em uma VM do Azure por meio do Visual Studio

Este documento descreve como publicar um aplicativo Web ASP.NET em uma VM (máquina virtual) do Azure usando o recurso de publicação **máquinas virtuais do Microsoft Azure** no Visual Studio 2019.  

## <a name="prerequisites"></a>Pré-requisitos
Para usar o Visual Studio para publicar um projeto ASP.NET em uma VM do Azure, a VM deve ser configurada corretamente.

- O computador deve ser configurado para executar um aplicativo Web ASP.NET e ter o WebDeploy instalado.

- A VM deve ter um nome DNS configurado. Para obter mais informações, consulte [criar um nome de domínio totalmente qualificado no portal do Azure para uma VM do Windows](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publicar seu aplicativo Web ASP.NET na VM do Azure usando o Visual Studio
A seção a seguir descreve como publicar um aplicativo Web ASP.NET existente em uma máquina virtual do Azure.

1. Abra sua solução de aplicativo Web no Visual Studio 2019.
2. Clique com o botão direito do mouse no projeto em Gerenciador de Soluções e escolha **publicar...**
3. Use a seta à direita da página para percorrer as opções de publicação até encontrar **máquinas virtuais do Microsoft Azure**.  

   ![Página de publicação-seta para a direita]

4. Selecione o ícone de **máquinas virtuais do Microsoft Azure** e selecione **publicar**.

   ![Publicar página-ícone de máquina virtual Microsoft Azure]

5. Escolha a conta apropriada (com a assinatura do Azure conectada à sua máquina virtual).  
   - Se você estiver conectado ao Visual Studio, a lista conta será preenchida com todas as suas contas autenticadas.  
   - Se você não estiver conectado, ou se a conta que você precisa não estiver listada, escolha "adicionar uma conta..." e siga os prompts para fazer logon.  
   ![Seletor de conta do Azure]  

6. Selecione a VM apropriada na lista de máquinas virtuais existentes.

   > [!Note]
   > Preencher essa lista pode levar algum tempo.

   ![Seletor de VM do Azure]

7. Clique em OK para iniciar a publicação.

8. Quando solicitado a fornecer credenciais, forneça o nome de usuário e a senha de uma conta de utilizador na VM de destino configurada com direitos de publicação. Essas credenciais normalmente são o nome de usuário e a senha do administrador usados ao criar a VM.  

   ![Logon no WebDeploy]

9. Aceite o certificado de segurança.

   ![Erro de certificado]

10. Assista à janela de saída para verificar o progresso da operação de publicação.

    ![Janela de Saída]

11. Se a publicação for bem-sucedida, um navegador será iniciado para abrir a URL do site publicado recentemente.

**Êxito!**

Agora você publicou com êxito seu aplicativo Web em uma máquina virtual do Azure.

## <a name="publish-page-options"></a>Opções de publicar página

Depois de concluir o assistente de publicação, a página de publicação é aberta no documento bem com o novo perfil de publicação selecionado.

### <a name="re-publish"></a>Publicar novamente

Para publicar atualizações em seu aplicativo Web, selecione o botão **publicar** na página publicar.  
- Se solicitado, insira o nome de usuário e a senha.  
- A publicação começa imediatamente.

![Página publicar – botão publicar]

### <a name="modify-publish-profile-settings"></a>Modificar configurações de perfil de publicação

Para exibir e modificar as configurações do perfil de publicação, selecione **configurações...** .  

![Página publicar – botão Configurações]

Suas configurações devem ter uma aparência semelhante a esta:  

![Configurações de publicação – página conexão]

#### <a name="save-user-name-and-password"></a>Salvar nome de usuário e senha
- Evite fornecer informações de autenticação toda vez que publicar. Para fazer isso, preencha os campos **nome de usuário** e **senha** e selecione a caixa **salvar senha** .
- Use o botão **validar conexão** para confirmar que você inseriu as informações corretas.

#### <a name="deploy-to-clean-web-server"></a>Implantar no servidor Web limpo

- Se você quiser garantir que o servidor Web tenha uma cópia limpa do aplicativo Web após cada upload e que nenhum outro arquivo seja deixado de uma implantação anterior, marque a caixa de seleção **remover arquivos adicionais no destino** na guia **configurações** .

- Aviso: A publicação com essa configuração exclui todos os arquivos existentes no servidor Web (diretório wwwroot). Verifique se você conhece o estado do computador antes de publicar com essa opção habilitada. 

![Configurações de publicação – página Configurações]

## <a name="next-steps"></a>Passos seguintes

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Configurar CI/CD para implantação automatizada na VM do Azure

Para configurar um pipeline de entrega contínua com Azure Pipelines, consulte [implantar em uma máquina virtual do Windows](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Página de publicação-seta para a direita]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Publicar página-ícone de máquina virtual Microsoft Azure]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Seletor de conta do Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Seletor de VM do Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Logon no WebDeploy]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Erro de certificado]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Janela de Saída]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Página publicar – botão publicar]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Página publicar – botão Configurações]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Configurações de publicação – página conexão]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Configurações de publicação – página Configurações]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
