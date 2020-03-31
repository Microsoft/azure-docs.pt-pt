---
title: Adicione um repositório Git a um laboratório em Azure DevTest Labs [ Microsoft Docs
description: Aprenda a adicionar um repositório GitHub ou Azure DevOps Services Git para a sua fonte de artefactos personalizados em Azure DevTest Labs.
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
ms.openlocfilehash: 1e7587c60e180fb35e1a2bed735b053b6b0c388a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294612"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Adicione um repositório Git para armazenar artefactos personalizados e modelos de Gestor de Recursos

Você pode [criar artefactos personalizados](devtest-lab-artifact-author.md) para os VMs no seu laboratório, ou [usar modelos de Gestor de Recursos Azure para criar um ambiente](devtest-lab-create-environment-from-arm.md)de teste personalizado . Você deve adicionar um repositório git privado para os artefactos ou modelos de Gestor de Recursos que a sua equipa cria. O repositório pode ser hospedado no [GitHub](https://github.com) ou nos [Serviços Azure DevOps.](https://visualstudio.com)

Oferecemos um [repositório GitHub de artefactos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) que pode implementar como está, ou pode personalizá-los para os seus laboratórios. Quando se personaliza ou cria um artefacto, não se pode armazenar o artefacto no repositório público. Você deve criar seu próprio repo privado para artefactos personalizados e para artefactos que você cria. 

Quando criar um VM, pode guardar o modelo de Gestor de Recursos, personalizá-lo se quiser e depois usá-lo mais tarde para criar mais VMs. Você deve criar o seu próprio repositório privado para armazenar os seus modelos personalizados de Gestor de Recursos.  

* Para aprender a criar um repositório GitHub, consulte [gitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Para aprender a criar um projeto Azure DevOps Services que tenha um repositório Git, consulte [Connect to Azure DevOps Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

A figura que se segue é um exemplo de como um repositório que tem artefactos pode parecer no GitHub:  

![Amostra GitHub artefactos repo](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Obtenha as informações e credenciais do repositório
Para adicionar um repositório ao seu laboratório, primeiro, obtenha informações chave do seu repositório. As seguintes secções descrevem como obter informações necessárias para repositórios que estão hospedados nos Serviços GitHub ou Azure DevOps.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obtenha o URL de clone de clone do gitHub e o token de acesso pessoal

1. Vá à página inicial do repositório GitHub que contém as definições de modelo de artefacto ou gestor de recursos.
2. Selecione **Clone or download** (Clonar ou transferir).
3. Para copiar o URL para a área de cópia, selecione o botão de url do **clone HTTPS.** Guarde o URL para posterior utilização.
4. No canto superior direito do GitHub, selecione a imagem de perfil e, em seguida, selecione **Definições**.
5. No menu de **definições pessoais** à esquerda, selecione **fichas**de acesso pessoal .
6. **Selecione Gerar novo símbolo**.
7. Na nova página de ficha de **acesso pessoal,** sob **a descrição de Token,** introduza uma descrição. Aceite os itens predefinidos em **âmbitos Select**, e, em seguida, selecione **Generate Token**.
8. Guarde o símbolo gerado. Usa o símbolo mais tarde.
9. Feche o GitHub.   
10. Continue a ligar o seu laboratório à secção de [repositório.](#connect-your-lab-to-the-repository)

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Obtenha o URL clone azure Repos e ficha de acesso pessoal

1. Vá à página inicial da sua coleção `https://contoso-web-team.visualstudio.com`de equipas (por exemplo, e depois selecione o seu projeto.
2. Na página inicial do projeto, selecione **Código**.
3. Para visualizar o URL do clone, na página **código** do projeto, selecione **Clone**.
4. Guarde a URL. Usa o URL mais tarde.
5. Para criar um sinal de acesso pessoal, no menu de entrega da conta de utilizador, **selecione O meu perfil**.
6. Na página de informações do perfil, selecione **Segurança**.
7. No separador **Segurança,** selecione **Adicionar**.
8. Na página **criar um token** de acesso pessoal:
   1. Insira uma **descrição** para o símbolo.
   2. Na lista **Expira,** selecione **180 dias**.
   3. Na lista **de Contas,** selecione **Todas as contas acessíveis**.
   4. Selecione a opção **Ler Apenas.**
   5. Selecione **Criar Token**.
9. O novo símbolo aparece na lista de Fichas de **Acesso Pessoal.** Selecione **Copy Token**e, em seguida, guarde o valor do símbolo para utilização posterior.
10. Continue a ligar o seu laboratório à secção de [repositório.](#connect-your-lab-to-the-repository)

## <a name="connect-your-lab-to-the-repository"></a>Ligue o seu laboratório ao repositório
1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços**e, em seguida, selecione **DevTest Labs** da lista de serviços.
3. Da lista de laboratórios, selecione o seu laboratório. 
4. **Selecione Configuração e políticas** > **Repositórios** > **+ Adicionar**.

    ![Botão de repositório Adicionar](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na segunda página dos **Repositórios,** especifique as seguintes informações:
   1. **Nome.** Insira um nome para o repositório.
   2. **Git Clone Url**. Introduza o URL de clone Git HTTPS que copiou anteriormente dos Serviços GitHub ou Azure DevOps.
   3. **Ramo.** Para obter as suas definições, entre no ramo.
   4. Ficha de **Acesso Pessoal.** Introduza o sinal de acesso pessoal que obteve mais cedo dos Serviços GitHub ou Azure DevOps.
   5. **Caminhos da Pasta**. Introduza pelo menos um caminho de pasta em relação ao URL do clone que contém as definições do seu artefacto ou do modelo do Gestor de Recursos. Quando especificar um subdiretório, certifique-se de que inclui o corte dianteiro no caminho da pasta.

      ![Área dos repositórios](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selecione **Guardar**.

### <a name="related-blog-posts"></a>Posts de blog relacionados
* [Problemas falham artefactos em Laboratórios DevTest](devtest-lab-troubleshoot-artifact-failure.md)
* [Junte-se a um VM a um domínio de Diretório Ativo existente usando um modelo de Gestor de Recursos em Laboratórios DevTest](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de ter criado o seu repositório privado Git, pode fazer um ou ambos os seguintes, dependendo das suas necessidades:
* Guarde os seus [artefactos personalizados.](devtest-lab-artifact-author.md) Pode usá-los mais tarde para criar novos VMs.
* [Crie ambientes multi-VM e recursos PaaS utilizando modelos](devtest-lab-create-environment-from-arm.md)de Gestor de Recursos . Depois, pode armazenar os modelos no seu repo privado.

Quando criar um VM, pode verificar se os artefactos ou modelos são adicionados ao seu repositório Git. Estão imediatamente disponíveis na lista de artefactos ou modelos. O nome do seu repo privado é mostrado na coluna que especifica a fonte. 
