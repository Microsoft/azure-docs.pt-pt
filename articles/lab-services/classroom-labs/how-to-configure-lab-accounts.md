---
title: Configurar contas de laboratório no Azure Lab Services | Documentos da Microsoft
description: Saiba como configurar uma conta de laboratório, depois de criado.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: ba469c038f04a31a57e798b97b5120bec573feae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65414045"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configurar contas de laboratório no Azure Lab Services 
No Azure Lab Services, uma conta de laboratório é um contentor para tipos de laboratório gerido, como laboratórios de sala de aula. Um administrador configura uma conta de laboratório com o Azure Lab Services e fornece acesso a proprietários de laboratório que podem criar laboratórios na conta. Este artigo descreve como criar uma conta de laboratório, ver todas as contas de laboratório ou eliminar uma conta de laboratório.

## <a name="connect-with-a-peer-virtual-network"></a>Conecte-se um elemento da rede virtual
Para ligar uma rede virtual como uma rede de mesmo nível de rede do laboratório de virtual, siga estes passos:

1. Sobre o **conta de laboratório** página, selecione **configuração de laboratórios** no menu da esquerda.

    ![Página de configuração de laboratórios](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Para **elemento da rede virtual**, selecione **ativado** ou **desativado**. O valor predefinido é **desativado**. Para ativar a rede virtual ponto a ponto, siga os passos abaixo: 
    1. Selecione **ativada**.
    2. Selecione o **VNet** na lista pendente. 
3. Selecione **Guardar** na barra de ferramentas. 

Laboratórios criados nesta conta estão ligados à rede virtual selecionada. Podem aceder aos recursos na rede virtual selecionada. Para obter mais informações, consulte [ligar a rede do seu laboratório com um elemento da rede virtual no Azure Lab Services](how-to-connect-peer-virtual-network.md).

Quando seleciona uma rede virtual para o **elemento da rede virtual** campo, o **criador do laboratório de permitir a escolher a localização de laboratório** opção está desativada. É como laboratórios na conta de laboratório tem de estar na mesma região que a conta de laboratório para eles se ligue a recursos na rede virtual ponto a ponto. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Permitir que o criador do laboratório escolher a localização para o laboratório
É possível permitir que o criador do laboratório criar laboratórios numa localização diferente da localização da conta do laboratório ao seguir estes passos: 

1. Sobre o **conta de laboratório** página, selecione **configuração de laboratórios** no menu da esquerda.
2. Para o **criador do laboratório de permitir a escolher a localização de laboratório**, selecione **ativado** se pretender que o criador de laboratório para poder selecionar uma localização para o laboratório. Se estiver desativada, os laboratórios são criados automaticamente na mesma localização em que a conta de laboratório existe. 
    
    Este campo está desativado Quando seleciona uma rede virtual para o **elemento da rede virtual** campo. É como laboratórios na conta de laboratório tem de estar na mesma região que a conta de laboratório para os mesmos aceder aos recursos na rede virtual ponto a ponto. 
1. Selecione **Guardar** na barra de ferramentas. 

    ![Configurar definição de localização de laboratório](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Especifique um intervalo de endereços para VMs do laboratório
O procedimento seguinte tem passos para especificar um intervalo de endereços para VMs do laboratório. Se atualizar o intervalo que especificou anteriormente, o intervalo de endereços modificado só se aplica a VMs que são criadas após a alteração foi feita. 

Seguem-se algumas restrições ao especificar o intervalo de endereços que deve ter em mente. 

- O prefixo tem de ser menor ou igual a 23. 
- Se uma rede virtual está em modo de peering para a conta de laboratório, o intervalo de endereço fornecido não pode sobrepor-se com o intervalo de endereços da rede virtual em modo de peering.

1. Sobre o **conta de laboratório** página, selecione **configuração de laboratórios** no menu da esquerda.
2. Para o **intervalo de endereços** campo, especifique o intervalo de endereços para VMs que serão criadas no laboratório. O intervalo de endereços deve estar na notação de classless entre domínios de encaminhamento (CIDR) (exemplo: 10.20.0.0/23). Máquinas virtuais no laboratório serão criadas neste intervalo de endereço.
3. Selecione **Guardar** na barra de ferramentas. 

    ![Configurar o intervalo de endereços](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um utilizador à função de Criador do Laboratório
Para configurar um laboratório de sala de aula numa conta de laboratório, o utilizador tem de ser membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Se estiver a planear utilizar a mesma conta de utilizador para criar um laboratório de sala de aula, pode ignorar este passo. Para utilizar outra conta de utilizador para criar um laboratório de sala de aula, siga os passos abaixo: 

Para dar permissão aos educadores para criarem laboratórios para as classes deles, adicione-os à função **Criador do Laboratório**:

1. Sobre o **conta de laboratório** página, selecione **controlo de acesso (IAM)** e clique em **+ adicionar atribuição de função** na barra de ferramentas. 

    ![Controlo de acesso -> botão Adicionar atribuição de função](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Sobre o **adicionar atribuição de função** página, selecione **criador do laboratório** para **função**, selecione o utilizador que pretende adicionar à função de criadores de laboratório e selecione **guardar**. 

    ![Adicionar o criador do laboratório](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Especifique as imagens do Marketplace disponíveis para os criadores de laboratório
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




## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Como proprietário de um laboratório, criar e gerir laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de um laboratório, configurar e publicar modelos](how-to-create-manage-template.md)
- [Como proprietário de um laboratório, configurar e controlar a utilização de um laboratório](how-to-configure-student-usage.md)
- [Como um utilizador de laboratório, acessar os laboratórios de sala de aula](how-to-use-classroom-lab.md)
