---
title: Tutorial - Crie uma réplica definida nos Serviços de Domínio AZure AD | Microsoft Docs
description: Saiba como criar e utilizar conjuntos de réplicas no portal Azure para resiliência de serviço com serviços de domínio AD Azure
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: justinha
ms.openlocfilehash: e8887cc39b48f090ff223e5e83c13d65b921dc0b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687503"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services"></a>Tutorial: Criar e utilizar conjuntos de réplicas para resiliência ou geolocalização em Serviços de Domínio do Diretório Ativo Azure

Para melhorar a resiliência de um domínio gerido pelo Azure Ative Directory Domain Services (Azure AD DS) ou implementar para localizações geográficas adicionais próximas das suas aplicações, pode utilizar *conjuntos de réplicas*. Cada espaço de nome gerido por Azure AD DS, como *aaddscontoso.com,* contém um conjunto de réplicas iniciais. A capacidade de criar conjuntos de réplicas adicionais noutras regiões do Azure proporciona resiliência geográfica para um domínio gerido.

Pode adicionar um conjunto de réplicas a qualquer rede virtual esprevada em qualquer região do Azure que suporte Azure AD DS.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Compreender os requisitos de rede virtual
> * Criar um conjunto de réplicas
> * Excluir um conjunto de réplicas

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio gerido por Serviços de Domínio do Diretório Ativo Azure criado utilizando o modelo de implementação do Gestor de Recursos Azure e configurado no seu inquilino Azure AD.
    * Se necessário, [crie e configuure um domínio gerido por Azure Ative Directory Domain Services][tutorial-create-instance].

    > [!IMPORTANT]
    > Os domínios geridos criados utilizando o modelo de implementação Classic não podem usar conjuntos de réplicas. Também precisa de utilizar um mínimo de *Enterprise* SKU para o seu domínio gerido. Se necessário, [mude o SKU para um domínio gerido][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, cria-se e gere conjuntos de réplicas utilizando o portal Azure. Para começar, inicie primeiro o sinal no [portal Azure.](https://portal.azure.com)

## <a name="networking-considerations"></a>Considerações de redes

As redes virtuais que acolhem conjuntos de réplicas devem ser capazes de comunicar entre si. As aplicações e serviços que dependem do Azure AD DS também precisam de conectividade de rede para as redes virtuais que hospedam os conjuntos de réplicas. O espreitamento da rede virtual Azure deve ser configurado entre todas as redes virtuais para criar uma rede totalmente malhada. Estes persipectivos permitem uma replicação intra-local eficaz entre conjuntos de réplicas.

Antes de utilizar conjuntos de réplicas em Azure AD DS, reveja os seguintes requisitos de rede virtual Azure:

* Evite sobrepor-se a espaços de endereço IP para permitir o espreitamento e encaminhamento de rede virtuais.
* Crie sub-redes com endereços IP suficientes para suportar o seu cenário.
* Certifique-se de que a Azure AD DS tem a sua própria sub-rede. Não partilhe esta sub-rede de rede virtual com VMs e serviços de aplicação.
* As redes virtuais espreitadas NÃO são transitivas.

> [!TIP]
> Quando cria uma réplica definida no portal Azure, os espreitadores de rede entre redes virtuais são criados para si.
>
> Se necessário, pode criar uma rede virtual e uma sub-rede quando adicionar uma réplica definida no portal Azure. Ou, pode escolher os recursos de rede virtuais existentes na região de destino para um conjunto de réplicas e deixar que os seus pares sejam criados automaticamente se eles já não existirem.

## <a name="create-a-replica-set"></a>Criar um conjunto de réplicas

Quando cria um domínio gerido, como *aaddscontoso.com,* é criado um conjunto de réplicas iniciais. Conjuntos de réplicas adicionais partilham o mesmo espaço e configuração de nome. As alterações ao Azure AD DS, incluindo a configuração, identidade do utilizador e credenciais, grupos, objetos de política de grupo, objetos de computador e outras alterações são aplicadas a todos os conjuntos de réplicas no domínio gerido utilizando a replicação de DS AD.

Neste tutorial, você cria uma réplica adicional definida numa região de Azure diferente do conjunto inicial de réplicaS AD DS.

Para criar um conjunto de réplicas adicionais, complete os seguintes passos:

1. No portal Azure, procure e selecione **serviços de domínio Azure AD**.
1. Escolha o seu domínio gerido, como *aaddscontoso.com*.
1. No lado esquerdo, selecione **conjuntos de réplicas**. Cada domínio gerido inclui uma réplica inicial definida na região selecionada, como mostra o seguinte exemplo de screenshot:

    ![Exemplo screenshot para ver e adicionar um conjunto de réplica no portal Azure](./media/tutorial-create-replica-set/replica-set-list.png)

    Para criar um conjunto de réplica adicional, selecione **+ Adicionar**.

1. Na janela *de conjunto de réplicas Adicionar,* selecione a região de destino, como *o Leste dos EUA.*

    Selecione uma rede virtual na região de destino, como *vnet-eastus,* em seguida, escolha uma sub-rede como *aadds-subnet*. Se necessário, escolha **Criar novo** para adicionar uma rede virtual na região de destino, em seguida, **conseguir** criar uma sub-rede para Azure AD DS.

    Se já não existirem, os perscumentos de rede virtual Azure são automaticamente criados entre a rede virtual do seu domínio gerido existente e a rede virtual de destino.

    O exemplo seguinte mostra o processo para criar uma nova réplica definida no *Leste dos EUA:*

    ![Imagem de exemplo para criar uma réplica definida no portal Azure](./media/tutorial-create-replica-set/create-replica-set.png)

1. Quando estiver pronto, **selecione Guardar**.

O processo de criação do conjunto de réplicas leva algum tempo à medida que os recursos são criados na região de destino. O domínio gerido em si é então replicado usando a replicação de DS AD.

A réplica definiu relatórios como *Provisioning à* medida que a implementação continua, como mostrado no exemplo seguinte screenshot. Quando concluído, o conjunto de réplicas mostra como *Running*.

![Imagem de exemplo do estado de implantação do conjunto de réplicas no portal Azure](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>Excluir um conjunto de réplicas

Um domínio gerido está atualmente limitado a quatro réplicas - o conjunto de réplicas iniciais, e três conjuntos de réplicas adicionais. Se já não precisar de um conjunto de réplicas, ou se pretender criar uma réplica definida noutra região, pode eliminar conjuntos de réplicas não necessários.

> [!IMPORTANT]
> Não é possível eliminar a última réplica definida num domínio gerido.

Para eliminar um conjunto de réplicas, complete os seguintes passos:

1. No portal Azure, procure e selecione **serviços de domínio Azure AD**.
1. Escolha o seu domínio gerido, como *aaddscontoso.com*.
1. No lado esquerdo, selecione **conjuntos de réplicas**. A partir da lista de conjuntos de réplicas, selecione o menu de contexto **ao** lado do conjunto de réplicas que pretende eliminar.
1. Selecione **Eliminar** do menu de contexto e, em seguida, confirmar que deseja eliminar o conjunto de réplicas.

> [!NOTE]
> A eliminação do conjunto de réplicas pode ser uma operação demorada.

Se já não necessitar da rede virtual ou do seu espreitamento utilizado pelo conjunto de réplicas, também pode eliminar esses recursos. Certifique-se de que nenhum outros recursos de aplicação na outra região precisam das ligações de rede antes de as eliminar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar o espreitamento da rede virtual
> * Criar uma réplica definida numa região geográfica diferente
> * Excluir um conjunto de réplicas

Para obter mais informações conceptuais, saiba como os conjuntos de réplicas funcionam em Azure AD DS.

> [!div class="nextstepaction"]
> [Réplica define conceitos e funcionalidades][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
