---
title: Gerir fórmulas no Azure DevTest Labs para criar VMs | Documentos da Microsoft
description: Saiba como atualizar e remover as fórmulas de Azure DevTest Labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: d8f2ae64e2f8e694de5a7cf5aa9049e63998dca0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452662"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Gerir fórmulas de Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Este artigo ilustra como criar uma fórmula de uma base de (imagem personalizada, imagem do Marketplace ou outra fórmula) ou uma VM existente. Este artigo também o orienta por meio do gerenciamento de fórmulas existentes.

## <a name="create-a-formula"></a>Criar uma fórmula
Qualquer pessoa com o DevTest Labs *utilizadores* permissões é capaz de criar VMs com uma fórmula como base. Existem duas formas de criar fórmulas: 

* A partir de uma base - Use quando desejar definir todas as características da fórmula.
* A partir de um laboratório existente VM - utilização quando pretender criar uma fórmula baseada nas definições de uma VM existente.

Para obter mais informações sobre como adicionar utilizadores e permissões, consulte [adicionar proprietários e utilizadores no Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Criar uma fórmula a partir de uma base
Os seguintes passos guiá-lo pelo processo de criação de uma fórmula de uma imagem personalizada, imagem do Marketplace ou outra fórmula.

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.

3. Na lista de laboratórios, selecione o laboratório pretendido.  

4. Na página do laboratório, selecione **fórmulas (reutilizáveis bases)**.
   
    ![Menu de fórmulas](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Sobre o **fórmulas** página, selecione **+ adicionar**.
   
    ![Adicionar uma fórmula](./media/devtest-lab-create-formulas/add-formula.png)

6. Sobre o **Vyberte bázi.** , selecione a base (imagem personalizada, imagem do Marketplace ou fórmula) a partir do qual pretende criar a fórmula.
   
    ![Lista de base](./media/devtest-lab-create-formulas/base-list.png)

7. Na **definições básicas** separador da **criar fórmula** , especifique os seguintes valores:
   
    * **Nome da fórmula** -introduza um nome para a sua fórmula. Este valor é apresentado na lista de imagens de base ao criar uma VM. O nome é validado como escrever e, se não for válido, uma mensagem indica os requisitos para um nome válido.
    * **Nome de utilizador** -introduza um nome de utilizador que é concedido privilégios de administrador.
    * **Palavra-passe** – introduza - ou selecione na lista pendente - um valor que estão associado com o segredo (palavra-passe) que pretende utilizar para o utilizador especificado. Para saber mais sobre a guardar segredos num cofre de chaves e utilizá-las durante a criação de recursos do laboratório, consulte [Store segredos no Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).
    * **Tamanho da VM** - selecione **alterar tamanho** para alterar o tamanho da VM. 
    * **Artefactos** - selecione **adicionar ou remover artefactos** página, em que selecionar e configurar os artefatos que pretende adicionar à imagem de base. Para obter mais informações sobre artefatos, consulte [criar artefactos personalizados para a máquina virtual do Azure DevTest Labs](devtest-lab-artifact-author.md).
8. Mude para o **definições avançadas** separador e especifique os seguintes valores:
    - **Rede virtual** - para alterar a rede virtual, selecione **alterar Vnet**. 
    - **Sub-rede** - para alterar a sub-rede, selecione **alterar sub-rede**. 
    - **Configuração do endereço IP** -Especifique se pretende que os endereços público, privado ou IP partilhados. Para obter mais informações sobre endereços IP partilhados, consulte [compreender partilhado endereços IP no Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Data de expiração e hora** - especifique a data de expiração e hora para a VM para que a VM é eliminada automaticamente. 
    - **Tornar esta máquina reclamáveis** -tornar uma máquina "reclamáveis" significa que ele não será atribuído propriedade no momento da criação. Em vez disso, os utilizadores de laboratório poderá assumir a propriedade ("declaração") a máquina na página do laboratório.     
    - **Número de instâncias reclamáveis** -especifique o número de instâncias reclamáveis que pretende criar. 
8. Selecione **submeter** para criar a fórmula.

9. Quando tiver sido criada a fórmula, será apresentada na lista na **fórmulas** página.

### <a name="create-a-formula-from-a-vm"></a>Criar uma fórmula de uma VM
Os seguintes passos guiá-lo pelo processo de criação de uma fórmula com base numa VM existente. 

> [!NOTE]
> Para criar uma fórmula de uma VM, a VM tem de ter sido criada após 30 de Março de 2016. 
> 
> 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório pretendido.  
4. O laboratório **descrição geral** página, selecione a VM a partir do qual pretende criar a fórmula.
   
    ![VMs de laboratórios](./media/devtest-lab-create-formulas/my-vms.png)
5. Na página da VM, selecione **criar fórmula (base reutilizável)**.
   
    ![Criar a fórmula](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na **criar fórmula** página, introduza um **nome** e **Descrição** para sua nova fórmula.
   
    ![Criar página fórmulas](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Selecione **OK** para criar a fórmula.

## <a name="modify-a-formula"></a>Modificar uma fórmula
Para modificar uma fórmula, siga estes passos:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório pretendido.  
4. Na página do laboratório, selecione **fórmulas (reutilizáveis bases)**.
   
    ![Menu de fórmulas](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Sobre o **fórmulas de laboratório** , selecione a fórmula que pretende modificar.
6. Sobre o **Aktualizovat vzorec** página, efetue as edições de pretendido e selecione **atualização**.

## <a name="delete-a-formula"></a>Eliminar uma fórmula
Para eliminar uma fórmula, siga estes passos:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório pretendido.  
4. O laboratório **configurações** página, selecione **fórmulas**.
   
    ![Menu de fórmulas](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Sobre o **fórmulas de laboratório** página, selecione as reticências à direita da fórmula que pretende eliminar.
   
    ![Menu de fórmulas](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. No menu de contexto a fórmula, selecione **eliminar**.
   
    ![Menu de contexto de fórmulas](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Selecione **Sim** na caixa de diálogo de confirmação de eliminação.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionados
* [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Passos Seguintes
Depois de criar uma fórmula para utilização quando criar uma VM, a próxima etapa é [adicionar uma VM para o laboratório](devtest-lab-add-vm.md).

