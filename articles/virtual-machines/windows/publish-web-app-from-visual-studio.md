---
title: Publicar uma Web App para um Azure VM do Visual Studio
description: Publique uma aplicação web ASP.NET para uma máquina virtual Azure do Estúdio Visual
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: bdc03e8c136606ab7768705b0c8dbcc97782966f
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "87088398"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publique uma ASP.NET Web App para um Azure VM do Visual Studio

Este documento descreve como publicar uma aplicação web ASP.NET para uma máquina virtual Azure (VM) utilizando a funcionalidade de publicação de **Máquinas Virtuais Microsoft Azure** no Visual Studio 2019.  

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar o Visual Studio para publicar um projeto ASP.NET para um Azure VM, o VM deve ser corretamente configurado.

- A máquina deve ser configurada para executar uma aplicação web ASP.NET e ter WebDeploy instalada. Para obter mais informações, consulte [Criar um VM ASP.NET com WebDeploy](https://github.com/aspnet/Tooling/blob/AspNetVMs/docs/create-asp-net-vm-with-webdeploy.md).

- O VM deve ter um nome DNS configurado. Para obter mais informações, consulte [Criar um nome de domínio totalmente qualificado no portal Azure para um Windows VM](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publique a sua aplicação web ASP.NET para o Azure VM usando o Visual Studio
A secção seguinte descreve como publicar uma aplicação web ASP.NET existente a uma máquina virtual Azure.

1. Abra a sua solução de aplicações web no Visual Studio 2019.
2. Clique com o botão direito no projeto no Solution Explorer e escolha **Publicar...**
3. Utilize a seta à direita da página para percorrer as opções de publicação até encontrar **as Máquinas Virtuais Microsoft Azure** .  

   ![Página de publicação - Seta direita]

4. Selecione o ícone **Microsoft Azure Virtual Machines** e selecione **Publicar** .

   ![Página de publicação - Ícone da máquina virtual do Microsoft Azure]

5. Escolha a conta apropriada (com a subscrição Azure ligada à sua máquina virtual).  
   - Se estiver inscrito no Visual Studio, a lista de contas está preenchida com todas as suas contas autenticadas.  
   - Se não estiver inscrito ou se a conta de que necessita não estiver listada, escolha "Adicionar uma conta..." e seguir as instruções para fazer login.  
   ![Selecionador de Conta Azure]  

6. Selecione o VM apropriado da lista de máquinas virtuais existentes.

   > [!Note]
   > Povoar esta lista pode levar algum tempo.

   ![Selecionador Azure VM]

7. Clique em OK para começar a publicar.

8. Quando solicitado para obter credenciais, forneça o nome de utilizador e a palavra-passe de uma conta de utilizador no VM-alvo que está configurado com direitos de publicação. Estas credenciais são tipicamente o nome de utilizador e palavra-passe de administrador utilizados na criação do VM.  

   ![WebDeploy Login]

9. Aceite o certificado de segurança.

   ![Erro do Certificado]

10. Observe a janela de saída para verificar o progresso da operação de publicação.

    ![Janela de saída]

11. Se a publicação for bem sucedida, um navegador lança-se para abrir o URL do site recém-publicado.

**Que sucesso!**

Publicou agora com sucesso a sua aplicação web numa máquina virtual Azure.

## <a name="publish-page-options"></a>Publicar Opções de Página

Após completar o assistente de publicação, a página Publicar é aberta no documento bem com o novo perfil de publicação selecionado.

### <a name="re-publish"></a>Re-publicação

Para publicar atualizações na sua aplicação web, selecione o botão **Publicar** na página Publicar.  
- Se solicitado, insira o nome de utilizador e a palavra-passe.  
- A publicação começa imediatamente.

![Página de publicação - Botão de publicação]

### <a name="modify-publish-profile-settings"></a>Modificar as definições de perfil de publicação

Para visualizar e modificar as definições de perfil de publicação, selecione **Definições...** .  

![Página de publicação - Botão de definições]

As suas definições devem ser mais ou menos assim:  

![Configurações de publicação - Página de conexão]

#### <a name="save-user-name-and-password"></a>Guardar nome de utilizador e palavra-passe
- Evite fornecer informações de autenticação sempre que publicar. Para tal, preencha os campos **de nome de utilizador** e **palavra-passe** e selecione a caixa **de palavra-passe Guardar.**
- Utilize o botão **'Validar Ligação'** para confirmar que introduziu as informações certas.

#### <a name="deploy-to-clean-web-server"></a>Implementar para limpar servidor web

- Se pretender garantir que o servidor web tem uma cópia limpa da aplicação web após cada upload e que nenhum outro ficheiro é deixado de uma implementação anterior, pode verificar os **ficheiros adicionais remover na** caixa de verificação de destino no separador **Definições.**

- Aviso: A publicação com esta definição elimina todos os ficheiros existentes no servidor web (diretório wwwroot). Certifique-se de que conhece o estado da máquina antes de publicar com esta opção ativada. 

![Publicar Definições - Página de Definições]

## <a name="next-steps"></a>Passos seguintes

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Configurar CI/CD para implantação automatizada para Azure VM

Para configurar um gasoduto de entrega contínuo com gasodutos Azure, consulte [implementar para uma máquina virtual do Windows](/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Página de publicação - Seta direita]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Página de publicação - Ícone da máquina virtual do Microsoft Azure]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Selecionador de Conta Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Selecionador Azure VM]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy Login]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Erro do Certificado]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Janela de saída]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Página de publicação - Botão de publicação]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Página de publicação - Botão de definições]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Configurações de publicação - Página de conexão]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Publicar Definições - Página de Definições]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
