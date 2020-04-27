---
title: Especifique artefactos obrigatórios para os seus Laboratórios Azure DevTest / Microsoft Docs
description: Saiba como especificar artefactos obrigatórios que necessitem de ser instalados antes de instalar quaisquer artefactos selecionados pelo utilizador em máquinas virtuais (VMs) no laboratório.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 090236ec3647c7c3e38eb862780a615f854e952b
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60562266"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Especifique artefactos obrigatórios para o seu laboratório em Laboratórios Azure DevTest
Como dono de um laboratório, pode especificar artefactos obrigatórios que são aplicados a todas as máquinas criadas no laboratório. Imagine um cenário em que quer que cada máquina do seu laboratório esteja ligada à sua rede corporativa. Neste caso, cada utilizador de laboratório teria de adicionar um artefacto de adesão de domínio durante a criação de máquinas virtuais para garantir que a sua máquina está ligada ao domínio corporativo. Por outras palavras, os utilizadores de laboratório teriam essencialmente de recriar uma máquina no caso de se esquecerem de aplicar artefactos obrigatórios na sua máquina. Como dono de laboratório, fazes com que o domínio se junte ao artefacto como artefacto obrigatório no teu laboratório. Este passo garante que cada máquina está ligada à rede corporativa e poupa tempo e esforço para os utilizadores do laboratório.
 
Outros artefactos obrigatórios podem incluir uma ferramenta comum que a sua equipa utiliza, ou um pacote de segurança relacionado com a plataforma que cada máquina precisa de ter por padrão, etc. Em suma, qualquer software comum que todas as máquinas do seu laboratório devem ter se torne um artefacto obrigatório. Se criar uma imagem personalizada a partir de uma máquina que tenha artefactos obrigatórios aplicados a ela e depois criar uma máquina fresca a partir dessa imagem, os artefactos obrigatórios são reaplicados na máquina durante a criação. Este comportamento também significa que, embora a imagem personalizada seja antiga, cada vez que cria uma máquina a partir dela, a versão mais atualizada dos artefactos obrigatórios é aplicada a ela durante o fluxo de criação. 
 
Apenas artefactos que não têm parâmetros são suportados como obrigatórios. O utilizador do laboratório não precisa de introduzir parâmetros adicionais durante a criação do laboratório e, assim, tornar o processo de criação de VM simples. 

## <a name="specify-mandatory-artifacts"></a>Especificar artefactos obrigatórios
Pode selecionar artefactos obrigatórios para máquinas Windows e Linux separadamente. Também pode reordenar estes artefactos dependendo da ordem em que gostaria que fossem aplicados. 

1. Na página inicial do seu laboratório, selecione **Configuração e políticas** em **DEFINIÇÕES**. 
3. Selecione **artefactos obrigatórios** em **RECURSOS EXTERNOS**. 
4. Selecione **Editar** na secção **Windows** ou na secção **Linux.** Este exemplo utiliza a opção **Windows.** 

    ![Página de artefactos obrigatórios - Botão editar](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Selecione um artefacto. Este exemplo utiliza a opção **7-Zip.** 
5. Na página **adicionar artefacto,** selecione **Adicionar**. 

    ![Página de artefactos obrigatórios - Adicione 7-zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Para adicionar outro artefacto, selecione o artigo e selecione **Adicionar**. Este exemplo adiciona o **Chrome** como o segundo artefacto obrigatório.

    ![Página de artefactos obrigatórios - Adicionar Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Na página de **artefactos Obrigatórios,** vê uma mensagem que especifica o número de artefactos selecionados. Se clicar na mensagem, verá os artefactos que selecionou. Selecione **Guardar** para salvar. 

    ![Página de artefactos obrigatórios - Guardar artefactos](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Repita os passos para especificar artefactos obrigatórios para VMs Linux. 
    
    ![Página de artefactos obrigatórios - Artefactos Windows e Linux](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Para **eliminar** um artefacto da lista, selecione... ** (elipse)** no final da linha, e selecione **Eliminar**. 
10. Para **reencomendar** artefactos na lista, passe o rato sobre o artefacto, selecione... ** (elipse)** que aparece no início da linha, e arrastar o item para a nova posição. 
11. Para guardar artefactos obrigatórios no laboratório, selecione **Guardar**. 

    ![Página de artefactos obrigatórios - Guardar artefactos em laboratório](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Feche a página **de Configuração e políticas** (selecione **X** no canto superior direito) para voltar à página inicial do seu laboratório.  

## <a name="delete-a-mandatory-artifact"></a>Eliminar um artefacto obrigatório
Para eliminar um artefacto obrigatório de um laboratório, faça as seguintes ações: 

1. **Selecione Configuração e políticas** em **DEFINIÇÕES**. 
2. Selecione **artefactos obrigatórios** em **RECURSOS EXTERNOS**. 
3. Selecione **Editar** na secção **Windows** ou na secção **Linux.** Este exemplo utiliza a opção **Windows.** 
4. Selecione a mensagem com o número de artefactos obrigatórios no topo. 

    ![Página de artefactos obrigatórios - Selecione a mensagem](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Na página de **artefactos Selecionados,** selecione **... (elipse)** para que o artefacto seja eliminado e selecione **Remover**. 
    
    ![Página de artefactos obrigatórios - Remova artefacto](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Selecione **OK** para fechar a página de **artefactos Selecionados.** 
7. Selecione **Guardar** na página de **artefactos Obrigatórios.**
8. Repita os passos para as imagens **linux,** se necessário. 
9. Selecione **Guardar** para guardar todas as alterações no laboratório. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Ver artefactos obrigatórios ao criar um VM
Agora, como utilizador de laboratório, pode ver a lista de artefactos obrigatórios enquanto cria um VM no laboratório. Não pode editar ou eliminar artefactos obrigatórios definidos no laboratório pelo dono do laboratório.

1. Na página inicial do seu laboratório, selecione **visão geral** do menu.
2. Para adicionar um VM ao laboratório, selecione **+ Adicione**. 
3. Selecione uma **imagem base**. Este exemplo utiliza o **Windows Server, versão 1709**.
4. Note que vê uma mensagem para **Artefactos** com o número de artefactos obrigatórios selecionados. 
5. Selecione **Artefactos**. 
6. Confirme que vê os **artefactos obrigatórios** que especificou na configuração e políticas do laboratório. 

    ![Criar um VM - artefactos obrigatórios](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Passos seguintes
* Aprenda a [adicionar um repositório de artefactos Git a um laboratório.](devtest-lab-add-artifact-repo.md)

