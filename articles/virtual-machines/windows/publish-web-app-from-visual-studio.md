---
title: Publique uma Web App para um Azure VM do Visual Studio
description: Publique uma ASP.NET Aplicação Web a uma Máquina Virtual Azure do Estúdio Visual
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: d864230ff524f6c008f9f05c16505cd9d0e9f229
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583364"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publique uma ASP.NET Web App para um Azure VM do Visual Studio

Este documento descreve como publicar uma aplicação web ASP.NET a uma máquina virtual Azure (VM) utilizando a funcionalidade de publicação da **Microsoft Azure Virtual Machines** no Visual Studio 2019.  

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar o Visual Studio para publicar um projeto ASP.NET a um Azure VM, o VM deve ser corretamente configurado.

- A máquina deve ser configurada para executar uma aplicação web ASP.NET e ter o WebDeploy instalado. Para mais informações, consulte [Criar um VM ASP.NET com WebDeploy](https://github.com/aspnet/Tooling/blob/AspNetVMs/docs/create-asp-net-vm-with-webdeploy.md).

- O VM deve ter um nome DNS configurado. Para mais informações, consulte Criar um nome de [domínio totalmente qualificado no portal Azure para um Windows VM](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publique a sua ASP.NET aplicação web para o Azure VM usando o Visual Studio
A secção seguinte descreve como publicar uma aplicação web ASP.NET existente a uma máquina virtual Azure.

1. Abra a sua solução de aplicação web no Visual Studio 2019.
2. Clique no projeto no Solution Explorer e escolha **Publicar...**
3. Utilize a seta à direita da página para percorrer as opções de publicação até encontrar **máquinas virtuais Microsoft Azure**.  

   ![Página de publicação - Seta direita]

4. Selecione o ícone **das Máquinas Virtuais Microsoft Azure** e selecione **Publicar**.

   ![Página de publicação - Ícone da Máquina Virtual do Microsoft Azure]

5. Escolha a conta apropriada (com a subscrição Azure ligada à sua máquina virtual).  
   - Se você está inscrito no Estúdio Visual, a lista de contas é preenchida com todas as suas contas autenticadas.  
   - Se não estiver inscrito, ou se a conta de que precisa não estiver listada, escolha "Adicione uma conta..." e seguir as instruções para iniciar sessão.  
   ![Selecionador de Conta Azure]  

6. Selecione o VM apropriado da lista de Máquinas Virtuais Existentes.

   > [!Note]
   > Povoar esta lista pode levar algum tempo.

   ![Azure VM Selector]

7. Clique em OK para começar a publicar.

8. Quando solicitado para obter credenciais, forneça o nome de utilizador e a palavra-passe de uma conta de utilizador no VM alvo que esteja configurado com direitos de publicação. Estas credenciais são tipicamente o nome de utilizador e senha de administração utilizados na criação do VM.  

   ![WebDeploy Login]

9. Aceite a certidão de segurança.

   ![Erro de certificado]

10. Observe a janela Saída para verificar o progresso da operação de publicação.

    ![Janela de saída]

11. Se a publicação for bem sucedida, um navegador lança-se para abrir o URL do site recém-publicado.

**O sucesso!**

Já publicou com sucesso a sua aplicação web numa máquina virtual Azure.

## <a name="publish-page-options"></a>Publicar Opções de Página

Depois de concluir o assistente de publicação, a página Publicar é aberta bem no documento com o novo perfil de publicação selecionado.

### <a name="re-publish"></a>Re-publicar

Para publicar atualizações na sua aplicação web, selecione o botão **Publicar** na página Publicar.  
- Se solicitado, introduza o nome de utilizador e a palavra-passe.  
- A publicação começa imediatamente.

![Publicar Página - Publicar botão]

### <a name="modify-publish-profile-settings"></a>Modificar as definições de perfil de publicação

Para visualizar e modificar as definições de perfil de publicação, selecione **Definições...**.  

![Publicar Página - Botão de Definições]

As suas definições devem ser parecidas com esta:  

![Publicar Definições - Página de ligação]

#### <a name="save-user-name-and-password"></a>Guardar o nome do utilizador e a palavra-passe
- Evite fornecer informações de autenticação sempre que publicar. Para isso, povoe o nome do **utilizador** e os campos **password** e selecione a caixa **de palavra-passe Save.**
- Utilize o botão **Validate Connection** para confirmar que introduziu as informações certas.

#### <a name="deploy-to-clean-web-server"></a>Implementar para limpar servidor web

- Se pretender garantir que o servidor web tem uma cópia limpa da aplicação web após cada upload e que nenhum outro ficheiro é deixado de uma implementação anterior, pode verificar os **ficheiros adicionais remover na** caixa de verificação de destino no separador **Definições.**

- Aviso: A publicação com esta definição elimina todos os ficheiros existentes no servidor web (diretório wwwroot). Certifique-se de que conhece o estado da máquina antes de publicar com esta opção ativada. 

![Publicar Definições - Página de Definições]

## <a name="next-steps"></a>Passos seguintes

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Configurar CI/CD para implantação automatizada para O VM Azure

Para configurar um gasoduto de entrega contínuo com pipelines Azure, consulte [A implantação para uma máquina virtual do Windows](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Página de publicação - Seta direita]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Página de publicação - Ícone da Máquina Virtual do Microsoft Azure]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Selecionador de Conta Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure VM Selector]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy Login]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Erro de certificado]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Janela de saída]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Publicar Página - Publicar botão]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Publicar Página - Botão de Definições]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Publicar Definições - Página de ligação]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Publicar Definições - Página de Definições]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
