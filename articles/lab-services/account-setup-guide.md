---
title: Guia de configuração de conta de laboratório acelerado para serviços do Azure Lab
description: Este guia ajuda os administradores a criar rapidamente uma conta de laboratório para uso dentro da escola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e1f36b6d0983c10926a790d42edef3736e848a59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669394"
---
# <a name="lab-account-setup-guide"></a>Guia de configuração de conta de laboratório
Se é administrador, antes de configurar o seu ambiente Azure Lab Services, primeiro precisa de criar uma *conta de laboratório* dentro da sua assinatura Azure. Uma conta de laboratório é um recipiente para um ou mais laboratórios, e leva apenas alguns minutos para se instalar.

Este guia inclui três secções:
-  Pré-requisitos
-  Planeie as definições da sua conta de laboratório
-  Crie a sua conta de laboratório

## <a name="prerequisites"></a>Pré-requisitos
As seguintes secções descrevem o que precisa de fazer antes de poder criar uma conta de laboratório.


### <a name="access-your-azure-subscription"></a>Aceda à sua subscrição Azure
Para criar uma conta de laboratório, precisa de ter acesso a uma assinatura Azure que já está configurada para a sua escola. A sua escola pode ter uma ou mais assinaturas. Você usa uma subscrição para gerir faturação e segurança para todos os seus recursos e serviços Azure, incluindo contas de laboratório.  As subscrições Azure são geralmente geridas pelo seu departamento de TI.  Para mais informações, consulte a secção "Subscrição" dos [Serviços Azure Lab - Guia de Administradores](./administrator-guide.md#subscription).

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>Calcule quantos tamanhos de VM e VM você precisa
É importante saber quantas [máquinas virtuais (VMs) e tamanhos VM](./administrator-guide.md#vm-sizing) o seu laboratório escolar requer. 

Para obter orientações sobre a estruturação dos seus laboratórios e imagens, consulte o post do blog [Move from a physical lab to Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

Para obter orientações adicionais sobre como estruturar laboratórios, consulte a secção "Lab" da [Azure Lab Services - Guia de Administradores](./administrator-guide.md#lab).

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Compreender os limites de VM de subscrição e a capacidade de VM regional
Depois de ter estimado o número de VMs e os tamanhos VM para os seus laboratórios, precisa:

- Certifique-se de que o limite de capacidade da sua subscrição Azure permite o número de VMs e o tamanho VM que planeia utilizar nos seus laboratórios.
- Crie a sua conta de laboratório numa região que tenha capacidade VM disponível suficiente.

Para mais informações, consulte [os limites de subscrição de VM e a capacidade regional.](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553)

### <a name="decide-how-many-lab-accounts-to-create"></a>Decida quantas contas de laboratório para criar

Para começar rapidamente, crie uma única conta de laboratório dentro do seu próprio grupo de recursos.  Mais tarde, pode criar contas de laboratório adicionais e grupos de recursos, conforme necessário. Por exemplo, pode eventualmente ter uma conta de laboratório e um grupo de recursos por departamento como forma de separar claramente os custos. 

Para obter mais informações sobre contas de laboratório, grupos de recursos e custos de separação, consulte:
- A secção "Grupo de Recursos" dos Serviços do [Laboratório Azure - Guia de Administradores](./administrator-guide.md#resource-group)
- A secção "Conta lab" dos Serviços do [Laboratório Azure - Guia de Administradores](./administrator-guide.md#lab-account) 
- [Gestão de custos para serviços do Azure Lab](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>Planeie as definições da sua conta de laboratório

Para planear as definições da sua conta de laboratório, considere as seguintes questões.

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>Quem devem ser os Proprietários e Contribuintes da conta do laboratório?

Os administradores de TI da sua escola normalmente assumem as funções de Proprietário e Contribuinte para uma conta de laboratório. Estas funções são responsáveis pela gestão das políticas que se aplicam a todos os laboratórios na conta do laboratório. A pessoa que cria a conta de laboratório é automaticamente proprietária. Pode adicionar proprietários e colaboradores adicionais do inquilino Azure Ative Directory (Azure AD) que está associado à sua subscrição. 

Para obter mais informações sobre as funções de proprietário e colaborador de contas de laboratório, consulte a secção "Gerir a identidade" dos Serviços do [Laboratório Azure - Guia de Administradores](./administrator-guide.md#manage-identity).

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Os utilizadores de laboratório vêem apenas uma lista dos VMs a que têm acesso através dos inquilinos da AD Azure nos Serviços Azure Lab.

### <a name="who-will-be-allowed-to-create-labs"></a>Quem será autorizado a criar laboratórios?

Pode optar por ter a sua equipa de TI ou membros do corpo docente a criar laboratórios. Para criar laboratórios, atribuem estas pessoas ao papel de Criador de Laboratório dentro da conta do laboratório. Você normalmente atribui este papel ao inquilino da AD Azure que está associado com a sua assinatura escolar. Quem criar um laboratório é automaticamente designado como dono do laboratório.  

Para obter mais informações sobre o papel de Criador de Laboratório, consulte a secção "Gerir a identidade" dos Serviços do [Laboratório Azure - Guia de Administradores.](./administrator-guide.md#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Quem será autorizado a possuir e gerir laboratórios?

Você também pode optar por ter ti e membros do corpo docente próprios\gerir laboratórios *sem* dar-lhes a capacidade de criar laboratórios.  Neste caso, os utilizadores do inquilino Azure AD da sua subscrição são designados pelo Proprietário ou Contribuinte para laboratórios existentes.  

Para obter mais informações sobre as funções de proprietário e contribuinte do laboratório, consulte a secção "Gerir a identidade" dos Serviços do [Laboratório Azure - Guia de Administradores](./administrator-guide.md#manage-identity).

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>Quer guardar imagens e partilhá-las em laboratórios?

A Shared Image Gallery é um serviço que pode utilizar para guardar e partilhar imagens. Para as aulas que precisam de usar a mesma imagem, os Criadores de Laboratório podem criar a imagem e depois exportá-la para uma galeria de imagens partilhada.  Depois de uma imagem ser exportada para a galeria de imagens partilhada, pode ser usada para criar novos laboratórios.

Talvez queira criar as suas imagens no seu ambiente físico e depois importá-las para uma galeria de imagens partilhada. Para mais informações, consulte o post do blog [Import uma imagem personalizada para uma galeria de imagens partilhada.](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

Se decidir utilizar o serviço De Imagem Partilhada, terá de criar ou anexar uma galeria de imagens partilhada à sua conta de laboratório. Pode adiar esta decisão por enquanto, porque uma galeria de imagens partilhada pode ser anexada a uma conta de laboratório a qualquer momento.  

Para obter mais informações, consulte:
- A secção "Galeria de Imagens Partilhada" dos Serviços do [Laboratório Azure - Guia de Administradores](./administrator-guide.md#shared-image-gallery)
- A secção "Preços" dos Serviços do [Laboratório Azure - Guia de Administradores](./administrator-guide.md#pricing)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Que imagens no Azure Marketplace vão usar os seus laboratórios?

O Azure Marketplace fornece centenas de imagens que pode permitir para que os Criadores de Laboratório possam usá-las para criar os seus laboratórios. Algumas imagens podem incluir tudo o que um laboratório já precisa. Noutros casos, poderá utilizar uma imagem como ponto de partida e, em seguida, o Criador de Laboratório pode personalizá-la instalando aplicações ou ferramentas adicionais.

Se não souber de que imagens precisa, pode voltar mais tarde para as ativar. A melhor maneira de ver quais as imagens disponíveis é primeiro criar uma conta de laboratório. Isto dá-lhe acesso para que possa rever a lista de imagens disponíveis e seus conteúdos.  

Para obter mais informações, consulte [especificar as imagens do Azure Marketplace que estão disponíveis para criadores de laboratório.](./specify-marketplace-images.md)
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>Os VMs do laboratório precisam de acesso a outros recursos do Azure ou no local?

Quando configurar uma conta de laboratório, também pode espreitar a sua conta de laboratório com uma rede virtual.  Tenha em mente que tanto a sua rede virtual como a conta de laboratório devem estar localizadas na mesma região.  Para decidir se precisa de espreitar com uma rede virtual, considere os seguintes cenários:

- **Acesso a um servidor de licença**
  
   Quando utiliza imagens do Azure Marketplace, o custo da licença do sistema operativo é agregado no preço dos serviços de laboratório. No entanto, não é necessário fornecer licenças para o próprio sistema operativo. Para software e aplicações adicionais instalados, você precisa fornecer uma licença, conforme apropriado.  Para aceder a um servidor de licença:
   - Pode optar por ligar-se a um servidor de licença no local.  A ligação a um servidor de licença no local requer uma configuração adicional.
   - Outra opção, que é mais rápida de configurar, é criar um servidor de licença que hospeda num Azure VM.  O Azure VM está localizado dentro de uma rede virtual que você espreita com a sua conta de laboratório.

- **Acesso a outros recursos no local, como uma partilha de ficheiros ou base de dados**

   Normalmente cria uma rede virtual para fornecer acesso a recursos no local, utilizando um portal de rede virtual site-to-site. A criação deste tipo de ambiente levará mais tempo.

- **Acesso a outros recursos Azure que estão localizados fora de uma rede virtual**

   Se precisar de acesso a recursos Azure que *não* estejam seguros dentro de uma rede virtual, pode aceder-lhes através da internet pública, sem ter de fazer qualquer espreitamento.

   Para obter mais informações sobre redes virtuais, consulte:
   - A secção "Rede Virtual" dos fundamentos da [Arquitetura nos Serviços do Laboratório Azure](./classroom-labs-fundamentals.md#virtual-network)
   - [Ligue a sua rede de laboratório a uma rede virtual de pares nos Serviços Azure Lab](./how-to-connect-peer-virtual-network.md)
   - [Criar um laboratório com um recurso partilhado nos Serviços de Laboratório Azure](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Crie a sua conta de laboratório

Depois de terminar o planeamento, está pronto para criar a sua conta de laboratório. Pode aplicar os mesmos passos para a criação [dos Serviços de Laboratório Azure em Equipas.](./lab-services-within-teams-overview.md)

1. **Crie a sua conta de laboratório.** Para obter instruções, consulte [Criar uma conta de laboratório.](./tutorial-setup-lab-account.md#create-a-lab-account)
   
    Para obter informações sobre convenções de nomeação, consulte a secção "Nomeação" dos Serviços do [Laboratório Azure - Guia de Administradores](./administrator-guide.md#naming).

1. **Adicione os utilizadores à função De Criador de Laboratório.** Para obter instruções, consulte [adicionar os utilizadores à função Criador do Laboratório](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

1. **Ligue-se a uma rede virtual de pares.** Para obter instruções, consulte [Ligar a rede de laboratório com uma rede virtual de pares.](./how-to-connect-peer-virtual-network.md)

   Também pode ter de consultar as instruções para [configurar o intervalo de endereços de VMs do laboratório](./how-to-configure-lab-accounts.md).

1. **Ativar e rever imagens**. Para obter instruções, consulte [especificar quais as imagens do Azure Marketplace disponíveis para os Criadores de Laboratório.](./specify-marketplace-images.md)

   Para rever o conteúdo de cada imagem do Azure Marketplace, selecione o nome da imagem. Por exemplo, a imagem que se segue mostra os detalhes da imagem VM da Ubuntu Data Science:

   ![Screenshot de uma lista de imagens disponíveis para revisão no Azure Marketplace.](./media/setup-guide/review-marketplace-images.png)

   Se uma galeria de imagens partilhada estiver anexada à sua conta de laboratório e pretender permitir que as imagens personalizadas sejam partilhadas pelos Criadores de Laboratório, complete passos semelhantes aos mostrados na seguinte imagem:

   ![Screenshot de uma lista de imagens personalizadas ativadas numa galeria de imagens partilhada.](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a criação e gestão de laboratórios, consulte:

- [Gerir contas de laboratório](how-to-manage-lab-accounts.md)  
- [Guia de configuração do laboratório](setup-guide.md)
