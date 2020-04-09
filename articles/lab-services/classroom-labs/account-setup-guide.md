---
title: Guia acelerado de configuração de conta de laboratório para serviços de laboratório azure
description: Este guia ajuda os administradores a criar rapidamente uma conta de laboratório para uso dentro da sua escola.
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
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: 8fcc46487e7f7c2d075639f10a30cae9950ff31b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879586"
---
# <a name="lab-account-setup-guide"></a>Guia de configuração de conta de laboratório

Como primeiro passo, os administradores devem criar uma conta de laboratório dentro da sua assinatura Azure. Uma conta de laboratório é um recipiente para os seus laboratórios de sala de aula, e só leva alguns minutos para ser configurado.

## <a name="understand-your-schools-lab-account-requirements"></a>Compreenda os requisitos da sua conta de laboratório da sua escola

Para entender como configurar a sua conta de laboratório com base nas necessidades da sua escola, deve considerar estas questões.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Tenho acesso a uma assinatura Azure?

Para criar uma conta de laboratório, precisa de acesso a uma subscrição Azure que esteja configurada para a sua escola. Sua escola pode ter uma ou mais assinaturas. Você usa uma subscrição para gerir faturação e segurança para todos os seus recursos e serviços Azure, incluindo contas de laboratório.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Quantas contas de laboratório precisam de ser criadas?

Para começar rapidamente, crie uma única conta de laboratório, e depois crie contas adicionais de laboratório, se necessário. Por exemplo, pode eventualmente ter uma conta de laboratório por departamento.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Quem devem ser proprietários e contribuintes da conta do laboratório?

Os seus administradores são normalmente os proprietários e contribuintes para uma conta de laboratório. São responsáveis pela gestão das políticas que se aplicam a todos os laboratórios contidos na conta do laboratório. A pessoa que cria a conta de laboratório é automaticamente um proprietário. Você pode adicionar proprietários e colaboradores adicionais, tipicamente do inquilino Azure Ative Directory (Azure AD) associado à sua subscrição. Isto pode ser útil para ajudar a gerir uma conta de laboratório atribuindo o papel do proprietário ou do colaborador ao nível da conta de laboratório.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Quem poderá criar e gerir laboratórios?

Pode optar por que os seus administradores e membros do corpo docente criem e gerem laboratórios. Estes utilizadores (normalmente do inquilino Azure AD associado à sua subscrição) são atribuídos ao papel de Criador de Laboratório dentro da conta de laboratório.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Queres dar aos criadores de laboratório a capacidade de salvar imagens que podem ser partilhadas em laboratórios?

Uma galeria de imagens partilhada é um repositório que pode usar para guardar e partilhar imagens. Se tiver várias aulas que precisam das mesmas imagens, os criadores de laboratório podem criar a imagem uma vez, e partilhá-la em laboratórios. No entanto, para começar, não precisa necessariamente de uma galeria de imagens partilhada, porque pode sempre adicionar uma mais tarde.

Se respondeu "sim" a esta pergunta, precisa de criar ou anexar uma galeria de imagens partilhada à sua conta de laboratório. Se responder "não sei", pode adiar esta decisão para mais tarde.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Que imagens no Azure Marketplace usarão os seus laboratórios de sala de aula?

O Azure Marketplace fornece centenas de imagens que pode permitir para que os criadores de laboratório possam usar a imagem para criar o seu laboratório. Algumas imagens podem incluir tudo o que um laboratório já precisa. Noutros casos, pode utilizar uma imagem como ponto de partida, e então o criador do laboratório pode personalizá-la instalando aplicações ou ferramentas adicionais.

Se não sabe quais as imagens que terá de usar, pode sempre voltar a isto mais tarde para as permitir. Além disso, a melhor maneira de ver quais as imagens disponíveis é primeiro criar uma conta de laboratório. Isto dá-lhe acesso, para que possa rever a lista de imagens disponíveis e os seus conteúdos.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>As máquinas virtuais do laboratório precisam de ter acesso a outros recursos Azure ou no local?

Quando criauma conta de laboratório, também tem a opção de fazer pares com uma rede virtual. Para decidir se precisa disto, considere as seguintes questões:

- **Precisa de fornecer acesso a um servidor de licenciamento?**
  
   Se planeia utilizar imagens do Azure Marketplace, o custo da licença do sistema operativo está agregado aos preços dos serviços laboratoriais. Portanto, não precisa de fornecer licenças para o próprio sistema operativo. No entanto, para software adicional e aplicações que estão instaladas, precisa de fornecer uma licença conforme apropriado.

- **Os VMs do laboratório precisam de acesso a outros recursos no local, como uma partilha de ficheiros ou uma base de dados?**

   Cria uma rede virtual para fornecer acesso aos recursos no local, normalmente utilizando um portal de rede virtual site-to-site. Se não tiver uma rede virtual configurada, terá de investir mais tempo para isso.

- **Os VMs de laboratório precisam de acesso a outros recursos Azure que estão localizados dentro de uma rede virtual?**

   Se precisa de acesso aos recursos Do Azure que *não* estão seguros dentro de uma rede virtual, então pode aceder a esses recursos através da internet pública, sem fazer qualquer espreitagem.

Se respondeu "sim" a uma ou mais perguntas, então terá de ver a conta do laboratório para uma rede virtual. Se respondeu "não sei", então pode adiar esta decisão para mais tarde. Pode sempre optar por espreitar uma rede virtual depois de criar a conta de laboratório.

## <a name="set-up-your-lab-account"></a>Configurar a sua conta de laboratório

Depois de entender os requisitos para a sua conta de laboratório, está pronto para o preparar.

1. **Crie a sua conta de laboratório.** Consulte o tutorial sobre [a criação](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) de uma conta de laboratório para instruções.

   Quando estás a criar uma conta de laboratório, podes achar útil familiarizar-te com os recursos do Azure envolvidos. Para obter mais informações, veja os artigos seguintes:

   - [Subscrição](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Grupo de recursos](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Conta de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Laboratório de sala de aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Selecionando uma região e localização](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Orientação de nomeação de recursos](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Adicione os utilizadores ao papel de criador de laboratório.** Para obter instruções, consulte [a adição de utilizadores à função de criador de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).

   Além disso, para obter mais informações sobre as diferentes funções que podem ser atribuídas aos utilizadores que gerem contas de laboratório e laboratórios, consulte o [guia sobre a gestão](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)da identidade.

3. **Ligue-se a uma rede virtual de pares.** Para obter instruções, consulte [a ligação da rede do seu laboratório com uma rede virtual de pares](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Também pode ser necessário consultar instruções sobre a configuração da gama de [endereços vMs do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Ativar e rever imagens.** Para obter instruções, consulte [imagens do Azure Marketplace para criadores de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Para rever o conteúdo de cada imagem do Azure Marketplace, selecione o nome da imagem. Por exemplo, a seguinte imagem mostra os detalhes para a imagem VM da Ciência dos Dados de Ubuntu:

   ![Screenshot das imagens do Review Azure Marketplace](../media/setup-guide/review-marketplace-images.png)

   Se tiver uma galeria de imagens partilhada anexa da sua conta de laboratório, e pretender que as imagens personalizadas sejam partilhadas por criadores de laboratório, complete passos semelhantes aos mostrados na seguinte imagem:

   ![Screenshot de ativar imagens personalizadas em uma galeria de imagens partilhadas](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Passos seguintes

- [Gerir contas de laboratório](how-to-manage-lab-accounts.md)

- [Guia de configuração de laboratório de sala de aula](setup-guide.md)
