---
title: Especifique artefactos obrigatórios para o seu Azure DevTest Labs | Microsoft Docs
description: Saiba como especificar artefactos obrigatórios que precisam de ser instalados antes de instalar quaisquer artefactos selecionados pelo utilizador em máquinas virtuais (VMs) em laboratório.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0677f8bea35cb34735fdcf34e717eea349fad8bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480308"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Especifique artefactos obrigatórios para o seu laboratório em Azure DevTest Labs
Como proprietário de um laboratório, pode especificar artefactos obrigatórios que são aplicados a todas as máquinas criadas no laboratório. Imagine um cenário em que quer que cada máquina do seu laboratório esteja ligada à sua rede corporativa. Neste caso, cada utilizador de laboratório teria de adicionar um artefacto de união de domínio durante a criação de máquinas virtuais para garantir que a sua máquina está ligada ao domínio corporativo. Por outras palavras, os utilizadores de laboratório teriam essencialmente de recriar uma máquina caso se esquecessem de aplicar artefactos obrigatórios na sua máquina. Como dono de laboratório, fazes com que o domínio se junte ao artefacto como artefacto obrigatório no teu laboratório. Este passo garante que cada máquina está ligada à rede corporativa e poupa tempo e esforço aos utilizadores do laboratório.
 
Outros artefactos obrigatórios podem incluir uma ferramenta comum que a sua equipa usa, ou um pacote de segurança relacionado com a plataforma que cada máquina precisa de ter por padrão, etc. Em suma, qualquer software comum que todas as máquinas do seu laboratório devem ter torna-se um artefacto obrigatório. Se criar uma imagem personalizada a partir de uma máquina que tenha artefactos obrigatórios aplicados a ela e, em seguida, criar uma máquina fresca a partir dessa imagem, os artefactos obrigatórios são reaplicados na máquina durante a criação. Este comportamento também significa que, mesmo que a imagem personalizada seja antiga, sempre que se cria uma máquina a partir dela, a versão mais atualizada dos artefactos obrigatórios é aplicada a ela durante o fluxo de criação. 
 
Apenas os artefactos que não têm parâmetros são suportados como obrigatórios. O utilizador do laboratório não precisa de introduzir parâmetros adicionais durante a criação de laboratório e, assim, tornar o processo de criação de VM simples. 

## <a name="specify-mandatory-artifacts"></a>Especificar artefactos obrigatórios
Pode selecionar artefactos obrigatórios para máquinas Windows e Linux separadamente. Também pode reordenar estes artefactos dependendo da ordem em que gostaria que se aplicassem. 

1. Na página inicial do seu laboratório, selecione **Configuração e políticas** em **DEFINIÇÕES**. 
3. Selecione **artefactos obrigatórios** em **RECURSOS EXTERNOS.** 
4. **Selecione Editar** na secção **Windows** ou na secção **Linux.** Este exemplo utiliza a opção **Windows.** 

    ![Página de artefactos obrigatórios - Botão de edição](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Selecione um artefacto. Este exemplo utiliza a opção **7-Zip.** 
5. Na página **de artefactos Adicionar,** selecione **Adicionar**. 

    ![Página de artefactos obrigatórios - Adicionar 7 zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Para adicionar outro artefacto, selecione o artigo e **selecione Adicionar**. Este exemplo adiciona o **Chrome** como o segundo artefacto obrigatório.

    ![Página de artefactos obrigatórios - Adicionar Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Na página **de artefactos obrigatórios,** vê-se uma mensagem que especifica o número de artefactos selecionados. Se clicar na mensagem, vê os artefactos que selecionou. **Selecione Guardar** para guardar. 

    ![Página de artefactos obrigatórios - Guardar artefactos](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Repita os passos para especificar artefactos obrigatórios para Os VMs Linux. 
    
    ![Página de artefactos obrigatórios - Artefactos Windows e Linux](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Para **eliminar** um artefacto da lista, selecione **... (elipse)** no final da linha, e selecione **Delete**. 
10. Para **reencomendar** artefactos na lista, hover mouse sobre o artefacto, selecione **... (elipse)** que aparece no início da linha, e arrastar o item para a nova posição. 
11. Para guardar artefactos obrigatórios em laboratório, **selecione Save**. 

    ![Página de artefactos obrigatórios - Guardar artefactos em laboratório](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Feche a página **de Configuração e políticas** (selecione **X** no canto superior direito) para voltar à página inicial para o seu laboratório.  

## <a name="delete-a-mandatory-artifact"></a>Apagar um artefacto obrigatório
Para eliminar um artefacto obrigatório de um laboratório, faça as seguintes ações: 

1. Selecione **Configuração e políticas** em **DEFINIÇÕES**. 
2. Selecione **artefactos obrigatórios** em **RECURSOS EXTERNOS.** 
3. **Selecione Editar** na secção **Windows** ou na secção **Linux.** Este exemplo utiliza a opção **Windows.** 
4. Selecione a mensagem com o número de artefactos obrigatórios no topo. 

    ![Página de artefactos obrigatórios - Selecione a mensagem](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Na página de **artefactos selecionados,** selecione **... (elipse)** para que o artefacto seja eliminado e selecione **Remover**. 
    
    ![Página de artefactos obrigatórios - Remover artefacto](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Selecione **OK** para fechar a página **de artefactos selecionados.** 
7. **Selecione Guardar** na página **de artefactos obrigatórios.**
8. Repita os passos para as imagens **Linux,** se necessário. 
9. **Selecione Guardar** para guardar todas as alterações no laboratório. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Ver artefactos obrigatórios ao criar um VM
Agora, como utilizador de laboratório, pode ver a lista de artefactos obrigatórios enquanto cria um VM no laboratório. Não pode editar ou apagar artefactos obrigatórios definidos no laboratório pelo dono do laboratório.

1. Na página inicial do seu laboratório, selecione **Overview** no menu.
2. Para adicionar um VM ao laboratório, selecione **+ Adicionar**. 
3. Selecione uma **imagem base**. Este exemplo utiliza **o Windows Server, versão 1709**.
4. Note que vê uma mensagem para **artefactos** com o número de artefactos obrigatórios selecionados. 
5. Selecione **Artefactos.** 
6. Confirme que vê os **artefactos obrigatórios especificados** na configuração e políticas do laboratório. 

    ![Criar um VM - artefactos obrigatórios](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Passos seguintes
* Aprenda a [adicionar um repositório de artefactos Git a um laboratório.](devtest-lab-add-artifact-repo.md)

