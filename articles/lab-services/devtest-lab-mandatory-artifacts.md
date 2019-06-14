---
title: Especifique os artefactos obrigatórios para o Azure DevTest Labs | Documentos da Microsoft
description: Saiba como especificar obrigatórios artefatos que precisam de ser instalado antes de instalar todos os artefactos selecionados pelo usuário em máquinas virtuais (VMs) no laboratório.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60562266"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Especifique os artefactos obrigatórios para o laboratório no Azure DevTest Labs
Como proprietário de um laboratório, pode especificar obrigatórios artefatos que são aplicados a todas as máquinas criadas no laboratório. Imagine um cenário onde pretende que cada máquina em seu laboratório estar ligado à sua rede empresarial. Neste caso, cada utilizador do laboratório teria de adicionar um artefato de associação de domínio durante a criação da máquina virtual para se certificar de que sua máquina está ligada ao domínio empresarial. Em outras palavras, os utilizadores de laboratório, essencialmente, teria de voltar a criar uma máquina, caso eles se esqueça de aplicar os artefactos obrigatórios nos respetivos computadores. Como proprietário de um laboratório, verifique o artefacto de associação de domínio como um artefato obrigatório no seu laboratório. Este passo certifica-se de que cada máquina está ligada à rede da empresa e economizando tempo e esforço para os seus utilizadores de laboratório.
 
Outros artefatos obrigatórios podem incluir uma ferramenta comuns que sua equipe usa ou um pacote de segurança relacionados com a plataforma que cada máquina tem de ter por padrão, etc. Em resumo, qualquer software comuns que todas as máquinas no seu laboratório tem de ter se torna um artefato obrigatório. Se criar uma imagem personalizada a partir de uma máquina que tenha artefactos obrigatórios aplicados a ele e, em seguida, criar uma nova máquina a partir dessa imagem, os artefactos obrigatórios são reaplicados a máquina durante a criação. Esse comportamento também significa que, apesar da imagem personalizada é antiga, sempre que criar uma máquina do mesmo a versão mais atualizada dos artefactos obrigatórios são aplicadas ao mesmo durante o fluxo de criação. 
 
Apenas os artefatos que não têm parâmetros são suportados como obrigatório. O utilizador do laboratório não tem de introduzir parâmetros adicionais durante a criação de laboratório e, portanto, tornando o processo de criação de VMS simples. 

## <a name="specify-mandatory-artifacts"></a>Especificar artefactos obrigatórios
Pode selecionar artefactos obrigatórios para máquinas Windows e Linux em separado. Também pode reorganizar estes artefactos dependendo da ordem na qual pretende que sejam aplicadas. 

1. Na home page do seu laboratório, selecione **Konfigurace a zásady** sob **definições**. 
3. Selecione **artefactos obrigatórios** sob **recursos EXTERNOS**. 
4. Selecione **edite** no **Windows** secção ou o **Linux** secção. Este exemplo utiliza a **Windows** opção. 

    ![Página de artefactos obrigatório - botão Editar](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Selecione um artefacto. Este exemplo utiliza **7-Zip** opção. 
5. Sobre o **adicionar artefacto** página, selecione **Add**. 

    ![Artefactos obrigatórios página – Adicionar 7-zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Para adicionar outro artefacto, seleccione o artigo e selecione **adicionar**. Este exemplo adiciona **Chrome** como o artefacto obrigatório segundo.

    ![Página de artefactos obrigatório - adicionar Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Sobre o **artefactos obrigatórios** página, verá uma mensagem que especifica o número de artefactos selecionado. Se clicar na mensagem, verá os artefatos que selecionou. Selecione **guardar** para guardar. 

    ![Página de artefactos obrigatório - guardar artefactos](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Repita os passos para especificar os artefactos obrigatórios para VMs do Linux. 
    
    ![Página de artefactos obrigatório - artefatos do Windows e Linux](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Para **elimine** um artefato da lista, selecione **... (reticências)**  no final da linha e selecione **eliminar**. 
10. Para **reordenar** artefactos na lista, faça pairar o rato passa o mouse sobre o artefacto, selecione **... (reticências)**  que aparece no início da linha e arraste o item para a nova posição. 
11. Para guardar os artefactos obrigatórios no laboratório, selecione **guardar**. 

    ![Página de artefactos obrigatório - guardar artefactos no laboratório](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Fechar o **Konfigurace a zásady** página (selecionar **X** no canto superior direito) para voltar à home page de seu laboratório.  

## <a name="delete-a-mandatory-artifact"></a>Eliminar um artefato obrigatório
Para eliminar um artefato obrigatório a partir de um laboratório, efetue as seguintes ações: 

1. Selecione **Konfigurace a zásady** sob **definições**. 
2. Selecione **artefactos obrigatórios** sob **recursos EXTERNOS**. 
3. Selecione **edite** no **Windows** secção ou o **Linux** secção. Este exemplo utiliza a **Windows** opção. 
4. Selecione a mensagem com o número de artefactos obrigatórios na parte superior. 

    ![Página de artefactos obrigatório - selecione a mensagem](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Sobre o **selecionado artefactos** página, selecione **... (reticências)**  ao artefacto que pretende eliminar e selecione **remover**. 
    
    ![Página de artefactos obrigatório - remover artefactos](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Selecione **OK** para fechar a **selecionado artefactos** página. 
7. Selecione **guardar** sobre o **artefactos obrigatórios** página.
8. Repita os passos para **Linux** imagens se for necessário. 
9. Selecione **guardar** para guardar todas as alterações para o laboratório. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Artefactos de obrigatórios de exibição ao criar uma VM
Agora, como um utilizador de laboratório pode ver a lista de artefactos obrigatórias ao criar uma VM no laboratório. Não é possível editar ou eliminar artefactos obrigatórios definidos no laboratório pelo seu proprietário de laboratório.

1. Na home page para seu laboratório, selecione **descrição geral** no menu.
2. Para adicionar uma VM para o laboratório, selecione **+ adicionar**. 
3. Selecione um **imagem base**. Este exemplo utiliza **versão 1709 do Windows Server**.
4. Tenha em atenção que, verá uma mensagem para **artefactos** com o número de artefactos obrigatórios selecionado. 
5. Selecione **artefactos**. 
6. Confirme que vê a **artefactos obrigatórios** especificado na configuração e políticas do laboratório. 

    ![Criar uma VM - artefactos obrigatórios](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [adicionar um repositório de Git de artefactos a um laboratório](devtest-lab-add-artifact-repo.md).

