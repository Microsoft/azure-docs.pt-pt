---
title: Publicar uma aplicação Web para uma VM do Azure a partir do Visual Studio
description: Publicar uma aplicação ASP.NET Web para uma Máquina Virtual do Azure a partir do Visual Studio
services: virtual-machines-windows
author: ghogen
manager: douge
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 5298721b7f60cec677c22faf1b35011aab02defb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60204421"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publicar uma aplicação ASP.NET Web para uma VM do Azure a partir do Visual Studio

Este documento descreve como publicar uma aplicação web do ASP.NET para uma máquina virtual do Azure (VM) a utilizar o **máquinas virtuais do Microsoft Azure** funcionalidade de publicação no Visual Studio 2017.  

## <a name="prerequisites"></a>Pré-requisitos
Para usar o Visual Studio para publicar um projeto do ASP.NET para uma VM do Azure, a VM tem de ser corretamente definida.

- Máquina tem de ser configurada para executar uma aplicação web ASP.NET e WebDeploy instalado.

- A VM tem de ter um nome DNS configurado. Para obter mais informações, consulte [criar um nome de domínio completamente qualificado no portal do Azure para uma VM do Windows](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publicar a aplicação web ASP.NET para a VM do Azure com o Visual Studio
A seguinte secção descreve como publicar uma aplicação web ASP.NET existente para uma máquina virtual do Azure.

1. Abra a sua solução de aplicação web no Visual Studio 2017.
2. Com o botão direito do rato no Explorador de soluções e escolha **publicar...**
3. Use a seta à direita da página para percorrer as opções de publicação até encontrar **máquinas virtuais do Microsoft Azure**.  

   ![Publicar Page - seta para a direita]

4. Selecione o **máquinas virtuais do Microsoft Azure** ícone e selecione **Publish**.

   ![Publicar Page - ícone de Máquina Virtual do Microsoft Azure]

5. Escolha a conta apropriada (com a subscrição do Azure ligada à máquina virtual).  
   - Se tem sessão iniciada Visual Studio, a lista de conta é preenchida com todas as suas contas autenticadas.  
   - Se não tiver entrado, ou se a conta de que precisa não estiver listada, escolha "Adicionar uma conta..." e siga as instruções para iniciar sessão.  
   ![Seletor de conta do Azure]  

6. Selecione a VM adequada na lista de máquinas de virtuais existentes.

   > [!Note]
   > Preencher essa lista pode demorar algum tempo.

   ![Seletor VM do Azure]

7. Clique em OK para começar a publicar.

8. Quando lhe forem pedidas credenciais, forneça o nome de utilizador e palavra-passe de uma conta de utilizador na VM que está configurado com a publicação de direitos (normalmente, o nome de utilizador do administrador e palavra-passe utilizada ao criar a VM) de destino.  

   ![Início de sessão de WebDeploy]

9. Aceite o certificado de segurança.

   ![Erro de certificado]

10. Assista a janela de saída para verificar o progresso da operação de publicação.

    ![Janela de saída]

11. Se publicação for bem sucedida, inicia um browser para abrir o URL do site publicado recentemente.

**Êxito!**

Agora publicou com êxito a aplicação web a uma máquina virtual do Azure.

## <a name="publish-page-options"></a>Página Opções de publicação

Depois de concluir o Assistente de publicação, a página de publicação é aberta o documento bem com o novo perfil de publicação selecionado.

### <a name="re-publish"></a>Voltar a publicar

Para publicar atualizações à sua aplicação web, selecione o **publicar** botão na página de publicação.  
- Se lhe for pedido, introduza o nome de utilizador e palavra-passe.  
- Publicação começa de imediato.

![Página de publicação - botão publicar]

### <a name="modify-publish-profile-settings"></a>Modificar definições de perfil de publicação

Para ver e modificar as definições de perfil de publicação, selecione **definições...** .  

![Publicar Page - botão Definições]

As definições de devem ter um aspeto semelhante ao seguinte:  

![Definições - página de ligação de publicação]

#### <a name="save-user-name-and-password"></a>Guarde o nome de utilizador e palavra-passe
- Para evitar fornecer informações de autenticação, sempre que publicar, que pode preencher o **nome de utilizador** e **palavra-passe** campos e selecione o **guardar palavra-passe** caixa.
- Utilize o **validar ligação** botão para confirmar que introduziu as informações corretas.

#### <a name="deploy-to-clean-web-server"></a>Implementar para limpar o servidor web

- Se pretender certificar-se de que o servidor web tem uma cópia limpa do aplicativo web após cada carregamento (e que não existem outros ficheiros restantes desligar-se em torno de uma implementação anterior), pode verificar o **remover ficheiros adicionais no destino** caixa de seleção de **definições** separador.

- Aviso: Publicação com esta definição elimina todos os ficheiros que existem no servidor web (diretório wwwroot). Certifique-se de que sabe o estado da máquina antes de publicar com esta opção ativada. 

![Definições - página de definições de publicação]

## <a name="next-steps"></a>Passos Seguintes

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Configurar CI/CD para implantação automatizada para VM do Azure

Para definir um pipeline de entrega contínua com Pipelines do Azure, veja [implementar uma máquina de Virtual do Windows](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Publicar Page - seta para a direita]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Publicar Page - ícone de Máquina Virtual do Microsoft Azure]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Seletor de conta do Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Seletor VM do Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Início de sessão de WebDeploy]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Erro de certificado]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Janela de saída]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Página de publicação - botão publicar]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Publicar Page - botão Definições]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Definições - página de ligação de publicação]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Definições - página de definições de publicação]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
