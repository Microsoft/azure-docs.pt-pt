---
title: Configure contas de laboratório nos Serviços de Laboratório Azure  Microsoft Docs
description: Este artigo descreve como criar uma conta de laboratório, ver todas as contas de laboratório ou apagar uma conta de laboratório nos Serviços de Laboratório Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 37a657093fd55ce752095417fe744f83946962db
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210585"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configure contas de laboratório em Serviços de Laboratório Azure 
Nos Serviços de Laboratório Azure, uma conta de laboratório é um recipiente para tipos de laboratório geridos, como laboratórios de sala de aula. Um administrador cria uma conta de laboratório com os Serviços de Laboratório Azure e dá acesso aos donos de laboratório que podem criar laboratórios na conta. Este artigo descreve como criar uma conta de laboratório, ver todas as contas do laboratório ou apagar uma conta de laboratório.

## <a name="connect-with-a-peer-virtual-network"></a>Conecte-se com uma rede virtual de pares
Para ligar uma rede virtual como uma rede de pares à rede virtual do laboratório, siga estes passos:

1. Na página **da Conta lab,** selecione **a configuração labs** no menu esquerdo.

    ![Página de configuração de laboratórios](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Para **a rede virtual Peer,** selecione **Ativado** ou **Desativado**. O valor predefinido é **desativado**. Para ativar a rede virtual de pares, faça os seguintes passos: 
    1. Selecione **Ativado**.
    2. Selecione o **VNet** da lista de lançamentos. 
3. Selecione **Guardar** na barra de ferramentas. 

Os laboratórios criados nesta conta estão ligados à rede virtual selecionada. Podem aceder aos recursos na rede virtual selecionada. Para mais informações, consulte [Connect a rede do seu laboratório com uma rede virtual de pares nos Serviços de Laboratório Azure](how-to-connect-peer-virtual-network.md).

Ao selecionar uma rede virtual para o campo de **rede virtual Peer,** o criador do **laboratório permitir escolher a** opção de localização do laboratório é desativado. É porque os laboratórios na conta do laboratório devem estar na mesma região que o laboratório conta para eles se conectarem com os recursos na rede virtual de pares. 

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Especifique um intervalo de endereços para VMs no laboratório
O procedimento seguinte tem medidas para especificar um intervalo de endereços para VMs no laboratório. Se atualizar a gama que especificou anteriormente, a gama de endereços modificada aplica-se apenas aos VMs que são criados após a alteração. 

Aqui estão algumas restrições ao especificar o intervalo de endereços que deve ter em mente. 

- O prefixo deve ser menor ou igual a 23. 
- Se uma rede virtual for espreitada para a conta de laboratório, o intervalo de endereços fornecido não pode sobrepor-se ao intervalo de endereços da rede virtual peered.

1. Na página **da Conta lab,** selecione **a configuração labs** no menu esquerdo.
2. Para o campo **de intervalo de endereços,** especifique o intervalo de endereços para VMs que serão criados em laboratório. A gama de endereços deve constar da notação de encaminhamento inter-domínio sem classe (CIDR) (exemplo: 10.20.0.0/23). Máquinas virtuais no laboratório serão criadas nesta gama de endereços.
3. Selecione **Guardar** na barra de ferramentas. 

    ![Configure gama de endereços](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um utilizador à função de Criador do Laboratório
Para configurar um laboratório de sala de aula numa conta de laboratório, o utilizador tem de ser membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Se estiver a planear utilizar a mesma conta de utilizador para criar um laboratório de sala de aula, pode ignorar este passo. Para utilizar outra conta de utilizador para criar um laboratório de sala de aula, siga os passos abaixo: 

Para dar permissão aos educadores para criarem laboratórios para as classes deles, adicione-os à função **Criador do Laboratório**:

1. Na página **da Conta Lab,** selecione o controlo de **acesso (IAM)** e clique + Adicione a atribuição de **funções** na barra de ferramentas. 

    ![Controlo de Acesso -> Adicionar Botão de Atribuição de Funções](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página de atribuição de **funções Add,** selecione **Lab Creator** para **Função**, selecione o utilizador que pretende adicionar à função De Criadores de Laboratório e selecione **Save**. 

    ![Adicionar criador de laboratório](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Se estiver a adicionar um utilizador de conta não Microsoft como criador de laboratório, consulte o utilizador da conta Add a non Microsoft como uma secção de criador de [laboratório.](#add-a-non-microsoft-account-user-as-a-lab-creator) 

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Especificar imagens do Marketplace disponíveis para criadores de laboratório
Enquanto proprietário de uma conta de laboratório, pode especificar as imagens do Marketplace que os criadores de laboratórios podem utilizar para criar laboratórios nessa conta. 

1. Selecione **Imagens do Marketplace** no menu à esquerda. Por predefinição, verá a lista completa de imagens (ativadas e desativadas). Pode filtrar a lista para ver apenas as imagens ativadas/desativadas ao selecionar a opção **Apenas ativado**/**Apenas desativado** na lista pendente na parte superior. 
    
    ![Página de imagens do Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    As imagens do Marketplace que aparecem na lista são as que satisfazem as seguintes condições:
        
    - Cria uma VM individual.
    - Utiliza o Azure Resource Manager para aprovisionar VMs
    - Não requer a compra de planos de licenciamento extra
2. Para **desativar** uma imagem do Marketplace que tenha sido ativada, efetue uma das seguintes ações: 
    1. Selecione **... (reticências)** na última coluna e selecione **Desativar imagem**. 

        ![Desativar uma imagem](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Selecione uma ou mais imagens a partir da lista ao selecionar as caixas de verificação antes dos nomes de imagem na lista e selecione **Desativar imagens selecionadas**. 

        ![Desativar várias imagens](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. De forma semelhante, para **ativar** uma imagem do Marketplace, efetue uma das seguintes ações: 
    1. Selecione **... (reticências)** na última coluna e selecione **Ativar imagem**. 
    2. Selecione uma ou mais imagens a partir da lista ao selecionar as caixas de verificação antes dos nomes de imagem na lista e selecione **Ativar imagens selecionadas**. 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Adicione um utilizador de conta não Microsoft como criador de laboratório
Para adicionar um utilizador como criador de laboratório, utiliza as suas contas de e-mail. Podem ser utilizados os seguintes tipos de contas de e-mail:

- Uma conta de e-mail fornecida pelo Office 365 Azure Ative Directory (AAD) da sua universidade. 
- Uma conta de e-mail da Microsoft, como `@outlook.com`, `@hotmail.com`, `@msn.com`ou `@live.com`.
- Uma conta de e-mail não-Microsoft, como a fornecida pela Yahoo ou pela Google. No entanto, este tipo de contas deve estar ligada a uma conta Microsoft.
- Uma conta do GitHub. Esta conta deve estar ligada a uma conta Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Usando uma conta de e-mail não Microsoft
Criadores de laboratório/instrutores podem usar contas de e-mail não microsoft para se registar e iniciar sessão num laboratório de sala de aula.  No entanto, o sessão no portal lab Services requer que os instrutores criem primeiro uma conta Microsoft que esteja ligada ao seu endereço de e-mail não Microsoft.

Muitos instrutores podem já ter uma conta Microsoft ligada aos seus endereços de e-mail não Microsoft. Por exemplo, os instrutores já têm uma conta Microsoft se usaram o seu endereço de e-mail com outros produtos ou serviços da Microsoft, tais como Office, Skype, OneDrive ou Windows.  

Quando os instrutores assinam no portal dos Serviços de Laboratório, são solicitados para o seu endereço de e-mail e senha. Se o instrutor tentar iniciar sessão com uma conta não Microsoft que não tenha uma conta Microsoft ligada, o instrutor receberá a seguinte mensagem de erro: 

![Mensagem de erro](../media/how-to-configure-student-usage/cant-find-account.png)

Para se inscrever em uma conta Microsoft, os instrutores devem ir para [http://signup.live.com](http://signup.live.com).  


### <a name="using-a-github-account"></a>Usando uma conta GitHub
Os instrutores também podem usar uma conta GitHub existente para se registar e iniciar sessão num laboratório de sala de aula. Se o instrutor já tiver uma conta Microsoft ligada à sua conta GitHub, então pode iniciar sessão e fornecer a sua palavra-passe, como mostrado na secção anterior. Se ainda não ligaram a sua conta GitHub a uma conta Microsoft, devem selecionar **opções de Iniciar sessão:**

![Ligação de opções de insessão](../media/how-to-configure-student-usage/signin-options.png)

Na página de **opções de iniciar** sessão, selecione **Iniciar sessão com o GitHub**.

![Inscreva-se com link GitHub](../media/how-to-configure-student-usage/signin-github.png)

Finalmente, são solicitados a criar uma conta Microsoft que esteja ligada à sua conta GitHub. Acontece automaticamente quando o instrutor seleciona **Seguinte**.  O instrutor é imediatamente assinado e ligado ao laboratório da sala de aula.

## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Paragem automática de VMs na desconexão
Pode ativar ou desativar o encerramento automático de VMs de laboratório do Windows (modelo ou aluno) depois de uma ligação remota de ambiente de trabalho ser desligada. Também pode especificar quanto tempo os VMs devem esperar que o utilizador se reconecte antes de desligar automaticamente.

![Definição automática de paragem na conta de laboratório](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Esta definição aplica-se a todos os laboratórios criados na conta do laboratório. Um dono de laboratório pode anular este cenário ao nível do laboratório. A alteração desta configuração na conta do laboratório só afetará os laboratórios que são criados após a mudança.

Para saber como um dono de laboratório pode configurar este cenário a nível de laboratório, consulte [este artigo](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como dono de laboratório, crie e gere a gestão de laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de laboratório, configura risa e publica modelos](how-to-create-manage-template.md)
- [Como dono de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como utilizador de laboratório, aceda a laboratórios de sala de aula](how-to-use-classroom-lab.md)
