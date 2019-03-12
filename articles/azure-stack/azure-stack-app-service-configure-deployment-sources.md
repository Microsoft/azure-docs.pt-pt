---
title: Configurar origens de implementação dos serviços de aplicação no Azure Stack | Documentos da Microsoft
description: Como um administrador de serviços pode configurar origens de implementação (Git, GitHub, BitBucket, DropBox e OneDrive) para serviços de aplicações no Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 776648fd5bab2dd4ef51ff4c26936f38d9987e94
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769817"
---
# <a name="configure-deployment-sources"></a>Configurar origens de implementação

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Serviço de aplicações no Azure Stack oferece suporte à implantação de demanda de vários fornecedores de controle de origem. Esta funcionalidade permite aos programadores de aplicações implementar direta a partir de seus repositórios de controlo de origem. Se quiserem que os utilizadores configurar o serviço de aplicações para ligar aos seus repositórios, um operador de nuvem em primeiro lugar tem de configurar a integração entre o serviço de aplicações no Azure Stack e o fornecedor de controlo de origem.  

Para além do local Git, são suportados os seguintes fornecedores de controle de origem:

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Visualizar origens de implementação na administração de serviço de aplicações

1. Inicie sessão no Portal de administração do Azure Stack (https://adminportal.local.azurestack.external) como o administrador de serviços.
2. Navegue até **todos os serviços** e selecione o **serviço de aplicações**.
    ![Administração de fornecedor de recursos do serviço de aplicações][1]
3. Clique em **configuração do controlo de origem**. Pode ver a lista de todas as origens de implementação configurado.
    ![Configuração de controlo de origem de administração de fornecedor de recursos de serviço de aplicações][2]

## <a name="configure-github"></a>Configurar o GitHub

Tem de ter uma conta do GitHub para concluir esta tarefa. Pode querer utilizar uma conta para a sua organização, em vez de uma conta pessoal.

1. Inicie sessão no GitHub, navegue até https://www.github.com/settings/developerse, em seguida, clique em **registar uma nova aplicação**.
    ![GitHub - registar uma nova aplicação][3]
2. Introduza um **nome da aplicação**; por exemplo, **serviço de aplicações no Azure Stack**.
3. Introduza o **URL da home page**. O URL da página tem de ser o endereço de portal do Azure Stack. Por exemplo, https://portal.local.azurestack.external.
4. Introduza um **descrição da aplicação**.
5. Introduza o **URL de chamada de retorno de autorização**. Numa implementação predefinida do Azure Stack, o URL está no formato https://portal.local.azurestack.external/TokenAuthorize. Se estiver executando num domínio diferente, substitua o nome de domínio de local.azurestack.external.
6. Clique em **registar a aplicação**. Uma página é exibida, listando os **ID de cliente** e **segredo do cliente** para a aplicação.
    ![GitHub - registo de aplicação concluído][5]
7.  Num novo separador do browser ou janela, inicie sessão no portal de administração do Azure Stack (https://adminportal.local.azurestack.external) como o administrador de serviços.
8.  Navegue até **fornecedores de recursos**e selecione o **Admin de fornecedor de recursos de serviço de aplicações**.
9. Clique em **configuração do controlo de origem**.
10. Copie e cole o **ID de cliente** e **segredo do cliente** para a entrada correspondente caixas do GitHub.
11. Clique em **Guardar**.

## <a name="configure-bitbucket"></a>Configurar o BitBucket

Tem de ter uma conta do BitBucket para concluir esta tarefa. Pode querer utilizar uma conta para a sua organização, em vez de uma conta pessoal.

1. Inicie sessão no BitBucket e navegue até **integrações** na sua conta.
    ![Dashboard de BitBucket - integrações][7]
2. Clique em **OAuth** sob a gestão de acesso e **Add consumidor**.
    ![BitBucket adicionar consumidor de OAuth][8]
3. Introduza um **Name** para o consumidor; por exemplo, **serviço de aplicações no Azure Stack**.
4. Introduza um **Descrição** para a aplicação.
5. Introduza o **URL de retorno de chamada**. Numa implementação predefinida do Azure Stack, o retorno de chamada URL está no formato https://portal.local.azurestack.external/TokenAuthorize. Se estiver executando num domínio diferente, substitua o nome de domínio de azurestack. Para a integração do BitBucket tenha êxito, o URL tem de seguir a capitalização listada aqui.
6. Introduza o **URL**. Este URL deve ser o portal do Azure Stack URL; Por exemplo, https://portal.local.azurestack.external.
7. Selecione o **permissões** necessário:
    - **Repositórios**: *Leitura*
    - **Webhooks**: *Leitura e escrita*
8. Clique em **Guardar**. Agora, ver esta nova aplicação, juntamente com o **chave** e **segredo**, em **OAuth consumidores**.
    ![Lista de aplicações do BitBucket][9]
9.  Num novo separador do browser ou janela, inicie sessão no portal de administração do Azure Stack (https://adminportal.local.azurestack.external) como o administrador de serviços.
10.  Navegue até **fornecedores de recursos** e selecione o **Admin de fornecedor de recursos de serviço de aplicações**.
11. Clique em **configuração do controlo de origem**.
12. Copie e cole o **chave** para o **ID de cliente** caixa de entrada e **segredo** para o **segredo do cliente** caixas de entrada de BitBucket.
13. Clique em **Guardar**.

## <a name="configure-onedrive"></a>Configurar o OneDrive

Tem de ter um Account da Microsoft associada a uma conta do OneDrive para concluir esta tarefa.  Pode querer utilizar uma conta para a sua organização, em vez de uma conta pessoal.

> [!NOTE]
> OneDrive para contas de empresas não são atualmente suportadas.

1. Navegue até https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm e inicie sessão com a sua Account Microsoft.
2. Sob **meus aplicativos**, clique em **adicionar uma aplicação**.
![Aplicações do OneDrive][10]
3. Introduza um **Name** para o novo registo de aplicação: introduza **serviço de aplicações no Azure Stack**e, em seguida, clique em **Criar aplicação**
4. O ecrã seguinte lista as propriedades de sua nova aplicação. Guardar a **ID da aplicação** para alguns localização temporária.
![Propriedades da aplicação OneDrive][11]
5. Sob **segredos da aplicação**, clique em **gerar nova palavra-passe**. Anote o **nova palavra-passe gerada**. Este é o seu segredo de aplicação e não é recuperável depois de clicar em **OK**.
6. Sob **plataformas**, clique em **adicionar plataforma**e, em seguida, selecione **Web**.
7. Introduza o **URI de redirecionamento**. Numa implementação predefinida do Azure Stack, o URI de redirecionamento está no formato https://portal.local.azurestack.external/TokenAuthorize. Se estiver executando num domínio diferente, substitua o nome de domínio de azurestack.
![Aplicação do OneDrive - adicionar plataforma Web][12]
8. Adicionar a **permissões do Microsoft Graph** - **permissões delegadas**
    - **Files.ReadWrite.AppFolder**
    - **Utilizador. Leitura**  
      ![Aplicação do OneDrive - permissões de gráfico][13]
9. Clique em **Guardar**.
10.  Num novo separador do browser ou janela, inicie sessão no portal de administração do Azure Stack (https://adminportal.local.azurestack.external) como o administrador de serviços.
11.  Navegue até **fornecedores de recursos** e selecione o **Admin de fornecedor de recursos de serviço de aplicações**.
12. Clique em **configuração do controlo de origem**.
13. Copie e cole o **ID da aplicação** para o **ID de cliente** caixa de entrada e **palavra-passe** para o **segredo do cliente** caixa de entrada para o OneDrive.
14. Clique em **Guardar**.

## <a name="configure-dropbox"></a>Configurar o DropBox

> [!NOTE]
> Tem de ter uma conta do DropBox para concluir esta tarefa. Pode querer utilizar uma conta para a sua organização, em vez de uma conta pessoal.

1. Navegue até https://www.dropbox.com/developers/apps e inicie sessão com as credenciais da conta DropBox.
2. Clique em **Criar aplicação**.

    ![Aplicativos do Dropbox][14]

3. Selecione **DropBox API**.
4. Defina o acesso de nível para **pasta de aplicação**.
5. Introduza um **nome** para a sua aplicação.
![Registo de aplicação do Dropbox][15]
6. Clique em **Criar aplicação**. É apresentada uma página que lista as definições da aplicação, incluindo **chave da aplicação** e **segredo da aplicação**.
7. Certifique-se de que o **nome da pasta de aplicação** está definida como **serviço de aplicações no Azure Stack**.
8. Definir o **URI de redirecionamento OAuth 2** e, em seguida, clique em **Add**. Numa implementação predefinida do Azure Stack, o URI de redirecionamento está no formato https://portal.local.azurestack.external/TokenAuthorize. Se estiver executando num domínio diferente, substitua o seu domínio para azurestack.
![Configuração de aplicação do Dropbox][16]
9.  Num novo separador do browser ou janela, inicie sessão no portal de administração do Azure Stack (https://adminportal.local.azurestack.external) como o administrador de serviços.
10.  Navegue até **fornecedores de recursos** e selecione o **Admin de fornecedor de recursos de serviço de aplicações**.
11. Clique em **configuração do controlo de origem**.
12. Copie e cole o **chave da aplicação** para o **ID de cliente** caixa de entrada e **segredo da aplicação** para o **segredo do cliente** caixa de entrada para a DropBox.
13. Clique em **Guardar**.

## <a name="next-steps"></a>Passos Seguintes

Os utilizadores podem agora utilizar as origens de implementação para itens como [implementação contínua](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment), [implementação de Git local](https://docs.microsoft.com/azure/app-service/deploy-local-git), e [sincronização da pasta de nuvem](https://docs.microsoft.com/azure/app-service/deploy-content-sync).

<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png
