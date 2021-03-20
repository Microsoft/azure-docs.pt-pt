---
title: Gerir fórmulas em Azure DevTest Labs para criar VMs | Microsoft Docs
description: Este artigo ilustra como criar uma fórmula a partir de uma base (imagem personalizada, imagem de marketplace ou outra fórmula) ou um VM existente.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f012a8c59a8e938334c3e1abc4c7b3ccd0e48d3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91308899"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Gerir fórmulas Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Este artigo ilustra como criar uma fórmula a partir de uma base (imagem personalizada, imagem de marketplace ou outra fórmula) ou um VM existente. Este artigo também o guia através da gestão das fórmulas existentes.

## <a name="create-a-formula"></a>Criar uma fórmula
Qualquer pessoa com permissões *de utilizadores* da DevTest Labs é capaz de criar VMs usando uma fórmula como base. Há duas formas de criar fórmulas: 

* A partir de uma base - Utilize quando quiser definir todas as características da fórmula.
* A partir de um VM de laboratório existente - Use quando pretender criar uma fórmula baseada nas definições de um VM existente.

Para obter mais informações sobre a adição de utilizadores e permissões, consulte [adicionar proprietários e utilizadores em Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Criar uma fórmula a partir de uma base
Os seguintes passos guiam-no através do processo de criação de uma fórmula a partir de uma imagem personalizada, imagem de Marketplace ou outra fórmula.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** da lista.

3. Da lista de laboratórios, selecione o laboratório desejado.  

4. Na página do laboratório, selecione **Fórmulas (bases reutilizáveis)** no menu esquerdo.
5. Na página **fórmulas,** selecione **+ Adicionar**.
   
    ![Adicione uma fórmula](./media/devtest-lab-create-formulas/add-formula.png)
6. Na página **base,** selecione a base (imagem personalizada, imagem de Marketplace ou imagem de Galeria de Imagens Partilhada) a partir da qual pretende criar a fórmula.

    :::image type="content" source="./media/devtest-lab-create-formulas/select-base.png" alt-text="Selecione a imagem base":::
1. No separador **Definições Básicas** da página de **fórmula Criar,** especifique os seguintes valores:
   
    * **Nome da fórmula** - Introduza um nome para a sua fórmula. Este valor é apresentado na lista de imagens base quando cria um VM. O nome é validado à medida que o escreve e, se não for válido, uma mensagem indica os requisitos para um nome válido.
    - Introduza uma **descrição** opcional para a fórmula. 
    * **Nome de utilizador** - Introduza um nome de utilizador que tenha privilégios de administrador.
    * **Palavra-passe** - Introduza - ou selecione a partir do dropdown - um valor que está associado ao segredo (palavra-passe) que pretende utilizar para o utilizador especificado. Para aprender sobre guardar segredos num cofre chave e usá-los ao criar recursos de laboratório, consulte [os segredos da Loja no Cofre da Chave Azure.](devtest-lab-store-secrets-in-key-vault.md)

        **Selecione Utilize um segredo guardado** se quiser usar um segredo do Azure Key Vault em vez de usar uma palavra-passe. 
    * **Tamanho da máquina virtual** - Selecione **O Tamanho** da Mudança para alterar o tamanho do VM. 
    - **Tipo de disco OS** - selecione o tipo de disco que pretende utilizar (HDD Padrão, SSD padrão ou Premium SSD).
    * **Artefactos** - Selecione Adicionar ou Remover a página **de artefactos,** na qual seleciona e configura os artefactos que pretende adicionar à imagem base. Para obter mais informações sobre artefactos, consulte [Criar artefactos personalizados para a sua máquina virtual Azure DevTest Labs.](devtest-lab-artifact-author.md)

        ![Página de definições básicas](./media/devtest-lab-create-formulas/basic-settings.png)
8. Mude para o separador **Definições Avançadas** e especifique os seguintes valores:
    - **Rede virtual** - Para alterar a rede virtual, selecione **Change Vnet**. 
    - **Sub-rede** - Para alterar a sub-rede, selecione **Alterar a Sub-rede**. 
    - **Configuração do endereço IP** - Especificar se pretende os endereços IP públicos, privados ou partilhados. Para obter mais informações sobre endereços IP partilhados, consulte [os endereços IP partilhados no Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Data e hora de validade** - Não pode editar este campo. 
    - **Tornar esta máquina reclamada** - Fazer uma máquina "reivindicada" significa que não será atribuída a propriedade no momento da criação. Em vez disso, os utilizadores de laboratório poderão apropriar-se ("reivindicação") da máquina na página do laboratório.  

        ![Screenshot que mostra as Definições Avançadas para a página "Criar fórmula (base reutilizável)".](./media/devtest-lab-create-formulas/advanced-settings.png)
    - Se selecionou uma imagem da Galeria de Imagens Partilhada como base, também vê o campo da **versão de imagem** que lhe permite selecionar a versão da imagem a partir da galeria que pretende utilizar como base. 

        ![Página definições avançadas](./media/devtest-lab-create-formulas/advanced-settings-shared-image-gallery.png)
8. **Selecione Submeter** para criar a fórmula.

9. Quando a fórmula foi criada, aparece na lista na página **de Fórmulas.**

### <a name="create-a-formula-from-a-vm"></a>Criar uma fórmula a partir de um VM
Os seguintes passos guiam-no através do processo de criação de uma fórmula baseada num VM existente. 

> [!NOTE]
> Para criar uma fórmula a partir de um VM, o VM deve ter sido criado após 30 de março de 2016. 
> 
> 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o laboratório desejado.  
4. Na **página** geral do laboratório, selecione o VM a partir do qual deseja criar a fórmula.
   
    ![Laboratórios VMs](./media/devtest-lab-create-formulas/my-vms.png)
5. Na página do VM, selecione **Criar fórmula (base reutilizável)**.
   
    ![Criar fórmula](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na página **de fórmula Criar,** insira um **Nome** e **Descrição** para a sua nova fórmula.
   
    ![Criar página de fórmula](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Selecione **OK** para criar a fórmula.

## <a name="modify-a-formula"></a>Modificar uma fórmula
Para modificar uma fórmula, siga estes passos:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o laboratório desejado.  
4. Na página do laboratório, selecione **Fórmulas (bases reutilizáveis)**.
   
    ![Screenshot que mostra a página do laboratório com "Fórmulas (bases reutilizáveis)" selecionada.](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na página de **fórmulas lab,** selecione a fórmula que deseja modificar.
6. Na página de **fórmula 'Actualizar',** faça as edições desejadas e selecione **Update**.

## <a name="delete-a-formula"></a>Eliminar uma fórmula
Para eliminar uma fórmula, siga estes passos:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o laboratório desejado.  
4. Na página **de Definições** do laboratório, selecione **Fórmulas**.
   
    ![Menu de fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na página de **fórmulas Lab,** selecione a elipse à direita da fórmula que pretende eliminar.
   
    ![Screenshot que mostra a página "Fórmulas de Laboratório" com a elipse para as opções de fórmula realçadas.](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. No menu de contexto da fórmula, selecione **Delete**.
   
    ![Menu de contexto de fórmula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Selecione **Sim** para o diálogo de confirmação de eliminação.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Publicações de blogs relacionadas
* [Imagens ou fórmulas personalizadas?](devtest-lab-faq.md#what-is-the-difference-between-a-custom-image-and-a-formula)

## <a name="next-steps"></a>Passos seguintes
Depois de ter criado uma fórmula para usar ao criar um VM, o próximo passo é [adicionar um VM ao seu laboratório.](devtest-lab-add-vm.md)

