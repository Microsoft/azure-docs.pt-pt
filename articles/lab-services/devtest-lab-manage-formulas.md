---
title: Gerir fórmulas em Azure DevTest Labs para criar VMs Microsoft Docs
description: Este artigo ilustra como criar uma fórmula a partir de uma base (imagem personalizada, imagem de Marketplace ou outra fórmula) ou um VM existente.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a668c1f7cf401c109c1041232d7f28dd2accd750
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760407"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Gerir fórmulas azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Este artigo ilustra como criar uma fórmula a partir de uma base (imagem personalizada, imagem de Marketplace ou outra fórmula) ou um VM existente. Este artigo também o guia através da gestão das fórmulas existentes.

## <a name="create-a-formula"></a>Criar uma fórmula
Qualquer pessoa com permissões de *Utilizadores* de DevTest Labs é capaz de criar VMs usando uma fórmula como base. Há duas formas de criar fórmulas: 

* A partir de uma base - Use quando quiser definir todas as características da fórmula.
* A partir de um VM de laboratório existente - Use quando quiser criar uma fórmula baseada nas configurações de um VM existente.

Para obter mais informações sobre a adição de utilizadores e permissões, consulte [Adicionar proprietários e utilizadores em Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Criar uma fórmula a partir de uma base
Os seguintes passos guiam-no através do processo de criação de uma fórmula a partir de uma imagem personalizada, imagem do Marketplace ou outra fórmula.

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.

3. Na lista de laboratórios, selecione o laboratório desejado.  

4. Na página do laboratório, selecione **Fórmulas (bases reutilizáveis)** .
   
    ![Menu de fórmula](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Na página **Fórmulas,** selecione **+ Adicionar**.
   
    ![Adicione uma fórmula](./media/devtest-lab-create-formulas/add-formula.png)

6. Na página **Base Escolha uma** base, selecione a base (imagem personalizada, imagem do Mercado ou fórmula) a partir da qual pretende criar a fórmula.
   
    ![Lista de base](./media/devtest-lab-create-formulas/base-list.png)

7. No separador **Definições Básicas** da página **de fórmula Criar,** especifique os seguintes valores:
   
    * **Nome da fórmula** - Introduza um nome para a sua fórmula. Este valor é apresentado na lista de imagens base quando cria um VM. O nome é validado à medida que o escreve, e se não for válido, uma mensagem indica os requisitos para um nome válido.
    * **Nome do utilizador** - Introduza um nome de utilizador que lhe seja concedido privilégios de administrador.
    * **Palavra-passe** - Introduza - ou selecione a partir do dropdown - um valor que esteja associado ao segredo (palavra-passe) que pretende utilizar para o utilizador especificado. Para aprender sobre guardar segredos num cofre chave e usá-los ao criar recursos de laboratório, consulte [os segredos da Loja no Cofre chave azure.](devtest-lab-store-secrets-in-key-vault.md)
    * **Tamanho VM** - Selecione **Alterar tamanho** para alterar o tamanho do VM. 
    * **Artefactos** - Selecione **adicionar ou remover artefactos,** no qual seleciona e configura os artefactos que pretende adicionar à imagem base. Para obter mais informações sobre artefactos, consulte [Criar artefactos personalizados para a sua máquina virtual Azure DevTest Labs](devtest-lab-artifact-author.md).
8. Mude para o separador de **definições Avançada** e especifique os seguintes valores:
    - **Rede virtual** - Para alterar a rede virtual, selecione **Change Vnet**. 
    - **Sub-rede** - Para alterar a sub-rede, selecione **Alterar sub-rede**. 
    - **Configuração** do endereço IP - Especifique se deseja os endereços IP públicos, privados ou partilhados. Para obter mais informações sobre endereços IP partilhados, consulte [Understand endereços IP partilhados em Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Data e hora** de validade - Especifique a data e a hora de validade do VM de modo a que o VM seja automaticamente eliminado. 
    - **Tornar esta máquina reivível** - Tornar uma máquina "repreensível" significa que não será atribuída propriedade no momento da criação. Em vez disso, os utilizadores de laboratório poderão tomar posse ("claim") da máquina na página do laboratório.     
    - **Número de casos repreensíveis** - especifique quantas instâncias requisitáveis quer criar. 
8. Selecione **Submeter** para criar a fórmula.

9. Quando a fórmula foi criada, aparece na lista na página **de Fórmulas.**

### <a name="create-a-formula-from-a-vm"></a>Criar uma fórmula a partir de um VM
Os seguintes passos guiam-no através do processo de criação de uma fórmula baseada num VM existente. 

> [!NOTE]
> Para criar uma fórmula a partir de um VM, o VM deve ter sido criado depois de 30 de março de 2016. 
> 
> 

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na página de **visão geral** do laboratório, selecione o VM a partir do qual pretende criar a fórmula.
   
    ![VMs de laboratório](./media/devtest-lab-create-formulas/my-vms.png)
5. Na página do VM, selecione **Fórmula Criar (base reutilizável)** .
   
    ![Criar fórmula](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na página de **fórmula Criar,** introduza um **Nome** e **Descrição** para a sua nova fórmula.
   
    ![Criar página de fórmula](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Selecione **OK** para criar a fórmula.

## <a name="modify-a-formula"></a>Modificar uma fórmula
Para modificar uma fórmula, siga estes passos:

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na página do laboratório, selecione **Fórmulas (bases reutilizáveis)** .
   
    ![Menu de fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na página de fórmulas do **Laboratório,** selecione a fórmula que pretende modificar.
6. Na página de **fórmula 'Actualizar',** faça as edificações desejadas e selecione **Update**.

## <a name="delete-a-formula"></a>Apagar uma fórmula
Para eliminar uma fórmula, siga estes passos:

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na página **Definições** do laboratório, selecione **Fórmulas**.
   
    ![Menu de fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na página de fórmulas do **Laboratório,** selecione a elipse à direita da fórmula que pretende eliminar.
   
    ![Menu de fórmula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. No menu de contexto da fórmula, selecione **Delete**.
   
    ![Menu de contexto de fórmula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Selecione **Sim** ao diálogo de confirmação de eliminação.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blog relacionadas
* [Imagens ou fórmulas personalizadas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Passos seguintes
Depois de ter criado uma fórmula para usar ao criar um VM, o próximo passo é [adicionar um VM ao seu laboratório.](devtest-lab-add-vm.md)

