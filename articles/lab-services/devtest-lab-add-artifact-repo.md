---
title: Adicionar um repositório git a um laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como adicionar um repositório do GitHub ou Azure DevOps Services git para sua fonte de artefatos personalizados no Azure DevTest Labs.
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
ms.openlocfilehash: 1555eb4e48a0cf43a38aa811e20ffbbed8ee87a9
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755822"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Adicionar um repositório Git para armazenar artefatos personalizados e modelos do Resource Manager

Você pode [criar artefatos personalizados](devtest-lab-artifact-author.md) para as VMs em seu laboratório ou [usar Azure Resource Manager modelos para criar um ambiente de teste personalizado](devtest-lab-create-environment-from-arm.md). Você deve adicionar um repositório git privado para os artefatos ou modelos do Resource Manager que sua equipe cria. O repositório pode ser hospedado no [GitHub](https://github.com) ou em [Azure DevOps Services](https://visualstudio.com).

Oferecemos um [repositório GitHub de artefatos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) que podem ser implantados no estado em que se encontram ou você pode personalizá-los para seus laboratórios. Ao personalizar ou criar um artefato, você não pode armazenar o artefato no repositório público. Você deve criar seu próprio repositório privado para artefatos personalizados e para artefatos que você criar. 

Ao criar uma VM, você pode salvar o modelo do Resource Manager, personalizá-lo, se desejar, e usá-lo posteriormente para criar mais VMs. Você deve criar seu próprio repositório privado para armazenar seus modelos personalizados do Resource Manager.  

* Para saber como criar um repositório GitHub, consulte [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Para saber como criar um projeto Azure DevOps Services que tenha um repositório git, consulte [conectar-se ao Azure DevOps Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

A figura a seguir é um exemplo de como um repositório com artefatos pode parecer no GitHub:  

![Repositório de artefatos do GitHub de exemplo](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Obter as informações e as credenciais do repositório
Para adicionar um repositório ao seu laboratório, primeiro, obtenha informações de chave do seu repositório. As seções a seguir descrevem como obter informações necessárias para repositórios hospedados no GitHub ou Azure DevOps Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obter a URL de clone do repositório do GitHub e o token de acesso pessoal

1. Vá para a home page do repositório GitHub que contém as definições de modelo de artefato ou do Resource Manager.
2. Selecione **Clone or download** (Clonar ou transferir).
3. Para copiar a URL para a área de transferência, selecione o botão **URL de clone https** . Salve a URL para uso posterior.
4. No canto superior direito do GitHub, selecione a imagem do perfil e, em seguida, selecione **configurações**.
5. No menu **configurações pessoais** à esquerda, selecione **tokens de acesso pessoal**.
6. Selecione **gerar novo token**.
7. Na página **novo token de acesso pessoal** , em **Descrição do token**, insira uma descrição. Aceite os itens padrão em **selecionar escopos**e, em seguida, selecione **gerar token**.
8. Salve o token gerado. Você usará o token mais tarde.
9. Feche o GitHub.   
10. Vá para a seção [conectar seu laboratório ao repositório](#connect-your-lab-to-the-repository) .

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Obter a URL de clone de Azure Repos e o token de acesso pessoal

1. Vá para a home page da sua coleção de equipe (por exemplo, https://contoso-web-team.visualstudio.com) e, em seguida, selecione seu projeto.
2. No home page do projeto, selecione **código**.
3. Para exibir a URL de clone, na página de **código** do projeto, selecione **clonar**.
4. Salve a URL. Você usará a URL mais tarde.
5. Para criar um token de acesso pessoal, no menu suspenso conta de usuário, selecione **meu perfil**.
6. Na página informações do perfil, selecione **segurança**.
7. Na guia **segurança** , selecione **Adicionar**.
8. Na página **criar um token de acesso pessoal** :
   1. Insira uma **Descrição** para o token.
   2. Na lista **expira em** , selecione **180 dias**.
   3. Na lista **contas** , selecione **todas as contas acessíveis**.
   4. Selecione a opção **somente leitura** .
   5. Selecione **criar token**.
9. O novo token aparece na lista de **tokens de acesso pessoal** . Selecione **copiar token**e, em seguida, salve o valor do token para uso posterior.
10. Vá para a seção [conectar seu laboratório ao repositório](#connect-your-lab-to-the-repository) .

## <a name="connect-your-lab-to-the-repository"></a>Conectar seu laboratório ao repositório
1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** na lista de serviços.
3. Na lista de laboratórios, selecione seu laboratório. 
4. Selecione **configuração e políticas**  > **repositórios**  >  **+ Adicionar**.

    ![O botão Adicionar repositório](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na segunda página **repositórios** , especifique as seguintes informações:
   1. **Nome**. Insira um nome para o repositório.
   2. **URL de git clone**. Insira a URL de clone HTTPS do git que você copiou anteriormente do GitHub ou Azure DevOps Services.
   3. **Ramificação**. Para obter suas definições, insira a ramificação.
   4. **Token de acesso pessoal**. Insira o token de acesso pessoal que você obteve anteriormente do GitHub ou Azure DevOps Services.
   5. **Caminhos de pasta**. Insira pelo menos um caminho de pasta relativo à URL de clone que contém suas definições de modelo de artefato ou do Resource Manager. Ao especificar um subdiretório, certifique-se de incluir a barra no caminho da pasta.

      ![Área de repositórios](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selecione **Guardar**.

### <a name="related-blog-posts"></a>Postagens de blog relacionadas
* [Solucionar problemas de artefatos com falha no DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Ingressar uma VM em um domínio Active Directory existente usando um modelo do Resource Manager no DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de criar seu repositório git privado, você pode executar um dos seguintes procedimentos, dependendo de suas necessidades:
* Armazene seus [artefatos personalizados](devtest-lab-artifact-author.md). Você pode usá-los posteriormente para criar novas VMs.
* [Crie ambientes de várias VMs e recursos de PaaS usando modelos do Resource Manager](devtest-lab-create-environment-from-arm.md). Em seguida, você pode armazenar os modelos em seu repositório privado.

Ao criar uma VM, você pode verificar se os artefatos ou modelos são adicionados ao repositório git. Eles ficam imediatamente disponíveis na lista de artefatos ou modelos. O nome do seu repositório privado é mostrado na coluna que especifica a origem. 
