---
title: Adicionar um repositório de Git a um laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como adicionar um repositório do GitHub ou o Git de serviços de DevOps do Azure para a sua origem de artefactos personalizados no Azure DevTest Labs.
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 5d7665cbfdf855e194f61910f0c8ee2bce5469b1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852496"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Adicionar um repositório de Git para armazenar artefactos personalizados e modelos do Resource Manager

Pode [criar artefactos personalizados](devtest-lab-artifact-author.md) para as VMs no seu laboratório, ou [utilizar modelos Azure Resource Manager para criar um ambiente de teste personalizada](devtest-lab-create-environment-from-arm.md). Tem de adicionar um repositório de Git privado para os artefactos ou modelos do Resource Manager que cria a sua equipa. O repositório pode ser alojado numa [GitHub](https://github.com) ou no [dos serviços do Azure DevOps](https://visualstudio.com).

Oferecemos uma [repositório do GitHub dos artefactos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) que pode implementar como-é, ou pode personalizá-las para os seus laboratórios. Quando personalizar ou criar um artefato, não é possível armazenar o artefacto no repositório público. Tem de criar seu próprio repositório privado para artefactos personalizados e para artefactos que criar. 

Quando cria uma VM, pode guardar o modelo do Resource Manager, personalize-se de que pretende e, em seguida, utilizá-lo mais tarde para criar mais VMs. Tem de criar seu próprio repositório privado para armazenar os modelos do Gestor de recursos personalizados.  

* Para saber como criar um repositório do GitHub, veja [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Para saber como criar um projeto de serviços de DevOps do Azure que tem um repositório de Git, veja [ligar aos serviços do Azure DevOps](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

A figura a seguir é um exemplo da aparência de um repositório com artefactos no GitHub:  

![Repositório de artefactos do GitHub de exemplo](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Obtenha as informações de repositório e credenciais
Para adicionar um repositório ao seu laboratório, primeiro, obtenha informações da chave do seu repositório. As secções seguintes descrevem como obter as informações necessárias para repositórios que estão alojados no GitHub ou serviços de DevOps do Azure.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obter o URL de clone do repositório de GitHub e o acesso pessoal token

1. Vá para a home page do repositório do GitHub que contém o artefacto ou definições de modelo do Resource Manager.
2. Selecione **Clone or download** (Clonar ou transferir).
3. Para copiar o URL para a área de transferência, selecione o **url de clone de HTTPS** botão. Guarde o URL para utilização posterior.
4. No canto superior direito do GitHub, selecione a imagem de perfil e, em seguida, selecione **definições**.
5. Na **configurações pessoais** menu à esquerda, selecione **tokens de acesso pessoal**.
6. Selecione **gerar novo token**.
7. Sobre o **novo token de acesso pessoal** página, em **Token Descrição**, introduza uma descrição. Aceitar os itens predefinidos sob **selecione os âmbitos**e, em seguida, selecione **gerar Token**.
8. Guarde o token de gerado. Utilizar o token mais tarde.
9. Feche o GitHub.   
10. Avance para o [ligar o seu laboratório para o repositório](#connect-your-lab-to-the-repository) secção.

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Obtenha o clone de repositórios do Azure, URL e o token de acesso pessoal

1. Vá para a home page de sua coleção de equipe (por exemplo, https://contoso-web-team.visualstudio.com)e, em seguida, selecione seu projeto.
2. Na home page do projeto, selecione **código**.
3. Para ver o URL do clone, no projeto **código** página, selecione **Clone**.
4. Guarde o URL. Utilize o URL mais tarde.
5. Para criar um token de acesso pessoal, no menu de lista pendente da conta de utilizador, selecione **meu perfil**.
6. Na página de informações do perfil, selecione **segurança**.
7. Sobre o **Security** separador, selecione **Add**.
8. Sobre o **criar um token de acesso pessoal** página:
   1. Introduza um **Descrição** para o token.
   2. Na **expira em** lista, selecione **180 dias**.
   3. Na **contas** lista, selecione **todas as contas acessíveis**.
   4. Selecione o **todos os âmbitos** opção.
   5. Selecione **criar Token**.
9. O novo token é apresentado na **Tokens de acesso pessoal** lista. Selecione **cópia Token**e, em seguida, guarde o valor do token para utilizar mais tarde.
10. Avance para o [ligar o seu laboratório para o repositório](#connect-your-lab-to-the-repository) secção.

## <a name="connect-your-lab-to-the-repository"></a>Ligar o seu laboratório para o repositório
1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** na lista de serviços.
3. Na lista de laboratórios, selecione o seu laboratório. 
4. Selecione **Konfigurace a zásady** > **repositórios** > **+ adicionar**.

    ![Botão de repositório de adicionar](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. No segundo **repositórios** , especifique as seguintes informações:
   1. **Nome**. Introduza um nome para o repositório.
   2. **Url de Clone de Git**. Introduza o URL de clone de Git HTTPS que copiou anteriormente partir do GitHub ou dos serviços de DevOps do Azure.
   3. **Ramo**. Para obter as definições, introduza o ramo.
   4. **Token de acesso pessoal**. Introduza o token de acesso pessoal que obteve anteriormente do GitHub ou dos serviços de DevOps do Azure.
   5. **Caminhos de pastas**. Introduza pelo menos um caminho de pasta relativo para o URL do clone que contém o artefacto ou definições de modelo do Resource Manager. Quando especificar um subdiretório, certifique-se de que incluir a barra no caminho da pasta.

      ![Área de repositórios](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selecione **Guardar**.

### <a name="related-blog-posts"></a>Postagens de blogs relacionados
* [Resolver problemas com falhas de artefactos no DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Associar uma VM a um domínio do Active Directory existente, utilizando um modelo do Resource Manager no DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos Seguintes
Depois de ter criado o seu repositório de Git privado, pode efetuar um ou ambos dos seguintes, consoante as suas necessidades:
* Store sua [artefactos personalizados](devtest-lab-artifact-author.md). Pode usá-los mais tarde para criar novas VMs.
* [Criar ambientes multi-VM e recursos de PaaS com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md). Em seguida, pode armazenar os modelos no seu repositório privado.

Quando cria uma VM, pode verificar que os modelos ou artefactos são adicionados ao seu repositório Git. Elas serão imediatamente disponibilizadas na lista de modelos ou artefactos. O nome do seu repositório privado é mostrado na coluna que especifica a origem. 
