---
title: Adicione um repositório de Git a um laboratório em Azure DevTest Labs | Microsoft Docs
description: Saiba como adicionar um repositório GitHub ou Azure DevOps Git para a sua fonte de artefactos personalizados em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 225aae5e0a017b711f29a47829b06f8bb7a6a8ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483147"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Adicione um repositório Git para armazenar artefactos personalizados e modelos de Gestor de Recursos

Pode [criar artefactos personalizados](devtest-lab-artifact-author.md) para os VMs no seu laboratório ou [utilizar modelos do Azure Resource Manager para criar um ambiente de teste personalizado.](devtest-lab-create-environment-from-arm.md) Você deve adicionar um repositório git privado para os artefactos ou modelos de Gestor de Recursos que a sua equipa cria. O repositório pode ser hospedado no [GitHub](https://github.com) ou nos [Serviços Azure DevOps](https://visualstudio.com).

Oferecemos um [repositório de artefactos GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) que pode implementar como está, ou pode personalizá-los para os seus laboratórios. Quando se personaliza ou cria um artefacto, não se pode armazenar o artefacto no repositório público. Você deve criar o seu próprio repo privado para artefactos personalizados e para artefactos que você cria. 

Quando criar um VM, pode guardar o modelo de Gestor de Recursos, personalizá-lo se quiser e, em seguida, usá-lo mais tarde para criar mais VMs. Você deve criar o seu próprio repositório privado para armazenar os seus modelos personalizados de Gestor de Recursos.  

* Para aprender a criar um repositório GitHub, consulte [o GitHub Bootcamp.](https://help.github.com/categories/bootcamp/)
* Para aprender a criar um projeto Azure DevOps Services que tenha um repositório Git, consulte [connect to Azure DevOps Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

A seguinte figura é um exemplo de como um repositório que tem artefactos pode parecer no GitHub:  

![Prove gitHub artefactos repo](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Obtenha as informações e credenciais do repositório
Para adicionar um repositório ao seu laboratório, primeiro, obtenha informações chave do seu repositório. As secções seguintes descrevem como obter informações necessárias para repositórios que estão hospedados nos Serviços GitHub ou Azure DevOps.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obtenha o URL do clone do repositório GitHub e o token de acesso pessoal

1. Vá à página inicial do repositório GitHub que contém as definições de modelo de artefacto ou gestor de recursos.
2. Selecione **Clone or download** (Clonar ou transferir).
3. Para copiar o URL para a área de transferência, selecione o botão **url de clone HTTPS.** Guarde o URL para utilização posterior.
4. No canto superior direito do GitHub, selecione a imagem de perfil e, em seguida, selecione **Definições**.
5. No menu **de definições pessoais** à esquerda, selecione **Fichas de acesso pessoal**.
6. **Selecione Gerer novo símbolo**.
7. Na página **de token de acesso pessoal Novo,** sob **descrição de Token,** insira uma descrição. Aceite os itens predefinidos nos **âmbitos Select** e, em seguida, selecione **Gerar Token**.
8. Salve o símbolo gerado. Usas o símbolo mais tarde.
9. Feche o GitHub.   
10. Continue até ao laboratório Ligar o seu laboratório à secção [de repositório.](#connect-your-lab-to-the-repository)

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Obtenha o URL clone do Azure Repos e o token de acesso pessoal

1. Vá à página inicial da sua coleção de equipas (por exemplo, `https://contoso-web-team.visualstudio.com` e, em seguida, selecione o seu projeto.
2. Na página inicial do projeto, selecione **Código.**
3. Para ver o URL do clone, na página **código** do projeto, selecione **Clone**.
4. Salve a URL. Use a URL mais tarde.
5. Para criar um token de acesso pessoal, no menu suspenso da conta de utilizador, selecione **O meu perfil**.
6. Na página de informações de perfil, selecione **Security**.
7. No **separador Segurança,** **selecione Adicionar**.
8. Na página **'Criar' Um símbolo de acesso pessoal:**
   1. Insira uma **descrição** para o token.
   2. Na lista **Expira,** selecione **180 dias**.
   3. Na lista **de Contas,** selecione **Todas as contas acessíveis**.
   4. Selecione a opção **Read Only.**
   5. **Selecione Criar Ficha**.
9. O novo símbolo aparece na lista de **Tokens de Acesso Pessoal.** Selecione **Copy Token** e, em seguida, guarde o valor simbólico para utilização posterior.
10. Continue até ao laboratório Ligar o seu laboratório à secção [de repositório.](#connect-your-lab-to-the-repository)

## <a name="connect-your-lab-to-the-repository"></a>Ligue o seu laboratório ao repositório
1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços** e, em seguida, selecione **DevTest Labs** da lista de serviços.
3. Da lista de laboratórios, selecione o seu laboratório. 
4. Selecione **Configuração e políticas**  >  **Repositórios**  >  **+ Adicionar**.

    ![O botão de repositório adicionar](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na segunda página de **Repositórios,** especifique as seguintes informações:
   1. **Nome**. Insira um nome para o repositório.
   2. **Url Git Clone**. Introduza o URL de clone do Git HTTPS que copiou anteriormente dos Serviços GitHub ou Azure DevOps.
   3. **Filial**. Para obter as suas definições, entre no ramo.
   4. **Ficha de Acesso Pessoal**. Insira o sinal de acesso pessoal que obteve anteriormente dos Serviços GitHub ou Azure DevOps.
   5. **Caminhos de pasta**. Introduza pelo menos um caminho de pasta em relação ao URL do clone que contém as definições do seu modelo de artefacto ou gestor de recursos. Quando especificar uma subdiretória, certifique-se de que inclui o corte dianteiro no caminho da pasta.

      ![Área de repositórios](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selecione **Guardar**.

### <a name="related-blog-posts"></a>Publicações de blogs relacionadas
* [Resolução de problemas falha artefactos em Laboratórios DevTest](devtest-lab-troubleshoot-artifact-failure.md)
* [Junte um VM a um domínio de Diretório Ativo existente usando um modelo de Gestor de Recursos em Laboratórios DevTest](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de ter criado o seu repositório privado git, pode fazer um ou ambos os seguintes, dependendo das suas necessidades:
* Guarde os seus [artefactos personalizados.](devtest-lab-artifact-author.md) Pode usá-los mais tarde para criar novos VMs.
* [Crie ambientes multi-VM e recursos PaaS utilizando modelos de Gestor de Recursos.](devtest-lab-create-environment-from-arm.md) Em seguida, pode armazenar os modelos no seu repo privado.

Quando criar um VM, pode verificar se os artefactos ou modelos são adicionados ao seu repositório Git. Estão imediatamente disponíveis na lista de artefactos ou modelos. O nome do seu repo privado é mostrado na coluna que especifica a fonte. 
