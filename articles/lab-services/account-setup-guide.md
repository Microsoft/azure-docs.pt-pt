---
title: Guia de configuração de conta de laboratório acelerado para serviços do Azure Lab
description: Este guia ajuda os administradores a criar rapidamente uma conta de laboratório para uso dentro da escola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: aa3e7b586546b3d87f5c6029b284eeb1402ed171
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659867"
---
# <a name="lab-account-setup-guide"></a>Guia de configuração de conta de laboratório
Para configurar o seu ambiente de Serviços Azure Lab, os administradores precisam primeiro de criar uma **conta de laboratório** dentro da sua assinatura Azure. Uma conta de laboratório é um recipiente para os seus laboratórios e só leva alguns minutos para se instalar.

Este guia inclui três secções:
-  A primeira secção centra-se nos pré-requisitos que precisam de ser preenchidos *antes* de configurar a sua conta de laboratório.
-  A segunda secção fornece orientação sobre o planeamento das definições da sua conta de laboratório.
-  A terceira secção fornece instruções passo a passo para a criação de uma conta de laboratório.

## <a name="prerequisites-for-setting-up-your-lab-account"></a>Pré-requisitos para a criação da sua conta de laboratório
Esta secção descreve os pré-requisitos que precisa de completar antes de poder configurar uma conta de laboratório.

### <a name="obtain-an-azure-subscription"></a>Obtenha uma assinatura Azure
Para criar uma conta de laboratório, precisa de ter acesso a uma subscrição do Azure que está configurada para a sua escola. A sua escola pode ter uma ou mais assinaturas. Você usa uma subscrição para gerir faturação e segurança para todos os seus recursos e serviços Azure, incluindo contas de laboratório.  As subscrições Azure são normalmente geridas pelo seu departamento de TI.  Para mais informações, leia o seguinte tópico:
 - [Guia de Administradores - Subscrição](./administrator-guide.md#subscription)

### <a name="estimate-the-number-of-vms-and-vm-sizes-that-you-need"></a>Calcule o número de tamanhos de VMs e VM de que precisa
Terá de estimar o número de máquinas virtuais (VMs) e os [tamanhos de VM](./administrator-guide.md#vm-sizing) de que a sua escola precisa.  Leia o seguinte post de blog para obter orientações sobre como estruturar os seus laboratórios\imagens.  Esta publicação de blog também o ajudará a decidir o número de VMs e os tamanhos VM que você precisará:
- [Passar de um Laboratório Físico para serviços de laboratório Azure](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

Veja também este artigo que explica orientações adicionais sobre como estruturar laboratórios:
- [Guia de Administradores - Laboratório](./administrator-guide.md)

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Compreender os limites de VM de subscrição e a capacidade de VM regional
Uma vez que tenha uma estimativa do número de VMs e dos tamanhos VM para os seus laboratórios, você precisa:

- Certifique-se de que o limite de capacidade da sua subscrição Azure permite o número de VMs e o tamanho VM que planeia utilizar nos seus laboratórios.

- Crie a sua conta de laboratório numa região que tenha capacidade VM suficiente disponível.

Leia o seguinte post de blog para saber mais: [Limites de Subscrição VM e Capacidade Regional.](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553)

### <a name="decide-how-many-lab-accounts-to-create"></a>Decida quantas contas de laboratório para criar

Para começar rapidamente, crie uma única conta de laboratório dentro do seu próprio grupo de recursos.  Mais tarde, pode criar contas de laboratório adicionais (e grupos de recursos) conforme necessário. Por exemplo, pode eventualmente ter uma conta de laboratório e um grupo de recursos por departamento como forma de separar claramente os custos.  Leia os seguintes artigos para saber mais sobre contas de laboratório, grupos de recursos e custos de separação:
- [Guia de Administradores - Grupo de Recursos](./administrator-guide.md#resource-group)
- [Guia de Administradores - Conta de Laboratório](./administrator-guide.md#lab-account) 
- [Gestão de custos para serviços do Azure Lab](./cost-management-guide.md)

## <a name="planning-your-lab-accounts-settings"></a>Planejando as configurações da sua conta de laboratório

Para planear as definições da sua conta de laboratório, deve considerar as perguntas abaixo.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Quem devem ser proprietários e contribuintes da conta do laboratório?

   Os administradores de TI da sua escola são tipicamente os Proprietários e Contribuintes para uma conta de laboratório. São responsáveis pela gestão das políticas que se aplicam a todos os laboratórios contidos na conta do laboratório. A pessoa que cria a conta de laboratório é automaticamente proprietária. Pode adicionar proprietários e colaboradores adicionais do inquilino Azure Ative Directory (AD) que está associado à sua subscrição. Para obter mais informações sobre as funções de proprietário e colaborador de contas de laboratório, leia:
   -  [Guia de Administrador - Gerir a Identidade.](./administrator-guide.md#manage-identity)

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   Os utilizadores do laboratório só vêem uma única lista das máquinas virtuais a que têm acesso através dos inquilinos dentro dos Serviços Azure Lab.

### <a name="who-will-be-allowed-to-create-labs"></a>Quem será autorizado a criar laboratórios?

   Pode optar por ter os seus membros de TI e docentes a criar laboratórios. Quando um utilizador cria um laboratório, é automaticamente designado como Dono do laboratório.  Para criar laboratórios, os utilizadores (tipicamente do inquilino AD AD Azure associado à sua subscrição) devem ser designados para o papel de Criador de Laboratório dentro da conta de laboratório.  Para obter mais informações sobre o papel de Criador de Laboratório, leia:
   -  [Guia de Administradores - Gerir identidade](./administrator-guide.md#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Quem será autorizado a possuir e gerir laboratórios?

   Você também pode optar por ter ti e membros do corpo docente próprios\gerir laboratórios *sem* dar-lhes a capacidade de criar laboratórios.  Neste caso, os utilizadores do inquilino Azure AD da sua subscrição são designados pelo Proprietário ou Contribuinte para laboratórios existentes.  Para obter mais informações sobre as funções de Proprietário e Colaborador de um laboratório, leia:
   - [Guia de Administradores - Gerir identidade](./administrator-guide.md#manage-identity)

### <a name="do-you-want-to-save-images-that-can-be-shared-across-labs"></a>Deseja guardar imagens que possam ser partilhadas em laboratórios?
A Shared Image Gallery é um repositório que pode usar para guardar e partilhar imagens. Para as aulas que precisam da mesma imagem, os criadores de laboratório podem criar a imagem e depois exportá-la para a Shared Image Gallery.  Uma vez que uma imagem é exportada para a Galeria de Imagem Partilhada, pode ser usada para criar novos laboratórios.

Além disso, pode querer criar as suas imagens no seu ambiente físico e depois importá-las para a Shared Image Gallery.  Para mais detalhes sobre este processo, leia a seguinte publicação de blog: 
- [Importar imagem personalizada para galeria de imagem partilhada](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

Se decidir que precisa de utilizar uma Galeria de Imagens Partilhadas, terá de criar ou anexar uma Galeria de Imagens Partilhadas à sua conta de laboratório. Além disso, pode adiar esta decisão para mais tarde, uma vez que pode ser anexada a uma conta de laboratório a qualquer momento.  Para mais informações sobre a Galeria de Imagens Partilhadas, leia:
- [Guia de Administrador - Galeria de imagens partilhada](./administrator-guide.md#shared-image-gallery)
- [Guia de Administradores - Preços partilhados da galeria de imagens](./administrator-guide.md#shared-image-gallery-2)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Que imagens no Azure Marketplace vão usar os seus laboratórios?
O Azure Marketplace fornece centenas de imagens que pode permitir para que os criadores de laboratório possam usar a imagem para criar o seu laboratório. Algumas imagens podem incluir tudo o que um laboratório já precisa. Noutros casos, pode usar uma imagem como ponto de partida, e então o criador do laboratório pode personalizá-la instalando aplicações ou ferramentas adicionais.

Se não souber de que imagens precisa, pode voltar mais tarde para as ativar. Além disso, a melhor maneira de ver quais imagens estão disponíveis é primeiro criar uma conta de laboratório. Isto dá-lhe acesso para que possa rever a lista de imagens disponíveis e seus conteúdos.  Para mais informações sobre imagens do mercado, leia:
- [Especifique as imagens do Marketplace disponíveis para os criadores de laboratório](./specify-marketplace-images.md)
  
### <a name="do-the-labs-vms-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Os VM do laboratório precisam de ter acesso a outros recursos do Azure ou no local?
Ao configurar uma conta de laboratório, também pode espreitar a sua conta de laboratório com uma rede virtual (VNet).  Tenha em mente que tanto o seu VNet como a sua conta de laboratório devem estar localizados na mesma região.  Para decidir se precisa de espreitar com um VNet, considere os seguintes cenários:

- **Acesso a um servidor de licenciamento**
  
   Quando utiliza imagens do Azure Marketplace, o custo da licença do sistema operativo é agregado no preço dos serviços de laboratório. No entanto, não é necessário fornecer licenças para o próprio sistema operativo. No entanto, para software e aplicações adicionais instalados, você precisa fornecer uma licença conforme apropriado.  Para aceder a um servidor de licenciamento:
   - Pode optar por ligar-se a um servidor de licenciamento no local.  A ligação a um servidor de licenciamento no local requer uma configuração adicional.
   - Outra opção, que é mais rápida de configurar, é criar um servidor de licenciamento que hospeda num Azure VM.  O Azure VM está localizado dentro de uma rede virtual que você espreita para a sua conta de laboratório.

- **Acesso a outros recursos no local, como uma partilha de ficheiros ou uma base de dados**

   Cria um VNet para fornecer acesso a recursos no local, normalmente utilizando um portal de rede virtual site-to-site. A criação deste tipo de ambiente levará mais tempo.

- **Acesso a outros recursos Azure que estão localizados fora de um VNet**

   Se precisar de acesso a recursos Azure que *não* estejam seguros dentro de um VNet, então pode aceder a esses recursos através da internet pública, sem fazer qualquer observação.

Para obter mais informações sobre redes virtuais, leia:
- [Fundamentos da arquitetura - Rede Virtual](./classroom-labs-fundamentals.md#virtual-network)
- [Como ligar-se a uma rede virtual](./how-to-connect-peer-virtual-network.md)
- [Como criar um laboratório com um recurso partilhado nos Serviços de Laboratório Azure](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Crie a sua conta de laboratório

Depois de terminar o planeamento, está pronto para criar a sua conta de laboratório.  Estes mesmos passos são aplicáveis para a criação de um laboratório [Azure Lab Services com equipas.](./lab-services-within-teams-overview.md)

1. **Crie a sua conta de laboratório.** Consulte o tutorial sobre a [criação de uma conta de laboratório](./tutorial-setup-lab-account.md#create-a-lab-account) para obter instruções.
   
    Para obter mais orientações sobre o nome, consulte o seguinte artigo:

   - [Orientação de nomeação para recursos](./administrator-guide.md#naming)

2. **Adicione os utilizadores ao papel de criador de laboratório.** Para obter instruções, consulte [a adição de utilizadores à função de criador do laboratório](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


3. **Ligue-se a uma rede virtual de pares.** Para obter instruções, consulte [a ligação da rede do seu laboratório a uma rede virtual de pares.](./how-to-connect-peer-virtual-network.md)

   Também poderá ter de consultar as instruções sobre [a configuração da gama de endereços de VMs de laboratório](./how-to-configure-lab-accounts.md).

4. **Ativar e rever imagens.** Para obter instruções, consulte [imagens do Azure Marketplace para criadores de laboratório](./specify-marketplace-images.md).

   Para rever o conteúdo de cada imagem do Azure Marketplace, selecione o nome da imagem. Por exemplo, a imagem que se segue mostra os detalhes da imagem VM da Ubuntu Data Science:

   ![Screenshot de Imagens do Mercado de Revisão Azure](./media/setup-guide/review-marketplace-images.png)

   Se uma galeria de imagens partilhada estiver anexada à sua conta de laboratório e pretender permitir que as imagens personalizadas sejam partilhadas por criadores de laboratório, complete passos semelhantes aos mostrados na seguinte imagem:

   ![Screenshot de Ativar imagens personalizadas numa galeria de imagens partilhada](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Próximos passos

Próximos passos que são comuns para a criação de um ambiente de laboratório:

- [Gerir contas de laboratório](how-to-manage-lab-accounts.md)
- [Guia de configuração do laboratório](setup-guide.md)