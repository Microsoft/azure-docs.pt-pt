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
ms.openlocfilehash: 88b37ea4ff717689f05afbb41d33a56a8cbb2c22
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547625"
---
# <a name="lab-account-setup-guide"></a>Guia de configuração de conta de laboratório

O primeiro passo que precisa de ser concluído pelos administradores é criar uma conta de laboratório dentro da sua assinatura Azure.  Uma conta de laboratório é um recipiente para os seus laboratórios de sala de aula e só leva alguns minutos para ser configurada.

## <a name="understand-your-schools-lab-account-requirements"></a>Compreenda os requisitos da sua conta de laboratório da sua escola

Para entender como configurar a sua conta de laboratório com base nas necessidades da sua escola, deve considerar as seguintes questões:

**Tenho acesso a uma assinatura Azure?**

Para criar uma conta de laboratório, você precisará de acesso a uma subscrição Azure que está configurada para a sua escola; a sua escola pode ter uma ou mais assinaturas.  Uma subscrição é usada para gerir faturação e segurança para todos os seus recursos Azure\services que são usados dentro dele, incluindo contas de laboratório.

**Quantas contas de laboratório precisam de ser criadas?**

Para começar rapidamente, uma abordagem razoável é criar uma única conta de laboratório e, mais tarde, criar contas laboratoriais adicionais, se necessário.  Por exemplo, pode eventualmente evoluir para ter uma conta de laboratório por departamento.

**Quem devem ser proprietários e contribuintes da conta do laboratório?**

Os seus administradores são normalmente os proprietários que são os que procuram uma conta de laboratório, uma vez que são responsáveis pela gestão das políticas que se aplicam a todos os laboratórios contidos na conta do laboratório.  A pessoa que cria a conta de laboratório é automaticamente um proprietário.  Você pode adicionar proprietários adicionais\colaboradores (normalmente do inquilino AAD associado à sua subscrição) para ajudar a gerir uma conta de laboratório atribuindo o papel de Proprietário\Colaborador ao nível da conta de laboratório.

**Quem poderá criar laboratórios?**

Pode optar por que os seus administradores e membros do corpo docente criem e gerem laboratórios; estes utilizadores (normalmente do inquilino AAD associado à sua subscrição) são atribuídos ao papel de Criador de Laboratório dentro da conta de laboratório.

**Queres dar aos criadores de laboratório a capacidade de salvar imagens que podem ser partilhadas em laboratórios?**

Uma Galeria de Imagem Partilhada é um repositório que pode usar para guardar e partilhar imagens.  O benefício disso é que se você tiver várias classes que precisam das mesmas imagens, os criadores de laboratório podem criar a imagem uma vez e partilhá-la em laboratórios.  No entanto, para começar, é perfeitamente razoável começar sem uma Galeria de Imagem Partilhada; e, pode sempre optar por adicionar um mais tarde.

Se respondeu "Sim" a esta pergunta, terá de criar e anexar uma Galeria de Imagem Partilhada à sua conta de laboratório.  Se responderes, "Não sei", podes adiar esta decisão para mais tarde.

Quando tiver uma Galeria de Imagem Partilhada anexada à sua conta de laboratório

**Que imagens no Azure Marketplace usarão os seus laboratórios de sala de aula?**

O Azure Marketplace fornece centenas de imagens que pode permitir para que os criadores de laboratório possam usar a imagem para criar o seu laboratório.  Algumas imagens podem incluir tudo o que um laboratório já precisa.  Noutros casos, pode utilizar uma imagem como ponto de partida e, em seguida, o criador do laboratório pode personalizá-la instalando aplicações adicionais, ferramentas, etc.

Se não sabe quais as imagens que terá de usar, pode sempre voltar a isto mais tarde para as permitir.  Além disso, a melhor maneira de ver quais as imagens disponíveis é primeiro criar uma conta de laboratório – isto lhe dará acesso para que possa rever a lista de imagens disponíveis e os seus conteúdos.  Mais informações são fornecidas abaixo.
  
**As máquinas virtuais do laboratório (VMs) precisam de ter acesso a outros recursos Azure ou on-prem?**

Ao configurar uma conta de laboratório, também tem a opção de fazer pares com uma rede virtual (VNet).  Para decidir se precisa de consultar um VNet, considere as seguintes questões:

- **Precisa de fornecer acesso a um servidor de licenciamento?**
  
   Se planeia utilizar imagens do Azure Marketplace, o custo da licença de SO está agregado no preço dos Serviços labiais, pelo que *não* precisa de fornecer licenças para o próprio SISTEMA.  No entanto, para aplicações adicionais de software que estejam instaladas, terá de fornecer uma licença conforme apropriado.

- **Os VMs do laboratório precisam de acesso a outros recursos on-prem, tais como uma partilha de ficheiros, base de dados, etc.?**

   É necessário criar um VNet para fornecer acesso aos recursos on-prem, normalmente utilizando um Portal de Rede Virtual site-to-site.  Se não tiver um VNet configurado, terá de ser investido um tempo adicional para isso.  Mais informações sobre como configurar isto são fornecidas abaixo.

- **Os VMs de laboratório precisam de acesso a outros recursos Azure que estão localizados dentro de uma VNet?**

    Se precisa de acesso aos recursos Azure que *não* estejam seguros dentro de um VNet, então pode aceder a esses recursos através da internet pública sem fazer qualquer espreitagem.

    Se respondeu "Sim" a uma ou mais perguntas, então terá de ver a conta do laboratório para um VNet.  Se respondeu: "Não sei", então pode adiar esta decisão para mais tarde, uma vez que pode sempre optar por espreitar um VNet depois de criar a conta de laboratório.

## <a name="set-up-your-lab-account"></a>Configurar a sua conta de laboratório

Assim que compreenderes os requisitos para a tua conta de laboratório, estás pronto para a preparar.  Siga os links nesta secção para ver como configurar a sua conta de laboratório:

1. **Crie a sua conta de laboratório**

   Consulte o tutorial sobre [a criação](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) de uma conta de laboratório para instruções.

   Ao criar uma conta de laboratório, poderá ser útil familiarizar-se com os recursos azure envolvidos; consulte a seguinte lista para obter mais informações e orientações sobre a criação destes recursos:

   - [Subscrição](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Grupo de Recursos](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Conta de Laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Laboratório de Sala de Aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Selecionando uma Região\Localização](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Orientação de Nomeação para Recursos](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Adicione utilizadores ao papel de Criador de Laboratório**

   Consulte o tutorial sobre a adição de [utilizadores à função de Criador](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role) de Laboratório para obter instruções.

   Além disso, para mais informações sobre as diferentes funções que podem ser atribuídas aos utilizadores que irão gerir contas de laboratório e laboratórios, consulte o [guia sobre a gestão](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)da identidade.

3. **Ligue-se a um vNet de pares**

   Consulte o guia como ligar [a rede do seu laboratório a um VNet para](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) obter instruções.

   Também pode ser necessário consultar instruções sobre a configuração da gama de [endereços VMs do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Ativar e rever imagens**

    Consulte o guia como [permitir imagens](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images) do Marketplace para os criadores de laboratório para obter instruções.

    Para rever o conteúdo de cada imagem do Marketplace, clique no nome da imagem.  Por exemplo, consulte a seguinte imagem que mostra os detalhes para a imagem VM da Ciência dos Dados de Ubuntu:

    ![Rever imagens do Marketplace](../media/setup-guide/review-marketplace-images.png)

    Se tiver uma Galeria de Imagem Partilhada anexada à sua conta de laboratório e pretender permitir que as imagens personalizadas sejam partilhadas por criadores de laboratório, terá de completar passos semelhantes, como mostra a seguinte imagem:

    ![Ativar imagens personalizadas na Galeria de Imagem Partilhada](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos:

- [Gerir contas de laboratório](how-to-manage-lab-accounts.md)

- [Guia de configuração de laboratório de sala de aula](setup-guide.md)
