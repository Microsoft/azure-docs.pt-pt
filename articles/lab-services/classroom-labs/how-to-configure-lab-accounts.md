---
title: Configurar contas de laboratório no Azure Lab Services | Microsoft Docs
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
ms.openlocfilehash: 597bbfdc35c556b5c75968084c01ac222fc468f4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718084"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configurar contas de laboratório no Azure Lab Services 
No Azure Lab Services, uma conta de laboratório é um contêiner para tipos de laboratório gerenciados, como laboratórios de sala de aula. Um administrador configura uma conta de laboratório com Azure Lab Services e fornece acesso a proprietários de laboratório que podem criar laboratórios na conta. Este artigo descreve como criar uma conta de laboratório, exibir todas as contas de laboratório ou excluir uma conta de laboratório.

## <a name="connect-with-a-peer-virtual-network"></a>Conectar-se a uma rede virtual de mesmo nível
Para conectar uma rede virtual como uma rede de mesmo nível à rede virtual do laboratório, siga estas etapas:

1. Na página **da Conta lab,** selecione **a configuração labs** no menu esquerdo.

    ![Página de configuração de laboratórios](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Para **a rede virtual Peer,** selecione **Ativado** ou **Desativado**. O valor predefinido é **desativado**. Para habilitar a rede virtual de mesmo nível, execute as seguintes etapas: 
    1. Selecione **Ativado**.
    2. Selecione o **VNet** da lista de lançamentos. 
3. Selecione **Guardar** na barra de ferramentas. 

Os laboratórios criados nessa conta estão conectados à rede virtual selecionada. Eles podem acessar os recursos na rede virtual selecionada. Para mais informações, consulte [Connect a rede do seu laboratório com uma rede virtual de pares nos Serviços de Laboratório Azure](how-to-connect-peer-virtual-network.md).

Ao selecionar uma rede virtual para o campo de **rede virtual Peer,** o criador do **laboratório permitir escolher a** opção de localização do laboratório é desativado. É porque os laboratórios na conta do laboratório devem estar na mesma região que a conta do laboratório para que eles se conectem aos recursos na rede virtual do par. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Permitir que o criador do laboratório escolha o local do laboratório
Você pode permitir que o criador do laboratório crie laboratórios em um local diferente do local da conta do laboratório seguindo estas etapas: 

1. Na página **da Conta lab,** selecione **a configuração labs** no menu esquerdo.
2. Para que o criador do laboratório permita que o criador do laboratório escolha a localização do **laboratório,** selecione **Habilitado** se quiser que o criador do laboratório seja capaz de selecionar um local para o laboratório. Se ele estiver desabilitado, os laboratórios serão criados automaticamente no mesmo local em que a conta de laboratório existe. 
    
    Este campo é desativado quando seleciona uma rede virtual para o campo de **rede virtual Peer.** É porque os laboratórios na conta do laboratório devem estar na mesma região que a conta do laboratório para que eles acessem os recursos na rede virtual do par. 
1. Selecione **Guardar** na barra de ferramentas. 

    ![Definir configuração de local do laboratório](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Especificar um intervalo de endereços para VMs no laboratório
O procedimento a seguir tem etapas para especificar um intervalo de endereços para VMs no laboratório. Se você atualizar o intervalo especificado anteriormente, o intervalo de endereços modificados se aplicará somente às VMs criadas depois que a alteração tiver sido feita. 

Aqui estão algumas restrições ao especificar o intervalo de endereços que você deve ter em mente. 

- O prefixo deve ser menor ou igual a 23. 
- Se uma rede virtual estiver emparelhada com a conta de laboratório, o intervalo de endereços fornecido não poderá se sobrepor ao intervalo de endereços da rede virtual emparelhada.

1. Na página **da Conta lab,** selecione **a configuração labs** no menu esquerdo.
2. Para o campo **de intervalo de endereços,** especifique o intervalo de endereços para VMs que serão criados em laboratório. O intervalo de endereços deve estar na notação CIDR (roteamento entre domínios sem classificação) (exemplo: 10.20.0.0/23). As máquinas virtuais no laboratório serão criadas neste intervalo de endereços.
3. Selecione **Guardar** na barra de ferramentas. 

    ![Configurar intervalo de endereços](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um utilizador à função de Criador do Laboratório
Para configurar um laboratório de sala de aula numa conta de laboratório, o utilizador tem de ser membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Se estiver a planear utilizar a mesma conta de utilizador para criar um laboratório de sala de aula, pode ignorar este passo. Para utilizar outra conta de utilizador para criar um laboratório de sala de aula, siga os passos abaixo: 

Para dar permissão aos educadores para criarem laboratórios para as classes deles, adicione-os à função **Criador do Laboratório**:

1. Na página **da Conta Lab,** selecione o controlo de **acesso (IAM)** e clique + Adicione a atribuição de **funções** na barra de ferramentas. 

    ![Controle de acesso – botão > Adicionar atribuição de função](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
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

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Adicionar um usuário não conta Microsoft como um criador de laboratório
Para adicionar um usuário como um criador de laboratório, você usa suas contas de email. Os seguintes tipos de contas de email podem ser usados:

- Uma conta de email fornecida pelo AAD (Office 365 Azure Active Directory da sua universidade). 
- Uma conta de e-mail da Microsoft, como `@outlook.com`, `@hotmail.com`, `@msn.com`ou `@live.com`.
- Uma conta de email que não seja da Microsoft, como uma fornecida pelo Yahoo ou pelo Google. No entanto, esses tipos de contas devem ser vinculados a um conta Microsoft.
- Uma conta do GitHub. Essa conta deve ser vinculada a um conta Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Usando uma conta de email que não seja da Microsoft
Os criadores/instrutores de laboratório podem usar contas de email que não são da Microsoft para se registrar e entrar em um laboratório de sala de aula.  No entanto, a entrada no portal de serviços de laboratório requer que os instrutores criem primeiro um conta Microsoft que esteja vinculado ao seu endereço de email que não seja da Microsoft.

Muitos instrutores podem já ter uma conta Microsoft vinculada a seus endereços de email que não são da Microsoft. Por exemplo, os instrutores já têm um conta Microsoft se usaram seu endereço de email com outros produtos ou serviços da Microsoft, como Office, Skype, OneDrive ou Windows.  

Quando os instrutores entram no portal dos serviços de laboratório, eles são solicitados a fornecer seu endereço de email e senha. Se o instrutor tentar entrar com um não conta Microsoft que não tenha uma conta Microsoft vinculada, o instrutor receberá a seguinte mensagem de erro: 

![Mensagem de erro](../media/how-to-configure-student-usage/cant-find-account.png)

Para se inscrever em uma conta Microsoft, os instrutores devem ir para [http://signup.live.com](http://signup.live.com).  


### <a name="using-a-github-account"></a>Usando uma conta do GitHub
Os instrutores também podem usar uma conta do GitHub existente para se registrar e entrar em um laboratório de sala de aula. Se o instrutor já tiver um conta Microsoft vinculado à sua conta do GitHub, ele poderá entrar e fornecer sua senha, conforme mostrado na seção anterior. Se ainda não ligaram a sua conta GitHub a uma conta Microsoft, devem selecionar **opções de Iniciar sessão:**

![Link de opções de entrada](../media/how-to-configure-student-usage/signin-options.png)

Na página de **opções de iniciar** sessão, selecione **Iniciar sessão com o GitHub**.

![Entrar com o link do GitHub](../media/how-to-configure-student-usage/signin-github.png)

Por fim, eles são solicitados a criar um conta Microsoft que esteja vinculado à sua conta do GitHub. Acontece automaticamente quando o instrutor seleciona **Seguinte**.  O instrutor é imediatamente conectado e conectado ao laboratório da sala de aula.

## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Desligamento automático de VMs na desconexão
Você pode habilitar ou desabilitar o desligamento automático de VMs do laboratório do Windows (modelo ou aluno) depois que uma conexão de área de trabalho remota for desconectada. Você também pode especificar quanto tempo as VMs devem aguardar até que o usuário se reconecte antes de desligar automaticamente.

![Configuração de desligamento automático na conta do laboratório](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Essa configuração se aplica a todos os laboratórios criados na conta do laboratório. Um proprietário de laboratório pode substituir essa configuração no nível de laboratório. A alteração nessa configuração na conta do laboratório afetará apenas os laboratórios criados depois que a alteração for feita.

Para saber como um dono de laboratório pode configurar este cenário a nível de laboratório, consulte [este artigo](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Como dono de laboratório, crie e gere a gestão de laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de laboratório, configura risa e publica modelos](how-to-create-manage-template.md)
- [Como dono de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como utilizador de laboratório, aceda a laboratórios de sala de aula](how-to-use-classroom-lab.md)
